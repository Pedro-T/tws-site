---
layout: single
title:  "Post-Mortem: Deer in the Headlights"
excerpt: "Post-mortem write-up of my 2025 '20 Second Game Jam' entry"
date:   2025-12-13 14:10:39 -0500
tags: godot gamejam 2d
categories: postmortem
permalink: /posts/dith-postmortem
toc: true
toc_sticky: true
toc_label: "Contents"
---

## Background

Deer in the Headlights, hereafter DITH because it's tedious to type repeatedly, is a game that I put together for the [20 Second Game Jam][twenty-second-jam-itch]'s 2025 run. This was my first game jam and I purposely picked one with a very generous time limit (initially three weeks, later extended to over a month) but a built-in scope limit. The premise is simple: the game has to last just twenty seconds. This is great for limiting scope creep because...well, how much can you really fit into a twenty second game?

DITH also happens to be my first "finished" game project. I've created several prototypes and so on in the past, but always lose interest primarily because of the open-ended nature of hobby development. I opted for a game jam this time around so that there would be a clearly defined development window and deadline motivating me to finish.


## Planning

Planning for DITH was pretty straightforward. I sat down and thought about games that are naturally very short while playing some of the previous 20 Second Game Jam entries linked to on the jam's page. One game in particular, [Pig in Hell][pig-in-hell-itch], nudged me in the direction of a "survive for X time" game, and for whatever reason after that the classic game Frogger popped into my mind.

Frogger is a straightforward game where the player has to move their character across the screen into a safe zone while dodging moving obstacles. I combined the core principle of Frogger with the danger/survive idea of Pig in Hell and came up with the game design:
- Concept: Play as a deer trapped on a highway, have to dodge traffic to escape to a safe area.
- 20 second limit: There is a hunter trying to shoot the deer. He will succeed after twenty seconds.

The jam also had three optional themes:
- Squash/Stretch
- Shadow/Light
- Pick it/Lick it/Roll it/Flick it

