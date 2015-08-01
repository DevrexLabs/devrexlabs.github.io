---
title: Extensibility
layout: submenu
---
## {{page.title}}
OrigoDb can be extended with custom behavior by choosing between existing or writing your own implementations of one or more of the following interfaces:
* `ILogFactory` - logging module, see [Logging](../logging)
* `ISynchronizer` - Handles reader/writer synchronization, used by the Kernel.
* `IStore` - custom storage provider. `FileStore`, `NullStore` and `SqlStore` are available.
* `ICommandJournal`
* `ISerializer`
* `IFormatter` - Xml, Json, BinaryFormatter (default), ProtoBuf...

###  Example
Todo: update this example, it is obsolete. IStore has been split up into ICommandStore and ISnapshotStore.

{% highlight csharp %}
var config = new EngineConfiguration();
config.SetStoreFactory(cfg => new MyStorageProvider(cfg));
db = Db.For<MyModel>(config);
{% endhighlight %}
When the engine requests an instance of `IStore`, the injected custom constructor function is invoked, overriding the default `FileStore` implementation. Similar injection methods for the other interfaces are available.
