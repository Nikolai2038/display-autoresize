# display-autoresize

Script to autoresize display in SPICE session for X11 displays.

## Steps

1. Make sure `bash` is available;
2. Create udev rule:

    - path to new udev rule: `/etc/udev/rules.d/50-display-autoresize.rules`
    - udev rule content:

        ```
        ACTION=="change", KERNEL=="card[0-9]*", SUBSYSTEM=="drm", RUN+="/usr/local/bin/display-autoresize"
        ```
     
3. Create script `/usr/local/bin/display-autoresize` (this file) and make executable;
4. Reload udev rules with `sudo udevadm control --reload-rules`;
5. Make sure `auto-resize` is enabled in `virt-viewer`/`spicy`;
6. Make sure `qemu-guest-agent spice-vdagent xserver-xspice xserver-xorg-video-qxl` are installed;
7. Make sure `spice-vdagentd` is loaded and running fine.

## Debugging

- Watch udev events on resize with `udevadm monitor`;
- Watch `dmesg` (may not be super useful) with `dmesg -w`;
- Watch logs with `tail -f /var/log/display-autoresize.log`.

## Credits:

- "Forked" from [gist](https://gist.github.com/IngoMeyer441/84cf1e40fa756a9c3e6c8d9e38ee9b6f);
- Credit for [Finding Sessions as Root](https://unix.stackexchange.com/questions/117083/how-to-get-the-list-of-all-active-x-sessions-and-owners-of-them);
- Credit for [Resizing via udev](https://superuser.com/questions/1183834/no-auto-resize-with-spice-and-virt-manager).
