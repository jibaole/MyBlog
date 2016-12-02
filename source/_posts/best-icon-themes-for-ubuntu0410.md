---
title: best icon themes for ubuntu 14.04&15.10
tags:
  - ubuntu
categories:
  - Operating system
toc: true
date: 2015-11-18 15:34:08
description:
feature:
---

#### HOW TO USE A NEW ICON SET IN UBUNTU:

You can install a new icon set in two ways. One is by adding a PPA. You add the PPA, install the icon set. But you won’t find icon sets for all the icon themes. Therefore, the other way round is to download the compressed file and extract it to ~/.icons. If this directory doesn’t exist, create one using the following command:
``` bash
mkdir ~/.icons
```
The icons extracted in the above directory will be available for the current user only. If you want the icons to be available for all the users, you should extract the icons to /usr/share/icons.
<!-- more -->
Now, once you have installed the icon set, you can use a `Unity Tweak Tool` to change the icon theme. Use the following command to install Unity Tweak Tool:
``` bash
sudo apt-get install unity-tweak-tool
```
Once installed, run the tool and click on Icons under Appearance section:
![](https://itsfoss.com/wp-content/uploads/2014/05/Unity_Tweak_Tool.jpeg)

You can select your preferred icon set among the available ones here. Now you now how to change icons in Ubuntu, lets take a look at some of the best icon themes for Ubuntu.

#### BEST ICON THEMES FOR UBUNTU 14.04 & 14.10

Without wasting further time, lets see some beautiful icon sets available for Ubuntu Unity (and perhaps Gnome as well, not tested):

* MOKA
![](https://itsfoss.com/wp-content/uploads/2014/05/moka-Ubuntu-themes.jpeg)
To install Moka in Ubuntu and other similar distributions, use the following commands:
``` bash
sudo add-apt-repository ppa:moka/stable
sudo apt-get update
sudo apt-get install moka-icon-theme
```

* NUMIX:
![](https://itsfoss.com/wp-content/uploads/2014/05/Numix_Circle.jpeg)
In the image above you can see Numix Circle icon theme. A few more similar themes along with some wallpapers are available in the Numix PPA:
``` bash
sudo add-apt-repository ppa:numix/ppa
sudo apt-get update
sudo apt-get install numix-icon-theme numix-icon-theme-circle
```

* UNIFORM:
![](https://itsfoss.com/wp-content/uploads/2014/05/Uniform_Icons.jpeg)


* PLATEAU:
![](https://itsfoss.com/wp-content/uploads/2014/05/Plateau_Icon_Themes.jpeg)