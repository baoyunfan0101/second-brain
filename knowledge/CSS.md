---
tags:
  - frontend
  - process
summary: selectors, box_model, layout (flex/grid), positioning, pseudo
use_cases:
  - ui
  - styling
  - layout
  - web
---

# CSS

## What is CSS

<span class="abbr" data-title="Cascading Style Sheets">CSS</span> defines how HTML elements are displayed and arranged on a page.

It mainly covers:
- color and typography
- spacing and borders
- positioning and layout
- responsive behavior across devices

## Basic Syntax

```css
selector {
  property: value;
}
```

Example:
```css
p {
  color: red;
  font-size: 16px;
}
```

- selector: targets elements
- property: the style attribute
- value: the assigned style

## Ways to Apply CSS

**Inline Style**
```css
<p style="color: red;">Hello</p>
```

**Internal Style**
```css
<style>
  p {
	color: red;
  }
</style>
```

**External Style**
```css
<link rel="stylesheet" href="style.css">
```

External CSS is preferred for scalability and reuse.

## Selectors

Consider this HTML:
```html
<div id="container">
  <h1 class="title">CSS Demo</h1>
  <p class="text">First paragraph</p>
  <p class="text highlight">Second paragraph</p>
  <span>Simple span</span>
  <div class="box">
    <p>Paragraph inside box</p>
  </div>
  <input type="text" value="hello">
</div>
```

**Type Selector**
```css
p
```

- Matches all `<p>` elements in the structure

**Class Selector**
```css
.text
```

- Matches elements with class "text"

**ID Selector**
```css
#container
```

- Matches the element with id "container"

**Universal Selector**
```css
*
```

- Matches every element

**Descendant Selector**
```css
div p
```

- Matches all `<p>` elements inside any `<div>`

**Child Selector**
```css
div > p
```

- Matches `<p>` elements that are direct children of a `<div>`

**Adjacent Sibling Selector**
```css
h1 + p
```

- Matches the first `<p>` immediately following `<h1>`

**General Sibling Selector**
```css
h1 ~ p
```

- Matches all `<p>` elements after `<h1>` at the same level

**Attribute Selector**
```css
input[type="text"]
```

- Matches `<input type="text">`

**Multiple Class Selector**
```css
.text.highlight
```

- Matches elements containing both classes

## Common Properties

**Text**
```css
color          /* text color */
font-size      /* text size */
font-weight    /* text thickness */
font-family    /* font type */
text-align     /* horizontal alignment */
line-height    /* line spacing */
```

**Background**
```css
background-color   /* background color */
background-image   /* background image */
background-size    /* background image size */
background-position /* background image position */
```

**Size**
```css
width        /* element width */
height       /* element height */
max-width    /* maximum width limit */
min-height   /* minimum height */
aspect-ratio /* width/height ratio */
```

**Border**
```css
border         /* border shorthand */
border-width   /* border thickness */
border-style   /* border style */
border-color   /* border color */
border-radius  /* corner rounding */
```

**Spacing**
```css
margin   /* outer spacing */
padding  /* inner spacing */
```

**Position**
```css
position  /* positioning mode */
top       /* vertical offset */
right     /* horizontal offset */
bottom    /* vertical offset */
left      /* horizontal offset */
z-index   /* stacking order */
```

**Display / Visibility**
```css
display     /* layout participation */
visibility  /* visual visibility */
```

- `display:none`: removed from layout (no space)
- `visibility:hidden`: invisible but keeps space

## Box Model

Each element is a box consisting of:
- content
- padding
- border
- margin

Example:
```css
div {
  width: 200px;              /* content width */
  padding: 20px;             /* inner spacing */
  border: 2px solid black;   /* border thickness + style + color */
  margin: 10px;              /* outer spacing */
}
```

Padding / Margin:
```css
padding: 10px;              /* all sides */
margin: 10px;

padding: 10px 20px;         /* top/bottom | left/right */
margin: 10px 20px;

padding: 10px 20px 30px;    /* top | left/right | bottom */
margin: 10px 20px 30px;

padding: 10px 20px 30px 40px; /* top | right | bottom | left */
margin: 10px 20px 30px 40px;

padding-top: 10px;          /* top */
padding-right: 20px;        /* right */
padding-bottom: 30px;       /* bottom */
padding-left: 40px;         /* left */

margin-top: 10px;           /* top */
margin-right: 20px;         /* right */
margin-bottom: 30px;        /* bottom */
margin-left: 40px;          /* left */
```

Important:
```css
box-sizing: content-box;     /* default, width = content only */
box-sizing: border-box;      /* width includes padding + border */
box-sizing: inherit;         /* inherits from parent */
box-sizing: initial;         /* resets to default (content-box) */
box-sizing: unset;           /* inherit if possible, else initial */
```

