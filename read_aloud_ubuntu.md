# Read everything loud on Ubuntu

With this tutorial, you can make your ubuntu machine to read any selected (or copied text into clipboard) out loud.

This is tested on Ubuntu 20.04

```bash
sudo apt install -y sox play
python -m pip install gtts

touch read-selected-text.sh
chmod +x read-selected-text.sh
gedit read-selected-text.sh
```

Copy the following script into the text editor, tune it as you wish and save it

```bash
#! /bin/bash

loudreader=play
loudreader_pid=$(pidof ${loudreader})
speed="1.5"   # tune this according to your preferences

if [ -z "$loudreader_pid" ]
then
    xclip -out -selection primary | xclip -in -selection clipboard
    xsel --clipboard | tr "\n" " " | gtts-cli - | ${loudreader} -t mp3 -   tempo ${speed}
else 
    kill $loudreader_pid
fi
```

The rest, you can follow section "Binding the script to a keyboard shorting" of
[this instruction](https://dev.to/tylerlwsmith/read-selected-text-out-loud-on-ubuntu-linux-45lj)
on how to bind a shortcut to run this script.

## References:
- https://dev.to/tylerlwsmith/read-selected-text-out-loud-on-ubuntu-linux-45lj
- https://gtts.readthedocs.io/en/latest/cli.html
- https://unix.stackexchange.com/a/227237/301050
