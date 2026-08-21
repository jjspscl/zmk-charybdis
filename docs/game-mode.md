# Game Mode

A dedicated persistent layer for gaming on the left half, with an inverted-T arrow
cluster and single-purpose keys so sustained holds never misfire into modifiers or
hidden layers.

## Enabling and disabling

Toggle in and out of Game Mode with the same gesture:

1. Hold left thumb **Escape** (this momentarily activates the MEDIA layer).
2. Tap **Q**.
3. Release Escape.

Repeating the gesture disables Game Mode. Game Mode persists across key presses until
you toggle it off (or hold Escape + Q again). It does **not** survive a reboot.

## Layout

```
        ·   ·   ↑   ·   ·       J   L   U   Y   '
        A   ←   ↓   →   ·       M   N   E   I   O
        Z   X   C   D   V       ·   ·   ·   ·   /
        ·   ·       ESC  SPACE  TAB     RET  BSP
```

- **Arrows:** `F = ↑`, `R = ←`, `S = ↓`, `T = →`.
- **Modifiers:** `A = Left Shift`, `Z = Left Ctrl`.
- **Space:** plain Space. It never activates the NAV layer and has no hold-tap latency.
- All other left and right alpha keys emit ordinary, single-purpose keypresses.
  Home-row mods, `Z/KBD` layer-tap, `X/RAlt`, and right-side thumb layer-taps are all
  neutralized on this layer.
- Holding right thumb **Backspace** still activates the numpad (see below).
- Escape keeps its `ESC/MEDIA` hold behavior so the toggle gesture stays reachable.

## Game Mode + Numpad

While Game Mode is active you normally still want numbers. Holding right **Backspace**
activates the `GAME_NUM` layer automatically (a ZMK conditional layer combining
`GAME` + `NUM`):

- The full standard numpad layout takes over the **left half** while Backspace is held.
- The **right half keeps the Game Mode letters** unchanged.
- Release Backspace to return to normal Game Mode keys.

This means every digit `0-9` and the common numpad symbols are reachable without
leaving the arrow cluster behind.

## Notes

- Combos (Keyboard Controls, Caps Word, Return to Base) are scoped away from Game Mode
  so they cannot fire accidentally during play.
- If you use the trackball in Game Mode, remember pointing only exists on the right
  half — see [trackball-troubleshooting.md](trackball-troubleshooting.md).