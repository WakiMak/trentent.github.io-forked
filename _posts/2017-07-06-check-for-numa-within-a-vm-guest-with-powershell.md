---
id: 2497
title: Check for NUMA within a VM guest with Powershell
date: 2017-07-06T16:32:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2497
permalink: /2017/07/06/check-for-numa-within-a-vm-guest-with-powershell/
image: /wp-content/uploads/2017/07/106_numa.gif
categories:
  - Blog
tags:
  - PowerShell
  - scripting
---
I have only tested this in VMWare, but it seems to work. Â I have [shamelessly stole the main code](https://stackoverflow.com/questions/6972437/pinvoke-for-getlogicalprocessorinformation-function) from here:

&nbsp;

<pre class="lang:ps decode:true">#Are we on a NUMA-aware system?  If we are, we will assign NIC's to NUMA specific CPU's.  If not then it's just to the one pool.
    $procInfo = @"
using System;
using System.Diagnostics;
using System.ComponentModel;
using System.Runtime.InteropServices;

namespace Windows
{
    public class Kernel32
    {
        [StructLayout(LayoutKind.Sequential)]
        public struct PROCESSORCORE
        {
            public byte Flags;
        };

        [StructLayout(LayoutKind.Sequential)]
        public struct NUMANODE
        {
            public uint NodeNumber;
        }

        public enum PROCESSOR_CACHE_TYPE
        {
            CacheUnified,
            CacheInstruction,
            CacheData,
            CacheTrace
        }

        [StructLayout(LayoutKind.Sequential)]
        public struct CACHE_DESCRIPTOR
        {
            public byte Level;
            public byte Associativity;
            public ushort LineSize;
            public uint Size;
            public PROCESSOR_CACHE_TYPE Type;
        }

        [StructLayout(LayoutKind.Explicit)]
        public struct SYSTEM_LOGICAL_PROCESSOR_INFORMATION_UNION
        {
            [FieldOffset(0)]
            public PROCESSORCORE ProcessorCore;
            [FieldOffset(0)]
            public NUMANODE NumaNode;
            [FieldOffset(0)]
            public CACHE_DESCRIPTOR Cache;
            [FieldOffset(0)]
            private UInt64 Reserved1;
            [FieldOffset(8)]
            private UInt64 Reserved2;
        }

        public enum LOGICAL_PROCESSOR_RELATIONSHIP
        {
            RelationProcessorCore,
            RelationNumaNode,
            RelationCache,
            RelationProcessorPackage,
            RelationGroup,
            RelationAll = 0xffff
        }

        public struct SYSTEM_LOGICAL_PROCESSOR_INFORMATION
        {
            public UIntPtr ProcessorMask;
            public LOGICAL_PROCESSOR_RELATIONSHIP Relationship;
            public SYSTEM_LOGICAL_PROCESSOR_INFORMATION_UNION ProcessorInformation;
        }

        [DllImport(@"kernel32.dll", SetLastError=true)]
        public static extern bool GetLogicalProcessorInformation(
            IntPtr Buffer,
            ref uint ReturnLength
        );

        private const int ERROR_INSUFFICIENT_BUFFER = 122;

        public static SYSTEM_LOGICAL_PROCESSOR_INFORMATION[] MyGetLogicalProcessorInformation()
        {
            uint ReturnLength = 0;
            GetLogicalProcessorInformation(IntPtr.Zero, ref ReturnLength);
            if (Marshal.GetLastWin32Error() == ERROR_INSUFFICIENT_BUFFER)
            {
                IntPtr Ptr = Marshal.AllocHGlobal((int)ReturnLength);
                try
                {
                    if (GetLogicalProcessorInformation(Ptr, ref ReturnLength))
                    {
                        int size = Marshal.SizeOf(typeof(SYSTEM_LOGICAL_PROCESSOR_INFORMATION));
                        int len = (int)ReturnLength / size;
                        SYSTEM_LOGICAL_PROCESSOR_INFORMATION[] Buffer = new SYSTEM_LOGICAL_PROCESSOR_INFORMATION[len];
                        IntPtr Item = Ptr;
                        for (int i = 0; i < len; i++)
                        {
                            Buffer[i] = (SYSTEM_LOGICAL_PROCESSOR_INFORMATION)Marshal.PtrToStructure(Item, typeof(SYSTEM_LOGICAL_PROCESSOR_INFORMATION));
                            Item += size;
                        }
                        return Buffer;
                    }
                }
                finally
                {
                    Marshal.FreeHGlobal(Ptr);
                }
            }
            return null;
        }
    }
}
"@
        $cp = New-Object CodeDom.Compiler.CompilerParameters             
	    $cp.CompilerOptions = "/unsafe"
	    $cp.WarningLevel = 4
	    $cp.TreatWarningsAsErrors = $false
        $cp.ReferencedAssemblies.Add("System.dll")

        Add-Type -TypeDefinition $procInfo -CompilerParameters $cp

        $NumaNode = [Windows.Kernel32]::MyGetLogicalProcessorInformation()
        $numberOfNumaNodes = ($NumaNode | where {$_.Relationship -eq "RelationNumaNode"}).count

        if ($numberOfNumaNodes -ge 2) {
            #NUMA detected
            $NUMA = $true
        }</pre>

I was going to use this to detect NUMA and configure a network adapter RSS based upon NUMA configuration... Â But I'm getting lazy and am going to ignore it. Â But I don't want this code to go away. Â So here it is. Â A way to detect if you are on a NUMA system in a guest VM in Powershell.

<img class="aligncenter size-full wp-image-2498" src="http://theorypc.ca/wp-content/uploads/2017/07/Powershell_results.png" alt="" width="1539" height="461" srcset="http://theorypc.ca/wp-content/uploads/2017/07/Powershell_results.png 1539w, http://theorypc.ca/wp-content/uploads/2017/07/Powershell_results-300x90.png 300w, http://theorypc.ca/wp-content/uploads/2017/07/Powershell_results-768x230.png 768w" sizes="(max-width: 1539px) 100vw, 1539px" /> 

If you have greater than "1" for the NumaNode count then NUMA is present.

CoreInfo.exe result on the same system:

<img class="aligncenter size-full wp-image-2499" src="http://theorypc.ca/wp-content/uploads/2017/07/CoreInfoResultOnSameSystem.png" alt="" width="585" height="525" srcset="http://theorypc.ca/wp-content/uploads/2017/07/CoreInfoResultOnSameSystem.png 585w, http://theorypc.ca/wp-content/uploads/2017/07/CoreInfoResultOnSameSystem-300x269.png 300w" sizes="(max-width: 585px) 100vw, 585px" /> 

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->