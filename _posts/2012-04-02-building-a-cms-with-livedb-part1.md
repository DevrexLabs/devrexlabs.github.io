---
title: Building a CMS with LiveDB - part 1
layout: layout
comments: true
---

# {{page.title}}
This is the first article in a series aiming to provide more detail of #liveDB development  than the getting start guide does. You can hack along or sit back and enjoy the ride and download the zipped VS2010 project later.

So, why on earth a CMS when there are so many available out-of-the box? Well, we’re actually building this for a client and they have pretty basic requirements at the moment, so integrating an existing CMS will probably cost more than implementing from scratch using #liveDB. Also, the client has kindly agreed to let us release the code under an open-source license. We believe it will serve as a good example for learning #liveDB. The primary focus will be on building the CMS backend. UI, if any will, be very limited.

## Customers Requirements

* Edit content  using a WYSIWYG editor
* Define and use templates for content
* Manage mail message templates
* Multiple Hierarchical menus
* Independent menus for different sub-sites
* Embedded videos in content pages
* Image galleries and slideshows
* Widgets to display editable content on the site
* Blogroll widget – display list of latest items

I convinced the client to use youtube and picasa for videos and images. We simply store whatever the WYSIWYG editor contains as content.

## Part 1
In this first article, we will set up the project a #liveDB project from scratch and get some basic page handling working including root model, entities, commands and queries. Given the requirements I think a common Page class can be used to represent content pages, blog articles, templates, widgets etc.  The backend doesn’t really need to know what the content is being used for. We may have to rethink this later but let’s try to be a bit agile for the moment.

Start out by creating a new class library and adding a reference to LiveDomain.Core, rename Class1 to Page and mark it with the Serializable attribute. Add some fields, properties and a constructor. Your code should look something like this:

{% gist /rofr/d56cfe45a49b213ea879 %}

As you can see there are no comments, no categories, no author etc. We can add these later if requested by the client, but for now, let’s keep things at a minimum.

The read-only fields communicate intent much better than auto-properties with private setters. The Id will serve as a unique identifier within the model and can be used in urls. Contents can hold just about anything, markup, markdown, plain text, xml, xsl etc.

Now lets create our main model.  During runtime, an instance of this model is your in-memory database, an object graph.  Add a serializable class called CmsModel derived from LiveDomain.Core.Model, a dictionary to hold the pages, a constructor to initialize the dictionary and a few methods to handle pages.

{% gist rofr/a7e287063d23219af8bf %}


Notice how the choice of dictionary as a data structure is encapsulated. We could have made it visible and let the commands access it directly. We prefer to put behavior in the model and entities keeping commands and queries as thin as possible. This makes testing easier and reduces dependencies on the model internals.

## Authoring commands

Modifying the model in any way can only be performed with command objects, so let’s create our first command. Add a class called PutPageCommand derived from Command<CmsModel>, mark it serializable, add a constructor and an override for the Execute method:

{% gist rofr/90a1f6c2beab7fbc87da %}

Now things are getting interesting. Execute() will be called by the engine passing in the one and only instance of CmsModel. The constructor gaurantees that the Page property is set to a valid instance making the call to PutPage safe.  A command should verify that any preconditions are satisfied before modifying any data, preferably by overriding Prepare() and throwing an exception. The engine will in this case abort the command and throw the exception back to the calling client code without writing the command to the journal. Optionally, Execute() can throw a CommandFailedException. Any other exception type will cause a complete restore to the state just prior to the command.

The next command will demonstrate throwing CommandFailedException. Add a new class, RemovePageCommand with the following content:

{% gist rofr/d78202a17709e5d462d8 %}

## Start your engines!

Now that we have an entity, a model and some commands, lets write some tests. Right-click any class and generate at least one unit test just to have the boilerplate done for us. The first test shows how simple it is to test behavior independent of commands and the #livedb engine thanks to our design choices earlier. The other test demonstrates how the engine, model and commands interact.

{% gist rofr/b8d73a302d2ea08422b4 %}

Here’s the contents of the c:\livedb\cms folder. If you run the SmokeTest again, the in-memory model will be restored from these files. For each run there will be one more document in the database! 

<img src="/images/building-cms-1.png"/>

In part 2 of this series we will have a closer look at querying. In the meantime, feel free to comment below. Your input will influence the direction of future posts and features of the CMS.