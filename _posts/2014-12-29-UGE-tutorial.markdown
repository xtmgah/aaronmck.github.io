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

UGE can seem complicated, but like just about any computer system, you'll need to know about 2% of it to get your routine work done.  I'll try to focus on those 2% here, and mix in some background so that you know where to look for the next step.  Most importantly our goal is that you don't get yelled at for doing something dumb (really everyone's primary goal).  Lastly, some of the information will be specific to the lab I'm working in at the University of Washington, but for anyone else reading this: you can pass over that stuff or fill in the specifics for your setup.

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

<div class="shell-wrap">
  <p class="shell-top-bar">/Your/Local/Dir</p>
  <ul class="shell-body">
    <li>ssh shead<br>   ...<br>   ...<br><a style="color=red">Last login: Fri Dec 26 18:13:44 2014 from othermachine.gs.washington.edu</a>
    </ul>
</div>
	

Ok, now we're on a machine we think can launch UGE jobs (in our case above, we've SSH'ed into ganesh, a machine someone's told us can interact with the UGE farm).  How do we verify that this machine connects to the UGE/SGE farm?  UGE comes with a lot of command-line tools to see what's running on the farm, to tell it to dispatch a job, etc.  Here
we'll use the **qstat** command to see *1)* if we can talk to the UGE farm *2)* what's already running on our farm. You should see something like:

<div class="shell-wrap">
  <p class="shell-top-bar">/Your/Local/Dir</p>
  <ul class="shell-body">
    <li>job-ID     prior   name       user         state submit/start at     queue<br>
<a style="color=red">--------------------------------------------------------------------------<br>
8103      18.37395 QLOGIN     fakeuser1    r     12/11/2014 16:58:18 ravana.q<br>
8992      17.93886 QLOGIN     fakeuser2    r     12/12/2014 12:31:55 ravana.q<br>
		</a>
  </ul>
</div>
	

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


<div class="shell-wrap">
  <p class="shell-top-bar">/Your/Local/Dir</p>
  <ul class="shell-body">
    <li>qlogin<br>
    <a style="color=red">Your job 13133 ("QLOGIN") has been submitted<br>
   waiting for interactive job to be scheduled ...<br>
   Your interactive job 13133 has been successfully scheduled.<br>
   Establishing /usr/local/bin/qlogin_command session to host s019.grid<br>
   Kickstarted on 2013-03-21</span>
 </ul>
</div>
	
From the status messages you can see that SGE put our interactive job on the queue, waited for a machine to open up, and dispatched us to the first open machine (in this case s019.grid). After this, you should get a terminal prompt, and be able to type command like you would on any other machine.  This machine is all yours until you give it up!  *Importantly, you should give the machine up as soon as you're done with it*, it's no fair to hog resources.  The whole point of SGE is that it can be managed properly, and it's very poor form to take that away from the administrators.  So when you're done browsing the file system, make sure to type *exit* and return your host back to the pool of available computers.  Ok, interactive sessions seem simple enough, how about launching your own jobs?

### Normal Jobs

More often, you'll want to kick off a job that will run independently on the farm, with no supervision from you.  Just like an interactive sesssion, SGE will find a host for you and start the requested program with the arguments you provide.  The difference is that you won't watch this all happen: SGE takes you command, runs it, releases the machine when the program is done, and stores the command-line output you would of seen for you.  Let's start with something basic.

##### Before you start

Before you start sending jobs out to the grid engine, it's good to get some default settings in place.  These are things that you don't want to have to specify everytime you run UGE because none of them should change regularly.  These settings get placed in a file in your unix home directory named *.sge_request*.  You should open that file and add at least some of the following lines.  Things you need to fill in are in brackets ( < ).  Ignore all the backticks in the following code, you can see what the whole file would look like below the examples:

Tell UGE what default queue you should dispatch jobs to (for us in the Shendure lab, this should be ravana.q:

	`-q <default.queue.name>`
	
Tell UGE to treat your executables as either scripts or binaries, usually a safe bet to set this to 'y':

	`-b y `
	
Start the job from the current directory (helps when you only specify relative paths like myfiles/thefileIwant.txt):

	`-cwd `
	
be verbose, and dump out a lot of information on runs:

	`-V `
	
merge the error output in with the normal output of a job. Generally as a newbie you want this set to yes (y) so you see all of your jobs output together:

	`-j y `
	
what conditions do you want to recieve an email for a = when the job is aborted or rescheduled, and e = send email at end of the job (b = beginning is the alternative):

	`-m ae `
	
this is a lab specific thing, we only want redhat 6 machines by default:

	`-l rhel=6` 
	
where to send the emails to:

	`-M USERID@uw.edu `
	
where to put your output for both normal runs and errors:

	-o /net/shendure/vol1/home/USERID/sge_logs/ 
	-e /net/shendure/vol1/home/USERID/sge_logs/`

together this looks like:

	-q ravana.q  
	-b y 
	-cwd 
	-V 
	-j y 
	-m ae 
	-l rhel=6 
	-M USERID@uw.edu 
	-o /net/shendure/vol1/home/USERID/sge_logs/ 
	-e /net/shendure/vol1/home/USERID/sge_logs/
	
Saved in the *.sge_request* file.  There are pleanty of other options you could request, you can find out more by running *man qsub* on your command line.

##### Basic UGE script

So let's say we want to use one of the [Picard](http://broadinstitute.github.io/picard/) tools to convert a bam file we have back to fastq files.  It really doesn't matter what we run, we just need to make it into a script file.  Here's my example shell script file:

	# test_run.sh

	java -Xmx4g \
	-jar /path/to/jar/file/SamToFastq.jar \
	I=/path/to/jar/file/mybam.bam \
	F=/path/to/jar/file/fq1.gz \
	F2=/path/to/jar/file/fq2.gz

It's great to have this in a script because you can test it just using your command line before you send it off to the cloud to be computed.  So after you've made your script, give it a test run on the command line:

<div class="shell-wrap">
  <p class="shell-top-bar">/Your/Local/Dir</p>
  <ul class="shell-body">
    <li>sh test_script.sh
    <br><br>
    <a style="color=red">your output here</a></li>
  </ul>
</div>

This lets you see that the script runs without errors.  The grid engine will also report back any errors, it just takes more time to debug as you have to wait for the program to get dispatched to a node, for it to fail, and UGE to return the error messages to you.

##### Running the basic script on UGE

Ok, now we need to actually run it using the grid engine.  You dispatch jobs using the *qsub* command. You use this command as follows:

<div class="shell-wrap">
  <p class="shell-top-bar">/Your/Local/Dir</p>
  <ul class="shell-body">
    <li>qsub -shell y -l virtual_free=4g,mfree=4g test_script.sh
<br><br>
    <a style="color=red">Your job 19221 ("test_script.sh") has been submitted</a></li>
  </ul>
</div>

Our job is now out there, we should be able to see it using qstat:

<div class="shell-wrap">
  <p class="shell-top-bar">/Your/Local/Dir</p>
  <ul class="shell-body">
    <li>qstat
<br><br>
    <a style="color=red">job-ID     prior   name       user         ....<br>
-----------------------------------------------------<br>
     19221 0.00000 test_scrip aaronmck     r    02/16/2015 12:09:07</a></li>
  </ul>
</div>
	
Again, the `r` mean it's running.  Congrats your very first UGE jobs is out there running!
Many thanks, including:
The terminal tag from [http://jumpstartlab.com](http://jumpstartlab.com/news/archives/2013/10/16/octopress-terminal-tag)

