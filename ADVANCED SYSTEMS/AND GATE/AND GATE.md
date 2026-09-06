# 2-bit AND gate
Load your ps to empty map, then run the following to build the gate:
```text
create 1 part 14.5 -496 0
size 1 % 1 9 6
create 1 part 18 -500 0
size 1 % 6 1 4
create 1 part 25.5 -496 0
size 1 % 1 9 6
create 1 part 20 -496 -2.5
size 1 % 10 9 1
create 1 part 20 -496 2.5
size 1 % 10 9 1
create 1 part 20 -495.5 0
size 1 % 2 8 4
color 1 % 0 255 0
create 1 part 17 -495.5 0
color 1 % 255 255 0
collision 1 % 3
create 1 part 23 -495.5 0
size 1 % 4 8 4
trigger add 1 %
```
You should now have a tub around y=-500, with a yellow part, a green wall and a trigger above a hole in the tub.
This AND gate works with the following 2 inputs:
```text
bot spawn 1 PL5_Rifleman 17 -495.5 0 -90
bot direct % direct 24 -495.5 0
```
```text
move 1 Wall_1 20 -487.5 0 -0 0 0
```
The first spawns a bot at the yellow part and directs it towards the trigger.
The second moves the wall out of the way, opening the path between the spawn point and trigger.
Combined, this causes the trigger to only activate when both inputs are activated.

This is positioned right above y=-500 as this is the killfloor, so when the gate is activated the bot is immediately dropped and killed to reduce lag.

# 3-bit AND gate
Take the above shown AND gate, and add another wall:
```text
create 1 part 18.75 -495.5 0
size 1 % 0.5 8 4
```
We then add a trigger to move this second wall
```text
move 1 Wall_2 18.75 -487.5 0 -0 0 0
```
Now our gate only activates after all 3 inputs are triggered, and this can be repeated to achieve an AND gate of any size.

The delay from input to output is around 1.5s, which is reduced to 0.5s when bot alert is set to true. A safe assumption is to give the gate 2s or 0.8s respectively.
