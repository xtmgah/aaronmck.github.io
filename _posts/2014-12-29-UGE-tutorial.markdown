---
layout: post
title:  "UGE - life on the farm"
date:   2014-12-29 14:56:10
categories: farm compute UGE grid engine
---
Sadly, there aren't a lot of clearly writen tutorials out there on Sun Grid Engine (SGE) or its cousins like UGE or OGE.  Although I make no promises about 'clearly writen',  hopefully you can come away from this mini-tutorial with a better understanding of what grid engines are designed for and some basic understanding of their usage.

#### Who Cares?

You.  Ok, more specificly, almost anyone who works in a computing environment.  It used to be that SGE (and related tools like LSF) were the domain of system adminstators and backend programmers, but this changed pretty rapidly (this is a good thing, you get access to new resources).  Now most academic labs have access to large clusters, and the expectation is that you'll use these resources fairly and appropriately.  Great! Except, that mean you have to learn how to do that, and that's the motivation behind the 'who cares' part: you, hopefully.  

#### What are we going to cover?

UGE can seem complicated, but like just about any computer system, you'll need to know about 2% of it to get your routine work done.  I'll try to focus on those 2% here, and mix in some background so that you know where to look for the next step.  Most importantly our goal is that you don't get yelled at for doing something dumb (really everyone's primary goal).  Lastly, some of the information will be specific to the lab I'm working in at the University of Washington, but for anyone else reading this: you can pass over that stuff or fill in the specifics of your setup.

#### What are we not going to cover?

