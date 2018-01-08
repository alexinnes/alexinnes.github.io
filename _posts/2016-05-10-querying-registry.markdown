---
layout: post
title: Querying a Remote Registry
date: 2016-05-10 18:00:00 +0000
author: Alex Innes
categories: script
---

So this week I have been working on a script that will allow me to view the registry on remote machines.
<!--more-->
While speaking to a colleague about this he already had a script put together, which worked...but what's the fun in that.
So taking a quick look at his code, I pulled the key factor.
In .NET there is a class that will allow you to open a remote registry.

```PowerShell
[Microsoft.Win32.RegistryKey]::OpenRemoteBaseKey([Microsoft.Win32.RegistryHive]$Hive,$computer)
```

This allows me to specify the hive and the computer on the network that I want to get values from. 

Using Get-Member (one of my favourite commands) I could see there was a "OpenSubKey()" method. 

<link rel="stylesheet" href="http://code.jquery.com/mobile/1.4.5/jquery.mobile-1.4.5.min.css">
<script src="http://code.jquery.com/jquery-1.11.3.min.js"></script>
<script src="http://code.jquery.com/mobile/1.4.5/jquery.mobile-1.4.5.min.js"></script>
<a href="#myPopup" data-rel="popup" data-position-to="window">
<img src="/Images/Querying_a_remote_registry/OpenRemoteBaseKey.PNG" alt="Get-Member on OpenRemoteBaseKey" >Click on the image to enlarge</a>
<div data-role="popup" id="myPopup">
<a href="#pageone" data-rel="back" class="ui-btn ui-corner-all ui-shadow ui-btn-a ui-icon-delete ui-btn-icon-notext ui-btn-right">Close</a>
<img src="/Images/Querying_a_remote_registry/OpenRemoteBaseKey.PNG" style="width:auto;height:auto;" alt="Get-Member on OpenRemoteBaseKey"></div>
<br>

This "OpenSubKey()" method allows you to specify a registry for it to open. There is also a "GetValueNames()" method that returns the keys in the specified registry.

What I wanted to do was to be able to specify a Hive/Registry/Key and for it to spit out the value. I also wanted the script to do was for me to just specify the Hive/Registry and for it to tell me the
other "folders" in that registry. 

So, this is what I came up with.

Please feel free to use it.

{% highlight powershell %}
Function Get-RemoteRegKey
{
[OutputType('System.Boolean')]
	[CmdletBinding(DefaultParameterSetName = "__AllParameterSets")]
	
	param( 
		[Parameter(
			Position = 0,
			ValueFromPipeline = $true,
			ValueFromPipelineByPropertyName = $true
		)]		
		[Alias("CN","__SERVER","IPAddress")]
		[string[]]$ComputerName = "",		

		[Parameter(
			Position = 1,
			ValueFromPipelineByPropertyName = $true,
			HelpMessage = "The HKEY to open, from the RegistryHive enumeration. The default is 'LocalMachine'.")]
		[ValidateSet("ClassesRoot","CurrentUser","LocalMachine","Users","PerformanceData","CurrentConfig","DynData")]
		[string]$Hive = "LocalMachine",

		[Parameter(
			Mandatory = $false,
			Position = 2,
			ValueFromPipelineByPropertyName = $true,
			HelpMessage = "The path of the subkey to open.")]
		[string]$reg,

		[Parameter(
			Mandatory = $false,
			Position = 3,
			ValueFromPipelineByPropertyName = $true,
			HelpMessage = "The name of the value to open.")]
		[string[]]$key
	)

process
{
    foreach($computer in $ComputerName)
       {
        write-verbose "Opening Remote have to: $Computer into Hive: $Hive"
        try
            {
                $query = [Microsoft.Win32.RegistryKey]::OpenRemoteBaseKey([Microsoft.Win32.RegistryHive]$Hive,$computer)
            }
            catch
                {
                    write-error "Error connecting to the remote registry. Please review your computername"
                    break
                }
        write-verbose "Opening $reg in $hive"
        try
            {
                $InitialKey = $query.OpenSubKey($reg).getvaluenames()
            }
            catch
                {
                    write-error "Could not open $hive\$reg - its possible it does not exsist."
                    break
                }
           if (!$InitialKey)
                {
                    write-verbose "Could not find any Keys in specified Reg - Returning object with no KeyValues - $($query.opensubkey($reg).name)"
                    $query_noValue = New-Object pscustomobject
                    $query_noValue | Add-Member -name Reg -type NoteProperty -Value  "$hive\$reg"
                    $query_noValue | Add-Member -Name SubReg -type NoteProperty -value $query.OpenSubKey("$reg").getsubkeynames()
                    return $query_noValue
                }
            if($key)
                {
                    write-verbose "Getting single value $key from $($query.opensubkey($reg).name)"
                    $query_value = New-Object pscustomobject 
                    $query_value | Add-Member -Name Reg -type NoteProperty -value "$hive\$reg"
                    $query_value | Add-Member -Name KeyKind -type NoteProperty -Value $query.opensubkey("$reg").getvaluekind($key).tostring() 
                    $query_value | add-member -name Key -type NoteProperty -value "$key"
                    $query_value | Add-Member -Name KeyValue -type NoteProperty -value  $query.opensubkey("$reg").GetValue($key)
                    $query_value | Add-Member -Name SubReg -type NoteProperty -value $query.OpenSubKey("$reg").getsubkeynames()
                    return $query_value
                }

        $query_obj = $null   
        $query_obj = @{}
        $query_Output = $null
        $query_Output = @()
        write-verbose "Starting loop for all keys in directory $($query.opensubkey($reg).name)"
        foreach($s in $InitialKey)
           {
        
               $query_obj = New-Object pscustomobject 
               $query_obj | Add-Member -Name Reg -type NoteProperty -value "$hive\$reg"
               $query_obj | Add-Member -Name KeyKind -type NoteProperty -Value $query.opensubkey("$reg").getvaluekind($s).tostring()
               $query_obj | add-member -name Key -type NoteProperty -value "$s"
               $query_obj | Add-Member -Name KeyValue -type NoteProperty -value  $query.opensubkey("$reg").GetValue($s)
               $query_obj | Add-Member -Name SubReg -type NoteProperty -value $query.OpenSubKey("$reg").getsubkeynames()
               $query_Output += $query_obj
       
       
           }
       }
}

end
{   
   
    return $query_Output
}

}


{% endhighlight %}

Here is a image of a typical output if you specify all parameters:

![all parameters](/Images/Querying_a_remote_registry/allparam.PNG)

Here is a image of a typical output when you do not specify the -Key parameter:

![no valueparam](/Images/Querying_a_remote_registry/noValue.PNG)

As you can see from the above image, because there are no keys in this Reg it just returns you the sub-folders (I call the subreg in the object)

If you have any questions about this script you can drop me a line on twitter {% include icon-twitter.html username=site.twitter_username %}



