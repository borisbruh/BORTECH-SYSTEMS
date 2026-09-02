if you haven't read the the first part of [BRM5 RGE 2.0 — Command & Programming Reference.md](https://github.com/borisbruh/BORTECH-SYSTEMS/blob/main/GUIDES/BRM5%20RGE%202.0%20%E2%80%94%20Command%20%26%20Programming%20Reference.md). 

I would recommend reading that part first before reading this.

---

Otherwise here's how you combine those commands to make RGE do shit.

---

### RGE Programming Vocabulary

firstly going over some common programming vocabulary that will be used in the following doc.


| Term                      | Meaning                                                            |
| ------------------------- | ------------------------------------------------------------------ |
| **Object (OBJ)**          | A physical entity that can be manipulated by RGE                   |
| **Trigger Object (TOBJ)** | An OBJ made to host Detection, Interaction and the link(s) to TG(s)|
| **Trigger Group (TG)**    | An ordered collection of executable commands                       |
| **Interaction**           | A Player pressing "f" on a TOBJ                                    |
| **Detection**             | An entity entering the trigger object's detection volume.          |
| **Executable**            | A command executed by a line in a TG                               |
| **Activation**            | The event that causes a TG to begin executing                      |
| **Reset**                 | Returns a TG to a state where it can be activated again            |
| **Looping**               | Causes a TG to repeat according to its configuration               |
| **UID**                   | Runtime unique identifier for an object in the form of a hash      |
| **Name**                  | Human-readable identifier used by applicable commands              |
| **Command (cmd)**         | A console command; tween, move, color, trigger, material           |


- - -


> [!IMPORTANT]
> ### Trigger Objects vs Trigger Groups
>
> A Trigger Object is an object in the world that can detect entities
> and/or receive player interaction.
>
> A Trigger Group is a separate programmable sequence of executable
> commands.
>
> A Trigger Object does not contain the executable commands.
> It links to Trigger Groups which contain them.




---


# RGE Programming Model

## 1. What RGE Programming Actually Is

RGE programming is based around objects, triggers, Trigger Groups, and executable commands.

Unlike a traditional programming language such as Lua, RGE does not primarily use scripts containing functions and variables. Instead, behaviour is constructed by configuring objects and connecting triggers to ordered command sequences.

A typical RGE system can be thought of as:

```text
TRIGGER OBJECTS
  │
  │ activation from player interaction / entity detection from a trigger object
  ▼
TRIGGER GROUP
  │
  │ execution of commands in order
  ▼
EXECUTABLE COMMANDS
  │
  ├── move
  ├── tween
  ├── wait
  ├── ...
  │
  ▼
OBJECT STATES CHANGES
```

### Example

A button-controlled door could be structured as:

```text
Player
  │
  │ presses button
  ▼
Door Button Trigger
  │
  │ activates
  ▼
"Door_Open" Trigger Group
  │
  ├── Tween Door → Open position
  ├── Wait 5 seconds
  ├── Tween Door → Closed position
  └── Reset Trigger Group
```

The important distinction is that the button itself does not contain the door's behaviour.

The button provides the event and the object hosts the link to the TG.

The Trigger Group contains the behaviour, the lines of cmds.

The executable cmds perform the actions.



## 2. The Four Main Components

Objects, Triggers Objects (TOBJ), Trigger Groups (TG), Executable Commands

- - -

### Objects (1/4)

Objects are the things being manipulated by RGE.

Examples:

```text
Part
Door
Button
Wedge
Light
Vehicle
Barrier
Model/group
```

Objects can generally be identified by their name and/or UID, depending on the command being used.

Objects provide the physical things that RGE commands operate on.

For example:

```text
Door
```

can be moved, resized, recolored, deleted, material changed, etc.

- - -

### Trigger Objects (2/4)

A Trigger Objects is an object that hosts detection, interaction and the link(s) to activate Trigger Groups.

Examples include:

```text
Button activation
Player detection
Vehicle detection
Other trigger conditions
```

A Trigger Object can be connected to one or more Trigger Groups.

Conceptually:

```text
[BUTTON]
    │
    ├──────► [Door_Open TG]
    │
    └──────► [Room_Light TG]
```

This means one event can cause multiple independent behaviours.

- - -

### Trigger Groups (3/4)

A Trigger Group is an ordered sequence of executable commands. You can think of it similar to a function, just holds commands to be executed.

For example:

```text
Door_Open

1. tween Door ...
2. wait 5
3. tween Door ...
4. wait 5
5. reset ...
```

When the Trigger Group activates, its executable commands are processed in order.

Trigger Groups are therefore the closest equivalent to a program/script within RGE.

