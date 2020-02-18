---
title: Chrome Install in Linux
date: 2017-11-13 09:22:25
tags: [Chrome]
categories: [Tools]
---


### Install Google Chrome in Linux Debian

`sudo dpkg -i google-chrome-stable_current_amd64.deb`

if you get an error like: "Errors were encountered while processing: google-chrome-stable" it is because some dependencies are missing. To fix that enter the following command:

`sudo apt-get -f install`

Then you should be able to run the installation without problems:

`sudo dpkg -i google-chrome-stable_current_amd64.deb`

To run Google Chrome type in terminal "google-chrome" or click the icon in the menu.