## Display

```css
display: block;        /* block-level, full width, new line */
display: inline;       /* inline, no width/height control */
display: inline-block; /* inline flow + box sizing */
display: none;         /* removed from layout */
```

- Defines how an element participates in layout

## Position

```css
position: static;    /* default flow */
position: relative;  /* offset from itself */
position: absolute;  /* relative to nearest positioned ancestor */
position: fixed;     /* relative to viewport */
position: sticky;    /* switch between relative and fixed */
```

- Defines how an element is positioned relative to a reference

Example:
```css
div {
  position: absolute;
  top: 0;
  left: 0;
}
```

- Result: top-left of parent element

## Flexbox

Used for one-dimensional layout.

```css
.container {
  display: flex;               /* flex layout container */
  justify-content: center;     /* main-axis alignment */
  align-items: center;         /* cross-axis alignment */
}
```

## Grid

Used for two-dimensional layout.

```css
.container {
  display: grid;                    /* grid layout container */
  grid-template-columns: 1fr 1fr;   /* two equal columns */
}
```

## Units

```css
px   /* absolute unit */
%    /* relative to parent */
em   /* relative to font-size (self) */
rem  /* relative to root font-size */
vw   /* viewport width */
vh   /* viewport height */
```

## Colors

```css
color: red;                  /* named color */
color: #ff0000;              /* hex color */
color: rgb(255, 0, 0);       /* rgb color */
color: rgba(255, 0, 0, 0.5); /* rgb + alpha */
```

## Inheritance, Cascade, Specificity

**Inheritance**
Some properties (like color, font) are inherited.

**Cascade**
Later rules can override earlier ones.

**Specificity**
Priority:
```css
!important > inline > id > class > tag
```

Example:
```html
<style>
p {
  color: blue;
}

.class {
  color: green;
}

#id {
  color: purple;
}

p {
  color: red !important;
}
</style>

<p id="id" class="class" style="color: orange;">Hello</p>
```

- Result: `res` > `orange` > `purple` > `green` > `blue`

## Pseudo-Classes

```css
a:hover            /* hover state */
input:focus        /* focused state */
li:first-child     /* first child element */
li:last-child      /* last child element */
```

Example:
```html
<a href="#">If hover this, a:hover applies</a>

<input type="text" placeholder="If focus this, input:focus applies">

<ul>
  <li>If first child, li:first-child applies</li>
  <li>Normal item</li>
  <li>If last child, li:last-child applies</li>
</ul>
```

## Pseudo-Elements

```css
p::before          /* insert content before */
p::after           /* insert content after */
p::first-letter    /* first letter styling */
p::first-line      /* first line styling */
```

Example:
```html
<style>
p::before {
  content: "[before] ";
}

p::after {
  content: " [after]";
}

p::first-letter {
  color: red;
}

p::first-line {
  font-weight: bold;
}
</style>

<p>Hello world</p>
```

- Result: \[before\] <strong><span style="color:red;">H</span>ello world</strong> \[after\]

## Responsive Design

```css
@media (max-width: 768px) { /* apply styles under width condition */
  body {
    background: red;        /* background color */
  }
}
```

## Practical Cases

### Center a div

**Horizontal Center**
```css
div {
  width: 200px;     /* fixed width */
  margin: 0 auto;   /* left/right auto */
}
```

**Horizontal + Vertical Center (flex)**
```css
.parent {
  display: flex;              /* flex container */
  justify-content: center;    /* horizontal center */
  align-items: center;        /* vertical center */
}
```

**Absolute Center**
```css
.parent {
  position: relative;         /* positioning reference */
}

.child {
  position: absolute;         /* positioned element */
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%); /* offset correction */
}
```

### Center content with equal left/right spacing

**Auto Margin Centering**
```css
.container {
  max-width: 800px;   /* limit content width */
  margin: 0 auto;      /* center horizontally */
  padding: 0 16px;     /* prevent content touching edges */
}
```

**Three-Column Layout (flex)**
```html
<style>
.wrapper {
  display: flex;
}

.side {
  flex: 1;             /* left/right take remaining space equally */
}

.content {
  width: 800px;       /* fixed content width */
}
</style>

<div class="wrapper">
  <div class="side"></div>
  <div class="content">Main Content</div>
  <div class="side"></div>
</div>
```

**Three-Column Layout (grid)**
```css
<style>
.wrapper {
  display: grid;
  grid-template-columns: 1fr 800px 1fr; /* left | content | right */
}
</style>

<div class="wrapper">
  <div></div>
  <div>Main Content</div>
  <div></div>
</div>
```