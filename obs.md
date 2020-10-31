# OBS Studio

I will dump here how I manage OBS in Linux.


<!-- vim-markdown-toc GFM -->

* [Install](#install)
* [Update](#update)
* [Run](#run)
* [Plugins](#plugins)
* [OBS Ninja](#obs-ninja)
* [Electron capture](#electron-capture)
	* [Install](#install-1)
	* [Run](#run-1)

<!-- vim-markdown-toc -->

## Install

I used to install the pacman package but I prefer now to **build from source in portable mode**. It gives me much more control and I can be on the bleeding edge. So clone the repo:

```bash
cd ~/opt
wget https://cdn-fastly.obsproject.com/downloads/cef_binary_3770_linux64.tar.bz2
tar -xjf ./cef_binary_3770_linux64.tar.bz2
git clone --recursive https://github.com/obsproject/obs-studio.git
cd obs-studio
mkdir build && cd build
cmake -DUNIX_STRUCTURE=0 -DCMAKE_INSTALL_PREFIX="${HOME}/obs-studio-portable" -DBUILD_BROWSER=ON -DCEF_ROOT_DIR="../../cef_binary_3770_linux64" ..
make -j4 && make install
```

This will build and generate a folder in `~/obs-studio-portable`

## Update

```bash
cd ~/opt/obs-studio
git pull --recurse-submodules
cmake -DUNIX_STRUCTURE=0 -DCMAKE_INSTALL_PREFIX="${HOME}/obs-studio-portable" -DBUILD_BROWSER=ON -DCEF_ROOT_DIR="../../cef_binary_3770_linux64" ..
make -j4 && make install
```

## Run

I have made a script called and put it in `/usr/local/bin/obs`

```bash
#!/bin/bash
# open obs
cd ~/obs-studio-portable/bin/64bit
./obs </dev/null &>/dev/null &
disown -h %1
```

Remember to make it executable `sudo chmod +x /usr/local/bin/obs`

## Plugins

Plugins are stored in `~/.config/obs-studio/plugins` and they have the following structure:

```
plugins
 |
 |-------plugin_1
 |          |
 |          |-----bin
 |          |      |-----64bit
 |          |             |--------plugin1.so
 |          |
 |          |-----data
 |                 |-----locale
 |                        |--------en-US.ini
 |                        |--------ko-KR.ini
 |-------plugin_2
 |          |
 |          |-----bin
 |          |      |-----64bit
 |          |             |--------plugin2.so
 |          |
 |          |-----data
 |                 |-----locale
 |                        |--------en-US.ini
 |                        |--------es-ES.ini
...
```

## OBS Ninja

At the moment the instance in <https://call.beachlab.org> is not working so I use <https://obs.ninja> instead.

## Electron capture

### Install

```bash
git clone https://github.com/steveseguin/electroncapture.git
cd electroncapture
npm install
npm build
```

### Run

```bash
cd electroncapture
npm run
```
