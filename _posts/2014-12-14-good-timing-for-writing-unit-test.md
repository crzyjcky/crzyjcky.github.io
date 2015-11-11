---
title: Good Timing for Writing Unit Test
author: admin
layout: post
permalink: /2014/12/14/good-timing-for-writing-unit-test/
categories:
  - Test
  - Uncategorized
---
### Good Timing:

  1. Before implementing any new features.
  2. Before fixing any bugs.

### Bad Timing:

  1. After the features has been committed.

Let me explains why writing test code \*AFTER\* the features have been committed and running in production is bad idea. A really bad idea.

I have seen many teams that:  
1. Postponed the unit-test writing to some time later.  
2. Developed the code without unit-testing, and assigned another team member to write unit-test code.

In reality, it doesn&#8217;t work.

If no one complains about the production code, and you unit-test reveals that there could be some errors in the production code, would you want to change the production code to pass the unit test?  
Most likely you don&#8217;t. You are too afraid to change the production code, because you might break something accidentally. If you change the production code, you may also create more tasks for QA, because they have to validate your changes.  
As a result, You will try to &#8216;bend&#8217; the unit tests to pass the production code, which make the test useless.

Besides, we should aim at testing the features / business requirements, rather than testing the class aimlessly. This kind of testing called BDD &#8211; Behavior Driven Development. Another reason we should writing test based on requirements is, given a class A, which contains a lot of obsolete methods, there is no point in testing them. We only test what is matter. Normally this important piece of requirement information is lost when there is no proper documentation. To have that information, we should write the test code while we still have it.

The moment you fix any bugs or implement any new features, this is the good timing for writing unit test, because you have to change the production code anyway, no matter you write unit-test or not.

It is important that writing the test code \*BEFORE\* implementing the bugfix, because from psychology stand point, most people will skip the unit test, after they see their code runs fine.