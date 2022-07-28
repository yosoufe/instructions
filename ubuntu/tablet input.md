# Tablet Input

I am using very old Samsung Galaxy Note 10.4 (2014) with [GfxTablet](https://github.com/rfc2822/GfxTablet)
to use the tablet as input for drawing during the meeting.

Since my monitor is very large and I have multiple monitors, I need to map the input table to 
just a single monitor or a portion of the monitor. The following aliases in `.bashrc` is very helpful regarding
this issue. They are aliases to
1. Run the driver to accept the input from the tablet over the network.
2. Remap the input to my main screen.
3. Remap the input to a quarter of the screen on a corner.

Read the xinput documents to learn about the arguments.

```bash
alias TABLET_INPUT_ENABLE='sudo <location of GfxTablet>/driver-uinput/networktablet'
alias TABLET_INPUT_REMAP='xinput map-to-output "$( xinput list --id-only "Network Tablet Pen (0)" )" DP-0'
alias TABLET_INPUT_REMAP_QUARTER='xinput set-prop "Network Tablet Pen (0)" "Coordinate Transformation Matrix" 0.39024 0 0.21951 0 0.421875 0 0 0 1'
```