This is intended for people who want to *use* SGE/UGE and it won't cover things like setting up a cluster.  Also, I can't really cover the basics of connnecting to your machine.  You should be confortable opening a terminal and SSH'ing to a remote machine.  Here at UW we have some pretty [aweful documentation](http://www.washington.edu/itconnect/connect/web-publishing/shared-hosting/ssh/ "ssh - UW") on how to do this, but it seems like there aren't many great options out there.  [This tutorial](https://help.ubuntu.com/community/UsingTheTerminal "Ubuntu Terminal") from Ubuntu is a good start, though simple Google searches such as [this](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=ssh+mac) might turn up some good info.

#### Basics

Why do we need SGE/UGE? Lets say your the IT person for a large department at a university. Everyone wants to run their analysis, preferably yesterday, and you need to buy a lot of 'compute' for them to run it on.  How do you manage this?  Back in the day everyone was bought a nice fast desktop, and everyone was happy.  Now people need tons of memory for some jobs, tons of processesors for other jobs, and tons of disk storage for others.  You could just buy one *enourmous* machine with lots of everything, but this is insanely expensive, and graduate students are prone to breaking even the most hardy of machine (ruining everyone else's work in the process).  

So you decide to buy a lot of cheaper machines, some with lots of memory, some with lots of processessors, and stick them on the network.  Problem solved!  But everyone complains because they don't know which machine to use, the machine they like is getting beaten up by 10 grad students because they all followed the same tutorial, and you have no way of knowing if the unsed machine is up and running or on fire in the back closet.  Booo.  You need some way of managing this: a way for people to specify what they need, and a system to route them to the right idle machine, and then to recover it when they're done.  Enter grid engines!
and 
The basic concept is this: **You tell a grid engine what to run, what resources it needs, and it takes care of the rest somewhere out in your cloud* of computers**

*For our purposes, farm and cloud are interchangable: both mean a number of computers that we can't phyically see or touch, but are nice enough to run computational jobs for us.

#### Let's get started - what's going on our farm?

So start a terminal window on your local machine (be it Windows, Mac, or Linux).  Now ssh into your remote machine of choice.  This should be a machine that can launch grid engine jobs.  If you don't know which machines on your network can do this, it's best to ask IT or whoever set up the server farm.  Ok, now my fake terminal window looks like this:

<div class="window"><nav class="control-window">
<a href="#finder" class="close" data-rel="close">close</a><a href="#" class="minimize">minimize</a><a href="#" class="deactivate">deactivate</a>
</nav><h1 class="titleInside">Terminal</h1><div class="terminal">
<table>
	<tr>
		<td class='gutter'><pre class='line-numbers'><span class='line-numbers'>1</span></pre></td>
		<td class='code'><pre><code><span class='line prompt'>$></span><span class='line command'> ssh shead</span><br><span class='line output'>   ...<br>   ...<br>   Last login: Fri Dec 26 18:13:44 2014 from othermachine.gs.washington.edu</span></code></pre></td>
	</tr>
</table>
<table>
	<tr>
		<td class='gutter'><pre class='line-numbers'><span class='line-numbers'>2</span></pre></td>
		<td class='code'><pre><code><span class='line prompt'>$></span><span class='line command'> ssh ganesh</span><br><span class='line output'>   ...<br>   ...<br>   Last login: Fri Dec 26 18:13:44 2014 from othermachine.gs.washington.edu</span></code></pre></td>
	</tr>
</table>
</div></div>

Ok, now we're on a machine we think can launch UGE jobs (in our case above, we've SSH'ed into ganesh, a machine someone's told us can interact with the UGE farm).  How do we verify that this machine connects to the UGE/SGE farm?  UGE comes with a lot of command-line tools to see what's running on the farm, to tell it to dispatch a job, etc.  Here
we'll use the **qstat** command to see *1)* if we can talk to the UGE farm *2)* what's already running on our farm. You should see something like:

<div class="window"><nav class="control-window">
<a href="#finder" class="close" data-rel="close">close</a><a href="#" class="minimize">minimize</a><a href="#" class="deactivate">deactivate</a>
</nav><h1 class="titleInside">Terminal</h1><div class="terminal">
<table>
	<tr>
		<td class='gutter'><pre class='line-numbers'><span class='line-numbers'>1</span></pre></td>
		<td class='code'><pre><code><span class='line prompt'>$></span><span class='line command'> qstat</span><br><span class='line output'>   job-ID     prior   name       user         state submit/start at     queue</span>
<span class='line output'>   --------------------------------------------------------------------------</span>
<span class='line output'>   8103      18.37395 QLOGIN     fakeuser1    r     12/11/2014 16:58:18 ravana.q</span>
<span class='line output'>   8992      17.93886 QLOGIN     fakeuser2    r     12/12/2014 12:31:55 ravana.q</span>
		</code></pre></td>
	</tr>
</table>
</div></div>

If you don't see something like this, either that the command is missing or the server can't connect to the SGE node. If you don;t see output like this it would be worth asking an administrator if you're on the right machine, or if you need to load a specific package or configuration to get SGE working for you.  

Ok, let's pretend you saw something like the above (I clipped a few columns at the end that are less informative).  There's a lot going on, let's break down each of the columns above:

* **job-ID**: a unique identification number is given to each job.
* **prior**:  each jobs priority.  When SGE / UGE looks for the next job to put out on the farm, it'll take the job with the highest priority next.  The priority values are only important relative to each other: when SGE has enough slots, everyone get their job run.  It only matters when SGE has to choose which job get's run next.
* **name**:  the name given to the job.  This is something either you set for each job, or SGE will make one up based on the command line you ran.  If the job name is QLOGIN, it means that the user has opened an interactive session (more later).  
* **user**:  who's running the job or interactive session (their UNIX username, use a command like *finger username* to find out who they really are).
* **state**:  the status of the  job: **d**(eletion),  **E**(rror), **h**(old), **r**(unning), **R**(estarted), **s**(uspended), **S**(uspended), **t**(ransfering), **T**(hreshold) or **w**(aiting).  You'll most commonly see jobs with **r**s and **w**s, the other states tend to only pop-up from time to time.
* **submit**:  the date and time that the job was submitted.
* **queue**:  Which queue it was submitted to.  The jobs above were submitted to the "ravana.q" queue, which is the default in our group.  These queues allow the administrators to controls jobs collectively from a group.  For instance every lab in a department could have their own queue, and labs that put a lot of machines on the farm could get higher priority to the machines over groups that put in a little.  Or groups that generally use a lot of compute could have a lower priority to allow other groups to get things run.  As you can imagine there are many ways to set this up.

You can find a lot more information about qstat by typing *man qstat* on the command line, or finding an online version of the documentation like [this](http://gridscheduler.sourceforge.net/htmlman/htmlman1/qstat.html "qstat man page").  So above we can see that fakeuser1 has a interactive session (QLOGIN) running.  What does this mean?

### Interactive session

So now's a good time to talk about the difference between a normal job and an interactive session: nothing!  An interactive session is just a shorthand way of telling SGE that you'd like it start up a process, the terminal, on one of it's machines.  The only difference is that you then interact with that job (type commands, see responses, etc), whereas a normal job would just execute somewhere are return you the results.  Ok, so let's go get an interactive job and try some things out:


<div class="window"><nav class="control-window">
<a href="#finder" class="close" data-rel="close">close</a><a href="#" class="minimize">minimize</a><a href="#" class="deactivate">deactivate</a>
</nav><h1 class="titleInside">Terminal</h1><div class="terminal">
<table>
	<tr>
		<td class='gutter'><pre class='line-numbers'><span class='line-numbers'>1</span></pre></td>
		<td class='code'><pre><code><span class='line prompt'>$></span><span class='line command'> qlogin</span><br><span class='line output'>   Your job 13133 ("QLOGIN") has been submitted
   waiting for interactive job to be scheduled ...
   Your interactive job 13133 has been successfully scheduled.
   Establishing /usr/local/bin/qlogin_command session to host s019.grid

   Kickstarted on 2013-03-21</span>
		</code></pre></td>
	</tr>
</table>
</div></div>

From the status messages you can see that SGE put our interactive job on the queue, waited for a machine to open up, and dispatched us to the first open machine (in this case s019.grid). After this, you should get a terminal prompt, and be able to type command like you would on any other machine.  This machine is all yours until you give it up!  *Importantly, you should give the machine up as soon as you're done with it*, it's no fair to hog resources.  The whole point of SGE is that it can be managed properly, and it's very poor form to take that away from the administrators.  So when you're done browsing the file system, make sure to type *exit* and return your host back to the pool of available computers.  Ok, interactive sessions seem simple enough, how about launching your own jobs?

### Normal Jobs

More often, you'll want to kick off a job that will run independently on the farm, with no supervision from you.  Just like an interactive sesssion, SGE will find a host for you and start the requested program with the arguments you provide.  The difference is that you won't watch this all happen: SGE takes you command, runs it, releases the machine when the program is done, and stores the command-line output you would of seen for you.  Let's start with something basic.

##### Basic SGE script

So let's say we want to use one of the [Picard](http://broadinstitute.github.io/picard/) tools to convert a bam file we have back to fastq files.  It really doesn't matter what we run, we just need to make it into a script file.  Here's my example shell script file:
<code>
# test_run.sh

java -Xmx4g -jar /path/to/jar/file/SamToFastq.jar I=/path/to/jar/file/mybam.bam F=/path/to/jar/file/fq1.gz F2=/path/to/jar/file/fq2.gz
</code>

It's great to have this in a script because you can test it just using your command line before you send it off to the cloud to be computed.  So after you've made your script, test run it on the command line:

<div class="window"><nav class="control-window">
<a href="#finder" class="close" data-rel="close">close</a><a href="#" class="minimize">minimize</a><a href="#" class="deactivate">deactivate</a>
</nav><h1 class="titleInside">Terminal</h1><div class="terminal">
<table>
	<tr>
		<td class='gutter'><pre class='line-numbers'><span class='line-numbers'>1</span></pre></td>
		<td class='code'><pre><code><span class='line prompt'>$></span><span class='line command'> sh test_script.sh</span><br>
</code></pre></td>
	</tr>
</table>
</div></div>


Many thanks, including:
The terminal tag from [http://jumpstartlab.com](http://jumpstartlab.com/news/archives/2013/10/16/octopress-terminal-tag)

