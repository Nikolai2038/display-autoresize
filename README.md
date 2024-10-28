# display-autoresize

**EN** | [RU](README_RU.md)

## 1. Description

Script to autoresize display in SPICE session. Tested on Arch Linux with:

- X11: `i3` windows manager;
- Wayland: `sddm` login manager, `sway` wayland compositor.

Preview:

![preview.gif](preview.gif)

## 2. Requirements

### 2.1. Guest

1. Install required packages:

    - Arch-based:

        ```bash
        sudo pacman --sync --refresh --needed bash xorg-xrandr screen jq spice-vdagent xf86-video-qxl
        ```

        - `bash` - for script execution;
        - `xorg-xrandr` - for `xrandr` command to get and modify display outputs;
        - `screen` - to run script for sway in background;
        - `jq` - to parse `swaymsg` output;
        - `spice-vdagent` and `xf86-video-qxl` - SPICE guest tools.

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

5. Make sure `spice-vdagentd` is loaded and running fine.

#### 2.1.1. Login Manager (SDDM)

Additionally, if you want this script to work for login manager you are using (SDDM for example) - make sure to start login manager from unprivileged user.
As I tested with SDDM, if it is running from root, `udev` events does not occur so script to autoresize is not called.
To start SDDM as unprivileged user:

```bash
sudo mkdir --parents /etc/sddm.conf.d && \
echo '[General]
DisplayServer=x11-user' | sudo tee /etc/sddm.conf.d/set_compositor.conf && \
echo 'allowed_users = anybody
needs_root_rights = no' | sudo tee --append /etc/X11/Xwrapper.config && \
sudo systemctl restart sddm.service
```

For more info, check:

```bash
man sddm.conf
man Xorg.wrap
```

### 2.2. Host

Just make sure that auto-resize is enabled when you are connecting via `virt-viewer`/`spicy`.

## 3. Debugging

- Watch udev events on resize with `udevadm monitor`;
- Watch `dmesg -w` (may not be super useful);
- Watch logs with `tail -f /var/log/display-autoresize.log`.

## 4. Credits

- "Forked" from [gist](https://gist.github.com/IngoMeyer441/84cf1e40fa756a9c3e6c8d9e38ee9b6f) but with `XAUTHORITY` and Sway (Wayland) support;
- Credit for [finding sessions as root](https://unix.stackexchange.com/questions/117083/how-to-get-the-list-of-all-active-x-sessions-and-owners-of-them);
- Credit for [resizing via udev](https://superuser.com/questions/1183834/no-auto-resize-with-spice-and-virt-manager);
- Credit for [`drm_info` solution](https://todo.sr.ht/~emersion/wlr-randr/15) to get current window resolution in Wayland.

## 5. Contribution

You can help me with:

- Testing in other distributions (Debian, Fedora, etc.) and update instructions for them;
- Testing on other Window Managers and Wayland compositors;
- Maybe optimizing code and finding more elegant and fast solutions for parsing;
- Translate `README.md` to other languages.

Feel free to contribute via [pull requests](https://github.com/Nikolai2038/display-autoresize/pulls) or [issues](https://github.com/Nikolai2038/display-autoresize/issues)!
