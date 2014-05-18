---
layout: post
title:  "Best tool for the job"
date:   2013-08-08 14:56:10
categories: tools git scala
---
I know it’s the most cliché statement, but it’s so important to pick the best tool for the job.  I’ve been writing a large scale simulation of a cell lineage in Python, and have totally hit the language’s limits (both in terms of memory and CPU).  Apparently 30-40 cell generations is just too much for Python to push forward.

Yes, I could write some cython mashup and gain both a speed and control bump.  The problem going down that route is that you have no idea what python is doing under the covers.  You have to google every use of a dict or string concatenation to try and figure out where you’re getting hammered (even with profiling), and then rewrite the (possibly) expensive parts in C.

So I switched the whole thing over to Scala.  The rewrite took a couple of hours, but it was mostly copy, paste, and minor correction.  It’s amazing how close the languages are semantically now, most of the edits were to convert to the java standard camel-case from python under_line.  Anyway, the speed-up was great, and having control of specific implementations of map or list was really helpful for optimizations.  Great!  But not the point.

What really blew my socks off though was [SBT](http://www.scala-sbt.org/) (simple build tool).  Coming from the world of java + ant builds a couple of years ago, this was a major revolution. No writing of a 100+ line xml file for a simple build? Count me in!  It just worked, and it worked well.  It’s amazing how far tools have come in the last couple of years, be it SBT, git and mercurial, Intellij, or homebrew for Mac (the last can’t be overstated, which installed SBT on my machine with a simple ‘brew install sbt’).  Thanks to all the great software engineers out there, you’re giving hours back to the rest of us!