---
title: Start
layout: layout
---
# In-memory database engine for NET/Mono
We're building OrigoDB, an open source in-memory database toolkit for NET/Mono.
This is the main project site for developers. If this is your first visit you should
probably start with the [Introduction](/intro).

{%highlight csharp%}
   var db = Db.For<KeyValueStore>("mode=embedded");
   db.Put("key", someValue);
{% endhighlight %}


## Learning
* What is it? Start with the [introduction] (/intro)
* [Engine and developer documentation](/docs)
* Download [example projects](/examples) to experiment with
* [Old blog](http://livedb.devrex.se/) 
* [New blog](http://robertfriberg.se/)
* [OrigoDb Server documentation](http://devrexlabs.com/docs) (external)
{% comment %} todo API docs {% endcomment %}


## Contributing
Like to help out? Check out the [Issue tracker](https://github.com/DevrexLabs/origodb/issues) to get some ideas then get in
touch to see how we proceed. 

## Support
Please post questions on the community support forum and general discussion list at [OrigoDb Google Group](https://groups.google.com/forum/#!forum/origodb).
It's really empty at the moment but the team is ready to jump on your question. Need more than the community can offer? 
[Devrex Labs offers commercial support](http://devrexlabs.com/) and consulting services, on site or remote.

## Links

* [OrigoDB Server](http://origodb.com/) - Commercial site offering OrigoDB Server including enterprise features and support.
* [Geekstream - tech bloch search demo](http://geekstream.devrexlabs.com) powered by OrigoDB
* [OrigoDB newsletter] (http://eepurl.com/OGGHz) - monthly newsletter, all things OrigoDB
* [Nuget packages](http://nuget.org/packages?q=origodb&sortOrder=package-download-count)


## Download/Install
* [Nuget packages](http://nuget.org/packages?q=origodb&sortOrder=package-download-count)

{% comment %}
* Latest binaries in a single package under [Releases page](https://github.com/DevrexLabs/origodb/releases)
{% endcomment %}

## Contact
* [@devrexlabs] (http://twitter.com/devrexlabs) - news and announcements on twitter
* [@robertfriberg](http://twitter.com/robertfriberg) - project coordinator, personal twitter
* Mail: origodb at devrexlabs.com
* [Chatroom on JabbR](https://jabbr.net/#/rooms/OrigoDB)
