---
title: Some random Geekstream metrics
layout: layout
comments: true
excerpt: Here is some technical data about the LiveDB database backing the Geekstream search engine. The data was collected on 2012-08-21. Geekstream runs on a virtual win2008 standard server with 2GB of RAM assigned. The web application has a pool of stateful tcp/ip connections to the LiveDB server running on the same virtual machine. Also, an XSockets server is running to handle websocket connections for the LiveSearch feature.
---

# {{page.title}}
Here is some technical data about the LiveDB database backing the Geekstream search engine. The data was collected on 2012-08-21. Geekstream runs on a virtual win2008 standard server with 2GB of RAM assigned. The web application has a pool of stateful tcp/ip connections to the LiveDB server running on the same virtual machine. Also, an XSockets server is running to handle websocket connections for the LiveSearch feature.

The web server caches nothing at all. For each web request one or more tcp/ip requests are sent to the LiveDB server. A bit foolish, but we want to exercise the server and client library thoroughly to see how they behave.

## Disk
*Snapshots* - There are no snapshots besides the initial snapshot, which has a size of 5KB.

*Journal* - Size of command journal on disk: 620 MB in 1MB  sized journal files.

When the server starts, the most recent snapshot is restored and then the subsequent commands in the journal are replayed. Load time varies depending on disk performance. With the current setup, time from start until the server is ready to handle requests takes 30 seconds. It loads considerably faster on my SSD-blessed laptop.

*Growth* – A 1MB journal file is created every 40 minutes = 36MB per day. The crawler pushes 1500 SetFeedLastCollectedCommands every 30 minutes, which probably makes up for a major part of the journal size. (It feels like a redesign is needed here!)

## Memory
LiveDB Server hosting the data uses 460MB RAM. It’s a 32-bit process so references are 32-bit pointers and the maximum process size is theoretically 4GB. Switching to 64-bit would double the size of the references, increasing RAM usage significantly (can be calculated by analyzing the model), but in return eliminating the 4GB limit.

## Model

* 1 508 feeds
* 51 678 feed items
* 162 103 unique search terms
* 4 832 883 search nodes (links from search terms to feed items)

## Performance
Search queries execute extremely fast. Client-server (web server <–> LiveDB server on same VM) round trip times are included in the search results and range from a few hundredths to tenths of seconds depending on the number of hits. The LiveSearch timings include roundtrip time from the web browser so aren’t really comparable.

## Scalability
I’m not sure I want to know what a Cisco extended memory server with 384GB RAM installed would cost. And even if that’s a lot of memory it’s still an upper limit. We have designed the model to support partitioning, allowing the data to be spread over several database instances. Partitioning, along with failover clustering, is a LiveDB feature we’re working on. Partitioning will allow in practice unlimited growth at the cost of slightly slower queries as the client has to merge and sort query results from each server in the partition.

## Links
The geekstream site: [http://geekstream.devrex.se/](http://geekstream.devrex.se/)

The source code on github: [http://github.com/devrexlabs/geekstream](http://github.com/devrexlabs/geekstream)


Cisco Extended Memory Overview: [http://bit.ly/NDI0UL](http://bit.ly/NDI0UL)

Cisco Extended Memory (<=384GB) Blade Server: [http://bit.ly/PtkyMX](http://bit.ly/PtkyMX)

XSockets real time web framework: [http://xsockets.net/](http://xsockets.net/)
