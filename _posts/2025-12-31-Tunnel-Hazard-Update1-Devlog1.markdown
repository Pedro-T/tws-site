---
layout: single
title:  "Devlog: Tunnel Hazard Update 1"
excerpt: "Development update and notes for Update 1 of a small web game"
date:   2025-12-31 14:10:39 -0500
tags: godot 2d tunnel-hazard devlog
categories: devlog
permalink: /posts/th-devlog1/
toc: true
toc_label: "Contents"
toc_sticky: true
---

<p>This week's free time was dedicated to the first post-release update for <a href="/projects/tunnel-hazard/" target="_blank">Tunnel Hazard</a>. Update one has three core parts:</p>
<ul>
<li>Overhaul player movement</li>
<li>Add two new stage background, with additional detail objects and new art</li>
<li>A series of smaller fixes and improvements such as object pooling and tweaks to the ending screen</li>
</ul>

## Changing Tracking Platform

<p>I also moved fully to a self-hosted instance of JetBrains YouTrack, making 1.0 the cut-off point for the existing GitHub Project. My primary gripe with Projects was the lack of depth to work item types and so on - too much reliance on labels. YouTrack is pretty nice so far and is free for up to ten users. As I'm primarily expecting to work solo or rarely in very small teams, this is unlikely to be a problem.</p>

<a href="/assets/image/posts/th-devlogs/devlog1/youtrack_board.png" target="_blank"><img src="/assets/image/posts/th-devlogs/devlog1/youtrack_board.png" alt="Image of the YouTrack project board, showing categories and swimlanes."></a>


## Part One - Player Movement Rework

<p>This was mentioned in the post-mortem as a point of player feedback and by the time the game was done, I was sort of over the movement system and its segmented nature. For this update I dropped the system entirely in favor of a simple left/right movement as short or long as the user wants. It's simpler to work with, offers more consistent behavior, and uses a third of the amount of code now.</p>

```gdscript
func _physics_process(delta: float) -> void:
    ... # other stuff irrelevant to movement

    else:
        process_input()
    move_and_slide()

func process_input() -> void:
    ... # other stuff irrelevant to movement

    var movement: float = Input.get_axis("move_left", "move_right")
    if movement != 0:
        set_state(PlayerState.WALK_LEFT if movement == -1 else PlayerState.WALK_RIGHT)
        velocity.x = WALK_SPEED * movement
    else:
        velocity.x = move_toward(velocity.x, 0, WALK_SPEED)
        set_state(PlayerState.IDLE)
```

<p>That's it. Much simpler.</p>

<p>Of course there were some other concepts that relied entirely on this manner of movement. For example, the falling projectiles spawned from eight specific nodes above the viewable area - aligned of course with the eight positions that a player could stand in between the entry and exit doorways. With that restriction removed, the player can simply stand in between two 'lanes' of falling items and be completely safe. This isn't the intent, so the nodes were dropped and the spawn controller now simply chooses a random spot between two bounds.</p>


## Part Two - New Stages & Art

<p>Two new stage backgrounds are also included, bringing the total to six variants. There are three themes (red, gray, and now brown) with slightly different designs and two backgrounds for each. The brown theme is less brick-heavy and incorporates dirt/rocks as well as a new exit setup: a mineshaft elevator.</p>
(image of elevator)

<p>As with the previous stages, this uses a mixture of TileMapLayer nodes and various Sprite2D/AnimatedSprite2D for smaller details. Speaking of sprites, several vending machine variants are now present along with an abandoned microwave oven and pallet for more debris variety.</p>

TODO (gallery of vending machine a/b, microwave)

<p>The vending machines are part of an attempt to start using layers more efficiently. Each body color is a different layer, with additional layer variants of glass (broken/intact), items in the machine, and grime on the machine. I don't need a ton of variants, but it would be simple to create over a dozen different machine sprites like this.</p>

TODO (webm of layer manipulation)


## Part Three - Tweaks and Object Pooling

(fixes)
(object pooling, and the leak issue plus _exit_tree handling)


## Check it Out on Itch

<p>Update 1 is already live by the time this is being posted, so check it out if you're interested and I would greatly appreciate any feedback.</p>

(itch embed link)





