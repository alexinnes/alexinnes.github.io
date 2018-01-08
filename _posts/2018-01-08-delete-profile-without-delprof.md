---
title: Delete Profile without DelProf
author: Alex Innes
date: 2018-01-08 00:00:00 +0000
layout: ''
---
Here is a script I created so that I could delete users accounts on stations but not have to use DelProf.
<--!more-->
It works by called with WMI class win32_userProfile, which will remove the user in the registry and it will remove their user folder. 

You can point it at any machine on the network and it will remove the account. 

It has some error checking, it will make sure the user is not logged in and there is a valid path to the users folder.

So here it is:
{% gist b34b6b7c1b8afb62a7f7642e5106e958 %}