- - -

### Executable Commands (4/4)

Executable commands are the individual actions performed by cmds in a TG.

For example:

```text
tween
wait
move
...
```

A TG can combine multiple commands to create more complex behaviour.

For example:

```text
1. tween Door_Left → open
2. tween Door_Right → open
3. wait 5
4. tween Door_Left → closed
5. tween Door_Right → closed
```

The result is a complete door sequence despite there being no traditional "door script".

- - -

## 3. Event → Logic → Action

A useful way to understand RGE is:

```text
EVENT by TRIGGER OBJECT
  ↓
TRIGGER GROUP ACTIVATION
  ↓
COMMAND SEQUENCE
  ↓
ACTION
```

Example: Automatic Door

```text
EVENT by TOBJ
Player enters detection volume and activates the linked TG
        ↓
LOGIC
Door_Open TG gets activated and begins executing its cmd lines
        ↓
COMMANDS
Tween door open
Wait
Tween door closed
        ↓
ACTION
Door physically moves "open" and moves "closed"
```

This distinction is important because an event and an action are not necessarily the same thing.

A TOBJ can activate a TG, so the TOBJ doesn't directly perform the final action itself.

- - -

## 4. Trigger Group Execution

When a TG activates, its executable commands run in their configured order.

For example:

```text
Trigger Group: TESTING_STUFF

1. command A
2. command B
3. command C
```

will execute conceptually as:

```text
A first
↓
B second
↓
C third
```

A few very important distinctions to make tho:

All commands execute instantaneously.

"tween" is the only command that after execution, continues asynchronously in the background, but the next cmd after it, is immediately executed.

"wait" is the only command that intentionally pauses Trigger Group execution.

All other cmds execute instantaneously and complete instantaneously, such as; move, color, trigger, material etc.

For example:

```text
1. tween Door 3 seconds
2. wait 5 seconds
3. tween Door 5 seconds
5. wait 5 seconds
```

can be understood as a timeline:

```text
OPEN

instantly tween command executes, which starts the animation
 ▼
|+|─|───── 3 second animation ─────►|───────────────────|─|─|────────────────────────────────────────────────────|─|

instantly after, wait command executes, which starts wait timer
   ▼
|─|+|────── 5 second wait ─────────────────────────────►|─|─|────────────────────────────────────────────────────|─|

CLOSE

after the 5 seconds wait, instantly tween command executes, which starts the animation
                                                         ▼
|─|─|───────────────────────────────────────────────────|+|─|───── 5 second animation ──────────────────────────►|─|

instantly after, wait command executes, which starts wait timer
                                                           ▼
|─|─|───────────────────────────────────────────────────|─|+|────── 5 second wait ──────────────────────────────►|─|
```

## 5. Trigger Group State

Trigger Groups have their own state.

A Trigger Group will become INACTIVE when activated and will need to be reset before it can be activated normally again.

For example:

```text
ACTIVE
   │
   │ TOBJ activates TG
   ▼
INACTIVE (executing)
   │
   │ commands finish
   ▼
INACTIVE (done executing)
   │
   │ reset
   ▼
ACTIVE
```

This is important when designing reusable systems.

For example, a door that should work repeatedly will need:

```text
Button
  ↓
Door_Open_&_Close TG
  ↓
Opens
  ↓
Waits
  ↓
Closes
  ↓
Reset TG
```

## 6. Trigger Groups are functions or Small Programs



A Trigger Group can be thought of as being similar to a function or small program / embedded system that runs when a linked trigger activates it.

> This is an analogy, not an indication that RGE supports
> traditional function semantics, parameters, return values,
> local variables, or recursion.

And you can also have them infinitely looping by setting the whitelist IsLooping to true.

For example:

```text
Trigger Group: Emergency_Lights
Whitelist: IsLooping = true

1. Color Light_A Red
2. Color Light_B Red
3. Color Light_C Red
4. Wait 1
5. Color Light_A Dim-Red
6. Color Light_B Dim-Red
7. Color Light_C Dim-Red
8. Wait 1
```

A more complicated system can use multiple Trigger Groups:

```text
             ┌──► TG - Door_Open    ───► TG - Door_Close
Button ──────┤
             └──► TG - Door_Lights
```

This allows individual pieces of behaviour to be separated rather than putting everything into one enormous TG.

- - -

## 7. Console vs Trigger Group Programming

RGE has an important distinction between commands entered through the console and commands that can be executed by a Trigger Group.

For example:

```text
CONSOLE
    │
    ├── Create/configure objects
    ├── Create/configure triggers
    ├── Configure Trigger Groups
    └── Insert executable commands
```