I went with shadow/light as a difficulty enhancer - what if you have to dodge the vehicles in the middle of the night and can hardly see except for areas illuminated by their headlight beams? (of course, this mechanic conveniently produced the game's title)

Finally, I decided that the exit should not be available from the start in case the player got luck with vehicle spawns and managed to just casually walk up to it. There would be some obstacle that is present until at least 14-15 seconds have passed.

I also set myself a challenge: I would limit all development time to (approximately) **twenty hours**. I didn't want to spend a hundred hours on a small game jam game, and saw this as another way to forcefully limit scope creep.


## Development

### Planning - GitHub Projects
Planning was a breeze. This was a very small solo project, so I stuck with a GitHub Project organized into three sprints. The first was four days, while sprints 2 and 3 were one week each. I broke DITH components up into granular tasks so that even a game this small consisted of seventy-five issues and would spend around 1.5 hours per day completing 4-6 tasks.

### Tools & Assets
Adding to the challenge, I had to learn how to use the Godot 4.5 game engine (which I had used for maybe a few hours a year before to prototype something that later became Tunnel Hazard) and use Aseprite, which I had limited experienced with, to create most of the art myself.

My goal was to minimize use of outside assets and limit myself to free ones. I ended up using a few sounds and sprites (the base deer sprites, which I adapted and expanded, plus the pine tree) from OpenGameArt under CC-BY or CC0 licenses.

## Development Progress

I went with a rapid iteration approach, planning out a few days in advance and creating small components quickly. The point was to create the complete game functionality before worrying about visuals/polish.

![Image](/assets/image/posts/dith-pm/make-it-exist.png)

This is what DITH looked like after the first six days. Admittently, I did not follow my own approach very well because those basic cars took quite some time to create (I am **not** a good or fast artist) and could easily have just been colored rectangles at this point.

![Image of early DITH development](/assets/image/posts/dith-pm/dith-early.png)

Still, I moved at a pretty good pace of steadily adding functionalities while periodically revamping portions of the visuals. I took some shortcuts, such as reusing portions of assets. There are six vehicles that can appear on the highway: three cars, two vans, and a bus. The three cars are... the same car in different colors and with one having a sunroof. After creating the first one (red), the blue and gray variants took almost no time. 

<figure class="align-center">
    <a href="/assets/image/posts/dith-pm/cars.png"><img src="/assets/image/posts/dith-pm/cars.png"></a>
    <figcaption>The same car base, used three times with minor changes</figcaption>
</figure>

Same idea for the vans - it's the same van with two different logos and a ladder rack on one. The tufts of grass? I chopped a piece off of the tree and rotated it. Same principle for a number of other details.

Shortcuts such as these and the focused scope kept the project moving at a great pace until until...

### Overcomplication and Premature Optimization

I fell into this one hard when working on the headlights mechanic. I had read that Godot's PointLight2D nodes might cause performance problems with many lights and, realizing that I could commonly have around 30-40 of these visible at once, felt I had to go the shader route. Problem: I don't know how to do that. Cue 3-4 hours of going in circles and mounting frustration with headlight effects that either worked but looked nothing like a headlight beam or did not work at all. 

<figure class="align-center">
    <img src="/assets/image/posts/dith-pm/dith-headlights-shader.jpg" alt="Image showing poorly done headlights">
    <figcaption>The closest I managed to get with the shader route, but not the beam shape I was looking for</figcaption>
</figure>

Eventually I pulled myself away from this and realized - what if the regular lights are just fine? There's so little going on in this small game that it might not even matter. Lo and behold, regular PointLight2D nodes with a simple headlight beam sprite are completely fine. No apparent performance degradation on my low-end test machine, which is a Thinkpad T480 from 2018, even using the compatibility renderer (for web). And for this sort of game it looks just fine. I essentially wasted around three hours on...nothing.

<p><strong>Lesson</strong>: don't chase unnecessary optimizations or excessive polish.</p>{: .notice}

### Time Limit: Refining the Scope

By the time I finished backing out the headlight shader fiasco and implementing the simpler 2d lighting option, I had around five hours of available time left to complete DITH. I still needed to polish the start and ending screens, fix various bugs, and do some further playtesting and adjustments. So I started by cutting optional components, which I had already marked as "Can" priority in the project as opposed to "Must" or "Should." Some of those dropped were:
- **The hunter himself**! He was supposed to be an animated character moving around and shooting at the deer, but due to time constraints he became a bush with a rifle poking out... *much* easier to animate.
- **The intro sequence**. There was supposed to be an intro where the hunter is chasing at the deer and the deer then jumps onto the freeway, but this was cut completely and the player simply starts already standing in one of the lanes.
- **Some QoL features** such as volume setting and a mute option. I figured that since this is a game jam entry that an individual might play a few times totalling a few minutes, not a big deal.
- **Mobile controls and format for mobile web**.


### Finishing Up & Polishing

While cutting those abovementioned features, I did find time to introduce one new feature though - a daytime option. The game is supposed to be dark, yes, but it could easily be TOO dark for some people. So I added a simple toggle to the start menu which would remove the darkness effect.

<figure class="half">
    <img src="/assets/image/posts/dith-pm/dith-dark-mode.png" alt="Image of the game in default dark mode - hard to see">
    <img src="/assets/image/posts/dith-pm/dith-light-mode.png" alt="Image of the game in light mode - much clearer">
    <figcaption>Comparison of dark mode (left) and light mode (right).</figcaption>
</figure>

I also added some simple animations to the start and end screens, always reusing assets where possible for efficiency. None of these are super impressive, but I find that they make the game feel a little more complete in areas that are often rushed or minimal in this type of project.

## Summary

Overall I would rate my outcome a solid 7/10 in the important context of this being my first game jam and first completed game. The game is obviously not perfect, but I achieved my key goals and the output is definitely playable. Feedback has been pretty good. At the time of writing this post, the submission has received three pieces of feedback from other submitters, all of which were positive and included some great suggestions.

**Will I return to this project in the future?** Probably not, since the premise is limited and too similar to the game that it was inspired by.

**Will I do another game jam? Almost definitely.** I've bought in to the idea that these are worthwile for the end-to-end project completion experience with time-based guardrails.

For now I've returned to a larger but still fairly small project, Tunnel Hazard, which you can check out [here][projects-th].


### Check It Out

You can try it out in-browser on Itch.io:

<iframe class="align-center" frameborder="0" src="https://itch.io/embed/4079043?bg_color=4f6781&amp;fg_color=ffffff" width="552" height="167"><a href="https://turtlesmock.itch.io/deer-in-the-headlights">Deer in the Headlights by TurtleSmock</a></iframe><br />


The [full project source, including assets, can be found on GitHub][dith-gh-repo] for reference.




[twenty-second-jam-itch]: https://itch.io/jam/20-second-game-jam-2025
[pig-in-hell-itch]: https://gistworks.itch.io/pig-in-hell
[dith-itch]: https://turtlesmock.itch.io/deer-in-the-headlights
[dith-gh-repo]: https://github.com/Pedro-T/deer-in-the-headlights
[projects-th]: /projects/tunnel-hazard
