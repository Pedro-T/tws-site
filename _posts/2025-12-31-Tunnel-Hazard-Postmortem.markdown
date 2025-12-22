---
layout: single
title:  "Post-Mortem: Tunnel Hazard"
excerpt: "Post-mortem write-up of Tunnel Hazard, a small game inspired by the classic Helmet handheld."
date:   2025-12-31 14:10:39 -0500
tags: godot gamejam 2d
categories: postmortem
permalink: /posts/th-postmortem/
toc: true
toc_label: "Contents"
toc_sticky: true
---

## Inspiration

Game & Watch was a series of handheld single-title game units released in 1980 by Nintendo and produced for about a decade. These were basic early handhelds which each had a single game. I never had an actual Game & Watch (this was before my time, I started with a Game Boy Color), but I did have Game & Watch Gallery 2 for the aforementioned Game Body Color. This was a game cartridge released in 1998 which contained both updated and true-to-original versions of six of the classic Game & Watch games from the handhelds:
- Ball
- Chef
- Donkey Kong
- Parachute
- Vermin

and of course, <strong>Helmet</strong>.

Helmet is a game where the player avoids tools falling from the sky while collecting a point for each door reached. In the updated version, Mario (or Wario) also collects coins.

<figure>
    <img src="/assets/image/posts/th-pm/th-inspiration-helmet-gb.jpg" height=144 width=160>
    <figcaption>Gameplay image of the 'modern' version. Source: Chris Martin at <a href="https://www.mobygames.com/game/gameboy-color/game-watch-gallery-2/screenshots/gameShotId,24821/">MobyGames</a></figcaption>
</figure> {: .half}

I played a LOT of this game in 1999-2000 and when thinking of an interesting small project to learn with, it popped back into my head over two decades later. So in late 2024 I set up a project and initially was just going to clone Helmet directly for practice, but then decided to create a full small game based off of the same core loop. 

## Planning

I set up a repository and project tracking via GitHub Projects for simplicity and agility due to the small size. 


## Movement - Flaws and 'Temporary' Hacks

### Slot-Based Movement

<p>The original Helmet handheld game's movement consisted of a few preset positions that the character would simply teleport between (since the handheld did not actually move pixels around, just had preset positions for the character to appear in on the LCD). The GameBoy version did the same, but did animate movement between each of those positions. I emulated this for Tunnel Hazard by always moving the player between preset intervals equivalent to the background's tile size (64 pixels). These slots are also aligned to where loot items appear and to the paths of objects falling from the sky - to prevent the player from being able to safely stand in between object paths.</p>
<p>This system is Not Great.</p>
<p>Player movement is constrained to specific intervals and is bounded on either end by the doors (which are trigger areas for a somewhat hacky knockback system when closed). Movement progress is calculated each update call and the player snaps to the goal position if passing that space without an input being given to continue or reverse. A slight tap to the movement inputs results in moving all the way to the next position.</p>

```gdscript
movement_progress += speed
if movement_progress >= 1:
    position = move_start_pos + Vector2(GameConstants.TILE_SIZE * movement, 0)
    movement_progress = 0
    set_state(PlayerState.IDLE)
    $RunningDust.emitting = false
    process_input()
position = move_start_pos + Vector2(GameConstants.TILE_SIZE * movement * movement_progress, 0)
```

### Getting the player out of the door

<p>I had a bit of a challenge when needing the player to walk through the left door (entrance) at the beginning of each stage, since the space between safely hidded and out past the doorway was more than a movement 'slot' or background tile. The temporary solution was to add a movement "lock" which would force the character to move two 'slots' to the right initially, then unlock. This lock status is checked every time movement passes the next 'slot' position and is disabled, providing a 2 * tile size movement just once when set. Every stage transition sets the player status to moving right, then enables the lock.</p><p>Does it work?<strong>Yes</strong>.<br />Is it good? <strong>Nope!</strong></p>

```gdscript
# Part of the movement code within _process
movement_progress += speed
if movement_progress >= 1:
       if lock_walk:
        lock_walk = false
        position = move_start_pos + Vector2(GameConstants.TILE_SIZE * movement, 0)
        movement_progress = 0
        move_start_pos = position
        return
    position = move_start_pos + Vector2(GameConstants.TILE_SIZE * movement, 0)
    movement_progress = 0
    set_state(PlayerState.IDLE)
    $RunningDust.emitting = false
    process_input()
    return
```

This temporary hack is still there in the 1.0 release. It generally works, but there are some odd behaviors that occur if the user attempts to move while "locked" which can sometimes show the character briefly moving in the wrong direction before resetting.

#### Better Approach

<p>If I was starting anew, I would just drop the slot-based movement altogether for a much simpler free-form physics movement using collision checks at the boundaries. This is planned for full overhaul in the first post-release update.</p>