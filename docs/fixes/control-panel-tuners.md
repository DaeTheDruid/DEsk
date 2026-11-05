# Choosing networks, Bluetooth devices and sound devices on the Control Panel

Branch: `control-panel-card`, against upstream v0.1.5.

## What was missing

The Control Panel could turn Wi-Fi and Bluetooth on and off and set the
volume, but not choose anything: no network to join, no Bluetooth device to
connect, no speaker or microphone to use. The way to do that existed --
the other mouse button on a control opens `nm-connection-editor`,
`blueman-manager` or `pavucontrol` -- but nothing on the panel said so.

## What it does now

- **CHOOSE buttons.** A brass push button beside the wireless lever, the
  Bluetooth lever and the volume knob. Pushed, it stays down while its tuner
  is out; another one swaps the tuner; the same one again puts it away. The
  word under each control, and the network meter, do the same.
- **The card.** A walnut card slides out from behind the top of the row of
  controls and over the view, with the tuner on it. Esc, or the same
  CHOOSE, slides it back; a click off the machine turns back to the desk.
- **The tuner.** A radio tuning dial: names round a cream scale under a
  fixed red pointer, a knob that rolls the scale (wheel, drag, arrow keys)
  and is pressed (or Return) to take the one under the pointer. A readout
  on the left names that one and how it stands; the one on the right always
  shows what is in use now. RESCAN (Wi-Fi), SEARCH (Bluetooth) or an
  Output/Input band switch (sound) on the left, Settings on the right.
- **The work is borrowed.** `src/devices.c` runs `nmcli`, `bluetoothctl` and
  `pactl` on a thread of its own, never through a shell, in the C locale. A
  Wi-Fi password is asked by nm-applet's dialog (NetworkManager's agent in a
  DEsk session); pairing is confirmed by blueman's.

## How it is made

Nothing about the room changes: no new geometry in either tracer, no new
traced layer, no change to `room.glsl`, so nothing more is traced on any
screen size and the CHOOSE buttons and the card work as soon as the set
does.

- The CHOOSE buttons are drawn pieces of the set, as the needles and lamps
  are (`DESK_CCP_CHOOSE`), placed on the panel by the same projection.
- The card is drawn by `desk_draw_tuner` into one picture and put up in the
  console's layer above everything live on the set (`ccd_paint`), drawn
  again only when what is on it changes. It rests above the CHOOSE row
  (`desk_console_card_place`) and is cut off at the top of that row while
  it slides, so it comes out from under the controls.
- Input goes to the card first (`ccd_press`, `ccd_motion`, `ccd_axis`,
  `ccd_keys`); anything off it is the set's, all still there under it.

## Testing

- Headless on two screens, drawn on the processor (pixman) because the
  test machine's render node was not open to the session: the scripted hand
  (`DESK_HAND`, see `docs/MODDING.md`) opens each of the three tuners,
  clicks the dial, flips the Output/Input band switch, drags the knob,
  presses Esc, and clicks off the machine; the screen was photographed at
  each step. No errors or assertions in the log.
- Not tried: joining a new password-protected network, and pairing a new
  Bluetooth device, which rely on nm-applet's and blueman's agents.
