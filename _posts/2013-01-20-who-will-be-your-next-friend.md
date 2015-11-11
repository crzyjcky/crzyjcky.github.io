---
title: Who Will Be Your Next Friend?
author: admin
layout: post
permalink: /2013/01/20/who-will-be-your-next-friend/
categories:
  - Neo4j
  - NoSQL
  - Social Science
tags:
  - neo4j
  - nosql
  - social-science
---
# Triadic Closure

Given 3 people &#8211; A, B and C, If A and B are friends, B and C are friends, there is high possibility that A and C are friends, or will be friends. If each person can be represented as a node, 3 nodes form a **Triadic Closure**.

<div id="attachment_178" style="width: 383px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/01/20/who-will-be-your-next-friend/triadic-closure/" rel="attachment wp-att-178"><img src="http://crzyjcky.com/wp-content/uploads/2013/01/triadic-closure.png" alt="Triadic Closure" width="373" height="613" class="size-full wp-image-178" /></a>
  
  <p class="wp-caption-text">
    Triadic Closure
  </p>
</div>

# Example</h2> 

<div id="attachment_175" style="width: 551px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/01/20/who-will-be-your-next-friend/character-relationship-chart/" rel="attachment wp-att-175"><img src="http://crzyjcky.com/wp-content/uploads/2013/01/character-relationship-chart.png" alt="Character Relationship Chart" width="541" height="373" class="size-full wp-image-175" /></a>
  
  <p class="wp-caption-text">
    Character Relationship Chart
  </p>
</div>

Given a character relationship chart, we want to find out who Ken will be friend with next. We can use Neo4j for this analysis.

First We store the relationship into Neo4j using Cypher language.

<pre class="brush: java; title: ; notranslate" title="">CREATE gouken = {id:1, name:"Gouken"}, 
chunLi = {id:2, name:"Chun Li"},
ken = {id:3, name:"Ken"},
ryu = {id:4, name:"Ryu"},
eHonda = {id:5, name:"E. Honda"},
guile = {id:6, name:"Guile"};

START n1 = node:node_auto_index(name = "Gouken"), n2 = node:node_auto_index(name = "Ryu")
CREATE (n1)-[:FRIEND]-(n2);

START n1 = node:node_auto_index(name = "Gouken"), n2 = node:node_auto_index(name = "Ken")
CREATE (n1)-[:FRIEND]-(n2);

START n1 = node:node_auto_index(name = "Ryu"), n2 = node:node_auto_index(name = "Ken")
CREATE (n1)-[:FRIEND]-(n2);

START n1 = node:node_auto_index(name = "Ryu"), n2 = node:node_auto_index(name = "Chun Li")
CREATE (n1)-[:FRIEND]-(n2);

START n1 = node:node_auto_index(name = "Ryu"), n2 = node:node_auto_index(name = "E. Honda")
CREATE (n1)-[:FRIEND]-(n2);

START n1 = node:node_auto_index(name = "E. Honda"), n2 = node:node_auto_index(name = "Guile")
CREATE (n1)-[:FRIEND]-(n2);

START n1 = node:node_auto_index(name = "Ken"), n2 = node:node_auto_index(name = "Guile")
CREATE (n1)-[:FRIEND]-(n2);
</pre>

<div id="attachment_176" style="width: 551px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/01/20/who-will-be-your-next-friend/character-relationship-chart-link/" rel="attachment wp-att-176"><img src="http://crzyjcky.com/wp-content/uploads/2013/01/character-relationship-chart-link.png" alt="Character Relationship Chart with Possible Link" width="541" height="373" class="size-full wp-image-176" /></a>
  
  <p class="wp-caption-text">
    Character Relationship Chart with Possible Link
  </p>
</div>

  
To find out who will be Ken&#8217;s next friend, we can run a query to find all incomplete Triadic Closures:

<pre class="brush: java; title: ; notranslate" title="">START ken = node:node_auto_index(name = "Ken")
MATCH (ken)-[:FRIEND]-(commonFriend)-[:FRIEND]-(stranger)-[rel?:FRIEND]-(ken)
WHERE rel is null 
RETURN distinct ken.name, stranger.name;
</pre>

Result:

<pre class="brush: plain; title: ; notranslate" title="">==&gt; +--------------------------+
==&gt; | ken.name | stranger.name |
==&gt; +--------------------------+
==&gt; | "Ken"    | "E. Honda"    |
==&gt; | "Ken"    | "Chun Li"     |
==&gt; +--------------------------+
</pre>

From the result, we know that Ken has high probability to be friend with E. Honda and Chun Li, because they both have common friends. 

The more common friends they share, the higher chance they will be friend eventually. To find out who will have higher chances to be friend with Ken, we can run a query to find out the number of common friends they share with Ken:

<pre class="brush: java; title: ; notranslate" title="">START ken = node:node_auto_index(name = "Ken")
MATCH (ken)-[:FRIEND]-(commonFriend)-[:FRIEND]-(stranger)-[rel?:FRIEND]-(ken)
WHERE rel is null 
RETURN distinct ken.name, stranger.name, count(commonFriend);
</pre>

Result:

<pre class="brush: plain; title: ; notranslate" title="">==&gt; +------------------------------------------------+
==&gt; | ken.name | stranger.name | count(commonFriend) |
==&gt; +------------------------------------------------+
==&gt; | "Ken"    | "E. Honda"    | 2                   |
==&gt; | "Ken"    | "Chun Li"     | 1                   |
==&gt; +------------------------------------------------+
</pre>

Ken and E. Honda&#8217;s common friends are Ryu and Guile, whereas with Chun Li, their common friend is Ryu only. From the result, we know that Ken will have higher chance to be friend with E. Honda.  
<SCRIPT charset="utf-8" type="text/javascript" src="http://ws.amazon.com/widgets/q?rt=tf_mfw&#038;ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822/US/crzyjcky-20/8001/72378f6e-c27a-4048-a804-1706d410e0a9"> </SCRIPT> 

<NOSCRIPT>
  <A HREF="http://ws.amazon.com/widgets/q?rt=tf_mfw&#038;ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822%2FUS%2Fcrzyjcky-20%2F8001%2F72378f6e-c27a-4048-a804-1706d410e0a9&#038;Operation=NoScript">Amazon.com Widgets</A>
</NOSCRIPT>