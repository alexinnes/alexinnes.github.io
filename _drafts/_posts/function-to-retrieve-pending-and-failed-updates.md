---
title: Function to Retrieve Pending and Failed Updates.
date: '2016-12-02 08:16:57'
layout: 
---
This function is something that helps me identify when the server has updates that pending or have failed
<!--more-->As both are generally require me to do something to the server.
I.e. Schedule a restart on the server or troubleshoot why the update has failed.

Now if you are running on Server 2012 then PowerShell actually has its own cmdlet to easily do this, but I needed this to work with 2008 server as well. This meaning it needed some more work.
Take a look:
{% gist 9e45bcc5c170715782bdb4be139130ca}

