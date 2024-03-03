# Linux Note

## Linux

#### Path

`export PATH="path_paste_here":$PATH`

#### Service

edit `/etc/systemd/system/$SERVICE.service` with 

```bash
[Unit]
Description=<DISCRIPTION>

[Service]
Type=simple
User=<YOUR_USERNAME>
ExecStart=<PATH_TO_START>
WorkingDirectory=<PATH_TO_WORKING_DIR>
Restart=always

[Install]
WantedBy=multi-user.target
```

#### Problem

check executive location

```bash
which <COMMAND_NAME>
```

## GNOME

#### Path of icon

`~/.local/share/applications/`

`/usr/share/applications/`

### Tobbar colour

edit `.themes/<THEME>/gnome-shell/gnome-shell.css`

```bash
/* Top Bar */
panel {
  background-color: rgba(0, 0, 0, 1); //change here
  font-weight: normal;
  color: white;
  font-feature-settings: "tnum";
  transition-duration: 250ms;
  font-size: 9.75pt;
  font-weight: 400;
  height: 28px !important;
  box-shadow: 0 5px 16px rgba(0, 0, 0, 0.05);
}
```

## C

### Clang
#### include path

```bash
clang -v -x c -E /dev/null
```

## Python

### ModuleNotFound when module is installed with pip

```pyth
import sys; print(sys.executable);
```

#### pip error: externally-managed-environment

1. remove file `/usr/lib/python3.x/EXTERNALLY-MANAGED`,

2. use `pip`'s argument `--break-system-packages`,

3. add following lines to `~/.config/pip/pip.conf`:

```python
[global]
break-system-packages = true
```

## SSH

#### ssh config file

`/etc/ssh/sshd_config`
