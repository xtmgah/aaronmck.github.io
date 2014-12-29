---
layout: post
title:  "UGE - Running jobs on the farm"
date:   2014-05-19 14:56:10
categories: farm compute UGE grid engine
---
Alright, here goes my attempt at a lightweight tutorial on using Sun Grid Engine equivilents such as UGE (Univa) or OGE (Sun became Oracle).  There are a lot of great resources out there, but there doesn't seem to be a lot of basic tutorials or [an ELI5](http://www.reddit.com/r/explainlikeimfive/ "ELI5 - reddit") write-ups.  This is too bad, as SGE and it's friends are becoming widely used.

Who Cares?
-------------
Almost anyone these days who works in a computing evironment.  It used to be that SGE (and related tools like LSF) were the domain of system adminstators and backend programmers, but this is all changing (this is a good thing).  Now most academic labs have access to large clusters, and the expectation is that you'll use these resources fairly and appropriately.  Great! Except, that mean you have to learn how to do that, and that's the motivation behind the 'who cares' part: you.  

What are we going to cover?
------------------------
UGE can seem complicated, but like just about any computer system, you'll need to know about 2% of it to get your standard work done.  I'll try to focus on those 2% here, and mix in some background so that you know where to look for the next step and don't get yelled at for doing something dumb (really everyone's primary goal).  
