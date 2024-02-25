# Linux Note

## Linux

#### Problem

> add path as environment variable

#### Solution

`export PATH="path_paste_here":$PATH`

#### Problem

> add service

#### Solution

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

> check path of a certain bin file

#### Solution

```bash
which <COMMAND_NAME>
```

## GNOME

### Desktop icons

#### Problem

> path of the icons

#### Solution

`~/.local/share/applications/`

`/usr/share/applications/`

### Tobbar

#### Problem

> topbar colour

#### Solution

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
#### Problem

> get include path

#### Solution
```bash
clang -v -x c -E /dev/null
```

## Python

### pip

#### Problem

> ```python
> error: externally-managed-environment
> 
> × This environment is externally managed
> ╰─> To install Python packages system-wide, try apt install
>     python3-xyz, where xyz is the package you are trying to
>     install.
> 
>     If you wish to install a non-Debian-packaged Python package,
>     create a virtual environment using python3 -m venv path/to/venv.
>     Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
>     sure you have python3-full installed.
> 
>     If you wish to install a non-Debian packaged Python application,
>     it may be easiest to use pipx install xyz, which will manage a
>     virtual environment for you. Make sure you have pipx installed.
> 
>     See /usr/share/doc/python3.11/README.venv for more information.
> 
> note: If you believe this is a mistake, please contact your Python installation or OS distribution provider. You can override this, at the risk of breaking your Python installation or OS, by passing --break-system-packages.
> hint: See PEP 668 for the detailed specification.
> ```

#### Solution

1. remove file `/usr/lib/python3.x/EXTERNALLY-MANAGED`,

2. use `pip`'s argument `--break-system-packages`,

3. add following lines to `~/.config/pip/pip.conf`:

```python
[global]
break-system-packages = true
```

## SSH

#### Problem

> ssh config file

#### Solution

`/etc/ssh/sshd_config`

## Jetbra

#### Problem

> does not work while using desktop icon

#### Solution

> modify desktop file adding `___MY_VMOPTIONS_SHELL_FILE="${HOME}/.jetbrains.vmoptions.sh"; if [ -f "${___MY_VMOPTIONS_SHELL_FILE}" ]; then . "${___MY_VMOPTIONS_SHELL_FILE}"; fi;`
