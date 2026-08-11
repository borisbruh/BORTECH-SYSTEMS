if you haven't read the the first part of [BRM5 RGE 2.0 — Command & Programming Reference.md](https://github.com/borisbruh/BORTECH-SYSTEMS/blob/main/GUIDES/BRM5%20RGE%202.0%20%E2%80%94%20Command%20%26%20Programming%20Reference.md). 

I would recommend reading that part first before reading this.

---

Otherwise here's how you combine those commands to make RGE do shit.

---

### RGE Programming Vocabulary

firstly going over some common programming vocabulary that will be used in the following doc.


| Term                      | Meaning                                                           |
| ------------------------- | ----------------------------------------------------------------- |
| **Object**                | A physical entity that can be manipulated by RGE                  |
| **Trigger**               | An Object that hosts Detection, Interaction and the link(s) to TG(s)|
| **Interaction**           | An event caused by a player interacting and pressing "f"          |
| **Detection**             | An event caused by an entity being detected in the detection area |
| **Trigger Group (TG)**    | An ordered collection of executable commands                      |
| **Executable**            | A command executed by a line in a TG                              |
| **Activation**            | The event that causes a TG to begin executing                     |
| **Reset**                 | Returns a TG to a state where it can be activated again           |
| **Looping**               | Causes a TG to repeat according to its configuration              |
| **UID**                   | Runtime unique identifier for an object                           |
| **Name**                  | Human-readable identifier used by applicable commands             |
| **Command (cmd)**         | A console command; tween, move, color, trigger, material          |


---


### RGE Programming Model

## 1. What RGE Programming Actually Is

RGE programming is based around objects, triggers, Trigger Groups, and executable commands.

Unlike a traditional programming language such as Lua, RGE does not primarily use scripts containing functions and variables. Instead, behaviour is constructed by configuring objects and connecting triggers to ordered command sequences.

A typical RGE system can be thought of as:

```text
OBJECTS
  │
  │ player interaction / entity detection
  ▼
TRIGGERS
  │
  │ activation
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

# Example

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

Objects, Triggers, Trigger Groups (TG), Executable Commands

- - -

# Objects

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

# Triggers

A Trigger is an object that hosts detection, interaction and the link to activate a Trigger Group.

Examples include:

```text
Button activation
Player detection
Vehicle detection
Other trigger conditions
```

A Trigger can be connected to one or more Trigger Groups.

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

# Trigger Groups

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

# Executable Commands

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
EVENT
  ↓
TRIGGER
  ↓
TRIGGER GROUP
  ↓
COMMAND SEQUENCE
  ↓
ACTION
```

Example: Automatic Door

```text
EVENT
Player enters detection area
        ↓
TRIGGER
Detection trigger activates the linked TG
        ↓
LOGIC
Door_Open TG gets activated and begins executing its lines
        ↓
COMMANDS
Tween door open
Wait
Tween door closed
        ↓
ACTION
Door physically opens and closes
```

This distinction is important because an event and an action are not necessarily the same thing.

A trigger can activate a TG, so the trigger doesn't directly perform the final action itself.

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
A
↓
B
↓
C
```

Only 1 command, "wait" can affect when subsequent commands execute. All other commands execute basically instantly, one after the other.

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
   │ trigger activates TG
   ▼
INACTIVE / EXECUTING
   │
   │ commands finish
   ▼
[STATE DEPENDS ON TG CONFIGURATION]
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

A useful way to think about a Trigger Group is:

A Trigger Group is a function or small RGE program / embedded system that runs when a linked trigger activates it.
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
├── Detection trigger, area {10, 10, 10}
└── Desired opened & closed positions, move door by 5 stds sideways.

BUILD
├── Create/Design & Configure Door
└── Create Detection Trigger Area

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
├── Enter Detection area
├── Verify opening
├── Verify timing
├── Verify closing
└── Verify it can be triggered again
```

- - -

now that you hopefully know and understand how programs work and are made in RGE.
Reading the rest of [BRM5 RGE 2.0 — Command %26 Programming Reference.md#rge-programming-model](https://github.com/borisbruh/BORTECH-SYSTEMS/blob/main/GUIDES/BRM5%20RGE%202.0%20%E2%80%94%20Command%20%26%20Programming%20Reference.md#rge-programming-model) should hopefully be much easier.

I hope you enjoy reading the rest.
