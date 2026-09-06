# You should read the [Handbook](https://github.com/borisbruh/BORTECH-SYSTEMS/blob/11af7b69eb47f71b8fed9a1f10cc618b6fb735cc/GUIDES/BRM5%20RGE%202.0%20%E2%80%94%20Command%20%26%20Programming%20Reference%20Handbook.md) before reading this as it has acronyms and core behavior laid out and will make reading this a lot easier.

There is a lot of niche interactions/behavior that isn't intuitive or very clear so this file will hopefully set the record straight.

All behavior stated in this file has been verified and checked

- - -

# There are only 3 ways to have cmds run without any "manual" input

## TG input :

1. Trigger activate ...

## 2 player / entity inputs :

2. Interaction
3. Entity Detection

This means, to have TGs executing in the first place, you must have some sort of "player" input.




- - -

### Trigger activate

In TG_1 we can have the following cmd:
```text
trigger activate 1 TG_2
```
by doing this, we can activate TG_2 from TG_1.

So we have activated a TG from a TG.

- - -

### Interaction

By setting:
```text
trigger whitelist 1 %Tobj IsButton true
```
we can have an interactive button that a player can press "f" on, which will activate any TGs that were linked to the button TOBJ.

- - -

### Detection

By setting:
```text
trigger whitelist 1 %Tobj IsTrigger true
```
we can get a detection volume (the size/volume of the obj) that will activate any TG's that are linked and that have the entity, which caused the activation, whitelisted.
The entities which can be whitelisted are:
```text
Players
Bots
Helicopters
Ground
```
All of the above entities are checked at the very center of them, if they are in a detection volume.
