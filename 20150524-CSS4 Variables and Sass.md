#“Houston, we will probably end up totally having a problem.”

This might seem a bit premature, however I couldn’t help but start to think about how [the new variable spec in CSS4](http://dev.w3.org/csswg/css-variables/) will inevitably bump uglies with our Sass tendencies in the near future. I’ve come up with an idea that will hopefully marry these two in an efficient and practical approach.


##The CSS4 Spec

For the uninitiated (me a week ago), new CSS variables are going to look like this:

```css
.class {
  --color-background: #FFBB00;
  background-color: var(--color-background);
}
```

The variable <code class="stx-css var">--color-background</code> is bound to the scope of the <code class="stx-css sel">.class</code> selector. Global variables can be defined outside of an element in a <code class="stx-css sel">:root</code> pseudo-class:

```css
/* global variables in the :root class */
:root {
  --color-background: #FFBB00;
}
/* calling our global variable outside the :root class */
.class {
  background-color: var(--color-background);
}
```

You can read more on variables in the [W3C Spec](http://dev.w3.org/csswg/css-variables/), or on the [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables).


##Sass

Regardless of whether or not you like the new variable syntax, chances are you already use a pre-processor to manage your variables and would like to continue to do so. Some of you ultra-organized individuals may even make use of variable maps in Sass. 

```scss
// a variable map in Sass
$colors: (
  primary: #FFBB00,
  secondary: #0969A2
);

// using `map-get` in Sass
body { 
  color: map-get($colors, primary); 
}
```
```css
body { 
  color: #FFBB00; 
}
```

Unfortunately the syntax for accessing Sass maps is a bit verbose, which is why some people prefer to use custom functions to access their map variables. Check out [Using Sass functions to access variable maps](http://codepen.io/jakealbaugh/blog/using-sass-functions-to-access-complex-variable-maps/) if you are interested in learning how you can dive deeper into variable maps.


##Working together

Just because you may like Sass variable syntax more than the CSS4 spec does not mean that you should just forsake the new spec. If you can refer to a variable in vanilla CSS, you should. The age of redundant hex colors is over! That is exciting. The Chrome inspector will inevitably allow you to change CSS4 variable definitions in-browser and you’ll see the results live update on the page. In the case of colors, that would mean we’d be able to change one variable and have everything that uses that variable change color at once. If we use our old methods, that feature will be useless. If our stylesheets *can* be more descriptive than ever before, they *should* be. 

What if we could use Sass to create and use our CSS4-compliant variables? We can. First let’s generate all our global CSS4 color variable declarations using a Sass variable map and iterate over it with an <code class="stx-css mtd">@each</code> loop:

```scss
// sass variable map
$colors: (
  primary: #FFBB00,
  secondary: #0969A2
);

// ripped CSS4 vars out of color map
:root {
  // each item in color map
  @each $name, $color in $colors {
    --color-#{$name}: $color;
  }
}
```

This will compile to valid CSS4 variable declarations.

```css
:root {
  --color-primary: #FFBB00;
  --color-secondary: #0969A2;
}
```

What we just did is fairly straightforward. We loop through each item in our <code class="stx-css var">$colors</code> map. While we loop, we can access both the name and color of the item in the map. Then, using the interpolation syntax (`#{}`), we can pull out the name of the variable as a string and combine it with a variable prefix <code class="stx-css var">--color-</code> as the CSS4 variable definition.

So that covers *declaring* the variables, now we need to be able to *reference* them. We can do that with a <code class="stx-css mtd">@function</code>:

```scss
@function color($color-name) {
  @return var(--color-#{$color-name});
}
```

This <code class="stx-css fnc">color</code> function will take a color name, and return our CSS4 variable reference. We can then use this function and have our color references be syntactically-awesome:

```scss
// calling our sass function
body { 
  color: color(primary); 
}
```
```css
/* compiles to */
body { 
  color: var(--color-primary); 
}
```

##Further Sophistication

Let’s say we have a complex variable map of sizes that includes both nested maps and top-level values:

```scss
$sizes: (
  gutter: 30px,
  spacer: 15px,
  container: (sm: 750px, md: 970px, lg: 1170px),
  viewport:  (sm: 768px, md: 992px, lg: 1200px)
);
```

To access our nested maps <code class="stx-css val">container</code> and <code class="stx-css val">viewport</code>, while maintaining the ability to reference our top-level variables <code class="stx-css val">gutter</code> and <code class="stx-css val">spacer</code>, we’ll need to slightly modify our prior approach:

```scss
// ripping CSS4 vars out of size map
:root {

  // each item in size map
  @each $name, $size in $sizes {
    // maps require a second loop
    @if type-of($size) == "map" {
    
      // each item in sub map
      @each $subname, $subsize in $size {  
        // --size-viewport-md
        --size-#{$name}-#{$subname}: $subsize;
      }
      
    // top-level sizes
    } @elseif type-of($size) == "number" {
    
      // --size-background
      --size-#{$name}: $size;
      
    }
  }
}
```

As you can see, we have the ability to detect the <code class="stx-css fnc">type-of</code> value for each item in our <code class="stx-css var">$sizes</code> map. If it is not a <code class="stx-css str">"number"</code> and it is a <code class="stx-css str">"map"</code>, we do another loop on that item’s map value. The output is a tidy collection of dash-spaced variable references:

```css
:root {
  --size-gutter: 30px;
  --size-spacer: 15px;
  --size-container-sm: 750px;
  --size-container-md: 970px;
  --size-container-lg: 1170px;
  --size-viewport-sm: 768px;
  --size-viewport-md: 992px;
  --size-viewport-lg: 1200px;
}
```

In order to *reference* our more sophisticated variables, we need a more sophisticated <code class="stx-css mtd">@function</code>:

```scss
// retrieve size from map with Sass ie. `size(viewport, sm)`
@function size($size-name, $size-variant:null) {
  // size variant is optional
  @if ($size-variant != null) {
    // map inception, need two names
    @return var(--sizes-#{$size-name}-#{$size-variant});
    
  } @else {
    // single-level size, one name
    @return var(--sizes-#{$size-name});
    
  }
}
```

As you can see, we will optionally pass in a <code class="stx-css var">$size-variant</code> value. In this case, those would be <code class="stx-css val">sm</code>, <code class="stx-css val">md</code>, or <code class="stx-css val">lg</code> for <code class="stx-css val">container</code> or <code class="stx-css val">viewport</code>. If the variant is there, we know that it is a nested map value and can write the appropriate CSS4 variable reference. If it is <code class="stx-css val">null</code>, we can spit out our standard top-level reference.

Here’s how we use it:

```scss
// referencing a top-level size variable with CSS via Sass
.container { 
  margin: size(spacer) auto; 
}

// referencing our nested size variables with CSS via Sass
@media (min-width: size(viewport, sm)) {
  .container { width: size(container, sm); }
}
```

Here’s the valid CSS4 output:

```css
.container {
  margin: var(--size-spacer) auto;
}

@media (min-width: var(--size-viewport-sm)) {
  .container {
    width: var(--size-container-sm);
  }
}
```

By managing our CSS4 variables in Sass, we are free to take advantage of all the awesome things about Sass, like looping over our <code class="stx-css val">viewport</code> sizes and outputting <code class="stx-css val">container</code> sizes for each.

```scss
// referencing our size variables with CSS via Sass @each loop
@each $name, $size in map-get($sizes, viewport) {
  @media (min-width: size(viewport, $name)) {
    .container { width: size(container, $name); }
  }
}
```

The above <code class="stx-css mtd">@each</code> loop will output container sizes for each of our viewport widths.

```scss
@media (min-width: var(--size-viewport-sm)) {
  .container {
    width: var(--size-container-sm);
  }
}
@media (min-width: var(--size-viewport-md)) {
  .container {
    width: var(--size-container-md);
  }
}
@media (min-width: var(--size-viewport-lg)) {
  .container {
    width: var(--size-container-lg);
  }
}
```

##Over the top

Let’s crank up the funk and groove out to the tune of complexity. Say that there was a case where we wanted to get the true Sass value of the variable instead of the CSS4 selector. To illustrate, I am going to move back to colors.

```scss
// color variable map
$colors: (
  text: #FFF,
  background: #333,
  // nested map inception
  primary: (
    base: #FFBB00,
    light: lighten(#FFBB00, 15%),
    dark: darken(#FFBB00, 15%),
    trans: transparentize(#FFBB00, 0.5)
  ),
  // and another. is the totem still spinning?
  secondary: (
    base: #0969A2,
    light: lighten(#0969A2, 15%),
    dark: darken(#0969A2, 15%),
    trans: transparentize(#0969A2, 0.5)
  )
);
```

To to get the *actual* value of the color, we need another optional parameter in our function. Let’s call it <code class="stx-css var">$true-val</code>.

```scss
@function color($color-name, $color-variant:null, $true-val:false) {
  
  // if we are returning the true color value
  @if $true-val == true {
    // color variant is optional
    @if ($color-variant != null) {
      // map inception, need two deep
      @return map-get(map-get($colors,$color-name),$color-variant);

    } @else {
      // single-level color, one deep
      @return map-get($colors,$color-name);

    }
    
  // if we’re only returning the CSS4 variable
  } @else {
    // color variant is optional
    @if ($color-variant != null) {
      // map inception, need two names
      @return var(--color-#{$color-name}-#{$color-variant});

    } @else {
      // single-level color, one name
      @return var(--color-#{$color-name});
      
    }

  }
}
```

We are defaulting the <code class="stx-css var">$true-val</code> value to <code class="stx-css boo">false</code>, which means the second block of code will run unless we pass in a true value.

```scss
body {
  color: color(primary,base);
  color: color(primary,base,false);
  color: color(primary,base,true);
  color: color(background,null,true);
}
```
```css
body {
  color: var(--colors-primary-base);
  color: var(--colors-primary-base);
  color: #FFBB00;
  color: #333;
}
```

You might be wondering when this would ever be useful, and I will answer that inadvertently through another important example. Remember at the beginning of this post where we saw that CSS4 variable could be defined inside of a selector’s scope? After briefly mentioning it I jumped straight into global variables using the <code class="stx-css val">:root</code> pseudo class. I didn’t forget about scoped variables. 

The fact is, the vanilla syntax for defining a variable is fairly concise. We can create a <code class="stx-css mtd">@mixin</code> to create variables, but it would take more code to write the inclusion of the mixin than to just write the standard CSS4 variable. Just to take this to the nth-degree and to appease the Sass gods, we will make both a <code class="stx-css mtd">@mixin</code> to create a variable and a custom <code class="stx-css mtd">@function</code> to reference *any* variable—global **or** scoped.

```scss
// define local variable just because
@mixin var($name,$value) {
  #{--$name}: $value;
}
// access any variable
@function v($name) {
  @return var(--#{$name});
}
```

We then define a local variable by including our <code class="stx-css mtd">@mixin</code>, and refer to the variable using our <code class="stx-css mtd">@function</code>. Note that I am also defining the variable in vanilla syntax so you can see how unnecessary the <code class="stx-css mtd">@mixin</code> is.

```scss
h1 {
  // getting a darker form of our true value
  $darker-primary: darken(color(primary,base,true), 5%);
  // defining the new variable without the extra -- fluff
  @include var(darker-primary, $darker-primary);
  // CSS4 syntax is much more terse
  --darker-primary: $darker-primary;
  // accessing our locally-scoped CSS4 variable
  color: v(darker-primary);
  border-color: v(darker-primary);
}
```

This will output the following valid CSS4 locally-scoped variable and references.

```css
h1 {
  --darker-primary: #e6a800; /* mixin */
  --darker-primary: #e6a800; /* vanilla */
  color: var(--darker-primary);
  border-color: var(--darker-primary);
}
```

This level of complexity may not suit everyone, but it is certainly doable if you enjoy writing less code.


##Bonanza!

Techniques like this allow us to utilize the power of Sass without having to ignore important advances in CSS. Any Sass we write should always output well-written modern CSS. Just because the W3C implements seemingly competing features into CSS does not mean we need to choose one over the other. Embrace the webs!

Props for making it to the end of this post. Here is everything we have learned, fully-functioning in a pen. Click “View Compiled” to see the output. Obviously the CSS4 variables are invalid CSS right now, but you can see that the Sass compiler can already handle it.

[[[pen slug-hash='VLKyVy' height='600' default-tab='css' theme-id='4396' ]]]











<hr>
<small>I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). If you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).</small>
