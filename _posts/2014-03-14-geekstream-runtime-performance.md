---
title: Geekstream runtime performance issue!
layout: layout
comments: true
---

# {{page.title}}
Whoa! Our first runtime performance issue with OrigoDB.

To populate geekstream we run a background process which periodically checks a number of RSS feeds. The feeds are added manually, either individually or from opml files. Initially, I was eager to
pull in a lot of content quickly. I was able to achieve this by gobbling some opml files found with google, the bigger the better. This resulted in some quality issues:
<ul>
	<li>Duplicate feeds due to multiple urls for the same source</li>
	<li>Duplicate items due to 'popular', 'latest' or syndicated streams</li>
	<li>Non tech related feeds</li>
</ul>
Today I started to do some manual cleaning by identifying feeds to remove using random searches like "fish" and "texas", then removing them with a command line tool (included in the source).

<code>$ geekstream.admin -r 2345</code>

To my surprise each invocation was taking several seconds to return. Are the commands taking time to execute or is there something else going on? I could tinker with the logging to see command execution timings but knowledge about the data model and the fact the queries run at the same time were blocked confirms that the commands are slow. So lets have a look at the code to see what's going on and what we can do about it. The application sends a RemoveFeedCommand to the server. The engine executes the command which calls this method:

{% gist rofr/9410254 %}

The size of <code>_mostRecentItems</code> is 10 so is surely insignificant. Following the call to <code>RemoveFeedFromSearchIndex(feedToRemove)</code> leads us to:

{% gist rofr/9410314 %}

Yeah, this looks interesting. The main for loop walks the entire dictionary of keywords which at the moment of writing is slightly more than 300 000. <code>_statistics.TotalKeywords</code> is a misleading name, it's actually the total number of IndexEntry objects linking keywords to articles. <code>entrySet.RemoveWhere()</code> performs a single iteration over the items in the HashSet so the total number of calls to the inner lambda on line 11 is equal to the total number of index entries, more than 11 million. The inner lambda is a hash lookup so should be <code>O(1)</code> which is somewhat confirmed by the <a href="http://msdn.microsoft.com/en-us/library/bb359438(v=vs.110).aspx">MSDN doc for HashSet&lt;T&gt;.Contains&lt;T&gt;(T)</a> method. Here's the index data structure for reference:

<code>Dictionary&lt;string,HashSet&lt;IndexEntry&gt;&gt; _searchIndex</code>

Before we start optimizing let's do some math to figure out how this scales. First some basic assumptions and measures and how they scale:
<ul>
	<li><code><strong>N</strong></code> -&gt; number of articles, grows linearly with time</li>
	<li><code><strong>A</strong></code> -&gt; average number of unique words per article, constant over time</li>
	<li><code><strong>K</strong></code> -&gt; number of keywords, Has a constant upper bound</li>
	<li><code><strong>L</strong></code> -&gt; number of links = N * A, so proportional to N</li>
</ul>
Looking back at our RemoveFeed algorithm we have a loop in a loop which suggests <code>O(N^2)</code> but it's actually <code>O(N)</code> because the total number of calls to the basic operation is <code>L</code>. We could actually write a projection over the command journal, calculate these numbers over time and plot them. But let's save that for separate blog article.

So what do we do about it? There are several things we can improve.
<ol>
	<li><strong>Add a reverse index</strong>, so that FeedItems have references back to each associated <code>HashSet&lt;IndexEntry&gt;</code> object. Then we could just iterate the FeedItems instead of the entire set of keywords. And given that most sources don't grow indefinitely were back to an <code>O(1)</code> algorithm at the expense of some extra memory, a so called space-time trade-off. Perhaps I'll elaborate on the details in a future blog article.</li>
	<li><strong>Run the outer loop in parallel</strong> utilizing cores/processors. Sure, we could divide the execution time by the number of cores (almost) but we'll be back at square one when the number of articles has increased by the same factor. Also, we'll be hogging CPU which could cause other problems. So this is just buying time.</li>
	<li><strong>Redesign the the algorithm to use a read phase and a write phase</strong>. The read phase iterates and builds a list of hashsets to be modified during write phase. The read phase happens during <code>Command&lt;M&gt;.Prepare(M db)</code> which immediately precedes <code>Command&lt;M&gt;.Execute()</code> but allows concurrent queries.</li>
</ol>
So which path do we choose? Or can we do a combination? It all depends on what we want to optimize. We probably want to optimize for query throughput which means fast command execution. In this sense alternative 1 and 3 have the same effect. The latter just calculates on the fly what the former stores in memory. We can combine both alt 1 and alt 2 with multi core processing (alt 2) during <code>Command.Execute()</code> to minimize the duration of the exclusive lock. Hogging cpu during <code>Command.Prepare()</code> will shorten the total execution time but will have an impact on concurrent queries.

As always you should do some profiling or other performance measurements and not base optimizations on assumptions.