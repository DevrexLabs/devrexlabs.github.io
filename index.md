---
title: Start
layout: layout
---
# In-memory database toolkit for NET/Mono
We're building OrigoDB, an open source in-memory database toolkit for NET/Mono.
This is the main project site for developers. 

## Examples
{%highlight csharp%}

   // Generic model
   var db = Db.For<KeyValueStore>("mode=embedded;");
   db.Put("key", someValue);
   db.Remove("key2");
   
   // Domain specific model
   var db = Db.For<BookmarkModel>("mode=remote;host=localhost;port=3001);
   var bookmark = new Bookmark("http://origodb.com/", "OrigoDB Server");
   bookmark.Tags.Add("in-memory");
   db.SaveBookmark(bookmark);
   var bookmarks = db.GetBookmarksByTag("awesome");
{% endhighlight %}


## Learning
* So what is it? Start with the [introduction] (/intro)
* [Engine and developer documentation](/docs)
* Download [example projects](/examples) to experiment with
* Read stuff on the [Old blog](http://livedb.devrex.se/) 
* Read stuff on the [New blog](http://robertfriberg.se/)
* Read [OrigoDb Server documentation](http://devrexlabs.com/docs) (external)
{% comment %} todo API docs {% endcomment %}


## Contributing
Like to help out? Check out the [Issue tracker](https://github.com/DevrexLabs/origodb/issues) to get some ideas then get in
touch to see how we proceed. The preferred procedure is:
1. Make a fork
2. Create a feature branch for your contribution
3. Make a pull request

## Support
Please post questions on the community support forum and general discussion list at [OrigoDb Google Group](https://groups.google.com/forum/#!forum/origodb).
It's really empty at the moment but the team is ready to jump on your question. Need more than the community can offer? 
Devrex Labs offers [commercial support](http://devrexlabs.com/) and consulting services, on site or remote.

## Links

* [OrigoDB Server](http://origodb.com/) - Commercial site offering OrigoDB Server including enterprise features and support.
* [Geekstream - tech bloch search demo](http://geekstream.devrexlabs.com) powered by OrigoDB
* [OrigoDB newsletter] (http://eepurl.com/OGGHz) - monthly newsletter, all things OrigoDB

## Download/Install
* [Nuget packages](http://nuget.org/packages?q=origodb&sortOrder=package-download-count) for core, modules, and models all have an OrigoDb.* prefix.
* Latest binaries in a single package under [Releases page](https://github.com/DevrexLabs/origodb/releases)


## Contact
* [@devrexlabs] (http://twitter.com/devrexlabs) - news and announcements on twitter
* [@robertfriberg](http://twitter.com/robertfriberg) - project coordinator, personal twitter
* Mail: origodb at devrexlabs.com
* [Chatroom on JabbR](https://jabbr.net/#/rooms/OrigoDB)

--
