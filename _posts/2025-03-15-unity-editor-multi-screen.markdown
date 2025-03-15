---
layout: post
title:  "Fix for Unity Editor multi-screens setup"
date:   2025-03-14 18:00:00 -0400
categories: post
---

*(**TL;DR**: Fix for dropdown components in Unity Editor from appearing on the wrong screen in a multi-screens setup on Windows)*

<br/>

## Introduction

Ever worked in the Unity Editor using a multi-screens setup in Windows and got UI components (like dropdown) to show in the wrong screen?

<center class="images borders">
  <div><a href="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-1.png" target="_blank"><img src="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-1.png" alt="Weird position of dropdown" title="Weird position of dropdown" height="200"/></a><br/>1</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-2.png" target="_blank"><img src="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-2.png" alt="Wrong screen" title="Wrong screen" height="200"/></a><br/>2</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-4.png" target="_blank"><img src="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-4.png" alt="What's supposed to look like" title="What's supposed to look like" height="200"/></a><br/>3</div>
</center>

1. When I click on the **[+]** button for rarities, it shows up with the wrong dimension and position.

2. It can even show up on the wrong screen!

3. What is supposed to look like.

<br/>

This is an issue that has been bothering me for years and it doesn't seems like Unity will fix it anytime soon.

<br/>

## Investigating the issue

<center class="images borders">
  <div><a href="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-3.png" target="_blank"><img src="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-3.png" alt="My multi-screens setup" title="My multi-screens setup" height="200"/></a><br/>My multi-screens setup</div>
</center>

My multi-screens setup isn't fancy but it does offer clues as to why it is messing up the layout of certains elements. My Unity editor is on the left screen **4** (1080p, 100%), while my main screen is **1** (4k, 150%).

My guess is that since the dropdown would normally overflow to the right side (so it would be on both 4 & 1), it get moved to snap to the edge of the screen. However, while doing so, it read the incorrect values thinking it needs to apply a scaling of 150% when it get snapped to a 100% screen or even snap to the wrong screen entirely.

The root issue doesn't seems to be fixable without access to Unity's source code, they should makes sure that whatever component triggered a dropdown ensure to use that component's screen for the calculation when snapping them to the edge of a screen rather than "guessing" the correct screen.

<br/>

## "Fixing" the issue

<center class="images borders">
  <div><a href="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-5.png" target="_blank"><img src="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-5.png" alt="New multi-screen layout" title="New multi-screen layout" height="200"/></a><br/>New multi-screen layout</div>
</center>

It's a stupid fix, but it works! It does confirm my suspicions about the root issue.

Now we have a new problem tho, the mouse won't transition between the screen in a natural manner...

<br/>

## Using LittleBigMouse

To fix the mouse transition between screens we can use [LittleBigMouse](https://github.com/mgth/LittleBigMouse). It allows you to remap the edges of screens differently than the Windows settings. Bonus, it can scale the screens so the transitions between screens of different resolution is much more close to the physical world.

<center class="images borders">
  <div><a href="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-6.png" target="_blank"><img src="/assets/posts/2025-03-15-unity-editor-multi-screen/screen-6.png" alt="Patched multi-screens setup in LBM" title="Patched multi-screens setup in LBM" height="200"/></a><br/>Patched multi-screens setup in LBM</div>
</center>

LittleBigMouse is a bit ugly and unintuitive but it get the job done and in the end works much better than the alternatives (like [Cursr](https://cursr.app/) which is too slow for any real world work).

<br/>

## Conclusion

Not exactly a proper fix but in the end, it works, and I did get slightly better mouse transitions between my screens, so not too bad.

I couldn't find an isssue in the issue tracker for it but did find a couple of posts mentioning the problems. Maybe I'll create an issue eventually but I've had bad experience with unity support before and my time is precious (maybe it did get better over the year with the new CEO).

<br/>
