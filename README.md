# display-autoresize

**EN**

## 1. Description

Script to autoresize display in SPICE session. Tested on:

- X11: i3;
- Wayland: sway.

## 2. Requirements

### 2.1. Guest

1. Install `bash`, `screen`, `jq`:

    - Arch-based:

        ```bash
        sudo pacman --sync --refresh --needed bash screen jq
        ```

2. Install `drm_info`:

    - Arch-based:

        ```bash
        yay --sync --refresh --needed --sudoloop drm_info
        ```

3. Download script `display-autoresize` from this repository and make it executable:

    ```bash
    sudo wget -O /usr/bin/display-autoresize https://raw.githubusercontent.com/Nikolai2038/display-autoresize/refs/heads/main/display-autoresize && \
    sudo chmod +x /usr/bin/display-autoresize
    ```

   - You can put script not in `/usr/bin/display-autoresize`, but make sure to change path to it in udev rule too (see below).

4. Create udev rule:

    ```bash
    echo 'ACTION=="change", KERNEL=="card[0-9]*", SUBSYSTEM=="drm", RUN+="/usr/bin/display-autoresize"' | sudo tee /etc/udev/rules.d/50-display-autoresize.rules && \
    sudo udevadm control --reload-rules
    ```

5. Make sure `qemu-guest-agent spice-vdagent xserver-xspice xserver-xorg-video-qxl` are installed;
6. Make sure `spice-vdagentd` is loaded and running fine.

### 2.2. Host

Just make sure that auto-resize is enabled when you are connecting via `virt-viewer`/`spicy`.


## 3. Debugging

- Watch udev events on resize with `udevadm monitor`;
- Watch `dmesg -w` (may not be super useful);
- Watch logs with `tail -f /var/log/display-autoresize.log`.

## 4. Credits:

- "Forked" from [gist](https://gist.github.com/IngoMeyer441/84cf1e40fa756a9c3e6c8d9e38ee9b6f). Add `XAUTHORITY` and Sway (Wayland) support;
- Credit for [finding sessions as root](https://unix.stackexchange.com/questions/117083/how-to-get-the-list-of-all-active-x-sessions-and-owners-of-them);
- Credit for [resizing via udev](https://superuser.com/questions/1183834/no-auto-resize-with-spice-and-virt-manager);
- Credit for [`drm_info` solution](https://todo.sr.ht/~emersion/wlr-randr/15) to get current window resolution in Wayland.

## 5. Contribution

Feel free to contribute via [pull requests](https://github.com/Nikolai2038/display-autoresize/pulls) or [issues](https://github.com/Nikolai2038/display-autoresize/issues)!
