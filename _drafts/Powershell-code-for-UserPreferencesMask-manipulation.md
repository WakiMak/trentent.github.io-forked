---
id: 2502
title: Powershell code for UserPreferencesMask manipulation
date: 2017-07-09T21:56:54-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2502
permalink: /?p=2502
categories:
  - Uncategorized
---
<pre class="lang:ps decode:true  ">&lt;#
    .Synopsis
      Cleans up temporary folders in various locations
    .Description
      Deletes folder or files in several "temporary" locations.  
    .EXAMPLE
    .Inputs
    .Outputs
    .NOTES
      Author: Trentent Tye
      Editor: Trentent Tye
      Company: TheoryPC

      #notes for UserPreferenceMask - https://technet.microsoft.com/en-us/library/cc957204.aspx  --&gt; only relevant for windows 2000...
      Bit ------- UI Setting ------------------------- Default Value ------------ Meaning
       0          Active window tracking                    0                     Active window tracking disabled.
       1          Menu animation                            1                     Menu animation enabled; menu effect depends on value of bit 9.
       2          Combo box animation                       1                     Slide-open effect for combo boxes enabled.
       3          List box smooth scrolling                 1                     Smooth-scrolling effect for list boxes enabled.
       4          Gradient captions                         1                     Gradient effect for window title bars enabled.
       5          Keyboard cues                             0                     Menu access key letters underlined only when the menu is activated from the keyboard.
       6          Active window tracking Z order            0                     Windows activated through active window tracking are not brought to the top.
       7          Hot tracking                              1                     Hot tracking enabled.

       8          Reserved                                  0                     This bit reserved for future use.
       9          Menu fade                                 1                     Menu fade animation enabled. If menu fade animation is disabled, menus use slide animation. This bit is ignored if menu animation (bit 1) is disabled.
       10         Selection fade                            1                     Selection fade enabled; the selected menu will remain on the screen briefly and then fade out after the user makes a selection.
       11         Tool tip animation                        1                     Tool tip animation enabled; effect depends on value of bit 12.
       12         Tool tip fade                             1                     Tool tip fade animation enabled. If tool tip fade animation is disabled, tool tips use slide animation. This bit is ignored if tool tip animation (bit 11) is disabled.
       13         Cursor shadow                             1                     Cursor shadow is enabled. This effect only appears if the system has a color depth of more than 256 colors.
       14         Reserved                                  0                     This bit reserved for future use.
       ..         Reserved                                  0                     This bit reserved for future use.
       30         Reserved                                  0                     This bit reserved for future use.
       31         UI effects                                1                     All UI effects (combo box animation, cursor shadow, gradient captions, hot tracking, list box smooth scrolling, menu animation, menu underlines, selection fade, tool tip animation) are enabled. 




      History
      Last Change: 2017.06.30 TT: Script created
	  Last Change: 
	  .Link
    #&gt;

Begin{
	$script_path = $MyInvocation.MyCommand.Path
	$script_dir = Split-Path -Parent $script_path
	$script_name = [System.IO.Path]::GetFileName($script_path)
}

