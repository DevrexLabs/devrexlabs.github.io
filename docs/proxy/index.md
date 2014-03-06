---
title: Proxy
layout: layout
---
## {{page.title}}
Transparent Proxy is a rapid development feature eliminating the need to author commands and queries. The proxy  intercepts method calls and routes them through an engine using generic commands and queries, ProxyCommand and ProxyQuery.

Transparent proxy is implemented as an extension method of `IEngine<T>`. This means proxying can be used with an embedded engine or an OrigoDb client.

## Creating the proxy

The absolute easiest way to create a proxy is using `Db.For<MyModel>()`. This will call `Engine.For<MyModel>` and call the `GetTransparentProxy()` extension method of `IEngine` from the `OrigoDb.Core.Proxy` namespace:
```csharp
//create proxy using Db.For
MyModel model = Db.For<MyModel>();
```
Or you can create it explicitly:

```csharp
   //create proxy explicitly
    using OrigoDb.Core.Proxy;
    var engine = Engine.For<MyModel>();
    MyModel modelProxy = engine.GetTransparentProxy();

    //call a method on the proxy
    modelProxy.AddReminder("Write more documentation", DateTime.Now.AddDays(1));
```
Here's whats happening under the hood:

* The proxy intercepts the call to AddRemimder
* Creates an instance of `ProxyCommand` containing the method name and arguments
* Executes the command on the `IEngine<T>` instance from which the proxy was created

The exact same thing happens with queries.
```csharp
    //query
    var reminders = modelProxy.GetRemindersDue();
```
## Commands and queries
Void public methods are considered commands. Non-void public methods are considered queries. At this time nothing else is proxied. We haven't even tested with overloads or out,ref or params parameters. So keep it simple!

Sometimes you want to return something from a command method. To avoid it being interpreted as a query, tag it with a `ProxyMethod` attribute:
```csharp
    [ProxyMethod(OperationType.Command)]
    public bool Remove(string key)
    { 
       ...
       return keyWasRemoved; 
    }
```
_Warning! If you forget the attribute, nothing get's recorded in the journal, the change is lost and you have a logically corrupt model on your hands._

### Safe results
If your command or query returns results that don't need to be cloned you can set the `ResultIsSafe` property on the `ProxyMethod` attribute:
```csharp
    [ProxyMethod(ResultIsSafe=bool)]
    public ReminderView[] GetRemindersDue(DateTime dueBy)
    {
       return _reminders.Where(r => r.Due <= dueBy)
          .Select(r => new ReminderView(r)).ToArray();
    }
```
_ Note. This is ugly design and needs reworking _

##  Design Considerations
Keep in mind that query and command results and parameters are cloned. All results and arguments have to be serializable. Design your model as a facade for your domain and keep it simple! The following code will not do as intended because the object returned by `GetReminder()` is a clone of the real object
```csharp
    //wrong!
    modelProxy.GetReminder(id).SetCompleted();

    //better
    model.SetCompleted(id);
```
## Versioning
If you change a method signature on the model, `ProxyCommand` objects in the journal could fail. We are looking at some kind of declarative migrations or an upgrade utility, but for now we recommend not changing any existing command methods.

Also, see the advice on the [Schema Evolution](schema-evolution) page. The reasoning applies also when proxying.