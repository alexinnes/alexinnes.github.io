---
title: Getting Logged on User
author: Alex Innes
date: 2018-09-13 00:00:00 +0000
layout: post

---
A small script which can be used to find what user is logged into a station. Can be handy if you are looking for a station that does not have any user logged into it.

<!--more-->

If filters out known SIDs and I have left the MS article in the comments if you need to add more to it.

Using it is as easy as loading the cmdlet:

    Get-LoggedOnUser -ComputerName Some-Computer

It will accept values from the pipeline too, so if you are working on a domain:

    Get-ADComputer SomeComputer | Get-LoggedOnUser

{% gist 4157865c0b0c1aaf4f6aec22055fe26c %}