Whereas:

```text
TRIGGER GROUP
    │
    └── Executes supported runtime commands
```

Not every command available through the console should automatically be assumed to be valid as a Trigger Group executable command.

Command-context compatibility should therefore be checked against the command compatibility reference.

- - -

## 8. Building an RGE System

Most RGE systems can be approached in this order:

```text
1. Define requirements
        ↓
2. Create/Design and configure objects
        ↓
3. Define the event(s)
        ↓
4. Create/configure triggers
        ↓
5. Create Trigger Groups
        ↓
6. Add executable commands
        ↓
7. Connect triggers → Trigger Groups
        ↓
8. Configure reset/loop/state behaviour
        ↓
9. Test the system
```

For example, when building a door:

```text
DEFINE REQUIREMENTS
├── Door object, size {0.5, 8, 4}
├── Detection trigger, volume {10, 10, 10}
└── Desired opened & closed positions, move door position {X, Y, Z}.

BUILD
├── Create/Design & Configure Door
└── Create Detection Trigger Volume

PROGRAM
├── Create & Configure Door_Open TG 
├── Add tween
├── Add wait
├── Add Activate Door_Close TG
└── Add reset (always last)
├── Create & Configure Door_Close TG
├── Add tween
├── Add wait
└── Add reset (always last)

CONNECT
└── Button → Door_Open

TEST
├── Test Detection Volume
├── Verify opening
├── Verify timing
├── Verify closing
└── Verify it can be triggered again
```

- - -



I've went over what can be done (almost anything btw).
Ill now go over what cannot be done

## RGE Programming Limitations

RGE is not a general-purpose scripting language.

The current documented model does not provide traditional:

- Variables
- Functions with arguments
- Return values
- Conditional statements, so no "if"
- Loops using a conventional programming syntax
- Arithmetic expressions, so no math
- Boolean expressions
- No randomness (but pseudo-randomness does exist)
- A TG has no idea what its doing, it just gets told to run, it asks no questions
- No state querying, you just have to interpret what state it should/might/could be in
- Cmds give no "feedback", as in, if you whitelist player true a trigger object, and its already true, console will just say, "Successfully changed", no matter what state it was previously

Instead, state and logic are generally represented through:

- Object properties
- Trigger Group status
- Trigger whitelists
- Multiple Trigger Groups
- Trigger activation
- Object positions/properties
- Looping TGs


All simple and complex RGE programs are just pre-programmed states that can be switched between.



Let me set out a simple example:

```text
Lets say you want to have a door that opens to 2 different positions,
depending on if a player is detected, smaller opening,
or is a ground vehicle is detected, larger opening.

You (currently) **cannot** have 1 Trigger Group do both,
and just tell it to open however much depending on what is detected.

To achieve this behaviour you would need:
2 Trigger Groups; "OPEN_PLAYER" & "OPEN_GROUND_VEHICLE"
```



Now for a more complex example:

```text
In the repo you will find a mortar platform made by me

the mortar's elevation can be adjusted, in intervals of 5 degress, from 45 degrees to 75.
so that's 7 different states: 45, 50, 55, 60, 65, 70 & 75 degrees.
each needed positions to be found and ofc coded
now then I wanted some azimuth (sideways) movement as well for MK. II
so I added 2.5 degrees azimuth rotation to the mortar, both ways
so now there are 7 * 3 = 21 states, which need positions and to be coded
now that may not sound too difficult until you find out that the mortar isn't 1 piece
There are like roughly 8 objects/models/groups,
so now is becomes 21 * 8 = 168 different positions that all need to be found and coded.
And I haven't even gotten into niche/specific states that require other states to be a specific state
and that's how quickly work explodes
and how you start to get very spaghetti code

and there is no debugger, not even good old print statements
and the console will only tell you if a cmds is incorrectly typed
or if a UID# doesn't exist, which usually means something is missing or again a typo.

essentially debugging RGE Trigger Group lines, manually, is a very bad experience

```


But, even with these, honestly, extreme conditions (the biggest thing I think is; no "if" or conditional cmd / statement / argument),
I still believe that anything can be made in RGE, given enough time.





---


now that you hopefully know and understand how programs work and are made in RGE.
Reading the rest of [BRM5 RGE 2.0 — Command %26 Programming Reference.md#rge-programming-model](https://github.com/borisbruh/BORTECH-SYSTEMS/blob/main/GUIDES/BRM5%20RGE%202.0%20%E2%80%94%20Command%20%26%20Programming%20Reference.md#rge-programming-model) should hopefully be much easier.

I hope you enjoy reading the rest.
