---
title: Relationship among RDMS, NoSQL and MapReduce
author: admin
layout: post
permalink: /2013/01/02/relationship-among-rdms-nosql-and-mapreduce-3/
categories:
  - Big Data
  - Hadoop
  - NoSQL
tags:
  - mapreduce
  - nosql
  - rdms
---
<div id="attachment_49" style="width: 730px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/01/02/relationship-among-rdms-nosql-and-mapreduce/rdms-nosql-mapreduce/" rel="attachment wp-att-49"><img class="size-full wp-image-49" alt="Relationship among RDMS, NoSQL and MapReduce" src="http://crzyjcky.com/wp-content/uploads/2013/01/rdms-nosql-mapreduce.png" width="720" height="469" /></a>
  
  <p class="wp-caption-text">
    Relationship among RDMS, NoSQL and MapReduce
  </p>
</div>

# For a lot of data

RDMS (PostgreSQL, MySQL etc.) can handles storing / retrieval of large data set very well. It also provides basic aggregate operations on the data set to give a summarize reports to the users.

# For a lot of &#8220;a lot of data&#8221;:

When the size of data set increase until RDMS can&#8217;t handle it efficiently, we can use NoSQL cluster to store data. In general, most NoSQL store data together in Denormalizated form (very much like OLAP in RDMS). What it does is basically &#8220;pre-joined&#8221; the different piece of data, thus reducing expensive JOIN operation during run-time and make the reading super fast. However, If users want to do different ways of JOIN operations, or other aggregate functions, the operation become very expensive, because the data store has to run JOIN operation. Worse still, since most NoSQL do not support JOIN natively, this operation will tend to be slower than its RDMS counterpart. One solution is that we could make use of MapReduce function. MapReduce is a programming paradigm that break the huge data set into smaller piece (so it is consumable) and allows data processing in number of nodes in parallel manner. MapReduce functionality can be found in Hadoop. A lot of NoSQL datastores, like MongoDB or Riak, provide certain level of native MapReduce implementation too.

<SCRIPT charset="utf-8" type="text/javascript" src="http://ws.amazon.com/widgets/q?rt=tf_mfw&#038;ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822/US/crzyjcky-20/8001/efbdc3bc-fccb-41af-a5b2-371059332e11"> </SCRIPT> 

<NOSCRIPT>
  <A HREF="http://ws.amazon.com/widgets/q?rt=tf_mfw&#038;ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822%2FUS%2Fcrzyjcky-20%2F8001%2Fefbdc3bc-fccb-41af-a5b2-371059332e11&#038;Operation=NoScript">Amazon.com Widgets</A>
</NOSCRIPT>