Process {

    function modifyBinaryValue ([int]$charNumber, [bool]$enabledDisabled, [array]$binaryArray) {
        #bits are broken up into binary groups of 8 so everything is divisble by 8.
        #if we have a remainder then that's the index number on that index of the array we need to query

        if (($charNumber % 8) -eq 0) {
            $arrayIndex = [math]::floor(($charNumber-1) / 8)
        } else {
            $arrayIndex = [math]::floor(($charNumber) / 8)
        }
        
        $remainder = (($charNumber % 8) -1) #array counting starts at 0, not 1.  Must subtract 1
        if ($remainder -le 0) {
            $remainder = 0
        }

        if ($enabledDisabled) {
            #1 == Enabaled
            $charValue = 1
        } else {
            #0 == Disabled
            $charValue = 0
        }


        #$workingArray = $binaryArray[$arrayIndex]

        #$workingArray.remove($remainder, 1).insert($remainder, $charValue)

        #$workingArray
        for ($i=0;$i -lt $binaryArray.Length; $i++) {
            if ($i -eq $arrayIndex) {
                $binaryArray[$arrayIndex].remove($remainder, 1).insert($remainder, $charValue)
            } else {
                $binaryArray[$i]
            }
        }
    }

    function reverse-characters ([string]$charArray) {
        $array = $charArray.ToCharArray()
        [array]::Reverse($array)
        $c = -join($array)
        $c
    }

    $userPreferencesMask = (Get-ItemProperty "HKCU:\Control Panel\Desktop" | select -Property "UserPreferencesMask").UserPreferencesMask
    #convert to Decimal
    #[convert]::ToInt32("11111111", 2)
    #convert To Binary
    #[convert]::ToString($userPreferencesMaskEnabled[1], 2)

    $array = @()
    foreach ($binary in $userPreferencesMask) {
        $binary = [convert]::ToString($binary, 2)
        #pad zeros (0) at the end until there are 8 characters
        $binary = $binary.PadRight(8, "0")
        #this is stored in little endian format.  Reverse the chars so we can work with it
        $binary = reverse-characters($binary)
        #$binary = $binary.ToCharArray()
        $array += $binary
    }

    write-verbose "Disabled  Array:     $Disabledarray"
    write-verbose "Enabled  Array:      $Enabledarray"
    write-verbose "Original Array:      $array"
    $modifiedArrayElement = modifyBinaryValue  -enabledDisabled $false -binaryArray $array -charNumber 35
    write-verbose "Modified Array:      $modifiedArrayElement"
    $arrayIndex = [math]::floor(13 / 8)

    $finishArray = @()
    
    for ($i=0;$i -lt $array.Length; $i++) {
	    if ($i -eq $arrayIndex) {
            $modifiedArrayElement = $modifiedArrayElement.TrimEnd("0")
            $modifiedArrayElement = $modifiedArrayElement.PadRight(($modifiedArrayElement.Length+1),"0")
            $finishArray += $modifiedArrayElement
        } else {
            $arrayObj = $array[$i]
            $arrayObj = $arrayObj.TrimEnd("0")
            $arrayObj = $arrayObj.PadRight(($arrayObj.Length+1),"0")
            $finishArray += $arrayObj
        }
    }

    $finishArray












    #convert binary back to decimal and then ouput in to registry
    $decimalValues  = @()
    for ($i=0;$i -lt $finishArray.Length; $i++) {
    [convert]::ToInt32($finishArray[$i], 2)
        #$decimalValues += $finishArray[$i]

    }








    foreach (

    $array.RemoveAt($arrayIndex)
    $array.Insert($arrayIndex, $newArray)
    $array


    $old = "3162498;08.10.2016;30.10.2016;CHN;"
$new = $old.remove(8,10).insert(8,"MyNewString")
#would result in this: 3162498;MyNewString;30.10.2016;CHN;

    $beginning = $_.Substring(0,12) -replace '\.','0'


        $array += [convert]::ToString($binary, 2)
    }


    $count = 0
    foreach ($digits in $userPreferencesMaskDisabled) {
    #[convert]::ToString($digits, 2)
    $binary = [convert]::ToString($digits, 2)
    $binary
    }

   function Toggle-Endian
{
################################################################
#.Synopsis
# Swaps the ordering of bytes in an array where each swappable
# unit can be one or more bytes, and, if more than one, the
# ordering of the bytes within that unit is NOT swapped. Can
# be used to toggle between little- and big-endian formats.
# Cannot be used to swap nibbles or bits within a single byte.
#.Parameter ByteArray
# System.Byte[] array of bytes to be rearranged. If you
# pipe this array in, you must pipe the [Ref] to the array, but
# a new array will be returned (originally array untouched).
#.Parameter SubWidthInBytes
# Defaults to 1 byte. Defines the number of bytes in each unit
# (or atomic element) which is swapped, but no swapping occurs
# within that unit. The number of bytes in the ByteArray must
# be evenly divisible by SubWidthInBytes.
#.Example
# $bytearray = toggle-endian $bytearray
#.Example
# [Ref] $bytearray | toggle-endian -SubWidthInBytes 2
################################################################
[CmdletBinding()] Param (
    [Parameter(Mandatory = $True, ValueFromPipeline = $True)] [System.Byte[]] $ByteArray,
    [Parameter()] [Int] $SubWidthInBytes = 1 )
 
    if ($ByteArray.count -eq 1 -or $ByteArray.count -eq 0)
        { $ByteArray ; return }
 
        if ($SubWidthInBytes -eq 1)
        { [System.Array]::Reverse($ByteArray); $ByteArray ; return }
 
        if ($ByteArray.count % $SubWidthInBytes -ne 0)
        { throw "ByteArray size must be an even multiple of SubWidthInBytes!" ; return }
 
        $newarray = new-object System.Byte[] $ByteArray.count
 
        # $i tracks ByteArray from head, $j tracks NewArray from end.
        for ($($i = 0; $j = $newarray.count - 1) ;
            $i -lt $ByteArray.count ;
            $($i += $SubWidthInBytes; $j -= $SubWidthInBytes))
        {
        for ($k = 0 ; $k -lt $SubWidthInBytes ; $k++) { 
            $newarray[$j - ($SubWidthInBytes - 1) + $k] = $ByteArray[$i + $k] 
        }
    }
    $newarray
}
    

    [convert]::ToString($userPreferencesMaskEnabled, 2)

    #https://technet.microsoft.com/en-us/library/cc957204.aspx //UserPreferencesMask offical doc (dated)


    function Delete-FolderAndContents {
        # http://stackoverflow.com/a/9012108

        param(
            [Parameter(Mandatory=$true, Position=1)] [string] $folder_path
        )

        process {
            $child_items = ([array] (Get-ChildItem -Path $folder_path -Recurse -Force))
            if ($child_items) {
                $null = $child_items | Remove-Item -Force -Recurse -ErrorAction SilentlyContinue -Confirm:$False
            }
            $null = Remove-Item $folder_path -Force -Recurse -Confirm:$False -ErrorAction SilentlyContinue
        }
    }

    ##
    ## main program
    ##
    Write-BISFLog -Msg "Cleans up temporary folders in various locations" -ShowConsole -Color DarkCyan -SubMsg

    $paths = @( "$env:windir\Temp",
                "$env:temp",
                "$env:systemdrive\SWINST\BGINFO" )

    foreach ($path in $paths) {
    Write-BISFLog -Msg "Cleaning directory: $path"
    Delete-FolderAndContents($path)
    }
}


End {
	Add-BISFFinishLine
}
</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->