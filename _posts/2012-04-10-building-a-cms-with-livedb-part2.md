---
title: Building a CMS with LiveDB - part 2
layout: layout
comments: true
excerpt: Welcome to part 2 of this series. In [part 1](), we set up a very simple model containing a Dictionary of Page objects. In this article we will extend the model to handle hierarchical menus. A complete VS2010 solution is available for download below. Let’s start by having a closer look at the requirements specifically related to menus.
---

# {{page.title}}
Welcome to part 2 of this series. In [part 1](), we set up a very simple model containing a Dictionary of Page objects. In this article we will extend the model to handle hierarchical menus. A complete VS2010 solution is available for download below. Let’s start by having a closer look at the requirements specifically related to menus.

* *Multiple Hierarchical menus* – Hierachical means that menus can contain submenus to an arbitrary depth. We’ll solve this by using plain old object oriented inheritance and composition. Multiple means the customer wants to display different menus on different parts of the site. More than one menu may link to the same page or url.
* *Define and use templates for content* – When creating a new page, the user can start with an empty page or copy a page from a list of templates. The list of templates will be implemented as a menu with a designated name. This will be handled at the application level and falls outside the scope of our model.
* *Manage mail message templates* – Same as for content templates.
* *Independent menus for different sub-sites* – see first bullet point.

## Entities
Lets begin by adding entity classes needed to represent the hierarchical menus. You might recognize the Composite design pattern in the following class diagram:

<img src="/images/building-cms-2.png"/>

A MenuItem has a name which is used to identify the top level menu objects or as a display name when the menu is rendered. An InternalMenuItem references a Page object by using the page’s key field. We could also have chosen to store a direct object reference but you will see later how this is a better choice.

These classes are pretty straight forward and anemic, so for brevity, only one listing is provided, ExternalMenuItem.  (The complete VS2010 project is available  for download at the end of the article.)  We’re cutting some corners here with this loose anemic design. Initially we started out with an immutable  design using readonly fields and validation logic in the constructors. That could have gotten us into more trouble later on than the anemic design will,  for example issues with JSON serialization and automapping to strongly typed views in ASP.NET MVC. It’s probably a good idea to redesign later when the overall architecture has settled.

{% highlight csharp %}
[Serializable]
public class ExternalMenuItem : MenuItem
{
	public string Url { get; set; }

}
{% endhighlight %}

## Adding a menu collection to the model and versioning
Next, we add a dictionary field to the CmsModel class mapping menu names to menus and initialize it in the constructor. However, this will not work when we load a snapshot created from a previous version of the model, because the default serializer doesn’t call the constructor during deserialization! There are two solutions to this problem, we can override SnapshotRestored and assign the field if it is null or we can delete all the snapshots forcing the engine to create a new instance of the model and replay the entire sequence of commands. Let’s use the former approach.

We will use Put and Remove methods for menus as we did for pages. We can add methods for partial menu modification later if necesssary, but let´s keep it as simple as possible until we have a good reason to do otherwise. Here is a listing of the modified CmsModel class.

{% gist rofr/ca9a57cd79ed8ee4e1ba %}

## Menu commands
The Put and Remove commands are really simple, no explanation needed.

{% gist rofr/b4b3e4eababf619cf30b %}

## Start your engines!
Finally, lets look at some test code that creates and adds a menu to the model. One interesting thing to note here is that the engine deserializes a snapshot and a number of commands created from the previous version of the code. The commands haven’t changed so this is not a problem, and we fixed the model by overriding SnapshotRestored. I’ll elaborate on schema evolution in a future part of this series.

Another thing to notice is that the menu being created is a fairly complex object graph, at least when it comes to O/R mapping. Using #liveDB you can just forget about relational modeling and O/R mapping altogether and focus on basic object-oriented modeling.

{% gist rofr/03e635ddb6beea5dae7d %}

Thanks for tuning in, and feel free to add your comment below! <a href="/LiveDomain.Cms.Core-part2.zip">Download zipped VS2010 project</a>
