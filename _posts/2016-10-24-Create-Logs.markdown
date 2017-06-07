---
layout: post
title: Creating Log Files for your Scripts
author: Alex Innes
categories: script writing error handling
date: '2016-10-24T00:00:00.000+00:00'
---

I decided to write a small script that can be used to create Windows Logs which can be viewed in the Event Viewer. Basically this is a script to create and log custom events and it creates a file too.
<!--more--> 
This need came about when I was creating some automation for a process to check configuration on a server. As there are multiple scripts that are not all written by myself I wanted a standardised way for any errors to be handled. As these are going to be run on the customers network and possibly by people who are not PowerShell savvy. Having it log to the EventLogs with clear errors would be a good idea.
{% gist 0b72b4e1ea0f6b554ae6bb4ac6c46cd6 %}
Basically this script works in two steps.
You need to create the EventLog using "Create-ScriptEventLog" 
Example being ```powershell Create-ScriptEventLog -LogName SomeScriptLog ```
The two parameters on this function are the LogName and the Source.
The logname is just what its called in the Event Viewer. The Source can be left blank at this stage as we define the source of the events when we write them. 
If you already have the Event Viewer open, you will need to close and open it.

The next part of the script is to actually write the logs. 

Example: ```powershell Write-ScriptEventLog -ErrorType Error -Message "Something has gone wrong" -Category 1 -EventID 1000 -LogName SomeScriptLog -Source "Some Part of the script" ```

This will create an event in the specified Event Log. 

This will put the source as the text you specified, but if you leave the source blank it will take the function name from where the Write-ScriptEventLog is sitting. This means that you can have multiple scripts/functions and the source of the event will show which function it has come from.
As well as writing to the Event Viewer it will also create a text log with all the same details. Which can be specified in the parameters.



