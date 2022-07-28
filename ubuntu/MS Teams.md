# MS Teams in Ubuntu

## Screen Sharing issue

Native MS Teams on linux does not support window sharing. Although the Web App does. The rest is only about the native app.

If you are using a very high resolution screen, the screen sharing experience will be very poor since MS Teams will not 
share in full resolution and the other side of the meeting could not see what you see and the image will be very pixelated.

One solution is to generate a virtual screen on a corner of the monitor that is a quarter of the original
Screen. And the screen will be available in Teams to share as a complete screen.

I do this by adding the following to the `~/.bashrc` file to generate aliases. 
To learn more about the arguments of `xrandr`, go to its [documentation](http://www.straightrunning.com/tools/xrandr.html) 
to learn how to specify the size arguments.

```.bash
alias MONITOR_CREATE='xrandr --setmonitor CAST-1 1920/299x1080/168+1080+0 none'
alias MONITOR_REMOVE='xrandr --delmonitor CAST-1'
```

Now `MONITOR_CREATE` creates the virtual screen and `MONITOR_REMOVE` removes it.
