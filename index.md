---
title: Start
layout: layout
---

Hey! We're building OrigoDB, the open source in-memory database toolkit for NET/Mono. You should probably try it out!

## What is OrigoDB?
OrigoDB is an in-memory database platform. The engine is 100% ACID, runs in-process and hosts a user defined data model.
The data model can be domain specific or generic and is defined using plain old NET types.
Persistence is achieved by snapshots and write-ahead command logging to the underlying storage.

Our design goals focus around rapid development, testability, simplicity, correctness,
modularity, flexibility and extensibility. Performance was never a goal but OrigoDB easily 
outperforms disk oriented systems. So there is room for performance optimizations.

 
## Example user defined model
{%highlight csharp%}
[Serializable]
public class KeyValueStore : OrigoDB.Core.Model
{
   Dictionary<string,object> _store = new Dictionary<string,object>();
   
   public void Put(string key, object value)
   {
      _store[key] = value;
   }
   
   public void Remove(string key)
   {
      _store.Delete(key);
   }
}
{% endhighlight %}
## Sample usage using proxy
{%highlight csharp%}
   var db = Db.For<KeyValueStore>("mode=embedded");
   db.Put("key", someValue);
{% endhighlight %}

The method calls are transparently intercepted by the proxy, mapped to command objects
and persisted to the command journal.


## Links

* [OrigoDB Server](http://origodb.com/) - Commercial site offering OrigoDB Server including enterprise features and support.
* [Geekstream - tech bloch search demo](http://geekstream.devrexlabs.com) powered by OrigoDB
* [Old blog](http://livedb.devrex.se/) 
* [New blog](http://robertfriberg.se/)
* [@devrexlabs] (http://twitter.com/devrexlabs) - news and announcements on twitter
* [@robertfriberg](http://twitter.com/robertfriberg) - personal twitter
 
* [OrigoDB newsletter] (http://eepurl.com/OGGHz) - monthly newsletter 
* [Mailing list](http://groups.google.com/origodb) for discussions and support
* [Nuget packages](http://nuget.org/packages?q=origodb&sortOrder=package-download-count)
* todo - Binary downloads
* todo release notes
