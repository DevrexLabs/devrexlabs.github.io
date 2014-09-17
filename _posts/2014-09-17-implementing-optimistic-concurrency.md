---
title: Implementing optimistic concurrency
layout: layout
comments: true
---

# {{page.title}}

Optimistic concurrency is a strategy for dealing with conflicting writes to a database. The example often used is when two users update the same piece of information at the same time. If no strategy is in place to deal with this situation, information can go lost. This is referred to as "Last write wins". A pessimistic strategy would be to first obtain a lock on the piece of information to be updated. This ensures no one else can save until we have released the lock. Finally, an optimistic strategy involves validating that the piece of information hasn't been modified by a different user and hoping for the best. If it has been modified, the operation is canceled and we have to somehow resolve the conflict.

One simple way to detect conflicts is to compare the initial state of an entity with it's current state in the database. Using OrigoDB, this could look something like this:

{% highlight csharp %}
[Serializable]
public class UpdateCustomerCommand : Command<MyModel>
{
   public int Id;
   public Customer Original;
   public Customer Modified;
   
   public override void Execute(MyModel db)
   {
      var current = db.GetCustomerById(Id);
	  if (Original.Name != current.Name) Command.Abort("Optimistic concurrency conflict");
	  //todo: compare more fields...
	  
	  //finally, if no conflicts...
	  db.Customers[Id] = Modified;
   }
}
{% endhighlight %}

This is a CRUD based solution. If any field has been modified, we abort. If you want to be more domain specific or granular you could do:

{% highlight csharp %}
[Serializable]
public class ChangeEmailCommand : Command<MyModel>
{
  public readonly string From;
  public readonly string To;
  public readonly int UserId;
   
  public override void Execute(MyModel db)
  {
    var user = db.GetUserById(Id);
	if (user.Email != From) Command.Abort("Optimistic concurrency conflict");
	user.Email = To;
  }
}
{% endhighlight %}

## Using Model.Revision
The Model base class has a Revision property which is incremented by the engine for each command executed. To be very strict you could require a specific revision during writes, ensuring no part of the database was modified since the view was rendered. A different approach would be tagging every entity modified during a write with the resulting Revision instead of a version per entity. Here's a partial generic entity model showing how this could be achieved:
{% highlight csharp %}

    [Serializable]
    public class EntityModel : Model
    {
        internal protected Dictionary<string, Entity> Entities;

        public EntityModel()
        {
            Entities = new Dictionary<string, Entity>();
        }
    }

    [Serializable]
    public class UpdateCommand : Command<EntityModel>
    {
        public readonly List<Entity> Modified;
        public readonly List<Entity> Inserted;
        public readonly List<Entity> Deleted;

        public UpdateCommand()
        {
            Modified = new List<Entity>();
            Inserted = new List<Entity>();
            Deleted = new List<Entity>();
        }

        public override void Execute(EntityModel model)
        {
            Validate(model);
            Apply(model);
        }

        private void Apply(EntityModel model)
        {
            ulong revision = model.Revision + 1;
            foreach (var entity in Deleted)
            {
                model.Entities.Remove(entity.Key);
            }

            foreach (var entity in Inserted)
            {
                entity.Version = revision;
                model.Entities.Add(entity.Key, entity);
            }

            foreach (var entity in Modified)
            {
                entity.Version = revision;
                model.Entities[entity.Key] = entity;
            }
        }

        private void Validate(EntityModel model)
        {
            var conflicts = new List<Conflict>();
            foreach (var entity in Deleted)
            {
                if (!model.Entities.ContainsKey(entity.Key))
                {
                    conflicts.Add(new Conflict(entity, ConflictReason.MissingKeyOnDelete));
                }
                else if (model.Entities[entity.Key].Version != entity.Version)
                {
                    conflicts.Add(new Conflict(entity, ConflictReason.ConflictingVersionOnDelete));
                }
            }

            foreach (var entity in Inserted)
            {
                if (model.Entities.ContainsKey(entity.Key))
                {
                    conflicts.Add(new Conflict(entity, ConflictReason.ExistingKeyOnInsert));
                }
            }

            foreach (var entity in Modified)
            {
                if (!model.Entities.ContainsKey(entity.Key))
                {
                    conflicts.Add(new Conflict(entity, ConflictReason.MissingKeyOnUpdate));
                }
                else if (model.Entities[entity.Key].Version != entity.Version)
                {
                    conflicts.Add(new Conflict(entity, ConflictReason.ConflictingVersionOnUpate));
                }
            }

            if (conflicts.Any())
            {
                throw new CommandAbortedException("Optimistic concurrency conflict", new OptimisticConcurrencyException(conflicts));
            }

            //make sure no duplicate keys in the UpdateCommand!
            int count = Modified.Concat(Inserted).Concat(Deleted).Select(e => e.Key).Distinct().Count();
            if (count != Modified.Count() + Inserted.Count() + Deleted.Count())
                throw new CommandAbortedException("Duplicate keys within transaction");
        }
    }

    [Serializable]
    public class Conflict
    {
        public readonly Entity Culprit;
        public readonly ConflictReason Reason;

        public Conflict(Entity culprit, ConflictReason reason)
        {
            Culprit = culprit;
            Reason = reason;
        }
    }

    public enum ConflictReason
    {
        MissingKeyOnDelete,
        MissingKeyOnUpdate,
        ConflictingVersionOnUpate,
        ConflictingVersionOnDelete,
        ExistingKeyOnInsert
    }

    [Serializable]
    public abstract class Entity
    {
        public static string RandomKey()
        {
            return Guid.NewGuid().ToString();
        }

        public ulong Version { get; internal set; }
        public readonly string Key;

        protected Entity() : this(RandomKey())
        {
                
        }

        protected Entity(string key)
        {
            Key = key;
        }
    }
{% endhighlight %}

## What is a conflicting write?
The examples above were based on users modifying the same entities. which is not the only type of conflict which can arise. In the following domain specific example a bid is only placed if the highest bid hasn't changed.

{% highlight csharp %}

[Serializable]
public class PlaceBid : Command<AuctionModel>
{
   public readonly int AssumedHighestBid;
   public readonly int Bid;
   public readonly int ItemId;
   
   public override void Execute(AuctionModel db)
   {
      var item = db.ItemsById(ItemId);
	  if (item.Bids.Last().Value != AssumedHighestBid) Command.Abort("Optimistic concurrency conflict");
	  item.Bids.Add(new Bid(Bid))
   }
}
{% endhighlight %}

## Summary
Having a strategy for dealing with write conflicts in a concurrent system is an absolute necessity. In addition, you need to consider the possible anomalies on a per transaction basis. The generic entity example above will not prevent conflicts of the kind in the PlaceBid example. 
