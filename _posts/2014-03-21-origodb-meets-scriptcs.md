---
title: OrigoDB meets scriptcs
layout: layout
comments: true
---

# {{page.title}}
Extending OrigoDB with some kind of scripting feature is something we have been thinking about for a long time. An obvious application would be the OrigoDB server console which now only understands 'exit'. Imagine the power of an interactive shell within the server process itself. So, I decided to play around with scriptcs to see what I could come up with.

It didn't go well at first. After fighting for hours (<a href="https://github.com/chocolatey/chocolatey/issues/438">for this reason</a>) with powershell trying to install chocolatey I gave up and decided to try building and installing from source which wen't amazingly well!
<ul>
	<li>Download and unzip the source</li>
	<li>Double click the build.cmd file</li>
	<li>Copy the output from ./Artefacts/Release/bin somewhere</li>
	<li>Add the deployment location to the PATH environment variable</li>
</ul>
After trying out the samples guided by <a title="Getting started with scriptcs" href="http://scottksmith.com/blog/2013/05/08/getting-started-with-scriptcs/">Scott Smith's 'Getting started with scriptcs'</a> I was ready for some experiments.

Running a git bash console on windows I fired up vi, typed the following script and saved as start.csx in an empty folder:

{% gist rofr/9682677 %}

In order to execute, we need to grab the origodb.core nuget package. From the folder with the script:
<pre>
   <code>$ scriptcs -install origodb.core</code>

</pre>
This will create a packages.config file and a packages sub folder in the current directory. Next step is to run the script:
<pre>
   <code>$ scriptcs start.csx</code>

</pre>
A directory called MyModel is created containing a snapshot and journal file. Running the script again should restore the
previous state but instead a SerializationException is thrown. So what's happening? Looking at the stack trace,
the roslyn generated assembly seems to have a random name which is included by the BinaryFormatter in the snapshot file.
Running the script again we get a new random name which doesn't match the one from the previous execution and the load fails.
If we used a model from a precompiled assembly this would not be an issue. For now I just deleted the data directory:
<pre>
   <code>$ rm -fr MyModel</code>

</pre>
Next I ran the script again but instead of terminating entered REPL mode and typed some interactive commands.
Here's a transcript:

{% gist rofr/9683255 %}


Alright! This is fun and looks really promising. The next step is writing some useful examples like journal/snapshot maintenance, backups, ad-hoc qeurying, connecting to a remote OrigoDB server and finally hosting a server with scriptcs.

