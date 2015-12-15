---
title: Simplify Hierarchical Query with CQRS
author: Jacky Lai
layout: post
permalink: /2015/12/13/simplify-hierarchical-query-with-cqrs/
categories:
  - Design Patterns
  - Uncategorized
tags:
  - cqrs
---
<link rel="stylesheet" type="text/css" href="https://gist.githubusercontent.com/andyferra/2554919/raw/10ce87fe71b23216e3075d5648b8b9e56f7758e1/github.css">

# Optimization is about Resource Trade-off

The performance of an application is based on

1. Memory Resource
2. Computing Resource
3. Network Resource
4. Developer Resource
5. *Disk space Resource*

Disk space resource is relatively the cheapest resource compared to others.

---

# Example Requirement: Find Shipping Methods

- During checkout process, user will be presented a list of shipping methods to choose from, based on the product and shipping address.
- shippingMethods = findShippingMethodsBy(product, shippingAddress);

---


# Example Requirement: Request Payload

Request Json Payload:

<pre class="brush: javascript; title: ; notranslate" title="">
{
	"productId": ”aabbcc",
	"address": ”123 Freedom Cir., Santa Clara, CA 95123"
}
</pre>

# Common Strategy: Back-end Processing

- Upon receiving request payload:

<pre class="brush: java; title: ; notranslate" title="">
// step 1: construct hierarchical object graph, an expensive operation.
product = productRepository.findBy(productId);
shippingAddress = new Address(address);

// step 2: find shipping methods.
shippingMethods = findShippingMethodsBy(product, shippingAddress);
</pre>

# Common Strategy: ER Diagram

![common-strategy-er-diagram.png](/wp-content/uploads/2015/12/common-strategy-er-diagram.png)

# Common Strategy - Modeling
Model with Hierarchical Data object, e.g. 

- Product
  - size (LARGE, SMALL)
  - type (GIFT_CARD, RESTRICTED) 
  - warehouses
  - address
  - shipping methods

- Shipping Address

# Issue #1: Network Traffic Increment.
![network-traffic-increment.png](/wp-content/uploads/2015/12/network-traffic-increment.png)

- For each request, *application layer* has to fetch *huge amount of data* across network from database, and process the data at *Application layer*.

# Question: Which is the best layer to filter data?

![which-is-the-best-layer-to-filter-data-browser.png](/wp-content/uploads/2015/12/which-is-the-best-layer-to-filter-data-browser.png)
![which-is-the-best-layer-to-filter-data-database.png](/wp-content/uploads/2015/12/which-is-the-best-layer-to-filter-data-database.png)

# Issue #2: Read Speed or Write Speed, Pick One.

- We cannot optimize both Read and Write speed at the same time.
- Without adding index, 
  - time complexity for read = O(n)
- After adding index,
  - time complexity for read = O(log n)

# Issue #2: Read Speed or Write Speed, Pick One. – Cont.

- Performance Summary from “The Performance Impact of Adding MySQL Indexes” 
  - [http://logicalread.solarwinds.com/impact-of-adding-mysql-indexes-mc12/#.VmkYP51Viko](http://logicalread.solarwinds.com/impact-of-adding-mysql-indexes-mc12/#.VmkYP51Viko)
- For a table with *553875* rows.

https://gist.githubusercontent.com/andyferra/2554919/raw/10ce87fe71b23216e3075d5648b8b9e56f7758e1/github.css

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
