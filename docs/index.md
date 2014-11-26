---
layout: submenu
title: Documentation
---

# Documentation
This is the documentation for OrigoDB Engine, Client API and Server.
Navigate using the menu which covers following topics:

* Overview
* Modeling
* Hosting and configuration
* Client Library API
* Storage
* OrigoDB Server

## Appetizer
{%highlight csharp%}

   // Generic data model, implicit commands
   var db = Db.For<KeyValueStore>("mode=embedded;");
   db.Put("key", someValue);
   db.Remove("key2");

   // Domain specific model, implicit commands
   var db = Db.For<BookmarkModel>("mode=remote;host=localhost;port=3001");
   var bookmark = new Bookmark("http://origodb.com/", "OrigoDB Server");
   bookmark.Tags.Add("in-memory");
   db.SaveBookmark(bookmark);
   var bookmarks = db.GetBookmarksByTag("awesome");

   //Domain specific model, explicit commands
   var engine = Engine.For<BookmarkModel>();
   engine.Execute(new AddBookmarkCommand("http://github.com", "Github"));
{% endhighlight %}


## Support

Please post questions on the [community support forum and general discussion list](https://groups.google.com/forum/#!forum/origodb) on Google Groups. It’s a bit empty at the moment but the team is ready to jump on your question. Need more than the community can offer? Devrex Labs offers [commercial support](/order) and consulting services, on site or remote.
