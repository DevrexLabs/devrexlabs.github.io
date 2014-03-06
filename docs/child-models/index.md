---
title: Child Models
layout: layout
---
## {{page.title}}
**Note! This is an experimental feature and needs more testing!**
This is a feature which enables reuse of existing models by including them in your Model. Here's a part of the Model class supporting this feature:
{% highlighting csharp %}
    [Serializable]
    public class Model
    {
       //keyed by model type
       private Dictionary<Type,Model> _childModels;

       public T ChildFor<T>() where T : Model
       {
          //return a model of type T, creating one if necessary
       }
    }
{% endhighlighting %}
And here's an example model (partial) which could be a good candidate for reuse:
{% highlighting csharp %}
    [Serializable]
    public class UsersModel : Model
    {
       public void Register(string name, string passwordHash)
       {
          //omitted
       }
       public bool Authenticate(string user, string password)
       {
          //omitted
       }
    }
{% endhighlighting %}
Including and accessing the UserModel using a transparent proxy:
{% highlighting csharp %}
    //get a proxy for the main model
    var db = Db.For<MyModel>();

    //Get a child proxy and do stuff 
    UsersModel userDb = db.ChildFor<UsersModel>()
    string hash = CryptoHelper.HashFor("DOHnuts");
    userDb.Register("homer", hash);
    bool isAuthenticated = userDb.Authenticate("homer", "DOHnuts");
{% endhighlighting %}
Or using traditional command/query style.
{% highlighting csharp %}
    [Serializable]
    public class RegisterUserCommand : Command<UsersModel>
    {
       //note how this command targets UsersModel
    }

    IEngine<MyModel> engine = Engine.For<MyModel>();

    //Execute() now accept commands targeted for any model type and routes to child model
    engine.Execute(new RegisterUserCommand("homer", hash));
{% endhighlighting %}