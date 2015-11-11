---
title: JavaScript OOP
author: admin
layout: post
permalink: /2013/02/17/javascript-oop/
categories:
  - Design Patterns
  - JavaScript
  - Uncategorized
---
# The need of OOP

## Is Convention Technique enough?

Both techniques have their merits in different types of web application development. Conventional technique allows developer to jump into coding right away, thus has a faster turnaround for small development. OOP technique, if done properly, can localize a testing area, meaning we only have to test the modified part, without testing the whole application. This will be a time-saving and bug reduction technique in a long run.

Conventional technique &#8211; for small scale web application development:

  * Pro: Easy to start to write. No/less initial planning.
  * Con: Any changes in requirements involve re-test the whole application.

OOP (Large-scale) technique &#8211;

  * Pro: Localized the need for test if changes happened.
  * Con: Steep learning curve.

<div id="attachment_205" style="width: 810px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/02/17/javascript-oop/localized-test/" rel="attachment wp-att-205"><img class="size-full wp-image-205" alt="localized-testing" src="http://crzyjcky.com/wp-content/uploads/2013/02/localized-test.png" width="800" height="600" /></a>
  
  <p class="wp-caption-text">
    localized-testing
  </p>
</div>

## Leverage the time-proven knowledge of OOP

One of the efforts that the industry has taken on is by leveraging OOP concept which has been proven to build large scale application successfully. OOP is by far the most successful design process to date.  
We have a lot of knowledge/research built upon OOP concept, like

  * Design Patterns
  * Domain-Driven Design
  * Model-Driven Design
  * UML
  * Use-Case Driven Design

## Attract more developers

Apart from this, we also have a large user base of Java Developer. Java Developer can easily up to speed by designing JavaScript application in OOP way.

# The problem of adopting OOP

General speaking, JavaScript is an object-oriented language. The main difference between JavaScript and other OOP language (such as Java) is that JavaScript does not support Class construct and Interface. This is the main hurdle for adopting techniques which built on top of other OOP language.  
Various techniques have been developed in order to emulate the behavior of Class construct (by taking advantage of Closure feature, which is unique feature to JavaScript). However, the various techniques confuses a lot of beginner (imagine that if we have many ways to create a class in Java, it will introduce unnecessary complexity in learning the language).

# Solution

In order to leverage current OOP design techniques, we have to be able to construct a class. This section is about how to construct a class. Bear in mind that there are a lot of different ways of constructing a “class”, I will only introduce one way of doing so in this article. Trying to cover all the possible ways to construct a class, will only confuse you. All the variants implementation is leveraging the concept of JavaScript closure.

## Constructing a class

<table>
  <tr>
    <td>
      Java
    </td>
    
    <td>
      JavaScript
    </td>
  </tr>
  
  <tr>
    <td>
      <pre class="brush: java; title: ; notranslate" title="">
public class Human
{
 public String name = "John";
 private int age = 20;

 public int getAge()
 {
 	return age;
 }

 public String getName()
 {
 	return name;
 }

 public void sayHello()
 {
 	say("Hello");
 }

 private void say(String sentence)
 {
 	System.out.println(sentence);
 }
}

Human human = new Human();
</pre>
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">
// Human class declaration
var Human = function() {
	// public variable
	this.name = "John";
	// private variable
	var age = 20;

	// public method
	this.getAge = function() {
		return age;
	}

	// public method, uses public variable
	this.getName = function() {
		return this.name;
	}

	// public method invokes private method
	this.sayHello = function() {
		say("hello");
	}

	// private method
	var say = function(sentence) {
		console.log(sentence); // or alert(sentence);
	}
}

// instantiate Human class
var human = new Human();
</pre>
    </td>
  </tr>
</table>

As you can see, the conversion from Java class to JavaScript class is almost an one-to-one mapping. The rules can be summarized as follows:

<table>
  <tr>
    <td>
      1
    </td>
    
    <td>
      Class Delaration
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">var &lt;className&gt; = function(){}</pre>
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      Public variable
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">this.&lt;variableName&gt;</pre>
    </td>
  </tr>
  
  <tr>
    <td>
      3
    </td>
    
    <td>
      Private variable
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">var &lt;variableName&gt;</pre>
    </td>
  </tr>
  
  <tr>
    <td>
      4
    </td>
    
    <td>
      Public function
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">this.&lt;functionName&gt; = function() {}</pre>
    </td>
  </tr>
  
  <tr>
    <td>
      5
    </td>
    
    <td>
      Private function
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">var &lt;functionName&gt; - function() {}</pre>
    </td>
  </tr>
  
  <tr>
    <td>
      6
    </td>
    
    <td>
      Instantiation
    </td>
    
    <td>
      <pre class="brush: jscript; title: ; notranslate" title="">var &lt;instanceName&gt; = new &lt;className&gt;();</pre>
    </td>
  </tr>
</table></ol> 

# Leverage the Power of Design Patterns

I will show you how to implement Observer Pattern in JavaScript class. A broadcaster generally contains 4 items:

  1. Array of listeners
  2. Add listener operation
  3. Remove listener operation
  4. Broadcast event

A listener need to implements an event handler which matches the event broadcasted.

<pre class="brush: jscript; title: ; notranslate" title="">// Broadcaster.js

var Broadcaster = function() {
	// 1. listener array
	var listeners = [];

	// 2. add listener
	this.addListener = function(listener) {
		listeners.push(listener);
	}

	// 3. remove listener
	this.removeListener = function(listener) {
		var i = listeners.length;
		while (i--) {
			if (listners[i] === listener) {
				listeners.splice(i, 1);
			}
		}
	}

	// 4. broadcast event
	var broadcast = function(event) {
		var len = listeners.length;
		for ( var i = 0; i &lt; len; i++) {
			listeners[i][event]();
		}
	}

	this.press = function() {
		// 5. broadcast event, which listeners need to provide corresponding handler to handle it
		broadcast("onPressed");
	}
}
</pre>

<pre class="brush: jscript; title: ; notranslate" title="">// Listener.js

var Listener = function() {
	// the onPressed method name must match the broadcasted event "onPressed"
	this.onPressed = function() {
		alert("this is one");
	}
}
</pre>

## How about Interface?

If we studied GOF design patterns carefully, we will notice that most design patterns rely on a language construct, called “Interface”. This follows the main principle – “Program to an &#8216;interface&#8217;, not an &#8216;implementation&#8217;.”  
The way other OOP languages enforces the interface validation rely on compiler to flag error during compilation. However JavaScript is a script which does not require compilation, so there is no way to enforce it during compilation. However if an error is detected during run-time, it is too late already.  
We have two solutions:

  1. Discipline ourselves to implement all the required methods (which I am using this strategy in this article). Or 
  2. Use third party JavaScript compiler to compile the JavaScript. Developers need to add an special annotation tag to indicate which interface it is implementing, and the compiler will validate to ensure all the operations have been implemented. An example of this tool is Google Closure Compiler.</p> 
    Example of annotated JavaScript class with Google Closure Library annotation:
    
    <pre class="brush: jscript; title: ; notranslate" title="">/**
 * A shape.
 * @interface
 */
function Shape() {};
Shape.prototype.draw = function() {};

/**
 * @constructor
 * @implements {Shape}
 */
function Square() {};
Square.prototype.draw = function() {
  ...
};
</pre>

# Demo

To illustrate the power of OOP/design patterns in JavaScript, I created a simple “Online Movie Ticket System”. I choose to build this ticket system (which is quite common, in my opinion), because the main idea of design pattern is to “provide a solution to common problems”.  
This application is constructed based on MVC pattern. We have one Model class as a broadcaster, and three listeners, which are SeatMapView, SelectedSeatView, and PriceCalculatorView. Every time the Model change its state, it will notify (broadcast) “onChange” event to all its listeners. The listeners will then respond to the event by its own way (according to its implementation).

## Mockup

<a href="http://crzyjcky.com/2013/02/17/javascript-oop/mockup/" rel="attachment wp-att-241"><img src="http://crzyjcky.com/wp-content/uploads/2013/02/mockup.png" alt="mockup" width="837" height="403" class="alignnone size-full wp-image-241" /></a>

