{{#include ../include/header016.md}}

# Mouse

Relevant official examples:
[`mouse_input`][example::mouse_input],
[`mouse_input_events`][example::mouse_input_events].

---

## Mouse Buttons

Similar to [keyboard input][input::keyboard], mouse buttons are available as a
[`ButtonInput`] [resource][cb::res], [events][cb::event], and [run
conditions][cb::rc] ([see list][bevy::input::common_conditions]). Use whichever
pattern feels most appropriate to your use case.

### Checking Button State

You can check the state of specific mouse buttons using the
[`ButtonInput<MouseButton>`][`MouseButton`] [resource][cb::res]:

 - Use `.pressed(…)`/`.released(…)` to check if a button is being held down
   - These return `true` every frame, for as long as the button is in the respective state.
 - Use `.just_pressed(…)`/`.just_released(…)` to detect the actual press/release
   - These return `true` only on the frame update when the press/release happened.

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:mouse-button-input}}
```

You can also iterate over any buttons that have been pressed or released:

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:mouse-button-input-iter}}
```

### Run Conditions

Another workflow is to add [run conditions][cb::rc] to your systems,
so that they only run when the appropriate inputs happen.

It is highly recommended you write your own [run conditions][cb::rc],
so that you can check for whatever you want, support configurable bindings, etc…

For prototyping, Bevy offers some [built-in run conditions][input::rc]:

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:run-conditions}}
```

### Mouse Button Events

Alternatively, you can use [`MouseButtonInput`] [events][cb::event] to get
all activity:

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:mouse-button-events}}
```

## Mouse Scrolling / Wheel

To detect scrolling input, use [`MouseWheel`] [events][cb::event]:

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:scroll-events}}
```

The [`MouseScrollUnit`] enum is important: it tells you the type of scroll
input. `Line` is for hardware with fixed steps, like the wheel on desktop
mice. `Pixel` is for hardware with smooth (fine-grained) scrolling, like
laptop touchpads.

You should probably handle each of these differently (with different
sensitivity settings), to provide a good experience on both types of hardware.

**Note:** the `Line` unit is not guaranteed to have whole number values/steps!
At least [macOS][platform::macos] does non-linear scaling / acceleration of
scrolling at the OS level, meaning your app will get weird values for the number
of lines, even when using a regular PC mouse with a fixed-stepping scroll wheel.

## Mouse Motion

Use this if you don't care about the exact position of the mouse cursor,
but rather you just want to see how much the mouse moved from frame to
frame. This is useful for things like controlling a 3D camera.

Use [`MouseMotion`] [events][cb::event]. Whenever the mouse is moved, you
will get an event with the delta.

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:mouse-motion}}
```

You might want to [grab/lock the mouse inside the game
window][cookbook::mouse-grab].

## Mouse Cursor Position

Use this if you want to accurately track the position of the pointer /
cursor. This is useful for things like clicking and hovering over objects
in your game or UI.

You can get the current coordinates of the mouse pointer, from the respective
[`Window`] (if the mouse is currently inside that window):

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:cursor-position}}
```

To detect when the pointer is moved, use [`CursorMoved`] [events][cb::event]
to get the updated coordinates:

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:cursor-events}}
```

Note that you can only get the position of the mouse inside a window;
you cannot get the global position of the mouse in the whole OS Desktop /
on the screen as a whole.

The coordinates you get are in "window space". They represent window
pixels, and the origin is the **top left** corner of the window.

They do not relate to your camera or in-game coordinates in any way. [See
this cookbook example][cookbook::cursor2world] for converting these window
cursor coordinates into world-space coordinates.

To track when the mouse cursor enters and leaves your window(s), use
[`CursorEntered`] and [`CursorLeft`] [events][cb::event].

```rust,no_run,noplayground
{{#include ../code016/src/input/mouse.rs:enter-leave}}
```

Note: to detect whether the cursor is inside a specific window, it is often more
convenient to just check the cursor position by querying the window (see earlier
code snippet), instead of using these events. You probably want to know the
cursor coordinates anyway. If you get `None`, you know the cursor is not inside
that window.
