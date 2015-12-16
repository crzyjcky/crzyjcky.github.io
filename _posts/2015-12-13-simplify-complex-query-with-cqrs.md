---
title: Simplify Complex Query with CQRS
author: Jacky Lai
layout: post
permalink: /2015/12/13/simplify-hierarchical-query-with-cqrs/
categories:
  - Design Patterns
  - Uncategorized
tags:
  - cqrs
---

<iframe src="//www.slideshare.net/slideshow/embed_code/key/8AgbwTCI6aqywg" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/JackyLai7/simplify-complex-query-with-cqrs" title="Simplify Complex Query with CQRS" target="_blank">Simplify Complex Query with CQRS</a> </strong> from <strong><a href="//www.slideshare.net/JackyLai7" target="_blank">Jacky Lai</a></strong> </div>

# Optimization is all about Resource Trade-off

The performance of an application is based on

1. Memory Resource
2. Computing Resource
3. Network Resource
4. Developer Resource
5. **Disk space Resource**

**Disk space resource** is relatively the cheapest resource compared to others.

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

---

# Common Strategy: Back-end Processing

- Upon receiving request payload:

<pre class="brush: java; title: ; notranslate" title="">
// step 1: construct hierarchical object graph, an expensive operation.
product = productRepository.findBy(productId);
shippingAddress = new Address(address);

// step 2: find shipping methods.
shippingMethods = findShippingMethodsBy(product, shippingAddress);
</pre>

---

# Common Strategy: ER Diagram

![common-strategy-er-diagram.png](/wp-content/uploads/2015/12/common-strategy-er-diagram.png)

---

# Common Strategy - Modeling
Model with Hierarchical Data object, e.g. 

- Product
  - size (LARGE, SMALL)
  - type (GIFT_CARD, RESTRICTED) 
  - warehouses
  - address
  - shipping methods

- Shipping Address

---

# Issue #1: Network Traffic Increment.
![network-traffic-increment.png](/wp-content/uploads/2015/12/network-traffic-increment.png)

- For each request, *application layer* has to fetch *huge amount of data* across network from database, and process the data at *Application layer*.

---

# Question: Which is the best layer to filter data?

![which-is-the-best-layer-to-filter-data-browser.png](/wp-content/uploads/2015/12/which-is-the-best-layer-to-filter-data-browser.png)
![which-is-the-best-layer-to-filter-data-database.png](/wp-content/uploads/2015/12/which-is-the-best-layer-to-filter-data-database.png)

---

# Issue #2: Read Speed or Write Speed, Pick One.

- We cannot optimize both Read and Write speed at the same time.
- Without adding index, 
  - time complexity for read = O(n)
- After adding index,
  - time complexity for read = O(log n)

---

# Issue #2: Read Speed or Write Speed, Pick One. – Cont.

- Performance Summary from “The Performance Impact of Adding MySQL Indexes” 
  - [http://logicalread.solarwinds.com/impact-of-adding-mysql-indexes-mc12/#.VmkYP51Viko](http://logicalread.solarwinds.com/impact-of-adding-mysql-indexes-mc12/#.VmkYP51Viko)
- For a table with **553875** rows.

| | Before Adding Indexes | After Adding Indexes |
| - | - | - |
| Insert Operation (sec) | 7.14 | 24.77 (3x) |
| | | |
| Data (mb) | 33.56 | 33.56 |
| Index (mb) | 13.52 | 95.70 |
| Total = Data + Index (mb) | 47.08 | 129.27 |

---

# Issue #2: Read Speed or Write Speed, Pick One. – Cont.

- What if we use Cache to reduce DB read?
- Cache is a Key-Value DB.
- Let’s say it takes 32 DB calls to build a complex object graph:
  - Best case: 32 cache hits.
  - Worst case: 32 cache misses + 32 DB calls.
- Network IO delays is unavoidable.

- There is another challenge: Cache Data Consistency.

> There are only two hard things in Computer Science: cache invalidation and naming things.
> -- Phil Karlton

---

# Issue #2: Read Speed or Write Speed, Pick One. – Cont.

![read-speed-or-write-speed-no-cache.png](/wp-content/uploads/2015/12/read-speed-or-write-speed-no-cache.png)

We need to maintain consistency for both normalized DB and denormalized DB, and this is tricky. 

Overall Consistency = Consistency (Normalized) && Consistency (Denormalized) 

![read-speed-or-write-speed-cache.png](/wp-content/uploads/2015/12/read-speed-or-write-speed-cache.png)

---

# Issue #3: “Join” logic has to be at both sides (W, R)

![join-logic-has-to-be-at-both-sides.png](/wp-content/uploads/2015/12/join-logic-has-to-be-at-both-sides.png)

---

# CQRS Comes to Rescue.

- Proposed by Greg Young.
  - Probably the best innovation from C# community to Java community.
- Command-Query Responsibility Segregation.
  - Command -> Write
  - Query -> Read
- Separate design for Write Operation and Read Operation.
  - For Write, we want consistency.
  - For Read, we want speed.
  
---

# Common Strategy

![common-strategy.png](/wp-content/uploads/2015/12/common-strategy.png)

Read: 
O(log n) if it is indexed correctly, 
O(n) without index.

---

# CQRS

![cqrs-2.png](/wp-content/uploads/2015/12/cqrs-2.png)

---

# Benefit #1: Fast Read

- Simple read. No join operation.
- We can achieve O(1) time complexity by using appropriate database.
- Minimized data transfer – reduced network IO delay.
- Reduced memory requirement – reduced GC.

![benefit-1-fast-read.png](/wp-content/uploads/2015/12/benefit-1-fast-read.png)

---

# Benefit #2: Fast Write

- Less indexes created. 
- Tables (for write operation) are not polluted by Indexes (which are created for read operation).

---

# Benefit #3: Simple Read Logic

- Less convoluted Read-logic.
- Simple logic reduces mistakes.
- It promotes knowledge sharing among team members.
- Shorten development time.

---

# Benefit #4: Flexibility - Different Databases

![benefit-4-flexibility.png](/wp-content/uploads/2015/12/benefit-4-flexibility.png)

---

# Benefit #5: Consistency

![benefit-5-consistency.png](/wp-content/uploads/2015/12/benefit-5-consistency.png)

---

# The End



