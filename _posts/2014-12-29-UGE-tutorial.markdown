---
layout: post
title:  "UGE - life on the farm"
date:   2014-12-29 14:56:10
categories: farm compute UGE grid engine
---
Alright, here goes my attempt at a lightweight tutorial on using Sun Grid Engine (SGE), or it's equivilents such as UGE (Univa) or OGE (Sun -> Oracle).  There are a lot of great resources out there for so many other things, but there doesn't seem to be a lot of basic tutorials or [an ELI5](http://www.reddit.com/r/explainlikeimfive/ "ELI5 - reddit") write-ups for SGE.  This is too bad, as SGE and it's friends are pretty widely used, and it seems like there's a lot of confusion.

Who Cares?
-------------
You.  Ok, more specificly, almost anyone who works in a computing environment.  It used to be that SGE (and related tools like LSF) were the domain of system adminstators and backend programmers, but this changed pretty rapidly (this is a good thing, you get access to new resources).  Now most academic labs have access to large clusters, and the expectation is that you'll use these resources fairly and appropriately.  Great! Except, that mean you have to learn how to do that, and that's the motivation behind the 'who cares' part: you, hopefully.  

What are we going to cover?
------------------------
UGE can seem complicated, but like just about any computer system, you'll need to know about 2% of it to get your routine work done.  I'll try to focus on those 2% here, and mix in some background so that you know where to look for the next step, and most importantly you don't get yelled at for doing something dumb (really everyone's primary goal).  Lastly, some of the information will be specific to the lab I'm working in at the University of Washington, but for anyone else reading this: you can pass over that stuff or fill in the specifics of your setup.

What are we not going to cover?
------------------------
This is intended for people who want to *use* SGE/UGE and it won't cover things like setting up a cluster, or anything else that's super complicated.  Also, I can't really cover the basics of connnecting to your machine.  You should be confortable opening a terminal and SSH'ing to a remote machine.  Here at UW we have some pretty [aweful documentation](http://www.washington.edu/itconnect/connect/web-publishing/shared-hosting/ssh/ "ssh - UW") on how to do this, but it seems like there aren't really many great options out there.  [This tutorial](https://help.ubuntu.com/community/UsingTheTerminal "Ubuntu Terminal") from Ubuntu is a good start.  

Basics
------------------------
Why do we need SGE/UGE? Say your the IT person for a large department at a university. Everyone wants to run their analysis, preferably yesterday, and you need to buy a lot of 'compute' for them to run it on.  How do you manage this?  Back in the day everyone got a nice fast desktop, and everyone was happy.  Now people need lots of memory for some jobs, tons of processesors for other jobs, and tons of disk storage for others.  You could just by an *enourmous* machine with lots of everything, but this is insanely expensive, and graduate students are prone to breaking even the most hardy of machine (ruining everyone else's work in the process).  

So you decide to buy a lot of cheap machines, some with lots of memory, some with lots of processessors, and stick them on the network.  Problem solved!  But everyone complains because they don't know which machine to use, the machine they like is getting beaten up by 10 grad students because they all followed the same tutorial, and you have no way of knowing if a machine is up and running or on fire in the back closet.  Booo.  You need some way of managing this: a way for people to specify what they need, and a system to route them to the right idle machine, and recover it when they're done.  Enter Grid Engines!

The basic concept is this: **You tell a grid engine what to run, and what resources it takes to run it, and it takes care of the rest somewhere out in your local or global "cloud" of computers**

Let's get started
------------------------
So start a terminal window on your local machine (be it Windows, Mac, or Linux).  Mine looks like this:

Now login to your remote machine of choice.  This should be a machine that can launch grid engine jobs.  If you don't know what machine's on your network can do this, it's best to ask IT or whoever setup the server farm.  Ok, now I have:

<div class="window">
          <nav class="control-window">
            <a href="#finder" class="close" data-rel="close">close</a>
            <a href="#" class="minimize">minimize</a>
            <a href="#" class="deactivate">deactivate</a>
          </nav>
          <h1 class="titleInside">Terminal</h1>
          <div class="container"><div class="terminal">
<table><tr>
<td class='gutter'><pre class='line-numbers'><span class='line-number'>1</span></pre></td>
<td class='code'><pre><code><span class='line output'>test output line</span></code></pre></td>
</tr></table>
</div></div>
        </div>

Many thanks, including:
The terminal tag from [http://jumpstartlab.com](http://jumpstartlab.com/news/archives/2013/10/16/octopress-terminal-tag)

