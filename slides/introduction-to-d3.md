class: center, middle

# Introduction to d3.js

---
# Stuff d3.js is not good at!

* Being a chart library
* Being Backward compatibile
* Rendering things for you
* Abstracting basic graphic primitives

???

* D3 is *not* a chart library (there are no charts, but you can make some)
* D3 doesn't play nice with browsers we don't like.
* D3 doesn't actually care about how you render things (svg much? html time? canvas pants?)
* D3 doesn't abstract rendering (no "circle" or "rectangle" api. You deal directly with your renderer yourself.)

---

# Stuff d3.js is good at!

* Providing a way to map data to documents.
* Being a general purpose visualization library
* Handling data transformation
* Providing basic math & layout algorithms

---

# d3.js is a little like jQuery

jQuery is a fast, small, and feature-rich JavaScript library. It makes things like HTML document traversal and manipulation, event handling, animation, and Ajax much simpler with an easy-to-use API that works across a multitude of browsers. With a combination of versatility and extensibility, jQuery has changed the way that millions of people write JavaScript.

[http://jquery.com/](http://jquery.com/)

---

# d3.js & jQuery: Selections

Finding elements in the DOM:

### d3:

```javascript
d3.select("#vis")
```

### jQuery:

```javascript
$("#vis")
```

---

# d3.js & jQuery: Styles

Applying css styles to DOM elements:

### d3:

```javascript
d3.select("#vis").style("font-size", "12px");
```

### jQuery:

```javascript
$("#vis").css("font-size", "12px");
```

---

# d3.js & jQuery: Attributes

Applying css styles to DOM elements:

### d3:

```javascript
d3.select("#vis").attr("name", "phonenumber");
```

### jQuery:

```javascript
$("#vis").attr("name", "phonenumber");
```

