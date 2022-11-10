---
layout: post
title:  "Super Mario Paint support (v2022.15)"
date:   2022-10-27 10:56:00 -0400
categories: post
---

[Super Mario Paint](https://github.com/DC37/Super-Mario-Paint) is probably the most popular of the [Mario Paint](https://en.wikipedia.org/wiki/Mario_Paint) clones ([join their discord community](https://discord.com/invite/MfQK5TF)!). [Notessimo](https://notessimo.net) started has a clone of Mario Paint as well even if it has diverged a lot since the first version.

<center class="images">
<a href="https://cdn.notessimo.com/web/static/compose/SMP1.3.png" target="_blank"><img src="https://cdn.notessimo.com/web/static/compose/SMP1.3.png" alt="Super Mario Paint v1.3" width="280"/></a>&nbsp;&nbsp;&nbsp;&nbsp;
<a href="https://cdn.notessimo.com/web/static/compose/V1-1.jpg" target="_blank"><img src="https://cdn.notessimo.com/web/static/compose/V1-1.jpg" alt="Notessimo V1" width="350"/></a><br/><br/>
</center>
*Above [Super Mario Paint](https://github.com/DC37/Super-Mario-Paint) v1.3 (left), [Notessimo V1](https://notessimo.net/compose/past/#v1) (right)*
<br/><br/>

The file format is pretty simple, just a `.txt` file with each line representing 1 beat.
```
TEMPO: 400.000000, EXT: 0, TIME: 4/4, SOUNDSET: 
1:0,MARIO C5,MARIO B2,MARIO E3,MARIO C4,VOL: 96
1:1,MUSHROOM B4,VOL: 96
1:2,YOSHI F4,VOL: 96
1:3,STAR C4,VOL: 96
2:0,FLOWER B3,VOL: 96
...
```

There is some subtilty though, an instrument will continue to play until it encounters another note from the same instrument (or a special "mute" note). Most soundfonts for Super Mario Paint seems to have instruments that cannot play infinitely (no looping sample) so they will end automatically by themselves when their sample's end (I use this fact to precisely calculate the expected note length in the conversion process so the note "bounce" appropriately).

Since it is only one beat per line, to get really creative, expert painters will use insane tempo (2400+). This makes for a poor viewing experience, so there is an option as well to "compress" the tempo and get to something much more enjoyable (ex: 200).

It is also possible to combine multiple `.txt` together, so the option to upload a `.zip` is also available. Custom soundfonts are also supported, a big feature I wanted to add for notessimo was the ability to share custom instruments so the ability to upload a `.SF2` was already implemented (a "playlist" of the instruments is created and available as a choice). 

Super Mario Paint import is free and available right now to be exported to video!

Big shoutout to [CyanSMP64](https://notessimo.net/u/Wikkid_Vommit) for testing the features! And check out the result below, [Lone Digger](https://notessimo.net/s/BMXpLJIH1F) by [Caravan Palace](https://en.wikipedia.org/wiki/Caravan_Palace) done by [Xanderoni](https://notessimo.net/u/Xanderoni).

<center class="video-wrapper"><iframe width="560" height="315" src="https://www.youtube.com/embed/Thy8Epx646U" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center>

<div id='discourse-comments'></div>

<script type="text/javascript">
  DiscourseEmbed = { discourseUrl: 'https://community.notessimo.net/',
                     discourseEmbedUrl: 'https://jd.boiv.in/post/2022/10/27/mario-paint.html' };

  (function() {
    var d = document.createElement('script'); d.type = 'text/javascript'; d.async = true;
    d.src = DiscourseEmbed.discourseUrl + 'javascripts/embed.js';
    (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(d);
  })();
</script>