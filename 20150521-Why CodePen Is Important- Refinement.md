#“Coolidge Code”

It is nearly impossible for me to implement a new approach appropriately the first time. The sense of novelty that accompanies the realities of newness can often cloud my judgement. I tend to use new techniques because “I can” rather than because “I should”. This tendency is eerily similar to the scientifically-observed [Coolidge effect](http://en.wikipedia.org/wiki/Coolidge_effect) in nature where:

> ...males (and to a lesser extent females) exhibit renewed sexual interest if introduced to new receptive sexual partners, even after refusing sex from prior but still available sexual partners.

I realize it may seem a bit off-course to bring up mammalian mating habits in a post about web development, but is there not a more perfect depiction of us and any new technique?

<figure>
<img alt="rhino mating fail" src="http://i.imgur.com/neDEkEF.gif" width=400 />
<figcaption>Me and Parallax Scrolling ~2013</figcaption>
</figure>

This is precisely what I find myself and others exhibiting around the web; so much so, that I propose the phrase *“Coolidge Code”* to represent any technique being used solely because it is available and in contrast to previously-accepted values.

#Drop Shadows <small>(a “Receptive Partner”)</small>

When I was first exposed to Photoshop drop shadows, the fact that I could easily create a shadow somehow trumped whether or not the shadow made sense. I either used the default settings or randomly tweaked a few parameters—it was enough to feel pro. 

<figure>
<img alt="venus needs a shadow" src="http://www.todayandtomorrow.net/wp-content/uploads/2011/03/venus_with_drop_shadow.jpg" width=240 />
<figcaption>An Extremely Tasteful Arm Shadow</figcaption>
</figure>

Later in life, I saw my “drop shadow technique” in other people’s work, and was able to objectively note how terrible it looked. I had realized that my “skillz” were not actually skills at all—they were flags of ignorance. Thanks to these revelations, over time I eventually learned how to add “tasteful” depth through subtlety in my graphic design work. 

#Box Shadows <small>(returning to old habits)</small>

A few years after promising myself I would use drop shadows appropriately and after I had begun learning how to code, `box-shadow` was added to CSS. I, like many other skeuomorphic-crazed individuals, jumped back into my ridiculous shadow abuse for no reason other than “DROP SHADOWS IN CODE OMGZ”. The novelty of it all was so enticing, that I was neglecting everything I had ever learned about design. And it wasn’t just shadows—gradients, textures, rounded corners, transitions—you name it, they were all bastardized into abhorrent manifestations fit for a homemade garage sale sign. 

<figure>
[[[pen slug-hash='acbaaaa3653cb1ab7ba3dbdafcb5a684' height='300' theme-id='4396' ]]]
<figcaption>A Perfectly Subtle Button</figcaption>
</figure>

#Coolidge Code Abounds

*“Coolidge Code”* pervades more than just aesthetic concerns, in UX we have techniques like “parallax scrolling”, “scroll jacking”, or even less-obvious tendencies in our JS frameworks, compilers, and build systems. There is nothing inherently wrong with any of these techniques and advances in technology. The problem lies in many initial implementations during our collective state of “Wow!” where we bastardize these ideas in a sprint of ignorance—like we are wearing a tux as a bathing “suit” to a pool party. 

Ironically enough, our primal enthusiasm for new things, more often than not, moves the community forward and we eventually settle at a tasteful standard over time. As positive as that is, the inescapable truth is that until that happens, most of us look like this:

<figure>
<img src="http://stream1.gifsoup.com/view5/4210981/inappropriate-o.gif" width=400 />
<figcaption>Me, playing my AngularCaster</figcaption>
</figure>

That moment when we finally figure out the “new” thing makes us feel so damn good, but it often comes with an expensive price. Our ignorance allows our work to become resource-heavy, network-laggy, distracting, or ugly. But time moves on. Eventually, we read enough damning articles about the approach or get that “fresh look” after a few months and say, “what on earth was I thinking?” 

<figure>
<img src="http://24.media.tumblr.com/025c562eae4fb588adf539baae5fb2c7/tumblr_mv4r22Nsfm1rgab2qo1_500.gif" width=400 />
<figcaption>Looking at code the morning after</figcaption>
</figure>

That is when we bind these ideas to standards which we promise never to depart from and move forward in bliss, vowing to never make the same mistake twice...

<figure>
<img src="http://cdn.mtlblog.com/uploads/2013/09/18-jesse2.gif" width=400 />
<figcaption>“Sass! I’m never going to get lost in my CSS again!”</figcaption>
</figure>

...and then we do it all over again with the next thing.

<figure>
<img src="http://33.media.tumblr.com/tumblr_m42v0t8A0b1qjemo2o1_400.gif" width=400 />
<figcaption>“Why is my CSS 500kb?”</figcaption>
</figure>


#CodePen is for Coolidge Code 

Unfortunately, this refinement of taste and reduction of novelty often happens during the course of client work. We “try stuff out” because after all, we need to learn, and there’s no better way to learn than to try. This often results in bad decisions being made in products for good clients. 

Thankfully, CodePen gives us an outlet for much of this nonsense—a place to run free and make our terrible first attempt at hand-coding SVG, to try out a preprocessor, or to learn flexbox. We can become acquainted with anything we are interested in learning—and do it at nobody’s expense. There is no deadline to publish by. We have the time to actually figure out that a tuxedo is not in the same genre of suit as a speedo—we can drastically animate our buttons without jeopardizing the integrity of our client's site.

There are two primary ways to refine on CodePen. **Doing** and **Observing**.

###Doing

**Short Runways**<br>“Starting up” on CodePen takes zero time compared to an average workflow. Blink, and you have Sass, add and drop jQuery in an instant, pull in some random Javascript, fork your own pen to try a different approach. The lowering of these barriers allows you to get to the meat without having to set the table. Make something easily, come back to it instantly and pivot to your heart’s content.

**Private Parties**<br>If you want your CodePen profile to only exhibit work you are confident in, get [CodePen Pro](http://codepen.io/pro/) and save things privately. Be free enough to be a moron. I have a host of private pens that are ugly and embarassing demos of first, second, and third attempts at various techniques. I always try new things out in CodePen well before I attempt to put it in production code. It gives me the room to look back and judge it objectively. I also tend to wait at least a day to publish any pen I have become excited about. Upon reflection, I *always* see at least one thing I missed in the haze of novelty.


###Observing

Refining your taste and growing as a dev require continued exposure to both ends of the code quality spectrum. If you see enough bad code, you’ll learn why good code is good and if you see enough good code, you’ll learn why bad code is bad. We all write good code and we all write bad code. CodePen has both.

Explore the expanse of quality in the CodePen community. Find something that looks cool but has poorly written code? Fork it and figure out how you would write it more efficiently. Find something that looks “meh” but is written masterfully? Fork it and make it purdy. This practice will help you think more critically about your approach the next time you set out to write something from scratch. 

Often times, I’ll see an approach and be all, “Why did they do it that way? They could just...oh...wait...yeah...that’s why.” That type of discovery is always enlightening. You learn to be objective and humble. Your first idea is not always great, most of the time it is not even good.

<figure>
<img src="http://www.reactiongifs.com/wp-content/uploads/2013/05/oh-yea-duh.gif" width=400 />
<figcaption>“Oh yeah...that’s why.”</figcaption>
</figure>


###Life’s Limits

**Your Time**<br>Inevitably, you will find yourself having less time to learn because you have so much work to do. That is ok. I often need to make sacrifices and do some experimentation in production code just to get the job done. Take advantage of CodePen when you can. Sometimes I will do bits of actual client work in private pens just to get off the ground. Then I can share the private urls with coworkers and get immediate feedback on approaches without having to set up the core of the project just to start sketching.

**CodePen’s Time**<br>Even though you can do an insane amount of things with CodePen, you cannot do everything. On the CodePen Radio podcast, Chris, Tim, and Alex have expressed that expanding CodePen beyond client side code would be a dream come true, and I think we’re all with them on that. But, that is not today’s reality. Your Coolidge Ruby code will have to find another home for now.

In the meantime, hop on CodePen and keep refining.


<hr>
<small>I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). If you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).</small>