## Class Diagram

<div id="attachment_242" style="width: 583px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/02/17/javascript-oop/demo-design/" rel="attachment wp-att-242"><img src="http://crzyjcky.com/wp-content/uploads/2013/02/demo-design.png" alt="demo-class-diagram" width="573" height="270" class="size-full wp-image-242" /></a>
  
  <p class="wp-caption-text">
    demo-class-diagram
  </p>
</div>

## Screenshot

<div id="attachment_244" style="width: 722px" class="wp-caption alignnone">
  <a href="http://crzyjcky.com/2013/02/17/javascript-oop/screenshot/" rel="attachment wp-att-244"><img src="http://crzyjcky.com/wp-content/uploads/2013/02/screenshot.png" alt="demo-screenshot" width="712" height="498" class="size-full wp-image-244" /></a>
  
  <p class="wp-caption-text">
    demo-screenshot
  </p>
</div>

## Code

index.html

<pre class="brush: xml; title: ; notranslate" title="">&lt;html&gt;
&lt;body&gt;
  &lt;h1&gt;Sunflower Online Movie Ticket System&lt;/h1&gt;
  &lt;table&gt;
    &lt;tr&gt;
      &lt;td width="80%" valign="top"&gt;
        &lt;h2&gt;Seat Map&lt;/h2&gt;
        &lt;table id="seatMap" width="100%" border="1" cellspacing="0" cellpadding="0"&gt;
          &lt;tr&gt;
            &lt;td&gt;&nbsp;&lt;/td&gt;&lt;td align="center"&gt;1&lt;/td&gt;&lt;td align="center"&gt;2&lt;/td&gt;&lt;td align="center"&gt;3&lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td align="center"&gt;A&lt;/td&gt;&lt;td id="A1"&gt;&nbsp;&lt;/td&gt;&lt;td id="A2"&gt;&nbsp;&lt;/td&gt;&lt;td id="A3"&gt;&nbsp;&lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td align="center"&gt;B&lt;/td&gt;&lt;td id="B1"&gt;&nbsp;&lt;/td&gt;&lt;td id="B2"&gt;&nbsp;&lt;/td&gt;&lt;td id="B3"&gt;&nbsp;&lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td align="center"&gt;C&lt;/td&gt;&lt;td id="C1"&gt;&nbsp;&lt;/td&gt;&lt;td id="C2"&gt;&nbsp;&lt;/td&gt;&lt;td id="C3"&gt;&nbsp;&lt;/td&gt;
          &lt;/tr&gt;
        &lt;/table&gt;
        
        &lt;h2&gt;Price Calculator&lt;/h2&gt;
        &lt;input id="priceCalculator" type="text" size="60" /&gt;
      &lt;/td&gt;
      &lt;td width="20%" valign="top"&gt;
        &lt;h2&gt;Selected Seat&lt;/h2&gt;
        &lt;textarea id="selectedSeat" rows="12"&gt;&lt;/textarea&gt;
      &lt;/td&gt;
    &lt;/tr&gt;
  &lt;/table&gt;

&lt;/body&gt;
&lt;script src="Model.js"&gt;&lt;/script&gt;
&lt;script src="SeatMapView.js"&gt;&lt;/script&gt;
&lt;script src="SelectedSeatView.js"&gt;&lt;/script&gt;
&lt;script src="PriceCalculatorView.js"&gt;&lt;/script&gt;
&lt;script&gt;
var seatMapElement = document.getElementById("seatMap");
var selectedSeatElement = document.getElementById("selectedSeat");
var priceCalculatorElement = document.getElementById("priceCalculator");

var model = new Model();
var seatMapView = new SeatMapView(seatMapElement);
var selectedSeatView = new SelectedSeatView(selectedSeatElement);
var priceCalculatorView = new PriceCalculatorView(priceCalculatorElement);

// initialization
seatMapView.init(model.getData());
selectedSeatView.init(model.getData());
priceCalculatorView.init(model.getData());

// add listeners
model.addListener(seatMapView);
model.addListener(selectedSeatView);
model.addListener(priceCalculatorView);

seatMapElement.addEventListener("click", model.onClickHandler, false);

