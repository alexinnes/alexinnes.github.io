---
layout: post
title:  "Careful with the -WhatIf"
author: "Alex Innes"
categories: script writing, Active Directory
---

So I happened across another issue with the Active Directory module.
<--!more--> 
I was asked by a colleague to create a quick script, along the lines of "I need to change all users password in an OU to the same value" (I didn't question, primary schools tend to have a "bespoke" setup.) 
Which, is quite simple:
{% Higlight PowerShell %}
Get-AdUser -filter * -searchbase <whatever the OU was> | Set-ADAccountPassword -NewPassword (ConvertTo-SecureString -asPlainText "somepassword" -Force) 
{% endhighlight %}

Easy enough, I thought, but I should test just incase the Set-ADAccountPassword does not accept multiple objects from the Get-AdUser cmdlet.

So log into my test network and bang out the one liner, but slap a -WhatIf on the end of "Set-AdAccountPassword" thinking - that will let me know if it changes all the passwords. Having used the WhatIf command before I know
how handy it is for testing one liners like this.

So the full command is looking like:

{% Higlight PowerShell %}
Get-AdUser -filter * -searchbase <whatever the OU was> | Set-ADAccountPassword -NewPassword (ConvertTo-SecureString -asPlainText "somepassword" -Force) -WhatIf
{% endhighlight %}

Cool, lets test this as I slapped the F5 key in ISE.

Hmm, no verbose output, I know - lets add verbose to the end.

{% Higlight PowerShell %}
Get-AdUser -filter * -searchbase <whatever the OU was> | Set-ADAccountPassword -NewPassword (ConvertTo-SecureString -asPlainText "somepassword" -Force) -WhatIf -Verbose
{% endhighlight %}


Another slap of the F5 key....no output.

That's strange, maybe this cmdlet does not output anything with the WhatIf command....

So did a bit of digging, and checking the AD accounts I notice the "passwordlastset" date has been updated.....
Check a few other accounts and yes, the cmdlet ignored the WhatIf and decided to change all of the accounts to what I had asked regardless to the WhatIF
Being my test network this was not too much of an issue - instead of fix any issues of service accounts throwing issues I just rebuilt the network.
I probably should have tested this on a small OU with a couple of users but that would be sensible.

A word of warning with the AD Module it may or may not ignore the WhatIf parameter.
