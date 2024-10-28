# display-autoresize

[EN](README.md) | **RU**

## 1. Описание

Скрипт для автоматического масштабирования дисплея в сессии SPICE. Протестировано на Arch Linux с:

- X11: оконный менеджер `i3`;
- Wayland: менеджер входа `sddm`, wayland-композитор `sway`.

Пример работы:

![preview.gif](preview.gif)

## 2. Требования

### 2.1. Гостевая система

1. Установите необходимые пакеты:

    - Arch-based:

        ```bash
        sudo pacman --sync --refresh --needed bash xorg-xrandr screen jq spice-vdagent xf86-video-qxl
        ```

        - `bash` - для выполнения скриптов;
        - `xorg-xrandr` - для команды `xrandr`, чтобы получать и изменять разрешения дисплеев;
        - `screen` - для запуска скрипта для sway в фоне;
        - `jq` - для парсинга вывода `swaymsg`;
        - `spice-vdagent` и `xf86-video-qxl` - гостевые дополнения SPICE.

2. Установите `drm_info`:

    - Arch-based:

        ```bash
        yay --sync --refresh --needed --sudoloop drm_info
        ```

3. Скачайте скрипт `display-autoresize` из этого репозитория и сделайте его исполняемым:

    ```bash
    sudo wget -O /usr/bin/display-autoresize https://raw.githubusercontent.com/Nikolai2038/display-autoresize/refs/heads/main/display-autoresize && \
    sudo chmod +x /usr/bin/display-autoresize
    ```

   - Вы можете разместить скрипт не в `/usr/bin/display-autoresize`, но не забудьте также поменять путь к нему в правиле udev (смотреть ниже).

4. Создайте правило udev:

    ```bash
    echo 'ACTION=="change", KERNEL=="card[0-9]*", SUBSYSTEM=="drm", RUN+="/usr/bin/display-autoresize"' | sudo tee /etc/udev/rules.d/50-display-autoresize.rules && \
    sudo udevadm control --reload-rules
    ```

5. Убедитесь, что `spice-vdagentd` запущен и работает без ошибок.

#### 2.1.1. Менеджер входа (SDDM)

Дополнительно, если вы хотите, чтобы этот скрипт работал и для менеджера входа (например, для SDDM) - убедитесь, что менеджер входа запущен не от суперпользователя.
Я тестировал SDDM, и если он запущен от суперпользователя, то события `udev` не происходят - соответственно, скрипт для автоматического масштабирования не вызывается.
Для запуска SDDM не от суперпользователя:

```bash
sudo mkdir --parents /etc/sddm.conf.d && \
echo '[General]
DisplayServer=x11-user' | sudo tee /etc/sddm.conf.d/set_compositor.conf && \
echo 'allowed_users = anybody
needs_root_rights = no' | sudo tee --append /etc/X11/Xwrapper.config && \
sudo systemctl restart sddm.service
```

Для дополнительной информации, смотрите:

```bash
man sddm.conf
man Xorg.wrap
```

### 2.2. Хостовая система

Просто убедитесь, что автоматическое масштабирование включено, когда подключаетесь через `virt-viewer`/`spicy`.

## 3. Отладка

- Просматривайте события udev при масштабировании через `udevadm monitor`;
- Просматривайте `dmesg -w` (возможно, не так полезно);
- Просматривайте логи через `tail -f /var/log/display-autoresize.log`.

## 4. Благодарности

- Скрипт является форком из [gist](https://gist.github.com/IngoMeyer441/84cf1e40fa756a9c3e6c8d9e38ee9b6f), но с поддержкой `XAUTHORITY` и Sway (Wayland);
- Спасибо за [поиск сессий под root](https://unix.stackexchange.com/questions/117083/how-to-get-the-list-of-all-active-x-sessions-and-owners-of-them);
- Спасибо за [масштабирование через udev](https://superuser.com/questions/1183834/no-auto-resize-with-spice-and-virt-manager);
- Спасибо за [решение через `drm_info`](https://todo.sr.ht/~emersion/wlr-randr/15) to get current window resolution in Wayland.

## 5. Развитие

Вы можете помочь с:

- Тестированием на других дистрибутивах (Debian, Fedora, etc.) и добавлением инструкций для них;
- Тестированием на других менеджерах окон и Wayland-композиторах;
- Возможной оптимизацией кода и поиском более правильных и быстрых решений для парсинга в скриптах;
- Переводом `README.md` на другие языки.

Не стесняйтесь участвовать в развитии репозитория, используя [pull requests](https://github.com/Nikolai2038/display-autoresize/pulls) или [issues](https://github.com/Nikolai2038/display-autoresize/issues)!
