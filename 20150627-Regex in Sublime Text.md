#Sublex Regime?

If you are anything like me, obfuscated tools like regular expressions are absolutely terrifying. Over the past three years, I have gradually learned enough to be able to increase my speed as a developer. I hope the following can *at least* show you that regex is approachable and if we’re lucky, you can walk away with enough to immediately begin ‘regexting’.

##Why yet another thing to learn?
Because it can help you so damn much. I’ll give you an example. A little while back I was working on a massive codebase for an airline’s web application. The styles were all in LESS. At one point when the site was very small, the original developers started `:extend`-ing styles for pretty much everything. `:extend` can be very helpful, but it can get to a point where it bloats file size more than mixins if you gzip. Some dude on my team figured that out. This meant we needed to replace all instances of `:extend` that were typographic styles, but not ones that weren’t. To add to the problem, `:extend` in LESS is more complex than in Sass. Here’s an example of the various `:extend` scenarios I had to deal with:

```less
h1:extend(.heading-sans-1) {}                // make mixin
h5:extend(.heading-serif-12) {}              // make mixin
.class-name-1 p:extend(.bodycopy-serif-1) {} // make mixin
.whatever:extend(.bodycopy-sans-2),
.whatnot:extend(.bodycopy-sans-3) {}         // make mixin

div:extend(.card-1) {}                       // don’t modify
div:extend(.card-2, .shadow-1) {}            // don’t modify
```

As you can see, this was a pretty complicated task, especially since there were over 100 LESS files. Thankfully, the typographic extends were all following a general pattern. This allowed me to get at them. I tested over and over again until I boiled it down to two Find/Replace commands that would nearly completely fix everything. The act of making the replacements took less time than saving and closing all the files afterwards. If you want more on that solution, see the end of this post.

This is why regex is amazing. Manually finding and replacing all those extends would have taken me at least an extra five or six hours of excruciating effort; and any manual slip up would translate into a tiny bug that no one would notice for a long time. If you can test your regex thoroughly, you can be confident that your edits worked and save time. Enough evangelism, let’s get into it.

##The basics.
In Sublime’s Find and replace, there is a regex toggle on the far left (next to the Case Sensitive button). This enables you to use regular expressions in your Find commands. You can write an expression, and Sublime will highlight matches in the same way any vanilla search works.

In regex, there are a handful of symbols. I would be lying to you if I said I knew them all, or that you’ll need any of them. What I *will* do is give you a quick breakdown of the meat n’ potatoes you’ll use all the time.

###Quantifiers
`+` is a wildcard “quantifier”. It states “take whatever you just got, and repeat it one to infinite times.” Since regex can capture literal characters, if I were to write the expression `a+`, it would match any sequence of one to infinite `a`’s. 

```
"aaabc"
```
```
finding: a+
replace: MAGIC
```
```
"MAGICbc"
```

Notice how there is only one instance of `MAGIC`. Because of the `+`, the Find pattern includes the entire `a` sequence. Without the `+` we get a different result:

```
"aaabc"
```
```
finding: a
replace: MAGIC
```
```
"MAGICMAGICMAGICbc"
```

Why didn’t we just say `aaa` in the original Find pattern? Great question. We could have, but it would have meant something entirely different. Our `a+` pattern is seeking out an *any-length* sequence of `a`’s. `aaa` would only match sequences of three. It all depends on the need. FWIW, you could also write `a{3}` and achieve the same result as `aaa`.


###Capturing Groups
Wrap anything in parens `()` and you will have created a “capture group”. `(a+)` would be the same as what we previously have been capturing (`a`, `aa`, `aaa`...). The parens by themselves do not do anything notable. 

Think of capture groups as storing a match as a variable. These groups are available to us in our Replace string. `$1` or `\1` would refer to the *first* set of parenthesis in our Find pattern. For example:

```
"abc"
```
```
finding: (a)bc
replace: $1 MUFFIN
```
```
"a MUFFIN"
```

