---
layout: single
title: Powershell - Recommended coding style
excerpt: 
permalink: /2011/06/powershell-recommended-coding-style.html
tags: 
- coding
- powershell
- scripting
published: true
comments: true
---
[source](http://stackoverflow.com/questions/2025989/what-is-the-recommended-coding-style-for-powershell)
Here are someRecommended coding style for powershell byRichard Berg

```
<span class="Apple-style-span" style="font-family: 'Courier New', Courier, monospace; font-size: x-small;"><#
.SYNOPSIS
Cmdlet help is awesome.  Autogenerate via template so I never forget.

.DESCRIPTION
.PARAMETER
.PARAMETER
.INPUTS
.OUTPUTS
.EXAMPLE
.EXAMPLE
.LINK
#>
function Get-Widget
{
    [CmdletBinding()]
    param (
        # Think about which params users might loop over.  If there is a clear
        # favorite (80/20 rule), make it ValueFromPipeline and name it InputObject.
        [parameter(ValueFromPipeline=$True)]
        [alias("Server")]
        [string]$InputObject,

        # All other loop candidates are marked pipeline-able by property name.  Use Aliases to ensure the most 
        # common objects users want to feed in will "just work".
        [parameter(Mandatory=$true, Position=0, ValueFromPipelineByPropertyName=$True)]
        [alias("FullName")]
        [alias("Path")]
        [string[]]$Name,

        # Provide &amp; document defaults for optional params whenever possible.
        [parameter(Position=1)]
        [int]$Minimum = 0,

        [parameter(Position=2)]
        [int]$ComputerName = "localhost",

        # Stick to standardized parameter names when possible.  *Especially* with switches.  Use Aliases to support 
        # domain-specific terminology and/or when you want to expose the parameter name of the .Net API you're wrapping.
        [parameter()]
        [Alias("IncludeFlibbles")]
        [switch]$All,
    )

    # The three main function blocks use this format if &amp; only if they are short one-liners    
    begin { $buf = new-list string }

    # Otherwise they use spacing comparable to a C# method
    process    
    {
        # Likewise, control flow statements have a special style for one-liners
        try
        {
            # Side Note: internal variables (which may be inherited from a parent scope)  
            # are lowerCamelCase.  Direct parameters are UpperCamelCase.
            if ($All)
                { $flibbles = $Name | Get-Flibble }   
            elseif ($Minimum -eq 0)          
                { $flibbles = @() }
            else
                { return }                       

            $path = $Name |
                ? { $_.Length -gt $Minimum } |
                % { $InputObject.InvokeGetAPI($_, $flibbles) } |
                ConvertTo-FullPath
        }
        finally { Cleanup }

        # In general, though, control flow statements also stick to the C# style guidelines
        while($true)
        {
            Do-Something
            if ($true)
            {
                try
                {
                    Do-Something
                    Do-Something
                    $buf.Add("abc")
                }
                catch
                {
                    Do-Something
                    Do-Something
                }
            }            
        }    
    }    
}

<# 
Pipelines are a form of control flow, of course, and in my opinion the most important.  Let's go 
into more detail.

I find my code looks more consistent when I use the pipeline to nudge all of Powershell's supported 
language constructs (within reason) toward an "infix" style, regardless of their legacy origin.  At the 
same time, I get really strict about avoiding complexity within each line.  My style encourages a long,
consistent "flow" of command-to-command-to-command, so we can ensure ample whitespace while remaining
quite compact for a .Net language. 

Note - from here on out I use aliases for the most common pipeline-aware cmdlets in my stable of 
tools.  Quick extract from my "meta-script" module definition:
sal ?? Invoke-Coalescing
sal ?: Invoke-Ternary
sal im Invoke-Method
sal gpv Get-PropertyValue
sal spv Set-PropertyValue
sal tp Test-Path2
sal so Select-Object2        
sal eo Expand-Object        

% and ? are your familiar friends.
Anything else that begins with a ? is a pseudo-infix operator autogenerated from the Posh syntax reference.
#>        
function PipelineExamples
{
    # Only the very simplest pipes get to be one-liners:
    $profileInfo = dir $profile | so @{Path="fullname"; KBs={$_.length/1kb}}
    $notNull = $someString | ?? ""        
    $type = $InputObject -is [Type] | ?: $InputObject $InputObject.GetType()        
    $ComObject | spv Enabled $true
    $foo | im PrivateAPI($param1, $param2)
    if ($path | tp -Unc)
        { Do-Something }

    # Any time the LHS is a collection (i.e. we're going to loop), the pipe character ends the line, even 
    # when the expression looks simple.
    $verySlowConcat = ""            
    $buf |
        % { $verySlowConcat += $_ }
    # Always put a comment on pipelines that have uncaptured output [destined for the caller's pipeline]
    $buf |
        ? { $_ -like "*a*" }


    # Multi-line blocks inside a pipeline:
    $orders |
        ? { 
            $_.SaleDate -gt $thisQuarter -and
            ($_ | Get-Customer | Test-Profitable) -and
            $_.TastesGreat -and
            $_.LessFilling
        } |
        so Widgets |        
        % {                
            if ($ReviewCompetition)
            {
                $otherFirms |
                    Get-Factory |
                    Get-ManufactureHistory -Filter $_ |
                    so HistoryEntry.Items.Widgets                     
            }
            else
            {
                $_
            }
        } |            
        Publish-WidgetReport -Format HTML


    # Mix COM, reflection, native commands, etc seamlessly
    $flibble = Get-WmiObject SomethingReallyOpaque |
        spv AuthFlags 0xf -PassThru |
        im Put() -PassThru |
        gpv Flibbles |
        select -first 1

    # The coalescing operator is particularly well suited to this sort of thing
    $initializeMe = $OptionalParam |
        ?? $MandatoryParam.PropertyThatMightBeNullOrEmpty |
        ?? { pwd | Get-Something -Mode Expensive } |
        ?? { throw "Unable to determine your blahblah" }           
    $uncFolderPath = $someInput |
        Convert-Path -ea 0 |
        ?? $fallback { tp -Unc -Folder }

    # String manipulation        
    $myName = "First{0}   Last{1}   " |
        ?+ "Suffix{2}" |
        ?replace "{", ": {" |
        ?f {eo richard berg jr | im ToUpper}            

    # Math algorithms written in this style start to approach the elegance of functional languages
    $weightedAvg = $values |
        Linq-Zip $weights {$args[0] * $args[1]} |
        Linq-Sum |
        ?/ ($weights | Linq-Sum)
}

# Don't be afraid to define helper functions.  Thanks to the script:Name syntax, you don't have to cram them into 
# the begin{} block or anything like that.  Name, params, etc don't always need to follow the cmdlet guidelines.
# Note that variables from outer scopes are automatically available.  (even if we're in another file!)
function script:Cleanup { $buf.Clear() }

# In these small helpers where the logic is straightforward and the correct behavior well known, I occasionally 
# condense the indentation to something in between the "one liner" and "Microsoft C# guideline" styles
filter script:FixComputerName
{
    if ($ComputerName -and $_) {            
        # handle UNC paths 
        if ($_[1] -eq "\") {   
            $uncHost = ($_ -split "\\")[2]
            $_.Replace($uncHost, $ComputerName)
        } else {
            $drive = $_[0]
            $pathUnderDrive = $_.Remove(0,3)            
            "\\$ComputerName\$drive`$\$pathUnderDrive"
        }
    } else {
        $_
    }
}
```