&lt;/script&gt;
&lt;/html&gt;
</pre>

Model.js

<pre class="brush: jscript; title: ; notranslate" title="">var Model = function() {
  
  // initialization, false = is vacant, not occupied yet
  var seatMap = {};
  seatMap["A1"] = false;
  seatMap["A2"] = false;
  seatMap["A3"] = false;
  seatMap["B1"] = false;
  seatMap["B2"] = false;
  seatMap["B3"] = false;
  seatMap["C1"] = false;
  seatMap["C2"] = false;
  seatMap["C3"] = false;

  var listeners = [];
  
  this.addListener = function(listener) {
    listeners.push(listener);
  }

  this.removeListener = function(listener) {
    var i = listeners.length;
    while (i--) {
      if (listeners[i] === listener) {
        listeners.splice(i, 1);
        break;
      }
    }
  }
  
  var broadcast = function(event, args) {
    var len = listeners.length;
    for (var i = 0; i &lt; len; i++) {
      listeners[i][event](args);
    }
  }
  
  // listener of dom element
  this.onClickHandler = function(e) {
    var element = e.target;
    
    if (!isExist(element.id)) {
      return;
    }

    // toggle value
    seatMap[element.id] = !seatMap[element.id];
    broadcast("onChange", seatMap);
  }

  this.getData = function() {
    return seatMap;
  }

  var isExist = function(item) {
    for (var a in seatMap) {
      if (a === item) {
        return true;
      }
    }
    return false;
  }
  
  var toggle = function(value) {
    value = !value;
    
    console.log(seatMap["A1"]);
    
    broadcast("onChange", seatMap);
  }
  
}
</pre>

PriceCalculatorView.js

<pre class="brush: jscript; title: ; notranslate" title="">var PriceCalculatorView = function(priceCalculatorElement) {
  var UNIT_PRICE = 10.00;
  
  this.onChange = function(data) {
    var numberOfSeat = calculateNumberOfSeat(data);
    priceCalculatorElement.value = numberOfSeat + " (seats) x $" + UNIT_PRICE + " (unit price) = $" + (numberOfSeat * UNIT_PRICE);
  }
  
  this.init = function(data) {
    this.onChange(data);
  }
  
  var calculateNumberOfSeat = function(data) {
    var numberOfSeat = 0;
    for (var a in data) {
      if (data[a]) {
        numberOfSeat++;
      }
    }
    return numberOfSeat;
  }
}
</pre>

SeatMapView.js

<pre class="brush: jscript; title: ; notranslate" title="">var SeatMapView = function(seatMapElement) {
  var COLOR_RED = "#F79F81";
  var COLOR_GREEN = "ACFA58";
  
  this.onChange = function(data) {
    for (var a in data) {
      var element = document.getElementById(a);
      element.bgColor = data[a] ? COLOR_RED : COLOR_GREEN;
    }
  }
  
  this.init = function(data) {
    this.onChange(data);
  }
}
</pre>

SelectedSeatView.js

<pre class="brush: jscript; title: ; notranslate" title="">var SelectedSeatView = function(selectedSeatElement) {
  this.onChange = function(data) {
    selectedSeatElement.innerHTML = "";
    for (var a in data) {
      if (data[a]) {
        selectedSeatElement.innerHTML += a + "\n";
      }
    }
  }
  
  this.init = function(data) {
    this.onChange(data);
  }
}
</pre>

Source code: <a href="http://crzyjcky.com/2013/02/17/javascript-oop/ticket-system/" rel="attachment wp-att-247">ticket-system</a>  
<SCRIPT charset="utf-8" type="text/javascript" src="http://ws.amazon.com/widgets/q?ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822/US/crzyjcky-20/8001/d5fcc65f-703e-462a-9fd3-85f0e4919f82"> </SCRIPT> 

<NOSCRIPT>
  <A HREF="http://ws.amazon.com/widgets/q?ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822%2FUS%2Fcrzyjcky-20%2F8001%2Fd5fcc65f-703e-462a-9fd3-85f0e4919f82&#038;Operation=NoScript">Amazon.com Widgets</A>
</NOSCRIPT>