This removes the "bc", which is not in our capture group, and replaces it with a space and the word "MUFFIN". What if our string had actual parenthesis in it? We escape them each with a `\`:

```
"(a)bc"
```
```
finding: \((a)\)bc
replace: $1 MUFFIN
```
```
"a MUFFIN"
```

What happened here is that the capture group is *inside* of the escaped parenthesis. If that was the other way around, we would get a different result:

```
"(a)bc"
```
```
finding: (\(a\))bc
replace: $1 MUFFIN
```
```
"(a) MUFFIN"
```

Here, our escaped parens are *inside* of the capture group, so they are included in the replacement text.


###Finding Non-literal values
Let’s say we want to match *any* sequence of letters, commonly known as a *word*. In this case, we need to refer to any letter, not just an `a`. For that, we can use a list, defined by wrapping `[]`. `a-z` is a quick way to refer to any lowercase letter.

```
"abc abc"
```
```
finding: ([a-z]+)
replace: $1 MONGOOSE
```
```
"abc MONGOOSE abc MONGOOSE"
```

If you are paying attention, you may be able to guess that `A-Z` will include uppercase and `0-9` or `\d` will include numbers.

```
"abc aBc aB3"
```
```
finding: ([a-zA-Z\d]+)
replace: $1 MARTIAN
```
```
"abc MARTIAN aBc MARTIAN aB3 MARTIAN"
```

###Not finding things

In some cases, *not* finding something can be quicker than *yes* finding a ton of things. You can preface a list with `^` to change the list’s meaning to be a collection of things you *don’t* want to find.

```
"abc aBc aB3"
```
```
finding: ([^ "]+)
replace: $1 MOSES
```
```
"abc MOSES aBc MOSES aB3 MOSES"
```

Don’t miss the invisible `space` character in our list. Believe it or not, it means `space`. It could also be written as `\s`. Also, note the `"` is also in our list since our string has surrounding quotes. Together, `([^ "]+)` reads “any sequence of non-space and non-quote characters”


##“Okay, okay, we get it...gimme something practical”

Simmer down, homeslice. Let’s say we have a ton of `dashed-class-names` in our HTML and CSS that we want to be underscores. Just Find `-` and Replace with `_`, right? No. What about CSS properties like `-webkit-transform-origin`? What about hyphened-words in our HTML text? We want to leave those alone. This is a perfect case for regex.

###CSS

In the CSS, we would want to start our match based on the `.` at the beginning of every class name then find everything up to the first `-`.

```css
.hello-world {
  -webkit-transform-origin: 0% 0%;
}
```
```
finding: (\.[a-zA-Z\d_]+)-
replace: $1_
```

Let’s break the Find expression down: 

First comes `\.`. This is a single and *literal* `.`. It is escaped with the `\` because in regex, `.` refers to “any character except a new line”. In order to reference an *actual* period, we escape it.

Then we capture a series of any valid classname characters `[a-zA-Z\d_]+`. This matches “any series of letters, numbers or underscores”. 

Finally, we close the group and cap it off with the `-` that we are looking to dispose of. It is outside the group because we want to remove it.

The result:

```css
.hello_world {
  -webkit-transform-origin: 0% 0%;
}
```

This expression will only match one instance of dash in a class name, which means we have to run the Replace as many times as there hyphens in our `most-hyphened-class-name`: 

- Once: `most_hyphened-class-name`
- Twice: `most_hyphened_class-name`
- Thrice: `most_hyphened_class_name`

In all honesty, running this Replace a handful of times with Sublime shortcuts is probably going to take much less time than writing a ridiculously complex (at least I think it would be) expression to do it all in one replace. Let me know if I’m wrong on that. I would love to see an “all in one” solution.

###Gotcha!
Wait, what about attribute selectors? Stop fussing. We’re getting there. We can get at attribute selectors (ie. `div[class="hello-world"]`) in the same expression as when we grab the HTML.

```css
#message[class="hello-world"],
#message[class^="hello-world"] {
  -webkit-transform-origin: 0% 0%;
}
```
```html
<div class="hello hello-world"></div>
<div class="hello-world"></div>
  dashed-word-bruh
</div>
```
```
finding: (class.+"[a-zA-Z\d_ ]+)-
replace: $1_
```

Ok, let’s go through it. It isn’t as crazy as you think. 

First, we have `class.+"` this matches the literal word `class` then looks for any character using the “any char but newline” symbol (`.`). Then repeats zero or more “any characters” using `+` before hitting a `"`. This is necessary because our CSS attribute selectors can have modifiers like `+=`, `^=`, `~=`, `*=` and so on. 

Next comes our `[a-zA-Z\d_ ]+` class name selector that is very similar to the one for standard CSS classes. The only difference is we add a space to the list because in the `class=""` context, you can have multiple space-separated classnames.

Finally, we grab the `-` outside of the group to be removed by our Replace statement.

The result is bonafide gold:

```css
#message[class="hello_world"],
#message[class^="hello_world"] {
  -webkit-transform-origin: 0% 0%;
}
```
```html
<div class="hello hello_world"></div>
<div class="hello_world"></div>
  dashed-word-bruh
</div>
```

###Combo
Using a `|` pipe character as an `or` operator, we can actually combine both our CSS and HTML expressions:

```
finding: (\.[a-zA-Z\d_]+)-|(class.+"[a-zA-Z\d_ ]+)-
replace: $1$2_
```

Our Replace needs to include the second group, even though it and the first are separated by the pipe. If a group is empty, the Replace number value is empty as well. You wont get an error or something for referring to an unmatched group.


##Bringing it home...
What if I told you you could go from underscored to camelCase as well? Using `\u`, it is possible!

```css
.hello_world,
#message[class="hello_world"],
#message[class^="hello_world"] {
  -webkit-transform-origin: 0% 0%;
}
```
```html
<div class="hello hello_world"></div>
<div class="hello_world"></div>
  dashed-word-bruh
</div>
```
```
finding: (\.[a-zA-Z\d]+)_([a-zA-Z\d])|(class.+"[a-zA-Z\d ]+)_([a-zA-Z\d])
replace: $1$3\u$2$4
```
```css
.helloWorld,
#message[class="helloWorld"],
#message[class^="helloWorld"] {
  -webkit-transform-origin: 0% 0%;
}
```
```html
<div class="hello withAnother"></div>
<div class="helloWorld"></div>
  dashed-word-bruh
