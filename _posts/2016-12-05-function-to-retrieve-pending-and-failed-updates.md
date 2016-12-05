---
title: Function to Retrieve Pending and Failed Updates.
date: '2016-12-02 08:16:57'
layout: post
---
This function is something that helps me identify when the server has updates that pending or have failed
<!--more-->As both are generally require me to do something to the server.
I.e. Schedule a restart on the server or troubleshoot why the update has failed.

Now if you are running on Server 2012 then PowerShell actually has its own cmdlet to easily do this, but I needed this to work with 2008 server as well. This meaning it needed some more work.
Take a look:
{% gist 9e45bcc5c170715782bdb4be139130ca %}

A quick overview of the function. To run it, all you need to do is load the script and run:

```powershell Get-PendingWindowsUpdates ```

As long as you are running this on your server that has Updates Services installed/working then it should return a hash table with four arrays (Unless it returned empty.).

You can specifiy a computer name and it will query that computer for which updates are: NotIntsalled, InstalledPendingReboot, Downloaded or Failed.

```powershell Get-PendingWindowsUpdates -Computername "SomeComputerName" ```

Some clarity to the arrays the hash table holds:
"NotInstalled"
This is updates that have been told they need to install but have not yet.

"InstalledPendingReboot"
Name tends to give it away - updates that have installed but require a reboot.

"Downloaded"
Updates that have been downloaded but not installed yet.

"Failed"
Updates that have tried to install but have failed.

