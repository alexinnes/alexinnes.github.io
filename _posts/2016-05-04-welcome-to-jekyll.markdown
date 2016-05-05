---
layout: post
title:  "Welcome to Jekyll!"
date:   2016-05-04 20:50:13 +0100
categories: jekyll update
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

{% highlight powershell %}
function Get-RMSTRegKeyValueFromCSV
{
    [CmdletBinding()]
    [Alias()]
    Param
    (
        [Parameter(
            Position=0,
            Mandatory=$true
        )]
        [string]$CSVFile,

        [Parameter(
			Position = 0,
			ValueFromPipeline = $true,
			ValueFromPipelineByPropertyName = $true
            
		)]		
		[Alias("CN","__SERVER","IPAddress", "DNSHostname")]
		[string[]]$ComputerName = ""
    )

    Begin
    {
        #get the CSV file
        $CSVData = Import-Csv -Path $CSVFile
        $CSVArray = $null
        $CSVArray = @()
        $CSVObject = @{}
        $RMSTRegTypes = @{
            REG_MULTI_SZ = "Multistring"
            REG_DWORD = "DWord"
            REG_QWORD = "QWord"
            REG_BINARY = "Binary"
            REG_EXPAND_SZ = "ExpandString"           
            }
    }
    Process
    {
        
        foreach($entries in $CSVData)
            {
                #create the object to pass.
                write-verbose "Creating an object from the imported CSV File: [$($CSVFile)]"
                $CSVObject = New-Object PSCustomObject
                $CSVObject | Add-Member -Name ComputerName -MemberType NoteProperty -Value $entries.Computername
                $CSVObject | Add-Member -Name Catagory -MemberType NoteProperty -Value $entries.Catagory
                $CSVObject | Add-Member -Name Hive -MemberType NoteProperty -Value $entries.Hive
                $CSVObject | Add-Member -Name Reg -MemberType NoteProperty -Value $entries.Reg
                $CSVObject | Add-Member -Name Key -MemberType NoteProperty -Value $entries.Key
                $CSVObject | Add-Member -Name Expected_Value -MemberType NoteProperty -Value $entries.Expected_Value
                $CSVArray += $CSVObject
                
            }
        foreach($CSVItems in $CSVArray)
            {
                try{
                    $RMSTData = $CSVItems | Get-RMSTRegKeyValue
                    foreach($RMSTReg in $RMSTData){
                        switch($RMSTReg.keykind){
                            multistring{ 
                                foreach($keyvalue in $RMSTReg.keyvalue){
                                     if(($keyvalue -match $CSVItems.expected_value) -eq $true){
                                        # echo "[RMST Value] $keyvalue [matches] [CSV Value] $($CSVItems.expected_value)"
                                        $matchobject = New-Object PSCustomObject
                                        $matchobject | Add-Member -Name Reg -MemberType NoteProperty -Value $RMSTReg.reg
                                        $matchobject | Add-Member -Name Key -MemberType NoteProperty -Value $RMSTReg.key
                                        $matchobject | Add-Member -Name KeyValue -MemberType NoteProperty -Value $keyvalue
                                        $matchobject | Add-Member -Name CSVValue -MemberType NoteProperty -Value $CSVItems.expected_value
                                        $matchobject | Add-Member -Name Matches -MemberType NoteProperty -Value $true
                                        $matchobject


                                        } 
                                        else{
                                            
                                            $notmatchobject = New-Object PSCustomObject
                                            $notmatchobject | Add-Member -Name Reg -MemberType NoteProperty -Value $RMSTReg.reg
                                            $notmatchobject | Add-Member -Name Key -MemberType NoteProperty -Value $RMSTReg.key
                                            $notmatchobject | Add-Member -Name KeyValue -MemberType NoteProperty -Value $keyvalue
                                            $notmatchobject | Add-Member -Name CSVValue -MemberType NoteProperty -Value $CSVItems.expected_value
                                            $notmatchobject | Add-Member -Name Matches -MemberType NoteProperty -Value $false
                                            $notmatchobject
                                        }                                 
                                }
                            }

                               Dword{ "dword"}
                               Qword{ "qword"}
                               Binary{ "bin"}
                               ExpandString{ "ES"}
                           



                           }
                        }

                    }
                catch{}#{write-warning "Specified Registry does not exisit [$($CSVItems.reg)\$($CSVItems.key)]"
                }
            



    }

    End
    {

       
    }
}



function Compare-RMSTRegValue ($CSV, $RMST)
{




}




{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
