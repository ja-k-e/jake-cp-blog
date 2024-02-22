# Surprises
>“Can a browser render CSS live if it is fed into the page character by character?”

This incredibly useless and almost ignorant thought occurred to me as I was hovering over CodePen in bed (as we all do). I had happened upon a pen that for the life of me, I can no longer find. It was 404 page with an illustration of a developer sitting at his computer. It had a video element embedded on top of the computer screen playing a video of CSS being written. If you can find it, let me know. I’d like to thank whoever made it.

That pen unearthed a question burrowed deep in the recesses of my changelog. I often have thoughts in this setting, groggily lit by the blueish hue of my MacBook Pro. When I do, I either:

**A.** Discard it immediately before I waste a bunch of time. 

This is the most common result—so much so that I have considered investing in a CodePenBedPan®. Or,

**B.** Most of these questions aren’t easily Google-able, so stay up entirely too late working them out. 

This particular night was a *classic* **B**.

### Surprise #1: It actually worked.
I created a private pen ([go pro already](http://codepen.io/pro/)) and threw together a basic test. Since this test eventually became [pen#PwLXXP](http://codepen.io/jakealbaugh/pen/PwLXXP), I do not have the exact code I wrote on hand, but it was more or less this:

[[[pen slug-hash='EjYgyY' height='300' theme-id='4396' ]]]

As you can see, the required Javascript is insanely terse. I went with jQuery because I was lazy and drafting. I opted to use CoffeeScript for legible multi-line strings.

The basic mechanism is the <code class="stx-js"><span class="mtd">writeStyles</span></code> method which takes three parameters. <code class="stx-js"><span class="var">message</span></code>, <code class="stx-js"><span class="var">index</span></code>, and <code class="stx-js"><span class="var">interval</span></code>. It checks to see if the <code class="stx-js"><span class="var">index</span></code> is valid. If the <code class="stx-js"><span class="var">index</span></code> is higher than the amount of characters in the <code class="stx-js"><span class="var">message</span></code>, we are at the end of the string and it stops. If the <code class="stx-js"><span class="var">index</span></code> is valid, it throws the character (character in <code class="stx-js"><span class="var">message</span></code> with position <code class="stx-js"><span class="var">index</span></code>) into a <code class="stx-js"><span class="mtd">writeStyleChar</span></code> method. 

This <code class="stx-js"><span class="mtd">writeStyleChar</span></code> method injects the character into the <code class="stx-html"><span class="tag">&lt;pre&gt;</span></code> and <code class="stx-html"><span class="tag">&lt;style&gt;</span></code> elements. Later on, I would use this method to detect syntax (comments, declaration keys and values, etc.) using regex. 

The <code class="stx-js"><span class="mtd">writeStyles</span></code> method then continues by instantiating a <code class="stx-js"><span class="mtd">setTimeout</span></code> which calls itself again creating a loop that will continue until there aren’t any more characters left.

### Surprise #2: The result was oddly dynamic.

My expectation was that the closing `}` would be required in order for the browser to use the code. **I was wrong**. In fact, all you need is a “somewhat” valid declaration.

[[[pen slug-hash='gpYLPB' height='300' theme-id='4396' ]]]

So if the characters being written could influence time in the result, so could the timing inherent to the order of declarations. When a stylesheet is typically loaded, the cascade and specificity dictates which declarations are used and which are overridden (<code class="stx-css"><span class="key">color</span>: <span class="val">blue</span>; <span class="key">color</span>: <span class="val">red</span>;</code> the color would be <code class="stx-css"><span class="val">red</span></code>). You do not see the overridden (<code class="stx-css"><span class="val">blue</span></code>) style. However, if you are writing the style one character at a time, this is not the case.

I realized that it is possible to override styles in sequence and display the sequence live on the page. In order to add a pause between declarations that override each other, I could simply add spaces to the string.

[[[pen slug-hash='vOBgKo' height='300' theme-id='4396' ]]]

### Surprise #3: The pen had a personality.

Once I figured out the *functionality*, I needed to explore the outer edges of the *idea*. As I started building out the pen in more detail, I started marking up the code with comments to keep things organized. As I did that, a story quickly began to unfold. From there it practically wrote itself. It was the most fun I’ve ever had coding.


[[[pen slug-hash='PwLXXP' height='300' theme-id='4396' ]]]

*(will not fully run while embedded in this post)*


### Surprise #4: You were all as delighted as I was.

I hesitated to make pen#PwLXXP public. You know that feeling, the one you get where you question everything in your life because you just shoveled your soul into something practically useless. The feeling that you are extremely deficient in all things dev; that posting your sweat-drenched code publicly would only destine it to falling back in the “picks” list with 3 likes. There is something precious about those pens while they remain private. The sky is the limit—they are the best idea you’ve ever had. So much creativity comes through iteration when you keep pens private ([go pro already](http://codepen.io/pro/)).

I hesitated to make pen#PwLXXP public, but I eventually did. The response was ridiculous. As ridiculous as someone making [a self-authoring version of one of the Reddit links](http://yourwebdev.ninja/generate.html). More than anything, I was excited that it made a lot of people happy—even non-devs.


###Surprise #5: It can run Javascript too.

Since everyone was so happy with pen#PwLXXP, I decided to make its sophisticated cousin. No jQuery, better syntax highlighting/regex, and most challenging: the ability to write and run Javascript with Javascript. This post is already getting long, so I’ll *briefly* cover how [pen#JoVrdw](http://codepen.io/jakealbaugh/pen/JoVrdw) functions.

[[[pen slug-hash='JoVrdw' height='300' theme-id='4396' ]]]

*(will not fully run while embedded in this post)*

I was able to create CSS and JS “modes” by using <code>\`</code> as a toggle character in the string. It is intercepted by the <code class="stx-js"><span class="mtd">writeChar</span></code> method:

```coffeescript
# toggle CSS/JS on `
if which == "`"
  which = ""
  __js = !__js
```

If in CSS mode, it handles syntax highlighting and DOM injection in an improved, but similar way to pen#PwlXXP. If in JS mode, it waits for a trigger to create and run the script.

```coffeescript
# Using JS
if __js
  # running a command block. initiated with "~"
  if which == "~" && !openComment
    script_tag = createElement "script"
    # two lookback matches based on prior scenario
    prior_comment_match = /(?:\*\/([^\~]*))$/
    prior_block_match = /([^~]*)$/

    if _code_block.match(prior_comment_match)
      # get all js until prior comment
      script_tag.innerHTML = _code_block.match(prior_comment_match)[0].replace("*/", "")
    else
      # get all js until prior ~
      script_tag.innerHTML = _code_block.match(prior_block_match)[0]

      # reset script area html
      $script_area.innerHTML = ""
      # append script to script area
      $script_area.appendChild script_tag
      
    # set char to current character
    char = which
    # add char to the pre element with potential syntax highlighting
    code_html = scriptSyntax($code_pre.innerHTML, char)
```
    
As you can probably see, the mechanism to run the Javascript is the `~` character. Whenever this gets fed into the method, it parses the <code class="stx-js"><span class="var">_code_block</span></code> (the current state of the string) to find the last bunch of Javascript that hasn’t been run, then wraps it in a <code class="stx-html"><span class="tag">&lt;script&gt;</span></code> element and throws it in the DOM.

If you haven’t, you should open up [pen#JoVrdw full view](http://codepen.io/jakealbaugh/full/JoVrdw) and watch it unfold. Let’s just say it gets a bit...nested.

# Parting words
CodePen is a great place to experiment, learn, and be joyful. For those of you that hesitate to experiment, don’t. You are capable of anything you put your mind to. Don’t be afraid of failure, at the very least you’ll learn something.

For those of you that find experimentation to be a waste of time, it is not. I have learned too much while experimenting to let you write it off. It helps keep “that thing you do for a living” from becoming stagnant. 

Find the joy in what we do, and share it with the rest of us.

---
I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). If you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).
