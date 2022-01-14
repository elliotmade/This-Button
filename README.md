# ThisButton

This is a library for Circuitpython to make adding buttons to a project quick and easy.  Inspired by the OneButton library for arduino (https://github.com/mathertel/OneButton); none of the ideas here are new.  Built and tested on the Raspberry Pi Pico.  Currently supports events on button click, long press, hold, and release.  Basic debouncing is applied to prevent false readings, and all of the functionality is based on the `time.monotonic_ns()` function and should be non-blocking - `no time.sleep()`s here.

## Getting Started

Get Circuitpython up and running on your device: https://circuitpython.org/downloads.  Grab thisbutton.py and save it to the `lib` folder on your board.

```python
import thisbutton as tb
import board
```

### Initialize a new button
```python
myButton = tb.thisButton(board.GP1, True)
```
The second parameter for the internal pull up is optional and will default to `True`.  The pin will be configured as an input, and `False` will set it to pull down.  One `thisButton` instance is required per pin.

### Assign a callback function to button event
```python
def boop():
    print("Boop")

myButton.assignClick(boop)
```
The method above works for callback functions that require no arguments; use `lambda` for a callback that does require them: `lambda : yourFunction("your","args")`.

```python
def boop(word):
    print(word + " " + "boop")

myButton.assignClick(lambda: boop("beep"))

#should print "beep boop"
```

### Add `tick()` in your main loop
```python
while True:
    myButton.tick()
```
This is required for each button instance that is created.  Goal is to call this as often as possible for best reliability and timing accuracy, but it can be impacted if other processes use up cycles in the main loop.

## Button Events

| Assign Function         | Description                                            |
| ----------------------- | ------------------------------------------------------ |
| `assignClick(function_name)`           | Activates as soon as the button is pressed *if no other events are used for this button*, otherwise activates on button release.|
| `assignLongPressStart(function_name)`  | Activates after button has been held down for a short time. |
| `assignHeld(function_name, milliseconds)`            | Activates continuously on an interval while the button is activated. |
| `assignLongPressRelease(function_name)`| Activates when the button is released after a long press or hold.   |

## Adjustable parameters
Each instance has it's own set of timing thresholds that can be adjusted if necessary.  Timing is not guaranteed, all events are triggered at the next opportunity after the specified time.

| Function                | Description                                            |
| ----------------------- | ------------------------------------------------------ |
| `setDebounceThreshold(milliseconds)`           | Adjust the time for which new state changes are ignored after the triggering event.  Call without arguments to reset to default.|
| `setLongPressThreshold(milliseconds)`  | Adjust the length of time to recognize a long press.  Call without arguments to reset to default. |
| `setHeldInterval(milliseconds)`            | Adjust idle time between repeats of the function while holding a button.  Call without arguments to reset to default. |

## Properties
There are a few properties that can be read from each button instance:
| Property                 | Description                                            |
| ------------------------ | ------------------------------------------------------ |
| `.isHeld` Boolean        | True when the button is held and a function is assigned to that event.|
| `.heldDuration` Float     | Number of milliseconds a button has been held for, only while the button is held and a function is assigned to that event. |
| `.gpioState` Boolean      | Gives the state of the pin directly. |

## Debugging
Some information can be printed to the serial port if `toggleDebug()' is called.  Use this to dial in timing thresholds.


## To do
[ ] Make `longPressStart` work with `held`.
[ ] Add interval optional argument to `assignLongPressStart`.
[ ] Support boards that don't have the _ns flavor of `time.monotonic()`.
[x] Support callback functions with arguments
[ ] Make a proper state machine
[ ] Double and multi click
[ ] Debug output for gap between desired and actual timing
[ ] Add an example