</div>
```

Wowzer, Bowser! The only difference with our Find expression is that we add a `([a-zA-Z\d])` to the end after our `_`. This becomes our second or fourth capture group and represents the letter immediately following the `_`.

In our replace, you’ll notice we have `\u` right before our third and sixth match. This means we will capitalize the next character. When there is no capture group two, it will uppercase the first letter of the fourth. Again, I will reiterate that you must run this multiple times in order to take care of class names with multiple underscores in them.

Fancy, right?

##Double bonus
If you end up getting decent at regex in Sublime, you’ll find it can help a ton in your Javascript as well. `.match()` and `.replace()` are fantastic little methods and accept regular expressions.

##Parting advice
If you want to get better at or more acquainted with regex, all you need to do is stop being afraid of it. Know that you are capable and just go for it. The best way to learn regex is to find answers to real problems in your day to day work. Keep an eye out for repetitive tasks and ask yourself if you can break it down into a series of replacements. The answer is almost always “yes” if it is repetitive. Keep at it.

I will put this out there, if you have a problem that you think can be solved with regex but don’t know how, put it in the comments and let’s have some `"asdffunasdf".replace(/asdf/g,"")`.

##Research
Here’s some awesome resources.

- [regex101.com](https://regex101.com): An awesome playground for regex. You can even save expressions not unlike CodePen.
- [regular-expressions.info](http://www.regular-expressions.info): A very resourceful site if you can get past the amazing styles...
- [google.com](http://google.com): An amazing and entirely *free* tool. Collects data from something called the information highway and answers your questions. Seriously, I have learned so much regex from this.
- [regex golf](https://regex.alf.nu): You read that right. It’s pretty fun. It does get insanely difficult.
- [regex for the people](http://jakealbaugh.com/regex/): A little collection (2 so far) of regular expressions with detailed explanations that I threw together. We’ll see if I keep it up, but hey it’s something.

Keep going, it gets easier and you’ll be saving loads of time in no time.


#Curious?
Were you wondering if I’d show my LESS `:extend` solution? Ok fine.

##Pass 1
Flag down edge cases where comma-separated selectors each have their own `:extend`:

```less
.class-name-1:extend(.bodycopy-sans-1),
.class-name-2:extend(.bodycopy-sans-2),
.class-name-3:extend(.bodycopy-sans-3),
.class-name-4:extend(.bodycopy-sans-1) { ... }
```
```
finding: ([a-z\.\#][\#\.a-zA-Z0-9_\- ]*):extend\((\.(bodycopy|header)-[a-z\-]*-[0-9])*\)[ ]*\,
replace: $1 { $2; } /* :extend => mixin */\n/* @@@$1,@@@ */
```
```less
.class-name-1 { .bodycopy-sans-1; } /* :extend => mixin */
/* @@@.class-name-1,@@@ */
.class-name-2 { .bodycopy-sans-2; } /* :extend => mixin */
/* @@@.class-name-2,@@@ */
.class-name-3 { .bodycopy-sans-3; } /* :extend => mixin */
/* @@@.class-name-3,@@@ */
.class-name-4:extend(.bodycopy-sans-4) { ... }
```

Then I globally searched for `/* @@@`, and manually remade the comma-separated selector list while maintaining each one’s personalized mixin. There were only around four or five instances of that happening across the 100 or so files so it took little to no time at all. To be clear, the above code ended up looking like this:

```less
.class-name-1 { .bodycopy-sans-1; } /* :extend => mixin */
.class-name-2 { .bodycopy-sans-2; } /* :extend => mixin */
.class-name-3 { .bodycopy-sans-3; } /* :extend => mixin */
.class-name-4 { .bodycopy-sans-4; } /* :extend => mixin */
.class-name-1,
.class-name-2,
.class-name-3,
.class-name-4 { ... }
```

##Pass 2
The actual replacement for the primary instances:

```less
.class-name-4:extend(.bodycopy-sans-1) { }
```
```
finding: ([a-z\.\#][\#\.a-zA-Z0-9_\- ]*):extend\((\.(bodycopy|header)-[a-z\-]*-[0-9][0-9]?)*\)[ ]*\{
replace: $1 { $2; /* extend => mixin */
```
```less
.class-name-4 { .bodycopy-sans-1; /* extend => mixin */ }
```

Feel free to hate on the organizational approach as it was a few years ago and I will not be offended; but I will point out that this saved us over 50kb and removed most of our 100-selector long declaration blocks which bogged down the inspector. Looking back, I can see ways to clean those expressions up, but hey. Let’s let dead dogs lie.

Speaking of dead dogs (because apparently that’s the sort of topic I prefer my phrases to focus on), I am really tired. Hopefully this helped. Again, let me know in the comments if you have some crazy regex ish you want to tackle together!




<hr>
<small>I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). Or if you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).</small>