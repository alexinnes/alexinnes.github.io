---
layout: post
title: Active Directory Annoyance
author: Alex Innes
categories: script writing, Active Directory
date: '2016-05-13T00:00:00.000+00:00'
---
I recently found something out (with much appreciated help over at http://powershell.org and Dave Wyatt).

I have been writing some PowerShell functions that will accept pipeline values from Active Directory object.
<!--more-->
Lets just take a basic function to give you an example.

{% highlight PowerShell %}
function get-name
{
    [CmdletBinding()]
    Param
    (
        # Param1 help description
        [Parameter(Mandatory=$true,
                   ValueFromPipelineByPropertyName=$true,
                   Position=0)]
        [alias("SamAccountName")]
        $myname
    )

    Begin
    {
    }
    Process
    {
    }
    End
    {
        echo $myname
    }
}
{% endhighlight %}

Very very basic function to show my issue.

So this function should be able to accept a pipeline value by property name and because there is an alias specified - "SamAccountName" it should be able to use this too.

So in theory, this:

{% highlight PowerShell %}
Get-Aduser "Alex.Innes" | Get-Name
{% endhighlight %}
should work...Yes?

No!

Returns nothing in the console:

![Output 1](/Images/Active_Directory_Annoyance/Output1.PNG)

I spend too long looking into this issue before reaching out to the PowerShell community, to see where I was going wrong in my script.

Thankfully, within 10 minutes Dave and I was happy to find out this is not something that I was doign wrong but an oddity within the Active Directory module.
The solution to this issue:

![Output 2](/Images/Active_Directory_Annoyance/Output2.PNG)

Yes, adding a "select *" in the pipline fixes the issue. Odd, yes - but if it works....
I had this issue with AD Computers objects too, so I am assuming its with all Active Directoy objects.

So hopefully if you have this issue, you might not spend as long banging your head off the desk and find this solution.





