# Task1
Display a barchart (start from barchart refactor sample):         
-  Adding space between columns.    
-  Adding colors to each bar.    
-  Adding a legend.    
-  Showing the chart vertically.

# Steps to reproduce the sample

## Create index.html
Index is responsible for joining all javascript and css files.
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
  </body>
  <link rel="stylesheet" href="./styles.css" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/d3/4.5.0/d3.min.js" charset="utf-8"></script>
  <script src="./data.js"></script>
  <script src="./main.js"></script>
</html>
```

## Create data.js
A global variable called "total Sales" is created. As it is brought in index.html the other files like main.js can use it.
```javascript
var totalSales = [
    { product: 'Hoodie', sales: 7 },
    { product: 'Jacket', sales: 6 },
    { product: 'Snuggie', sales: 9 },
    ];
```

## Create main.js
First of all, the color style of the chart is selected.
It is possible to choose others in the following web page:
https://bl.ocks.org/pstuffa/3393ff2711a53975040077b7453781a9
```javascript
var barColor = d3.scaleOrdinal(d3.schemeCategory20);
```
The variables are declared. They allow creating a "svg" with certain "margins" and a scale determined by "x" and "y".
```javascript
let margin = null,
    width = null,
    height = null;

let svg = null;
let x, y = null; // scales
```
All functions are also initialized, which are explained below.
```javascript
setupCanvasSize();
appendSvg("body");
setupXScale();
setupYScale();
appendXAxis();
appendYAxis();
appendChartBars();
AppendLegend();
```
This function sets margins, width and height.
```javascript
function setupCanvasSize() {
    margin = {top: 100, left: 180, bottom: 120, right: 130};
    width = 960 - margin.left - margin.right;
    height = 800 - margin.top - margin.bottom;
  }
```
A new element of type svg is appended to the html document. Its attributes such as width, height and position are configured.
```javascript
function appendSvg(domElement) {
  svg = d3.select(domElement).append("svg")
              .attr("width", width + margin.left + margin.right)
              .attr("height", height + margin.top + margin.bottom)
              .append("g")
              .attr("transform",`translate(${margin.left}, ${margin.top})`);
}
```
The data is scaled to the pixels on the screen.
- The X axis scales the products to pixels, but it is not a linear scaling.
- The Y axis passes the number of sales to pixels, since both are numbers is a linear scaling.
```javascript
function setupXScale()
{
    x = d3.scaleBand()
    .rangeRound([0, width])
    .domain(totalSales.map(function(d, i) {
      return d.product;
    }));
}

function setupYScale()
{
    var maxSales = d3.max(totalSales, function(d, i) {
        return d.sales;
    });

    y = d3.scaleLinear()
    .range([height,0])
    .domain([0, maxSales]);
}
```
Next, the lines of the X and Y axes are added to svg. And move to their respective positions. Now it is possible to see the axes without data.
```javascript
function appendXAxis() {
  svg.append("g")
    .attr("transform",`translate(0, ${height})`)
    .call(d3.axisBottom(x));
}

function appendYAxis() {
  svg.append("g")
  .call(d3.axisLeft(y));
}
```
We proceed to graph the bars within the axes. Those bars are represented as rectangles.
- The rectangles have the dimensions X and Y.
- The positions are determined by width and height.
- The spacing in X is determined by the subtraction "x.bandwidth () -5".
- The rectangles are colored with the "fill" attribute.
```javascript

function appendChartBars()
{
  var rects = svg.selectAll('rect')
    .data(totalSales);

    var newRects = rects.enter();

    newRects.append('rect')
      .attr('x', function(d, i) {
        return x(d.product);
      })
      .attr('y', function(d) {
        return y(d.sales);
      })     
      .attr('height', function(d, i) {
        return height - y(d.sales);
      })
      .attr('width', function(d){
          return x.bandwidth() -5
      })  
      .attr('fill', function(d){
          return barColor(d.product)
      }) 
      ;
}
```
Finally, a function is created to add a legend to the graph.
```javascript
function AppendLegend() {
    // building a legend is as simple as binding
    // more elements to the same data. in this case,
    // <text> tags
    svg.append('g')
    .attr("transform", `translate(50, 0)`) 
      .attr('class', 'legend')
        .selectAll('text')
        .data(totalSales)
          .enter()
            .append('text')
              .text(function(d) { return '• ' + d.product; })
              .attr('fill', function(d) { return barColor(d.product); })
              .attr('y', function(d, i) { return 20 * (i + 1); })           
  }
```
You can see the final result on the following web page provided by github.

https://manu0970.github.io/Task1/
