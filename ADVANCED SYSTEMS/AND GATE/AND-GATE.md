INPUT_ON
```text
trigger removebutton 10 %Input
trigger set 10 %Input INPUT_ON false
color 10 %input 0 255 0
trigger set 10 %Volume LED_BLACK false
trigger set 10 %Volume LED_WHITE true
wait 0.01
trigger set 10 %Input INPUT_OFF true
trigger addbutton 10 %Input

reset
```

INPUT_OFF
```text
trigger removebutton 10 %Input
trigger set 10 %Input INPUT_OFF false
color 10 %input 162 161 164
trigger set 10 %Volume LED_WHITE false
trigger set 10 %Volume LED_BLACK true
trigger activate 10 LED_BLACK
wait 0.01
trigger set 10 %Input INPUT_ON true
trigger addbutton 10 %Input
trigger reset 10 LED_WHITE

reset
```

GATE_ON
```text
trigger removebutton 10 %Gate IsButton
trigger set 10 %Gate GATE_ON false
color 10 %gate 0 255 0
trigger whitelist 10 LED_BLACK Bots false
trigger whitelist 10 LED_WHITE Bots true
wait 0.01
trigger set 10 %Gate GATE_OFF true
trigger addbutton 10 %Gate

reset
```

GATE_OFF
```text
trigger removebutton 10 %Gate
trigger set 10 %Gate GATE_OFF false
color 10 %gate 162 161 164
trigger whitelist 10 LED_WHITE Bots false
trigger whitelist 10 LED_BLACK Bots true
trigger activate 10 LED_BLACK
wait 0.01
trigger set 10 %Gate GATE_ON true
trigger addbutton 10 %Gate
trigger reset 10 LED_WHITE

reset
```







































