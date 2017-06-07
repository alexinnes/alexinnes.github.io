---
layout: post
title: System Specs with GUI
author: Alex Innes
categories: []
date: '2016-08-23T00:00:00.000+00:00'
---
Firstly apologies for the gap in posting. I had a couple issues with my health and then had a backlog of work.
<!--more-->

This is the first time in a while I have managed to write some PowerShell in a while. I was asked by a colleage if I could write something to show the system specs of a machine
and show its drivers/hardware IDs.
So I threw together this script:

{% gist cde384e2e1f7348d30d03a47bc96dc2b %}

Uses XAML which I generate in Visual Studios (Create a WPF project) and build the interface and just copy the XAML.
I add the "Name" tages so I can reference them in my PowerShell.
Then grabbing the system specs and drivers is as easy as calling get-ciminstance or get-wmiobject.
I had to do some work trying to get the information the listboxes by creating another object with the data inside.

Feel free to use the script and suggest edits/improvements on the gist.

Cheers,

Alex.