---
title: How Hadoop Speed Up Data Processing?
author: admin
layout: post
permalink: /2013/09/09/how-hadoop-speed-up-data-processing/
categories:
  - Hadoop
  - Uncategorized
---
Hadoop speed up data processing in two ways:

# A. Moving Computation Program to Data Node

  1. Traditional Method: Moving Data to Computation Node
<div id="attachment_281" style="width: 469px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/?attachment_id=281"><img class="size-full wp-image-281 " alt="traditional-data-transfer" src="http://crzyjcky.com/wp-content/uploads/2013/09/traditional-data-transfer.png" width="459" height="99" /></a>
  
  <p class="wp-caption-text">
    Traditional Method: Moving Data to Computation Node
  </p>
</div>

  2. Hadoop Method: Moving Computation Program to Data Node
<div id="attachment_282" style="width: 469px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/09/09/how-hadoop-speed-up-data-processing/hadoop-program-transfer/" rel="attachment wp-att-282"><img class="size-full wp-image-282" alt="hadoop-program-transfer" src="http://crzyjcky.com/wp-content/uploads/2013/09/hadoop-program-transfer.png" width="459" height="99" /></a>
  
  <p class="wp-caption-text">
    Hadoop Method: Moving Computation Program to Data Node
  </p>
</div></ol> 

The size of data is a lot bigger than the size of program. Instead of transferring data from data node, Hadoop distributes computation program to data node. This saves a lot of network bandwidth and data transferring time.

# B. Parallel Processing</h2> 

To enable parallel processing, 

  1. Data is split into several fragments, and is distributed to several data nodes.
  2. Data processing can be run in parallel on each fragment.
  3. Result will be combined.

<a href="http://crzyjcky.com/2013/09/09/how-hadoop-speed-up-data-processing/parallel-computation/" rel="attachment wp-att-283"><img class="alignnone size-full wp-image-283" alt="parallel-computation" src="http://crzyjcky.com/wp-content/uploads/2013/09/parallel-computation.png" width="482" height="399" /></a>