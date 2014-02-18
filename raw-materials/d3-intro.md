# Intro to d3

## Stuff d3 is not!

* D3 is *not* a chart library (there are no charts, but you can make some)
* D3 doesn't play nice with browsers we don't like.
* D3 doesn't actually care about how you render things (svg much? html time? canvas pants?)
* D3 doesn't abstract rendering (no "circle" or "rectangle" api. You deal directly with your renderer yourself.)

## Stuff d3 is!

* D3 is a way to map data to documents.
* D3 is a general purpose visualization library
* D3 handles data transformation (into other forms or into elements)

## I heard d3 is like jQuery

A little:

* selection: 
  
  ```
  d3.select("#vis")
  ```
  
  ```
  $("#vis")
  ```

* applying styles:

  ```
  d3.select("#vis").style("font-size", "12px");
  ```

  ```
  $("#vis").css("font-size", "12px");
  ```

* binding to events:
  ```
  d3.select("#vis")
    .on("mouseover", function(d) {
      // 'this' is the element
      // 'd' is the datum associated with it
      // to act on this element with d3, we have to
      // reselect it:
      d3.select(this).classed("selected", true);
    });
  ```
  
  ```
  $('#vis').mouseover(function() {});
  ```

* getting data:

  ```
  d3.json("data.json", function(err, data) {});
  ```
 
  ```
  $.getJSON("data.json", function(data) {});
  ```

## Hardest part about d3: Joining

The act of joining data to the objects representing them.
For example, this array: [1,2,3], may map to 3 divs. How to create that join, 
and maintain it when that array changes, is pretty key in d3.

The join:
  * connects a datum to its representation
  * keeps track of changes (new datum, updated datum, removed datum) and lets
  you react to those.

  If we were to implement it ourselves: http://macwright.org/mistakes/#5010465

## Let's make things!

JSBIN TEMPLATE FOR YOU: http://jsbin.com/iYoleLE/1/edit

```
var data = [
    10, 20, 30
  ];

// select our container
d3.select("#vis")

  // declare your intent to create some divs
  // this creates a selection.
  .selectAll("div")

  // bind the data 
  .data(data)

  // indicate what you'd like to have happen
  // when new datums join your data
  .enter()
    
    // ... add a div for each
    .append("div")

    // ... set its contents to the value
    .text(function(d) {
      return d;
    });
```
This code: http://jsbin.com/ADIhEge/1/edit

## Ways to modify d3 selections:

The following functions modify a selection somehow:

* `.text()` - sets text content
* `.html()` - sets html content
* `.attr()` - sets attribute on the element
* `.style()` - sets a css property on the element
* `.property()` - for properties not addressable by standard attributes/styles (like value)

There are two ways to do that:

* static value - independent of data:

  ```
  d3.select("#vis")
    .style("color", "red");
  ```

  code here: http://jsbin.com/iGeLAHU/1/edit

* dynamic value - dependent on the data:

  ```
  d3.selectAll("div")
    .data(data)
    .append("div")
    .enter()
       .style("color", function(d, i) {
          if (i % 2 === 0) {
            return "red";
          } else {
            return "blue";
          }
       });
  ```
  code here: http://jsbin.com/icaHeCo/1/edit

They also function as getters if you only pass one argument:

```
d3.select("#vis").attr("bla"); // returns something
```

## Lifecycle events (most confusing part about d3)

What's that `.enter()` thing? Well the life of a d3 selection can be divided into three different areas:

* `.enter()` - what happens when new data elements come in
* `.update()` - what happens when existing data elements come in
* `.exit()` - what happens when data elements are no longer present

```
var data = [
    10, 20, 30
  ];

function draw(data) {
  // select our container
  var selection = d3.select("#vis")
  
    // declare your intent to create some divs
    .selectAll("div")
  
    // bind the data 
    .data(data, function(d, i) { return i; });
  
  // if there are existing elements, color them
  // green
  selection
    .style("color", "green");
  
  // indicate what you'd like to have happen
  // when new datums join your data
  selection.enter()
      
    // ... add a div for each
    .append("div")

    // ... set its contents to the value
    .text(function(d) {
      return d;
    })

    // ... add some pizazz 
   .style("color", "red");
  
  // exiting elements should be colored light grey
  selection.exit()
    .style("color", "lightgrey");
    
}

draw(data);
setTimeout(function() {
  draw([10,2,30,40]);
}, 3000);
```
code here: http://jsbin.com/EvAWawA/1/edit

## Other cool things about d3: scales

Transform data from your domain, to some other scale (like, your visual display size, etc.)

### continuous scales:

```
// linear scale
var scale = d3.scale.linear()
  .domain([0,100])
  .range([0,1000]);

d3.select("#vis")
  .text(scale(10));

// sqrt scale (useful when mapping data to area)
var scale2 = d3.scale.sqrt()
  .domain([0,10])
  .range([1, 1000]);

var vals = [];
for(var i = 0; i < 10; i++) {
  vals.push(scale2(i));
}
d3.select("#vis2")
  .html(vals.join("<br/>"));

// log scale
var scale3 = d3.scale.log()
  .domain([1,10])
  .range([0,1]);

var vals = [];
for(var i = 0; i < 10; i++) {
  vals.push(scale3(i));
}
d3.select("#vis3")
  .html(vals.join("<br/>"));

```

code here: http://jsbin.com/ijaFIcU/1/edit

### discrete scales:

mapping values into "bins":

```
// quantized scale
var scale = d3.scale.quantize()
  .domain([0,100])
  .range([0,1000]);

// effectively rounding 0-49 => 0, 50-100 => 1000
d3.select("#vis")
  .text(scale(50));

var scale2 = d3.scale.quantile()
  .domain([1, 1, 2, 3, 2, 3, 16])
  .range(['white', 'blue', 'red']);

// bins values for you. careful - will hide outlines
d3.select("#vis2").text(
   scale2(1) + " " + scale2(2) + " " + scale2(14));
```

code here: http://jsbin.com/ukEKotA/1/edit

## UGH can we make an actual chart already, GOODNESS GRACIOUS.

Yes. Fine. Let's make a bar chart

```
var data = [1,3,7,25,25,26,40,42,42,42,51];

var height = 400;
var width  = 500;

var chart = d3.select("#vis")
  .append("svg")
  .style("height", height + "px")
  .style("width", width + "px");

function draw(data) {
  
  // position of bar
  var xScale = d3.scale.linear()
    .domain([0, data.length - 1])
    .range([0, width]);
  
  // height of bar
  var yScale = d3.scale.linear()
    .domain(d3.extent(data))
    .range([0, height]);

  var barSelection = chart.selectAll("rect")
    .data(data);
  
  // append rects for data
  barSelection
    .enter()
      .append("rect")
  
      // position it along the x axis
      .attr("x", function(d, i) { 
        return xScale(i);
      })
  
      // since svg 0,0 is top left, we need to 
      // find the point at which we start the rect
      .attr("y", function(d, i) {
        return height - yScale(d); // SVG Gotcha!
       })
      
      // width of bar is just overall width divided by
      // how many data points we have
      .attr("width", width / data.length)
  
      // the height of the bar is based on our y scale
      .attr("height", function(d, i) {
        return yScale(d);
      })
  
      // fill is a rect attribute, not a css style
      .attr("fill", "blue");
}

draw(data);
```
code here: http://jsbin.com/opabaQO/2/edit

## d3.chart

Please all welcome Mike to talk about d3.chart which makes d3 less sucky!

## Resources

http://d3js.org
http://bost.ocks.org/mike/join/ - joins! ah!
http://macwright.org/presentations/dcjq/
