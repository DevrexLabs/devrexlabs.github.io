---
title: Quick Start Guide
layout: submenu
---
#{{page.title}}
Here's a complete guide to get you started with developing your first OrigoDB application!
The following topics are covered:

* Adding the library to your project
* Define the in-memory model
* Create commands and queries
* Hosting the engine
* Executing commands
* Executing queries

## Add the library
The OrigoDB.Core library is a single assembly. Grab the latest OrigoDB.Core.dll from the download page and add as a reference to your .NET project. Or install the [nuget package](url:http://nuget.org/List/Packages/OrigoDB.Core) by typing `Install-Package OrigoDB.Core` in visual studio's package manager console.

## Define the in-memory model
Create a class that derives from `Model` and add members to hold data, usually collections. Mark the class and any referenced types with the `Serializable` attribute. An instance of this class is your in-memory database.

{% highlight csharp %}
    [Serializable]
    public class Task
    {
       public string Title{get;set;}
       public string Description{get;set;}
       public DateTime DueBy{get;set;}
    }

    [Serializable]
    public class TaskModel : OrigoDB.Core.Model
    {
       public List<Task> Tasks{get;set;}
       public TaskModel() { Tasks = new List<Task>(); }
    }
{% endhighlight %}

## Create commands
Commands are used to update the model. Derive from `Command<M>` or `Command<M,R>` where `M` is the type of your model and `R` is the result type

{% highlight csharp %}
    [Serializable]
    public class AddTaskCommand : OrigoDB.Core.Command<TaskModel>
    {
       public string Title{get;set;}
       public string Description{get;set;}
       public DateTime DueBy{get;set;}

       public override void Execute(TaskModel model)
       {
          var task = new Task{ Title = Title, Description = Description, DueBy = DueBy };
          model.Tasks.Add(task);
       }
    }
{% endhighlight %}
## Hosting the engine
`Engine.For<M>()` will create an initial model, write it as a snapshot to disk and then return an engine ready to execute commands and queries.

{% highlight csharp %}
    IEngine<TaskModel> engine = Engine.For<TaskModel>();
{% endhighlight %}

## Executing commands
Create a command object and pass it to the engine for execution:
{% highlight csharp %}
    AddTaskCommand addTaskCommand = new AddTaskCommand {
       Title = "Start using OrigoDB",
       Description = "No more relational modeling, sql or object relational mapping for me!",
       DueBy = DateTime.AddDays(-1)
    };

    //The engine will execute the command against the model and persist to the command journal
    engine.Execute(addTaskCommand);
{% endhighlight %}

## Executing queries
You can use either ad-hoc linq queries passed as lambdas to the engine or you can write strongly typed query classes. 
{% highlight csharp %}
    // can't serialize lambdas, need local engine
    var localEngine = (ILocalEngine<TaskModel>) engine;
    
    //ad-hoc lambda query
    var tasksDue = localEngine.Execute(db => db.Tasks
            .Where(t => DateTime.Today > t.DueBy)
            .OrderByDesc(t => t.DueDy).ToArray());

    [Serializable]
    public class TasksDueBefore : OrigoDB.Core.Query<TaskModel, IEnumerable<Task>>
    {
       public DateTime DueDate{get;set;}

       public IEnumerable<Task> override Execute(TaskModel model)
       {
          return model.Tasks.Where(t => DueDate > t.DueBy).ToArray();
       }

    // executing the strongly typed query
    var query = new TasksDueBefore{DueDate = DateTime.Today.AddDays(1)};
    IEnumerable<Task> tasksDue = engine.Execute(query);
{% endhighlight %}

## Summary
We've covered the absolute basics here, but essentially there's not much more to developing than defining the model, and writing commands and queries. We used explicit transactions, an anemic model and the transaction script pattern. Next, you might wan't to check out [implicit transactions](/docs/proxy), where commands and queries are derived from methods on the model.