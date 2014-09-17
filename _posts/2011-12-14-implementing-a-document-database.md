---
title: Implementing a document database
layout: layout
comments: true
---

# {{page.title}}
<p>A prevalence engine supports two types of transactions, commands and queries. This is a very simple and flexible generalization which can be utilized for persistence using several different approaches. One way is to define a domain-specific business model together with a set of commands, for example PlaceOrderCommand, ChangeCustomerAdressCommand and so on. This approach works very well with object-oriented domain modeling. One major benefit is that no mapping is required between the object-oriented model and a separate database model. The in-memory object-oriented model IS the database.</p>
<p>Sometimes we want a more generic way of representing and accessing data and this can be achieved by creating a database-oriented model and commands specific for that type of database. Most databases are modified using a small set of operations specific to the type of database used.</p>
<ul>
<li>Relational databases have insert, update and delete commands </li>
<li>Key/values stores and document databases have put and delete operations </li>
<li>graph databases have commands like CreateNode, SetProperty, CreateAssociation, etc </li>
</ul>
<p>Any of these can be implemented based on a prevalent system architecture. And doing so would be much simpler because persistence and concurrency is already taken care of. Also you get the extreme performance benefit of an in-memory database model.</p>
<p>As a proof of concept we designed and implemented a barebones document oriented database engine based on #liveDB in less than an hour. It's a bit unpolished and has just the essential features but hopefully serves as a good demonstration. Here&rsquo;s the in-memory model which derives from LiveDomain.Core.Model:</p>

{% gist rofr/eeac5c0390101b97783a %}

<p>A document database contains a set of documents, each identified by a unique key. This example implementation uses a Dictionary&lt;object,object&gt; to hold the documents. A richer implementation might use separate collections for different types of documents, might help you generate unique keys when adding documents, use an ID property instead of explicitly requiring you to supply a key, but remember this is just a simple POC for educational purpose.</p>
<h2>Commands</h2>
<p>Two commands are needed to manipulate the model, one to add/replace and one to remove documents. Commands derive from LiveDomain.Core.Command and are written to the command journal when executed providing a complete history of events and a way to restore the in-memory model when the prevalent engine is loaded. The Put-command has no return value, the Remove-command will return false if no such key was found. An additional command to update documents (apply an action) based on some predicate would be very useful, but that&rsquo;s for later.</p>

{% gist rofr/6a1ab40cb1a5eeb96860 %}
<h2>Querying</h2>
<p>Querying is very simple, just pass a Func&lt;&gt; that takes a dictionary and returns whatever you like. Querying by anything other than key will run in time linear to the number of documents, or O(N). This won&rsquo;t scale well but you can handle a surprising number of documents before performance becomes an issue. Adding support for indexing will improve this to O(log N) or O(1) using binary trees or dictionaries respectively.</p>

<h2>Wrapping it up</h2>
<p>Last, a database engine class that encapsulates the prevalence engine and commands providing a simple document database interface.</p>

{% gist rofr/d54e7b89947ff9ffc5dd %}

<h2>Start your engines!</h2>
<p>Now we can do stuff like this:</p>

{% gist rofr/1ccb93d107d9ae16c7fd %}

<h2>Document-oriented vs. Domain specific model</h2>
<p>Using domain-specific task-oriented commands the logic is executed directly within the model using an exclusive lock. Each command ensures the model is valid before altering its state. Also, the command is the implicit unit of work. There is no real need for explicit transactions grouping multiple commands as this can instead be achieved using a composite command pattern.</p>
<p>With a document-oriented approach documents are pulled from the database, manipulated and then saved back to the database. In this case concurrency becomes an issue, two clients can modify the same document and the last one to save will win. This can be remedied by adding support for optimistic concurrency. Also, the operations are CRUD-based (put,remove) so business transactions will need to be composed of multiple operations. So support for explicit transactions is probably a needed feature.</p>
<p>Thank you for stopping by, feel free to leave a comment, send an email, follow us on twitter etc. Any feedback is much appreciated!</p>
<p><a style="display: none;" rel="tag" href="http://www.codeproject.com/script/Articles/BlogFeedList.aspx?amid=2478516">CodeProject</a></p>