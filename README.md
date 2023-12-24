# awesome-wm-and-kde-plasma
How to install Awesome WM instead KWin on KDE Plasma.

![Awesome with Plasma Screenshot](/screenshots/screenshot-1.png "Bomber Classic Rodolfo")

## Installation
### Packages
    * Xorg packages: `xorg-server`, `xorg-xinit`
    * Terminal: `xterm`
    * Awesome WM: `awesome`
    * Compositor: `picom`
    * KDE Plasma: `plasma`
    * Desktop Display Manager: `sddm`

    * All packages for Arch Linux: `sudo pacman -S xorg-server xorg-xinit xterm awesome picom plasma sddm`

    * Enable sddm: `sudo systemctl enable sddm`
        obs.: Remember to change to Plasma(X11) on sddm login.

## Configuration
### Adding stuff to the Awesome config
    * Create the configuration file:
        ````
        mkdir -p ~/.config/awesome
        cp /etc/xdg/awesome/rc.lua ~/.config/awesome/
        ````

    * Edit line 199 in `~/.config/awesome/rc.lua` to hide wibox:
        ````
        s.mywibox = awful.wibar({ position = "top", screen = s, visible = false})
        ````

    * To improve compatibility with Plasma, add the following lines in your Awesome config:
        * In line 447 `awful.rules.rules = {`:
            ````
            { -- General plasma rules
                rule_any = {
                    class = {
                        "plasmashell",
                        "ksmserver-logout-greeter",
                    },
                },
                properties = {
                    floating = true,
                    border_width = 0,
                    titlebars = false, -- custom property to control titlebars
                },
            },

            { -- KDE apps
                rule_any = {
                    class = {
                        "spectacle",
                        "krunner",
                    },
                },
                properties = {
                    floating = true,
                },
            },

            rule_any = {
                name = {
                    "Desktop.*Plasma",
                },
            },
            ````

### Replace Kwin with Awesome WM
    * Mask `Kwin` service:
        ````
        sudo systemctl mask plasma-kwin_x11.service
        qdbus org.kde.KWin /Compositor suspend
        ````

    * Create `awesome-plasma` service:
        ````
        mkdir -p ~/.config/systemd/user
        touch ~/.config/systemd/user/awesome-plasma.service
        sudo chmod +x ~/.config/systemd/user/awesome-plasma.service
        ````
    * Add in `awesome-plasma.service`:
        ````
        [Install]
        WantedBy=plasma-workspace.target

        [Unit]
        Description=Plasma Custom Window Manager
        Before=plasma-workspace.target

        [Service]
        ExecStart=/usr/bin/awesome
        Slice=session.slice
        Restart=on-failure
        ````
    * Enable `awesome-plasma` service:
        ````
        systemctl --user enable awesome-plasma.service
        ````

## References
    * [Tutorials/Using Other Window Managers with Plasma](https://userbase.kde.org/Tutorials/Using_Other_Window_Managers_with_Plasma)
    * [KDE with Awesome WM](https://martin-ueding.de/posts/kde-with-awesome-wm/)
    * This repository was inspired by [i3-and-kde-plasma](https://github.com/heckelson/i3-and-kde-plasma)
