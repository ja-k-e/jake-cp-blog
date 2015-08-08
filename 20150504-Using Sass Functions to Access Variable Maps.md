#Let’s get Mercator with this ish

###Maps? Murps?

The verdict is out, Sass variable maps are pretty awesome. I am going to pretend I’ve known about them forever so that you think I’m cool. Jaykay, I just started using them.

For the uninitiated, [Dennis Gaebel](http://codepen.io/grayghostvisuals/) has an awesome Sass variable maps write up on his blog: [Real Sass, Real Maps](http://blog.grayghostvisuals.com/sass/real-sass-real-maps/).

The basic idea is that you can store groups of Sass variables in a JSON-ish map:

```scss
$colors: (
  primary: #FFBB00,
  secondary: #0969A2
);
```

These can then be accessed using the <code class="stx-css"><span class="fnc">map-get</span></code> function:

```scss
// using `map-get` in Sass
h1 { color: map-get($colors, primary); }
```
```css
h1 { color: #FFBB00; }
```


###Inception

Now maps, being the lovely little things that they are, can be nested for added specificity and better organization:

```scss
// color variable map
$colors: (
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

To access a nested map, you simply nest the <code class="stx-css"><span class="fnc">map-get</span></code>:

```scss
h1 {
  color: map-get(map-get($colors, primary), base);
}
```

The inner <code class="stx-css"><span class="fnc">map-get</span></code> grabs our <code class="stx-css"><span class="val">primary</span></code> map, then the last value <code class="stx-css"><span class="val">base</span></code> grabs that <code class="stx-css"><span class="val">primary</span></code> map’s <code class="stx-css"><span class="val">base</span></code> value.


###Functions FTW

Functionally, nested maps are awesome. Unfortunately the syntax is a bit...meh. We can fix that nonsense with a <code class="stx-css"><span class="mtd">@function</span></code>:

```scss
// retrieve color from $colors map ie. `color(primary, base)`
@function color($color-name, $color-variant) {
  // map inception
  @return map-get(map-get($colors, $color-name), $color-variant);
}

// now we use the function
h1 {
  color: color(primary, base);
}
```

It is lovely, isn’t it. We can even take it a step further. Let’s say we want to have nested maps *and* non-nested values in our map:

```scss
// color variable map
$colors: (
  // non-nested values
  text: #FFF,
  background: #333,
  // nested map inception
  primary: (
    base: #FFBB00,
    light: lighten(#FFBB00, 15%),
    dark: darken(#FFBB00, 15%),
    trans: transparentize(#FFBB00, 0.5)
  ),
  secondary: (
    base: #0969A2,
    light: lighten(#0969A2, 15%),
    dark: darken(#0969A2, 15%),
    trans: transparentize(#0969A2, 0.5)
  )
);
```

We can then update our function to *optionally* receive the nested variant name:

```scss
// retrieve color from $colors map ie. `color(base, primary)`
@function color($color-name, $color-variant:null) {
  // color variant is optional
  @if ($color-variant != null) {
    // map inception
    @return map-get(map-get($colors, $color-name), $color-variant);
  } @else {
    @return map-get($colors, $color-name);
  }
}
```

And use it like this:

```scss
// using the function to get an non-map color
body {
  background-color: color(background);
}
// using the function to get a nested map color
h1 {
  color: color(primary, base);
}
```


###Simplify ze Defs

Some of you may have noticed and not enjoyed the redundency in our variable maps:

```scss
// color variable map
$colors: (
  primary: (
    base: #FFBB00,
    light: lighten(#FFBB00, 15%),
    dark: darken(#FFBB00, 15%),
    trans: transparentize(#FFBB00, 0.5)
  ),
  // ...
);
```

Notice how the base color <code class="stx-css hex">#FFBB00</code> is repeated in hex form across all the values in the <code class="stx-css val">darken</code>, <code class="stx-css val">lighten</code>, and <code class="stx-css val">transparentize</code> functions. Unfortunately, we cannot refer to a sibling value in the other values (ie <code class="stx-css"><span class="mtd">@this</span>.<span class="val">base</span></code>), so the hex must be repeated. The workaround? Simply extract the hex into a variable outside of the map.

```scss
// base color defs
$color-primary: #FFBB00;
// color variable map
$colors: (
  primary: (
    base: $color-primary,
    light: lighten($color-primary, 15%),
    dark: darken($color-primary, 15%),
    trans: transparentize($color-primary, 0.5)
  ),
  // ...
);
```

We can afford to be more verbose with our variable definition <code class="stx-css var">$color-primary</code> because we don’t have to write it everywhere, just in our map definitions. Then, if we want to change our color scheme around, we can swap a single value instead of four values. Doing it this way is more a matter of preference, but I enjoy making the map completely color-agnostic by manipulating a color variable instead of hardcoding it. 

If you want to be an ultra-streamline magic person, you can store your <code class="stx-css val">darken</code>, <code class="stx-css val">lighten</code>, and <code class="stx-css val">transparentize</code> amounts and use them across the board for each of your colors: 

```scss
// base color defs
$color-primary: #FFBB00;
$color-secondary: #0969A2;
$color-shade-amount: 15%;
$color-trans-amount: 0.5;
// color variable map
$colors: (
  primary: (
    base: $color-primary,
    light: lighten($color-primary, $color-shade-amount),
    dark: darken($color-primary, $color-shade-amount),
    trans: transparentize($color-primary, $color-trans-amount)
  ),
  secondary: (
    base: $color-secondary,
    light: lighten($color-secondary, $color-shade-amount),
    dark: darken($color-secondary, $color-shade-amount),
    trans: transparentize($color-secondary, $color-trans-amount)
  )
);
```



###When to do any of this

The times I find it best to use a <code class="stx-css mtd">@function</code> like this is when your variable map is a single category of values and justifiably complicated. In addition to color schemes, things like container and device sizes are a great time to use them. Here are a few basic guidelines I’ve formed for myself:

- If you only have a few variables in a group (ie two colors) on a small project or only refer to them a few times, just use vanilla variables. 
  - no sense creating a function just to look cool.
- Don’t create one giant “global variable” map:
  - like <code class="stx-css"><span class="var">$vars</span>: ( <span class="val">colors</span>: (...), <span class="val">sizes</span>: ( <span class="val">containers</span>: (...), <span class="val">fonts</span>: (...) ) )</code>
  - your function to get the values will be oddly verbose and clutter-y
  - just make multiple functions per map...if each map is necessary


###Whelp, that’s all I got.

We just looked at some helpful ways to harness Sass variable maps and functions. Don’t abuse this stuff or else you’ll trap yourself in a very confusing and verbose corner. Keep it simple and write pretty code.

If you want to see a demo of some of this stuff, I got a tiny (and ugly) playground here:

[[[pen slug-hash='zGGXwj' height='300' theme-id='4396' ]]]

> Credits: I can’t remember with certainty where I was initially exposed to this idea, but I know I read [this article by Erskine Design](http://erskinedesign.com/blog/friendlier-colour-names-sass-maps/) at some point.


<hr>
<small>I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). If you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).</small>