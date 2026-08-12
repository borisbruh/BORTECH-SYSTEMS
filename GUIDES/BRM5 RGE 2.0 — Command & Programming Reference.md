# BRM5 RGE 2.0 — Command & Programming Reference

> **Status:** Work in progress
>
> **Game:** Roblox — Blackhawk Rescue Mission 5 (BRM5)
> 
> **System:** RGE 2.0
> 
> **Purpose:** Reference for writing and understanding RGE commands and trigger-based programs.

---

## Table of Contents

* [1. RGE Overview](#1-rge-overview)
* [2. Maps, Worlds, Objects & Models](#2-maps-worlds-objects--models)
* [3. The Console](#3-the-console)
* [4. Command Syntax](#4-command-syntax)
* [5. UIDs & Names](#5-uids--names)
* [6. Trigger Groups](#6-trigger-groups)
* [7. Trigger Objects](#7-trigger-objects)
* [8. Trigger Whitelists](#8-trigger-whitelists)
* [9. Trigger Execution](#9-trigger-execution)
* [10. Naming Convention](#10-naming-convention)
* [RGE Programming Model](#rge-programming-model)
* [11. Command Reference](#11-command-reference)
* [12. File](#12-file)
* [13. World](#13-world)
* [14. Time](#14-time)
* [15. Kick](#15-kick)
* [16. Ban](#16-ban)
* [17. Serverlock](#17-serverlock)
* [18. Squadchanging](#18-squadchanging)
* [19. Firstperson](#19-firstperson)
* [20. Vehiclespawning](#20-vehiclespawning)
* [21. Compounds](#21-compounds)
* [22. Friendlyfire](#22-friendlyfire)
* [23. HUD](#23-hud)
* [24. Revive](#24-revive)
* [25. Squadspawn](#25-squadspawn)
* [26. Move](#26-move)
* [27. Tween](#27-tween)
* [28. Create](#28-create)
* [29. Size](#29-size)
* [30. Color](#30-color)
* [31. Transparency](#31-transparency)
* [32. Delete](#32-delete)
* [33. Duplicate](#33-duplicate)
* [34. Rename](#34-rename)
* [35. Material](#35-material)
* [36. Collision](#36-collision)
* [37. Spawn](#37-spawn)
* [38. Squad](#38-squad)
* [39. Respawn](#39-respawn)
* [40. Teleport](#40-teleport)
* [41. Weather](#41-weather)
* [42. Bot](#42-bot)
* [43. Trigger](#43-trigger)
* [44. Wait](#44-wait)
* [45. Explosion](#45-explosion)
* [46. Reset](#46-reset)
* [47. Undo](#47-undo)
* [48. Command Availability](#48-command-availability)
* [49. Known Quirks & Warnings](#49-known-quirks--warnings)
* [50. Documentation Status](#50-documentation-status)

---

# 1. RGE Overview

**RGE** is BRM5's map editing and server control system.

It can be used inside a **Private Server (PS)** to modify the loaded map, control players, manipulate objects, create trigger-based systems, and more.

The RGE console can be opened by pressing:

```text
P
```

inside a Private Server, it will only work if you have admin perms on the ps.

A Private Server can be joined using a link containing a unique identifier, for example:

```text
123a4567-8b9c-01d2-abc1-efg123111a1a
```

The Private Server hosts the current:

* Map/file
* Worlds
* Objects
* Trigger groups
* Players
* Bots
* Other RGE-related state

---

# 2. Maps, Worlds, Objects & Models

## Maps / Files

A **map**, also referred to as a **file**, contains the saved RGE environment.

Files are stored in the Roblox user's map collection.

The collection appears to have a limit of approximately **50 files**.

When this documentation says **"the map"**, it means the file currently loaded into the server.

---

## Worlds

A **world** is a container within a map.

Objects such as:

* Parts
* Wedges
* Corners
* Cylinders
* Balls
* Models/groups
* Trigger objects
* Bots

exist within worlds.

Worlds are referenced using:

```text
[world:number>0]
```

Example:

```text
1
```

The exact maximum world number is currently unknown.

---

## Objects

An object can be:

* Part
* Wedge
* Corner
* Cylinder
* Ball
* Model/group

Objects have properties including:

```text
World
UID
Name
Transparency
Material
Color {r, g, b}
IsTrigger
IsButton
Size {sX, sY, sZ}
Position {pX, pY, pZ}
Orientation {rX, rY, rZ}
Collision Characters
Collision Bullets
```

Except Models/groups which only have:

```text
World
UID
Name
Position {pX, pY, pZ} (the average position of all the parts in the model/group)
Orientation {rX, rY, rZ}
```


Not every property can necessarily be changed by a normal command.

---

## Models / Groups

A model/group is a collection of parts grouped together, for example using:

```text
Ctrl + G
```

Models can be saved to the user's collection and later inserted into worlds.

---

# 3. The Console

The RGE console is where commands are entered to modify the Private Server.

For practical purposes, the console has very high-level access to the server.

Commands that can be executed by the console are not necessarily executable from trigger-group lines.

For example:

```text
trigger executable
```

works from the console but cannot be executed from a trigger-group line because it requires a client.

The game reports in the console:

```text
Must be called from client
```

when this restriction is violated.

---

# 4. Command Syntax

This documentation uses the following notation.

## Required argument

```text
[name:string]
```

The command requires a string value.

---

## Number

```text
[time:number]
```

A numerical value.

---

## Positive world number

```text
[world:number>0]
```

A world number greater than `0`.

---

## Object UID or name

```text
[uid/%name:string]
```

The object can be identified using either:

* Its UID
* Its name prefixed with `%`

Example:

```text
4db00adf-e297-43cf-9a02-50e48a53a0cc
```

or:

```text
%A_PART
```

---

## Restricted values

```text
[color:string{red/blue/orange/yellow/green}]
```

The argument must be one of the listed values.

---

## Boolean

Some commands use:

```text
[boolean{true/false}]
```

Example:

```text
true
false
```

---

# 5. UIDs & Names

Every object has a UID.

### Important: UIDs are not persistent.

Object UIDs change when:

* The server is restarted
* A map is loaded
* A map is reloaded

Therefore, code relying on hard-coded UIDs can break after the next server/map load.

Object names do not change automatically when this happens.

### Recommendation

Use **unique object names** whenever possible.

For long-term RGE systems, a unique name is generally much more useful than hard-coding a UID.

Example:

```text
%CIVI_ARM
```

rather than relying on:

```text
4db00adf-e297-43cf-9a02-50e48a53a0cc
```

---

# 6. Trigger Groups

A **trigger group (TG)** is a collection of executable RGE command lines.

Trigger groups are the primary mechanism for creating programmable systems in RGE.

A trigger group has:

```text
Name
Status
Color {r, g, b}
Whitelist
Lines
```

Trigger groups exist separately from normal objects inside a world.

Because of this, an object and a trigger group can have the same name without conflicting.

---

## Trigger Group Names

Trigger group names are unique within their world.

Therefore, `%` is not required when referencing a trigger group.

Example:

```text
TEST_TRIGGER
```

rather than:

```text
%TEST_TRIGGER
```

---

# 7. Trigger Objects

A normal object can be converted into a trigger object using:

```text
trigger add
```

Trigger objects have two important properties:

```text
IsTrigger
IsButton
```

---

## IsTrigger

When:

```text
IsTrigger = true
```

the object becomes a detection volume.

Its detection volume is based on the object's size.

The object:

* Has no character/bullet collision
* Has transparency forced to `1.000`
* Cannot have its transparency changed while it remains a trigger
* Is invisible through normal gameplay
* Detection volume can only be seen when in the RGE Studio camera
* Can detect eligible entities inside its volume

The detection object can be linked to one or more trigger groups.

---

## IsButton

When:

```text
IsButton = true
```

the trigger object becomes an interactable button as well.

Players in game that are close to the center to the obj can see an:

```text
F
```

interaction prompt and press `F` to activate the linked trigger group.

`IsButton` requires `IsTrigger` to be true.

Therefore:

```text
IsTrigger = false
IsButton = false
```

is valid, while:

```text
IsTrigger = false
IsButton = true
```

is invalid.

---

## IsTrigger / IsButton relationship

The relationship is:

```text
IsTrigger = false
└── IsButton must be false

IsTrigger = true
├── IsButton = false
└── IsButton = true
```

Adding a button to an object whose `IsTrigger` is false, fails.

Example:

```text
trigger addbutton 1 %TestObj
```

can produce:

```text
Invalid argument #3 (uid)
```

if `TestObj` is not already a trigger.

---

## Removing a Button

Removing a button does **not** remove the trigger.

Therefore:

```text
trigger removebutton
```

causes:

```text
IsButton = false
```

while leaving:

```text
IsTrigger = true
```

---

## Removing a Trigger

When:

```text
trigger remove 1 %TestObj
```

is executed:

```text
IsTrigger = false
IsButton = false
```

The object's character and bullet collisions are also restored.

Therefore:

```text
Collision Characters = true
Collision Bullets = true
```

---

## Trigger links survive IsTrigger changes

Suppose:

```text
DetectArea
```

is linked to:

```text
TEST_TG
```

If `IsTrigger` is turned off, the link is **not destroyed**.

If `IsTrigger` is subsequently turned back on, the object remains linked to:

```text
TEST_TG
```

This means disabling/re-enabling a trigger does not necessarily require reconnecting the trigger group.

---

# 8. Trigger Whitelists

Trigger groups have five whitelist settings:

```text
Players
Bots
Helicopters
Ground
IsLooping
```

The first four determine what can activate a trigger through a detection object.

---

## Players

Detects players.

Detection appears to be based on the center of the player's character.

It does not matter whether the player is currently inside a vehicle.

---

## Bots

Detects AI bots.

Detection appears to be based on the center of the bot.

---

## Helicopters

Detects aerial vehicles.

Detection appears to be based on the center of the vehicle.

---

## Ground

Detects ground vehicles.

Detection appears to be based on the center of the vehicle.

---

## Whitelist Logic

The four detection categories operate as **OR conditions**.

For example:

```text
Players = true
Ground = true
Bots = false
Helicopters = false
```

means:

Activate when a player **OR** a ground vehicle enters the detection area.

Each linked trigger group checks its own whitelist independently.

Example:

```text
DetectionArea
├── TEST_PLAYERS
│   └── Players = true
│
├── TEST_GROUND
│   └── Ground = true
│
└── TEST_HELI
    └── Helicopters = true
```

A player can activate `TEST_PLAYERS` without activating the other groups.

A ground vehicle can activate `TEST_GROUND`.

A helicopter can activate `TEST_HELI`.

---

## Buttons and Whitelists

Button interaction is different from detection.

When a player presses `F` on a button, the linked trigger group activates **regardless of its detection whitelist**.

### Recommended button setup

For a button, it is generally desirable to set:

```text
Players = false
Bots = false
Helicopters = false
Ground = false
```

Otherwise, simply walking/bringing an eligible entity into the button's detection volume can activate the group without the player pressing `F`.

---

# 9. Trigger Execution

When a trigger group is activated, it immediately begins executing its lines.

Example:

```text
Line 1
Line 2
Line 3
Line 4
```

Execution proceeds:

```text
Line 1
  ↓
Line 2
  ↓
Line 3
  ↓
Line 4
```

There is no line limit currently known.

---

## Commands normally execute immediately

RGE does not generally wait for one command to finish before executing the next line.

Most commands complete immediately, assuming no server lag.

The two important exceptions are:

```text
tween
wait
```

---

## Tween

`tween` takes a duration and animates an object during that period.

For example:

```text
tween 1 %DOOR 5 10 0 0 0 90 0
```

starts a five-second animation.

---

## Wait

`wait` pauses trigger-group execution for the specified duration.

Conceptually, it behaves similarly to:

```text
sleep(duration)
```

Example:

```text
tween 1 %DOOR 5 10 0 0 0 90 0
wait 5
```

This is useful when subsequent commands should occur after the tween has finished.

---

## Trigger Group Status

A trigger group has two statuses:

```text
Active
Inactive
```

### Active

The trigger group is ready for normal activation.

### Inactive

The trigger group has been activated and is no longer normally available for another detection activation until its reset.

It can still be manually activated through the console/GUI.

---

## Activation

When a trigger group is activated:

```text
Active
   ↓
triggered
   ↓
Inactive
```

Its lines immediately begin executing until completion.

---

## Reset

Resetting returns the group to:

```text
Active
```

A trigger group can be reset using:

```text
trigger reset [world:number>0] [triggerGroup:string]
```

A trigger group can also reset itself using:

```text
reset
```

as one of its own executable lines.

---

## IsLooping

`IsLooping` causes the trigger group to repeatedly execute its lines.

Example:

```text
trigger whitelist 3 TEST_TRIGGER IsLooping true
```

Conceptually:

```text
Line 1
  ↓
Line 2
  ↓
Line 3
  ↓
Last line
  ↓
Line 1
  ↓
Line 2
  ↓
...
```

The next iteration begins as soon as the previous iteration reaches its end.

A `wait` can therefore be used to control the frequency of a looping group.

If `IsLooping` is changed to false while an iteration is already executing, the current iteration continues until its final line. It then stops looping.

---

## Multiple Trigger Groups

One object can be linked to multiple trigger groups.

For example:

```text
DetectionArea
├── DOOR_OPEN
├── LIGHT_ON
└── ALARM
```

When the object activates, the linked groups execute independently/in parallel-ish, subject to server scheduling and lag.

---

## Continuous Detection

Detection objects can repeatedly attempt to activate their linked trigger groups while eligible entities remain within their detection volume.

This can happen extremely rapidly.

Because of this, trigger systems can unintentionally become extremely high-frequency.

---

## Trigger Spam / Engine Quirk

A trigger group containing something like:

```text
explosion ...
reset
```

can behave incorrectly when continuously activated at extremely high frequency.

In testing, the group may execute only once and then require a manual reset.

Adding an extremely short wait such as:

```text
wait 0.00000001
```

can cause the sequence to execute repeatedly.

However, this can produce extreme server lag and should **not** be considered a safe technique.

---

# 10. Naming Convention

A consistent naming convention is strongly recommended.

The naming style itself should communicate what an object is used for.

This is the naming convention that I use.

---

## Movable Objects / Models — `lower_snake_case`

Examples:

```text
light_switch_on
clear
open_main_gate
alarm
```

This makes it immediately obvious that the object is intended to be manipulated/moved.

---

## Trigger Groups — `UPPER_SNAKE_CASE`

Examples:

```text
CIVI_ARM
HEAD
LEFT_GATE
CAR_DOOR
```

---

## Trigger-Linked Objects — `Camel_Snake_Case`

Examples:

```text
Car_Steering_Wheel_Right
Chair
Door_Button
Detection_Area
```

This makes trigger-related objects distinguishable from ordinary movable objects.

---

## Recommended Minimum Name Length

Use at least **two characters** for names whenever practical.

More importantly, names should be:

* Unique
* Descriptive
* Consistent
* Stable between map/server loads

---

# RGE Programming Model

Before you move on, you should read [RGE Programming Model](https://github.com/borisbruh/BORTECH-SYSTEMS/blob/main/GUIDES/RGE%20Programming%20Model.md), it will make understanding all of RGE trigger systems and commands, much easier.

It goes over how they all tie in together and connect to make programs.

After you have read that, the following will be much easier to understand.

---

# 11. Command Reference

The following sections document the known commands.

There is a great [open-source BRM5 commands IDE by triple-alt](https://triple-alt.github.io/brm5-command-editor/editor.html)
it can used as a secondary reference for command syntax and currently documents almost all commands

> **Important:** The IDE is not treated as the final authority on undocumented/hidden behavior. In-game testing takes precedence where the two differ.

---

# 12. File

**Save/load RGE 2.0 files.**

Used to save/load maps and models in the user's collections.

> "The map" means the map/file currently loaded in the server.

```text
file save [name:string]
```

Saves the current map as a file.

```text
file load [name:string]
```

Loads a file.

```text
file delete [name:string]
```

Permanently deletes a file.

```text
file loadcopy [name:string]
```

Loads a copy of a file onto the server.

```text
file savecopy [name:string]
```

Saves a copy of the current map into the file list.

```text
file list
```

Lists saved files.

```text
file savemodel [name:string] [world:number>0] [uid/%name:string]
```

Saves the specified model into the asset browser.

```text
file deletemodel [name:string]
```

Deletes the specified model from the asset browser.

```text
file insertmodel [world:number>0] [name:string] [pX:number] [pY:number] [pZ:number] [r:number]
```

Inserts a saved model into the world.

> **Trigger-group limitation:** `insertmodel` cannot be used from a trigger group because trigger groups do not have access to the player's model collection.

---

# 13. World

**[DEPRECATED] Save/load RGE 1.0 worlds.**

> ⚠️ Use `file` for RGE 2.0 files.

```text
world list
```

Lists saved RGE 1.0 worlds/files.

```text
world load [savefile:string] [world:number>0]
```

Loads a world saved using RGE 1.0.

---

# 14. Time

**Configure time settings.**

The day/night cycle runs at approximately:

```text
60 seconds real time ≈ 45 seconds in-game
```

```text
time start
```

Starts the day/night cycle.

```text
time stop
```

Stops the day/night cycle.

```text
time set [time:number]
```

Sets the time of day.

```text
time now
```

Returns the current time to the console.

Example:

```text
19.89
```

---

# 15. Kick

**Kick a player.**

```text
kick [player:string]
```

Kicks the specified player.

Example:

```text
kick PlayerName
```

---

# 16. Ban

**Ban a player.**

```text
ban [player:string]
```

Bans the specified player.

Example:

```text
ban PlayerName
```

---

# 17. Serverlock

**Lock or unlock the Private Server.**

When locked, players cannot join the server.

```text
serverlock enable
```

Prevents players from joining.

```text
serverlock disable
```

Allows players to join.

---

# 18. Squadchanging

**Configure whether players can change squads.**

BRM5 has public squads accessible through the radial menu:

* Default
* Blue
* Red
* Orange
* Yellow
* Green

Players can also create private squads and invite other players.

```text
squadchanging enable
```

Enables squad changing.

```text
squadchanging disable
```

Disables squad changing.

---

# 19. Firstperson

**Configure whether players can change perspective.**

```text
firstperson lock
```

Locks the perspective to first person.

```text
firstperson unlock
```

Allows players to change perspective.

---

# 20. Vehiclespawning

**Configure whether players can spawn vehicles.**

Vehicle spawning normally uses the ground and aerial vehicle-spawning tablets placed in RGE.

```text
vehiclespawning enable
```

Enables vehicle spawning.

```text
vehiclespawning disable
```

Disables vehicle spawning.

---

# 21. Compounds

**Configure compound settings.**

Compounds in the main/default map contain pre-spawned enemy personnel and destructible objects.

```text
compounds enable
```

Enables compounds.

```text
compounds disable
```

Disables compounds.

---

# 22. Friendlyfire

**Configure friendly-fire settings.**

```text
friendlyfire disable
```

Disables friendly fire.

```text
friendlyfire squad
```

Enables squad-based friendly fire.

```text
friendlyfire all
```

Enables friendly fire between all players.

> The open-source editor currently represents this command slightly differently (`enable/disable/all`). In-game syntax should be considered authoritative until tested.

---

# 23. HUD

**Configure the heads-up display.**

The HUD can show:

* Ammunition
* Time
* Health
* Exact compass heading

```text
hud enable
```

Enables the HUD.

```text
hud disable
```

Disables the HUD.

---

# 24. Revive

**Configure the revive/downed-player system.**

```text
revive enable
```

Enables reviving.

```text
revive disable
```

Disables reviving.

---

# 25. Squadspawn

**Set the spawn point for a squad.**

```text
squadspawn [pX:number] [pY:number] [pZ:number] [r:number] [color:string{red/blue/orange/yellow/green}]
```

Sets the spawn point for the specified squad.

Position:

```text
{pX, pY, pZ}
```

Rotation:

```text
r
```

---

# 26. Move

**Moves an object in a world.**

```text
move [world:number>0] [uid/%name:string] [pX:number] [pY:number] [pZ:number] [rX:number] [rY:number] [rZ:number]
```

Position:

```text
{pX, pY, pZ}
```

Rotation:

```text
{rX, rY, rZ}
```

### UID example

```text
move 5 4db00adf-e297-43cf-9a02-50e48a53a0cc 10 10 10 0 0 34.896
```

Moves the object to:

```text
Position: {10, 10, 10}
Rotation: {0, 0, 34.896}
```

### Name example

```text
move 5 %A_PART 5 5 5 0 45 0
```

Moves `A_PART` to:

```text
Position: {5, 5, 5}
Rotation: {0, 45, 0}
```

> ⚠️ Duplicate object names can cause ambiguous or unexpected behavior.

---

# 27. Tween

**Animates an object to a position and rotation.**

```text
tween [world:number>0] [uid/%name:string] [duration:number] [pX:number] [pY:number] [pZ:number] [rX:number] [rY:number] [rZ:number]
```

Example:

```text
tween 1 %BASKETBALL 5 3 12 4.5 0 0 0
```

Animates `BASKETBALL` over five seconds to:

```text
Position: {3, 12, 4.5}
Rotation: {0, 0, 0}
```

> ⚠️ Large numbers of simultaneous tweens can cause significant lag.

---

# 28. Create

**Creates a basic object in a world.**

```text
create [world:number>0] [type:string{part/wedge/corner/cylinder/ball}] [pX:number] [pY:number] [pZ:number]
```

Types:

```text
part
wedge
corner
cylinder
ball
```

Example:

```text
create 1 part 3 5 4
```

Creates a part in world `1` at:

```text
{3, 5, 4}
```

The created object is normally named:

```text
Part
```

---

# 29. Size

**Resizes an object.**

```text
size [world:number>0] [uid/%name:string] [sX:number] [sY:number] [sZ:number]
```

Example:

```text
size 1 %CUBE 1 2 1
```

Changes the object's size to:

```text
{1, 2, 1}
```

---

# 30. Color

**Changes an object's RGB color.**

```text
color [world:number>0] [uid/%name:string] [r:number] [g:number] [b:number]
```

RGB values range from:

```text
0–255
```

Values above `255` cannot be used.

Example:

```text
color 1 %A 12 12 12
```

Produces a very dark gray.

---

# 31. Transparency

**Changes an object's transparency.**

```text
transparency [world:number>0] [uid/%name:string] [transparency:number]
```

Range:

```text
0 = opaque
1 = transparent
```

Example:

```text
transparency 55 %glass 0.25
```

Sets `glass` to 25% transparency.

> ⚠️ Trigger objects are an exception: when `IsTrigger = true`, transparency is forced to `1.000` and cannot be manually changed.

---

# 32. Delete

**Permanently deletes an object.**

```text
delete [world:number>0] [uid/%name:string]
```

Example:

```text
delete 1 %SPHERE
```

> ⚠️ Treat deletion as permanent. Deleted objects cannot be recovered through the RGE undo system.

---

# 33. Duplicate

**Duplicates an object.**

```text
duplicate [world:number>0] [uid/%name:string]
```

Example:

```text
duplicate 1 %BLOCK
```

Can also be performed manually using:

```text
Ctrl + D
```

---

# 34. Rename

**Renames an object.**

```text
rename [world:number>0] [uid/%name:string] [name:string]
```

Example:

```text
rename 1 %Part CAR_DOOR
```

The open-source command editor documents this command and its syntax.

---

# 35. Material

**Changes an object's material.**

The RGE material list is very large.

```text
material [world:number>0] [uid/%name:string] [material:string]
```

Example:

```text
material 1 %A RBLX/Plastic
```

> **TODO:** Add complete material list.

The command editor contains a large material enumeration that can be used as a reference for the available material names.

---

# 36. Collision

**Alters an object's collision behavior.**

```text
collision [world:number>0] [uid/%name:string] [collision:number{0/1/2/3}]
```

Collision modes:

| Value | Characters | Bullets |
| ----: | :--------: | :-----: |
|   `0` |      ✅     |    ✅    |
|   `1` |      ❌     |    ✅    |
|   `2` |      ✅     |    ❌    |
|   `3` |      ❌     |    ❌    |

Example:

```text
collision 1 %A 2
```

Makes object `A` collide with characters but not bullets.

> The command editor confirms the four collision values, while the exact interpretation above is based on in-game testing/observations.

---

# 37. Spawn

**Places an asset/prop from the game's storage into a world.**

The game contains a large collection of props that can normally be placed through the RGE GUI/Asset Browser.

```text
spawn [world:number>0] [Assetname:string] [pX:number] [pY:number] [pZ:number] [rX:number] [rY:number] [rZ:number]
```

Example:

```text
spawn 1 Zoyas 1 10 1 0 180 0
```

Places `Zoyas` at:

```text
Position: {1, 10, 1}
Rotation: {0, 180, 0}
```

> **Note:** The behavior and available prop names are partly inferred because `spawn` is a hidden command. The open-source IDE contains a prop enumeration and documents the command as spawning a specified prop at a position/rotation. [A thread in the RGE Library discord server](https://discord.com/channels/1494569291557634088/1496857552179429386/threads/1530012926197629008) which has a large list of hidden props.

---

# 38. Squad

**Sets a player's squad.**

```text
squad [player:string] [color:string{red/blue/orange/yellow/green}]
```

Example:

```text
squad bacon1 red
```

Moves player `bacon1` into the red squad.

---

# 39. Respawn

**Respawns players.**

```text
respawn all
```

Respawns all players.

```text
respawn others
```

Respawns all other players.

```text
respawn squad [color:string{red/blue/orange/yellow/green}]
```

Respawns players in the specified squad.

```text
respawn player [player:string]
```

Respawns the specified player.

---

# 40. Teleport

**Teleports players to a position and rotation.**

The command editor documents:

```text
teleport [type] [target] [pX] [pY] [pZ] [rX] [rY] [rZ]
```

with supported target types:

```text
all
squad
player
other
```

Example:

```text
teleport player PlayerName 0 5 0 0 0 0
```

Teleports the specified player to:

```text
Position: {0, 5, 0}
Rotation: {0, 0, 0}
```

The command's exact in-game behavior for every target type should be considered **partially unverified** until tested.

---

# 41. Weather

**Changes weather and lunar settings.**

The command editor documents:

```text
weather lunar [cycle]
```

and:

```text
weather set [type]
```

### Lunar cycle

```text
weather lunar [cycle:string{1/2/3/4/5/6/7/8}]
```

Sets the lunar cycle.

Example:

```text
weather lunar 2
```

### Weather

```text
weather set [type:string]
```

Known weather types in the command editor include:

```text
ClearSky
RonoClearSky
Overcast1
Overcast2
Overcast3
Fog1
Fog2
Fog3
Rain1
Rain2
Rain3
Storm1
Storm2
Storm3
```

Weather can apparently be cleared using:

```text
weather set
```

The weather command was added to the open-source editor in April 2026.

---

# 42. Bot

**Spawns and controls AI bots.**

The command editor currently documents four subcommands.

## Spawn

```text
bot spawn [world:number] [name:string] [pX:number] [pY:number] [pZ:number] [rotation:number]
```

Spawns a bot.

Example:

```text
bot spawn 1 Guard 0 0 0 0
```

---

## Delete

```text
bot delete [bUID:uid]
```

Deletes the specified bot.

---

## Alert

```text
bot alert [world:number] [boolean{true/false}]
```

Alerts or calms bots in the specified world.

---

## Direct

```text
bot direct [bUID:uid] [mode:string{weak/strong/direct}] [pX:number] [pY:number] [pZ:number]
```

Directs a bot toward a position using the specified priority mode.

Example:

```text
bot direct 4db00adf-e297-43cf-9a02-50e48a53a0cc strong 10 0 5
```

> **TODO:** More in-game testing is needed for the exact meaning and priority behavior of the three modes.

---

# 43. Trigger

**Creates and manages trigger objects and trigger groups.**

Trigger commands are the core of RGE's programming system.

---

## Add Trigger

```text
trigger add [world:number>0] [uid/%name:string]
```

Makes an object a trigger.

This causes:

```text
IsTrigger = true
```

and:

```text
IsButton = false
```

The object becomes:

* Collision-less
* Fully transparent
* A detection volume

---

## Add Button

```text
trigger addbutton [world:number>0] [uid/%name:string]
```

Makes a trigger object also interactable by players.

Requires:

```text
IsTrigger = true
```

The player will see an `F` interaction prompt near the center of the trigger object.

---

## Remove Button

```text
trigger removebutton [world:number>0] [uid/%name:string]
```

Removes the button functionality.

It leaves:

```text
IsTrigger = true
```

unchanged.

Therefore:

```text
IsTrigger = true
IsButton = false
```

---

## Remove Trigger

```text
trigger remove [world:number>0] [uid/%name:string]
```

Removes the trigger from an object.

This results in:

```text
IsTrigger = false
IsButton = false
```

and restores normal character/bullet collisions and transparency to 0.

---

## Color

```text
trigger color [world:number>0] [triggerGroup:string] [r:number] [g:number] [b:number]
```

Changes the trigger group's/displayed trigger color.

Example:

```text
trigger color 1 TEST_TRIGGER 255 0 0
```

---

## Set

```text
trigger set [world:number>0] [uid/%name:string] [triggerGroup:string] [enable:boolean{true/false}]
```

Links or unlinks a trigger group from an object.

### Link

```text
trigger set 3 %TestBox TEST_TRIGGER true
```

Links:

```text
TestBox
    ↓
TEST_TRIGGER
```

### Unlink

```text
trigger set 3 %TestBox TEST_TRIGGER false
```

Unlinks the trigger group.

### Important

Disabling `IsTrigger` on the object does **not** destroy this link.

If `IsTrigger` is later enabled again, the previous trigger-group link remains.

---

## Activate

```text
trigger activate [world:number>0] [triggerGroup:string]
```

Activates a trigger group.

This does nothing when its status is `Inactive`.

---

## Reset

```text
trigger reset [world:number>0] [triggerGroup:string]
```

Resets a trigger group to:

```text
Active
```

### Important

if a line in a trigger group is "reset", and there are more lines of commands after the "reset" line, then the reset will not work
the trigger group will go back to status `Inactive` and continue to execute the rest of the lines of commands

---

## Create

```text
trigger create [world:number>0] [name:string]
```

Creates a trigger group.

Example:

```text
trigger create 1 TEST_TRIGGER
```

---

## Delete

```text
trigger delete [world:number>0] [triggerGroup:string]
```

Deletes a trigger group.

---

## Executable

```text
trigger executable [world:number>0] [triggerGroup:string] [line:number>0] [command:string?]
```

Sets/edits an executable line in a trigger group.

Example:

```text
trigger executable 3 TEST_TRIGGER 5 wait 1
```

sets line `5` to:

```text
wait 1
```

### Nested commands

Because `[command:string?]` accepts a complete command, commands can theoretically modify other trigger lines.

Example:

```text
trigger executable 3 TEST_TRIGGER 3 trigger executable 3 TEST_TRIGGER 5 wait 1
```

This makes line `3` execute:

```text
trigger executable 3 TEST_TRIGGER 5 wait 1
```

which changes line `5`.

### Trigger-group limitation

`trigger executable` cannot be executed from a trigger-group line.

The game reports:

```text
Must be called from client
```

when attempted from a TG.

---

## Whitelist

```text
trigger whitelist [world:number>0] [triggerGroup:string] [whitelist:string] [enable:boolean{true/false}]
```

Example:

```text
trigger whitelist 3 TEST_TRIGGER Players false
```

Supported whitelist values:

```text
Players
Bots
Helicopters
Ground
IsLooping
```

---

# 44. Wait

**Pauses trigger-group execution.**

`wait` is a trigger-group-line-only command.

```text
wait [duration:number]
```

Example:

```text
wait 1
```

Pauses execution for one second before the next line executes.

It is especially useful after `tween`.

Example:

```text
tween 1 %DOOR 3 10 0 0 0 90 0
wait 3
```

---

# 45. Explosion

**Creates an explosion at a position.**

```text
explosion [radius:number] [damage:number] [pX:number] [pY:number] [pZ:number] [type:string]
```

Known target types:

```text
GroundVehicle
Motar
ObjBig
RPG7v2
Mine
Flash
Breach
Igla
C4
HelicopterVehicle
Grenade
None
```

Example:

```text
explosion 10 50 0 0 0 GroundVehicle
```

Creates an explosion with:

```text
Radius: 10
Damage: 50
Position: {0, 0, 0}
Target: GroundVehicle
```

Explosions can damage players, bots vehicles. From testing Players have 100 hp.

---

# 46. Reset

`reset` can only be used in trigger-group lines.

## Trigger-group line

Inside a trigger group:

```text
reset
```

resets the trigger group containing that line.

This returns its status to:

```text
Active
```

---

# 47. Undo

Undo is not a normal RGE console command.

The user can use:

```text
Ctrl + Z
```

to undo certain editor actions.

However:

> ⚠️ Deleted objects cannot be recovered through this undo system.

Treat `delete` as permanent.

---

# 48. Command Availability

Not every command can necessarily be executed in every context.

There are at least two important execution contexts:

```text
Console
Trigger Group
```

Some commands work in both.

Some only work from the console/client.

Some commands cannot work from trigger groups because they depend on resources belonging to the player.

Example:

```text
file insertmodel
```

requires the user's model collection and therefore cannot be meaningfully executed by a trigger group.

Another example:

```text
trigger executable
```

must be called from the client.

> **TODO:** Build a complete command-context compatibility table through testing.

---

# 49. Known Quirks & Warnings

## Exact syntax matters

RGE commands are sensitive to syntax.

Incorrect:

* Capitalization
* Spacing
* Argument order
* Argument values

can cause commands to fail.

Do not assume the more forgiving parser of an external IDE represents the game's actual command parser.

---

## UIDs change

Do not rely on object UIDs for persistent systems.

Use unique names wherever practical.

---

## Duplicate names

Multiple objects with the same name can cause ambiguous or unexpected behavior when using:

```text
%name
```

Use unique names.

---

## Trigger transparency

When:

```text
IsTrigger = true
```

transparency is forced to:

```text
1.000
```

and cannot be changed normally.

---

## Trigger collision

When a trigger is added:

```text
IsTrigger = true
```

normal character/bullet collision is disabled.

When it is removed:

```text
IsTrigger = false
```

character and bullet collision return.

---

## Buttons can accidentally trigger

A button is also a trigger volume.

Therefore, if a button's linked TG has:

```text
Players = true
```

a player walking into the button can activate it without pressing `F`.

For pure button systems, use:

```text
Players = false
Bots = false
Helicopters = false
Ground = false
```

and let `F` interaction be the intended activation method.

---

## Multiple linked TGs

Multiple trigger groups can be linked to one object.

Each TG independently evaluates its own whitelist.

---

## Extremely short waits

Very short waits can be used to make extremely high-frequency trigger systems execute repeatedly.

Example:

```text
wait 0.00000001
```

However, this can cause severe server performance problems.

Do not use this unless the behavior is specifically intended and the performance impact is understood.

---

## `IsLooping` is not a hard stop

Turning:

```text
IsLooping = false
```

while a loop is already executing does not immediately terminate the current iteration.

The current pass continues to its final line.

The loop then stops.

---

# 50. Documentation Status

| Command                | Status                                        |
| ---------------------- | --------------------------------------------- |
| `file`                 | 🟢 Documented                                 |
| `world`                | 🟢 Documented                                 |
| `time`                 | 🟢 Documented                                 |
| `kick`                 | 🟢 Documented                                 |
| `ban`                  | 🟢 Documented                                 |
| `serverlock`           | 🟢 Documented                                 |
| `squadchanging`        | 🟢 Documented                                 |
| `firstperson`          | 🟢 Documented                                 |
| `vehiclespawning`      | 🟢 Documented                                 |
| `compounds`            | 🟢 Documented                                 |
| `friendlyfire`         | 🟢 Documented                                 |
| `hud`                  | 🟢 Documented                                 |
| `revive`               | 🟢 Documented                                 |
| `squadspawn`           | 🟢 Documented                                 |
| `move`                 | 🟢 Documented                                 |
| `tween`                | 🟢 Documented                                 |
| `create`               | 🟢 Documented                                 |
| `size`                 | 🟢 Documented                                 |
| `color`                | 🟢 Documented                                 |
| `transparency`         | 🟢 Documented                                 |
| `delete`               | 🟢 Documented                                 |
| `duplicate`            | 🟢 Documented                                 |
| `rename`               | 🟢 Documented                                 |
| `material`             | 🟡 Syntax documented; full material list TODO |
| `collision`            | 🟢 Documented                                 |
| `spawn`                | 🟡 Hidden command; behavior partly inferred   |
| `squad`                | 🟢 Documented                                 |
| `respawn`              | 🟢 Documented                                 |
| `teleport`             | 🟡 Needs full in-game testing                 |
| `weather`              | 🟡 Syntax documented; behavior needs testing  |
| `bot`                  | 🟡 Syntax documented; behavior needs testing  |
| `trigger`              | 🟢 Core behavior documented                   |
| `trigger removebutton` | 🟢 Verified in-game                           |
| `wait`                 | 🟢 Documented                                 |
| `explosion`            | 🟢 Documented                                 |
| `reset`                | 🟢 Documented                                 |
| `undo`                 | ℹ️ User-only                                  |

---

# Appendix A — Minimal Trigger Example

A basic detection system can be thought of as:

```text
trigger create 1 door_open
trigger add 1 %DoorDetection
trigger set 1 %DoorDetection door_open true
trigger whitelist 1 door_open Players true
trigger executable 1 door_open 1 move 1 %DOOR 0 0 0 0 90 0
```

Conceptually:

```text
Player enters DoorDetection
        ↓
DoorDetection activates door_open
        ↓
door_open becomes Inactive
        ↓
Line 1 executes
        ↓
DOOR moves
```

---

# Appendix B — Basic Button Example

A pure button should generally have all detection whitelist categories disabled.

```text
trigger create 1 open_door
trigger add 1 %DoorButton
trigger addbutton 1 %DoorButton

trigger whitelist 1 open_door Players false
trigger whitelist 1 open_door Bots false
trigger whitelist 1 open_door Helicopters false
trigger whitelist 1 open_door Ground false

trigger set 1 %DoorButton open_door true

trigger executable 1 open_door 1 move 1 %DOOR 0 0 0 0 90 0
```

The intended activation method is:

```text
Player
  ↓
Press F
  ↓
DoorButton
  ↓
open_door
  ↓
DOOR moves
```

---

# Appendix C — Looping Example

A looping trigger group can repeatedly execute a sequence:

```text
trigger whitelist 1 alarm IsLooping true
```

For example:

```text
explosion 10 50 0 0 0 None
wait 1
```

Conceptually:

```text
Explosion
 ↓
Wait 1 second
 ↓
Explosion
 ↓
Wait 1 second
 ↓
...
```

---

# Appendix D — RGE Programming Mental Model

The most useful way to think about RGE is:

```text
WORLD
│
├── OBJECTS
│   │
│   ├── Parts
│   ├── Models
│   ├── Trigger Objects
│   └── Props
│
└── TRIGGER GROUPS
    │
    ├── Status
    ├── Whitelist
    └── Command Lines
```

An object can act as an **input**:

```text
Player
   ↓
Detection Object
   ↓
Trigger Group
   ↓
Command Lines
   ↓
World changes
```

A button provides another input:

```text
Player presses F
       ↓
Button Object
       ↓
Trigger Group
       ↓
Command Lines
```

Trigger groups can also activate other trigger groups through commands such as:

```text
trigger activate
```

This allows RGE systems to be chained together.

---

# Appendix E — Verified vs. Reference Information

This documentation intentionally distinguishes between three sources of information:

### 🟢 In-game verified

Behavior directly observed/tested in BRM5.

Examples:

* UID changes
* `IsTrigger` behavior
* `IsButton` dependency
* Button whitelist behavior
* Trigger-group execution behavior
* `IsLooping`
* `trigger executable` client restriction
* Trigger-group links surviving `IsTrigger` removal

### 🔵 IDE/source documented

Information found in the open-source BRM5 command editor.

The editor contains structured command definitions, parameters, enums, examples, and command parsing behavior.

Repository:

https://github.com/triple-alt/brm5-command-editor

Online editor:

https://triple-alt.github.io/brm5-command-editor/editor.html

### 🟡 Unverified / needs testing

Anything that is only inferred from the command editor or has not yet been tested thoroughly in-game.

This distinction is important because the external editor is a **command editor/reference**, not the BRM5 game itself.

---

# Final Note

The goal is not merely to list commands, but to build an accurate model of how **RGE actually behaves** so that increasingly complex trigger systems and programs can be written reliably.
