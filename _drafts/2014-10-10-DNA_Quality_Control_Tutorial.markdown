---
layout: post
title:  "How to (initially) QC sequencing data"
date:   2014-05-19 14:56:10
categories: sequencing quality control
---

There are some great guides to QC'ing data already out there:
![CureFFI fastq data](http://www.cureffi.org/2012/08/27/fastqc-for-large-numbers-of-samples/)
![seqanswers - how to QC exome data](http://seqanswers.com/wiki/How-to/exome_analysis)

Our goal here is to quality control sequencing data. Everything downstream generally depends on removing bad reads, 
poor quality regions, sequencing adapters, and other abnormalities that will ruin our analysis.  There are core steps
that you'll do for all projects, and then additional add-on steps that depend on the specific nature of your analysis. 
You'll want to do different finishing steps for an exome sequencing of tumor samples vrs DMS RNA-Seq reads.

The goal of this mini tutorial is to walk through the common QC steps.  There are a billion flavors of QC out there, 
and many tools which are outdated or simply written to generate a paper and then abandoned.  Also many genomic tools
are an absolute nightmare to install and get running; here we'll avoid anything like that (I hope).  Lastly, and 
probably most importantly, this will focus on Illumina reads.  It might work for other sequencing systems, but some
of the nuances here won't apply. You've been warned.

The basics

To be clear, whatever your library started as, you're sequecing DNA.  Altough RNA-Seq measures mRNA reads, what goes onto
the flowcell is the cDNA of your RNA.  To sequence DNA on the Illumina platform you need to attach adapters to the ends
of your fragments (where ever those fragments come from).  Here's the general layout:

