---
title: Running Script as System
author: Alex Innes
date: 2018-10-03 00:00:00 +0000
layout: post

---
So I ran into a problem while troubleshooting our automation suite on a clients server. <!--more-->We could see that it could connect back to the server but when I logged out, it would not connect.  
The problem being my user account was allowed to connect but the system account was not.

So, I needed to run my connection tests as system, something I had never done in PowerShell.

Scouring the web for ideas on how I could do this and possibly write something that I could use going forward. I found many complicated methods on how to write this. Which I started to dive into.

Struggling to get something working, something dawned on me.  An old acronym from my an old computing lecturer: K.I.S.

Keep. It. Simple.

Which lead me to:

    Start-Process -FilePath cmd.exe -Verb runas -ArgumentList '/k "C:\Sysin
    ternal\psexec.exe" -i -s powershell.exe'

Instead of writing a complicated script, you can just run the above. Which opens a PS window as System.

For me this was the best solution, for others it might not be. It reminded me that when looking for solution, keeping it simple is often the best route.