---
id: 522
title: Citrix Provisioning Services - Updating VMWare Tools and Target Device software - with all native tools
date: 2016-04-05T16:01:00-06:00
author: trententtye
layout: post
permalink: /2016/04/05/citrix-provisioning-services-updating-vmware-tools-and-target-device-software-with-all-native-tools/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/04/citrix-provisioning-services-updating.html
blogger_internal:
  - /feeds/7977773/posts/default/5927511585577098809
categories:
  - Blog
tags:
  - Citrix
  - Provisioning Services
  - PVS
  - Target Device
  - VMWare
  - VMWare tools
---
<ol style="margin-bottom: 0pt; margin-top: 0pt;">
  <li dir="ltr" style="background-color: transparent; font-family: Arial; font-size: 18.6667px; font-style: normal; font-variant: normal; font-weight: bold; list-style-type: decimal; text-decoration: none; vertical-align: baseline;">
    <h1 dir="ltr" style="line-height: 1.2; margin-bottom: 0pt; margin-top: 24pt; text-align: justify;">
      <span style="background-color: transparent; font-family: 'arial'; font-size: 18.6667px; font-style: normal; font-variant: normal; font-weight: bold; text-decoration: none; vertical-align: baseline; white-space: pre-wrap;"><span style="color: white;">Prerequistes</span></span>
    </h1>
  </li>
</ol>

<div dir="ltr" style="margin-left: -0.34999999999999964pt;">
  <table style="border-collapse: collapse; border: none;">
    <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr style="height: 5px;">
      <td style="background-color: #bfbfbf; padding: 0px 8px 0px 8px; vertical-align: top; border: solid #000000 1px;">
        <div dir="ltr" style="line-height: 1.2; margin-bottom: 0pt; margin-top: 0pt; text-align: justify;">
          <span style="background-color: transparent; font-family: 'arial'; font-size: 13.3333px; font-style: normal; font-variant: normal; font-weight: bold; text-decoration: none; vertical-align: baseline; white-space: pre-wrap;"><span style="color: white;">Step</span></span>
        </div>
      </td>
      
      <td style="background-color: #bfbfbf; padding: 0px 8px 0px 8px; vertical-align: top; border: solid #000000 1px;">
        <div dir="ltr" style="line-height: 1.2; margin-bottom: 0pt; margin-top: 0pt; text-align: justify;">
          <span style="background-color: transparent; font-family: 'arial'; font-size: 13.3333px; font-style: normal; font-variant: normal; font-weight: bold; text-decoration: none; vertical-align: baseline; white-space: pre-wrap;"><span style="color: white;">Detail</span></span>
        </div>
      </td>
    </tr>
    
    <tr style="height: 0px;">
      <td style="padding: 0px 8px 0px 8px; vertical-align: top; border: solid #000000 1px;">
      </td>
      
      <td style="padding: 0px 8px 0px 8px; vertical-align: top; border: solid #000000 1px;">
        <div dir="ltr">
          2 servers:
        </div>
        
        <ol>
          <li dir="ltr">
            <div dir="ltr">
              Staging server with Windows 2008 R2 SP1
            </div>
          </li>
          
          <li dir="ltr">
            <div dir="ltr">
              VMWare machine configured identically to your PVS target devices (BLD server)
            </div>
          </li>
        </ol>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        <div dir="ltr">
          'Windows Server Backup' feature installed on the staging server
        </div>
        
        <div dir="ltr">
          <img src="https://lh5.googleusercontent.com/UAgYVnOtt3vAm5yxG1anRNGp_Y3PB0bNiuWapHaOeHlWtFCNEW7yKiaaANIXJ5MQuCcdUHtYfGxrP_VBsVrUdy7PkZuFt_FDM_F3xScqUiF9Fdgca-QBroJxJso-DClb7NxF51PPdqt8ASiZMw" width="555" height="373" />
        </div>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        <div dir="ltr">
          The Citrix utility 'CVHDMOUNT.EXE' is installed on the staging server
        </div>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        <div dir="ltr">
          'Backup' space (approx. 100GB)<br /> <img src="https://lh4.googleusercontent.com/89DQsgqu28OwJEy_q9-3LLoBJrQoxw147Y37F__DnbZmT3IqEkuhQQiU6tYZHfFdyrOpMj6VmlauHNrXY05l7b1OP9eL0SjI4-MRZePNboQQ6f-Btg7eU53wIqQ0fJtMeS-NzpsACwPZh31NNw" width="563" height="259" />
        </div>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        <div dir="ltr">
          VMWare VMDK hard disk of a greater size than the PVS disk:
        </div>
        
        <div dir="ltr">
          <img src="https://lh4.googleusercontent.com/Akonae0kiLvUSRlXC6KjULB7NQwi2kkqvsFHybXNS0N0w7ik0hFhd7int1cRCKWc9fuOd1DK9m-Rsds2V_GPITzL7q9zNIbQsr4Csdti3cxFMD6xV5SKq0Mqvlrl7rve51wuXGlITmbo3fTrEQ" width="269" height="16" /> < <img src="https://lh5.googleusercontent.com/clIBLdkAkPKRyzv-HDAGtCvClXJMRylui_S4EAeO-2_fw89cIzQdkKObN6oP8mJpv8VLLdrqt39RG1bR28Aql7tcxW0OqBmNw_eLvt9wVB-KwxeLCb36H8SGvtVHrUMaFHvIEEZEILwagKTBzw" width="274" height="121" />
        </div>
      </td>
    </tr>
  </table>
</div>

&nbsp;

<h2 dir="ltr">
  Copy and install required drivers on staging server
</h2>

&nbsp;

<div dir="ltr">
  <table>
    <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
      <td>
        <div dir="ltr">
          Step
        </div>
      </td>
      
      <td>
        <div dir="ltr">
          Detail
        </div>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        <div dir="ltr">
          From a Windows Server, browse to a server with Citrix Provisioning Services installed on it.
        </div>
        
        <p>
          &nbsp;
        </p>
        
        <div dir="ltr">
          <img src="https://lh5.googleusercontent.com/5PLWAoSUwHbBnqECpTqfq8kIRmw-6ROtgAXTfFTvi6oyfMlY_SVq2DsnVdoFv6LLW_lLMNXlxhNsplmSg3yKZ-AyRn1_-PbDPsg_29tg3DR2I6FDs6AVrOuYwx0EBjZgD_w173Qb" width="560" height="76" />
        </div>
        
        <div dir="ltr">
        </div>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        <div dir="ltr">
          Copy the drivers folder and CVhdMount.exe to the server
        </div>
        
        <div dir="ltr">
        </div>
        
        <div dir="ltr">
          <img src="https://lh5.googleusercontent.com/S9wQQX3SGD9d4XRQ4i3m3A4xUdx4fs--QJLr1j0uUJHCC-2AF9OCkz7NPn8tj_freYJKK_PyRAzRFnxZtdP3Sw8w9_NNYqEE1qfyrxLLGCKXY3mk3yqgWzhoId36Y5L6_iiaB2OlUJvcnSsDYA" width="92" height="207" />
        </div>
        
        <p>
          &nbsp;</td> </tr> 
          
          <tr>
            <td>
            </td>
            
            <td>
              <div dir="ltr">
              </div>
              
              <div dir="ltr">
                Open the drivers folder, right-click on cfsdep2.inf and select "Install"
              </div>
              
              <div dir="ltr">
                <img src="https://lh6.googleusercontent.com/P5dF6X_eJkxx0QuTzR-qJUiwJgaZVsEp6ACczHtY5Z0yPLrTDK9YI2_4PDHMn-gvTZvYzt40_alMisyQDEQRZvMbcJg3frWexhGyWnayyGierytJvAj83eYfQPxKH0RIi-LFLPj3fqI2rzpXdw" width="464" height="243" />
              </div>
              
              <p>
                &nbsp;</td> </tr> 
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Open Device Manager, right-click the computer name and choose "Add Legacy Hardware..."
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh6.googleusercontent.com/FgYhYuBUfc2iOOZ1UJbcUGYVuteQ_aEbMYtddfUTda8b3eLxICakrMwtXFV0x_BLdYnj16qvNJHm5bniuHUqI0ZMj9peBs7R3R1-yflz9VTy4gCOMx1E2BrdM8xoQcTTLXitLUhtSYKjAsf9WQ" width="284" height="133" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Select "Next"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh4.googleusercontent.com/03AfUDrOrW0AYcA3-PFNprggdUp2hMlQeATg2KMh_5JRq4yIBXm6EwwJLdEsvzBLuLEnyLAvhCocPDwz2p_WhMB6qo1KdozuQrWDPufon0qnC7sF85y0hrkOTSP3beswKzNX35UIMPyk8XRiyw" width="563" height="409" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Select "Install the hardware that I manually select from a list (Advanced)" and click "Next"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh5.googleusercontent.com/04c0k3ZtcGE-pIIGzhMkAKA2wc5GEr57PPI3AZ13k9Ow1c-wlRZbd9VZSBdDwm-SOc7sfC84bJpDcLuD1QF17vifQJ7qPvM3PTEmWiaSHtuF3oNj-KNCJUEwLpcUJAeeJJdivs7JufblL9m5nw" width="563" height="406" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Click "Next"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh6.googleusercontent.com/LgTZakYz88cjXvJsVmiILPKsFyFXD6w5vi0Tfuhkp9W-PguhtmKJHalGRcZ44HX51tQHfk6SD-FcPFIZbD17-e5LRosF2ccJrNlElYYxAuVqGMU83HjWtfTwM_TX8ASU1VdyY-Gw3k2DBZkAQA" width="563" height="408" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Click "Have Disk..."
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh5.googleusercontent.com/xZs5b6TB4V88S-CUNIvcZPvNTNupeuKINTbbh8ngVMdmN12-KEZyLQ9swAbrxGcOfHKBy7ZsWLFob3rPA4BI1e2squmkAabYj33RZgJL3EkQnGZwMpPssSvKGNooN8z-VZ2JWuA-iZV9N-JbnA" width="563" height="407" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Click "Browse" and go to the "drivers" folder and select "cvhdbusp6.inf" and click "Open"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh4.googleusercontent.com/b5g7NJ5pRcWYNjnR1OwlZiNge8UYBgkVxiAcMqqVmBgTLJcN9cr3RiE67vIwyInKLvywOi8yZWvktZ3gKqsQEhbVGx1Hiap-NjN-FzsPFR7Vex2bsffkG-4AwvT0oGLaAqcayM5L2jdcJord1g" width="561" height="411" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Click "OK"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh6.googleusercontent.com/pG-JfKHu5GYSW-Uw2e64xs-jQZcLV7IbGzO5c1DcchGTPuOajPXNZINgk5Idy-oc-_6KSFXm2OuCYwCL4Su1zQ90RqzjcJDzO3cy9jp7q6s67LxZQ-rdQYjvk0NReQYAEwNGl7pPI4vkhJ6LKA" width="426" height="221" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Ensure "Citrix Virtual Hard Disk Enumerator PVS" is listed and click "Next"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh5.googleusercontent.com/nXd55HBq3dL5EIAIN3Qksyf6gcLa12uo20aya2Imo3NqOMQbAk2uJ3hXXX2IVWNR7l8sE1L9E2jrrn0Uzoyjmt0iCYFr1FAooUVUL9-G0Z86kjn-2L3XkPfEO4sqZWWsvAN50tcFHxHYYoAI9A" width="563" height="405" />
                    </div>
                  </td>
                </tr>
                
                <tr>
                  <td>
                  </td>
                  
                  <td>
                    <div dir="ltr">
                      Click "Finish"
                    </div>
                    
                    <div dir="ltr">
                      <img src="https://lh3.googleusercontent.com/J-OBvpYiywgIMuSTXVB_KJLEL50NCCH6aJftJS8O-vB_kBinDsf9iJY00aLKf8WIA6hs4X3PHXfm0Bh8bTvWp3NyuvCRIAeGrc28moncxAErhtbTvWuWNLaa2RyUZI1sg3bWb3RWThp0YDLWiQ" width="563" height="408" />
                    </div>
                  </td>
                </tr></tbody> </table> </div> 
                
                <p>
                  &nbsp;
                </p>
                
                <h2 dir="ltr">
                  Setup VMWare virtual disk
                </h2>
                
                <div dir="ltr">
                  <table>
                    <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                      <td>
                        <div dir="ltr">
                          Step
                        </div>
                      </td>
                      
                      <td>
                        <div dir="ltr">
                          Detail
                        </div>
                      </td>
                    </tr>
                    
                    <tr>
                      <td>
                      </td>
                      
                      <td>
                        <div dir="ltr">
                          Open the "VMWare vSphere Client"
                        </div>
                        
                        <p>
                          &nbsp;
                        </p>
                        
                        <div dir="ltr">
                          <img src="https://lh4.googleusercontent.com/D2bS2B9LeGcwBu74oZQD_mgwc1ZrFgS6HTehCOsz02KRZABQ2zeZJXo98Chgi_xX1zLJsUY_BweLGx-lEYrh15mGeELr7v5gN-OrpHIGSKmqR246-A07e3cK874B-nYh5xynVM-s" width="319" height="382" />
                        </div>
                        
                        <div dir="ltr">
                          Right-click on the server you want to do the cloning on and click 'Edit Settings...'
                        </div>
                      </td>
                    </tr>
                    
                    <tr>
                      <td>
                      </td>
                      
                      <td>
                        <div dir="ltr">
                          Under the 'Hardware' tab, click 'Add...'
                        </div>
                        
                        <div dir="ltr">
                          <img src="https://lh3.googleusercontent.com/fdYHLr9Dy0Xntx_Xck_MAhMqrgvWcULTf_K2lq5mNjyi0c-ON1DClyCeYBIyBB0zACLp1dusDswgHFml1f91kTUwUDRzX722GSjqyDRNxvwa8-da58fb38utJxvXvFQ_nVxtFLjVSpbd5SQS3w" width="326" height="140" />
                        </div>
                      </td>
                    </tr>
                    
                    <tr>
                      <td>
                      </td>
                      
                      <td>
                        <div dir="ltr">
                          Select 'Hard Disk' and click 'Next'
                        </div>
                        
                        <div dir="ltr">
                          <img src="https://lh3.googleusercontent.com/uRIWfNC-UwYYprbzbHM3JmyoRupAMb3Po7dqMWatg5Y8xO2TIAeXtzxUnsOM1n1It9yC2PfFXL8T4LWXC9NI1-YQQNNs419N_0gWQdAgGGgX1-mbBsizXEIjYiRrE6Tsx-uIzCBKYe5Jg-Ll1w" width="409" height="319" />
                        </div>
                      </td>
                    </tr>
                    
                    <tr>
                      <td>
                      </td>
                      
                      <td>
                        <div dir="ltr">
                          Select 'Create a new virtual disk' and click 'Next'
                        </div>
                        
                        <div dir="ltr">
                          <img src="https://lh4.googleusercontent.com/8HD_NDOG7IaxvM7BwS8leqjsaKEgY2CLrRBsorEWf3KYqUFOymPNCNZvVoX-ptYUhYAH1nDkJc2kVggtEyOm-SVc0q-Y8jqkmmUFIaSFBM5zl18jZe66s2sO88szxbh-BeizzXCbKegEy0oS3w" width="462" height="362" />
                        </div>
                        
                        <p>
                          &nbsp;</td> </tr> 
                          
                          <tr>
                            <td>
                            </td>
                            
                            <td>
                              <div dir="ltr">
                                Set the 'Disk Size' to be greater than the size of the VHD file, select the 'Disk Provisioning' options you require, select the 'Location' you want to store the disk and remember where it is stored. Â You will need this location soon. Â Click 'Next'
                              </div>
                              
                              <div dir="ltr">
                                <img src="https://lh5.googleusercontent.com/mWEtDgMlCewijfH1EGHJsxeLCLxEDYwWw6Kltyt5yE5inpm7X2mrU-cS4o3YhM7yp4PPaSI71DGL_LcmNk3wVczkefJboYb_OfndxRNFgGu5u_hpqZUF6vdVawTCgDkZ5XVKTKqFiS8ZvPiimA" width="430" height="339" />
                              </div>
                            </td>
                          </tr>
                          
                          <tr>
                            <td>
                            </td>
                            
                            <td>
                              <div dir="ltr">
                                Click 'Next'
                              </div>
                              
                              <div dir="ltr">
                                <img src="https://lh4.googleusercontent.com/-mWP8-NYjnSFsHfWPmfSrizA-CAGL-IZd8vC4UK3W4GdCcERezYz7fLx2c4yLimWVW2EfSL7qrwbi9h5vpuaxr64OXwInJDIioHAJ1vBt8N9j8XHZQc6-5ulbfr3-priLpehHPaJ-0cf2Bf00g" width="452" height="354" />
                              </div>
                            </td>
                          </tr>
                          
                          <tr>
                            <td>
                            </td>
                            
                            <td>
                              <div dir="ltr">
                                Click 'OK'
                              </div>
                              
                              <div dir="ltr">
                                <img src="https://lh6.googleusercontent.com/OaapVSkzbPh6d4uFmQoMJ0mYB_n77UpDlJSGM_MxiXTf5LI1_bBA2bSNSqm_igPa_XCChKGtmK47kpF4GBeFXf0We6FZAPdzX3vIwPzPyiYQdbhD1Ufz-IpPkePM2CRwAIHbYPyjGOhFAXbBng" width="408" height="362" />
                              </div>
                            </td>
                          </tr>
                          
                          <tr>
                            <td>
                            </td>
                            
                            <td>
                              <div dir="ltr">
                                Format the disk and set it as active
                              </div>
                              
                              <div dir="ltr">
                                <img src="https://lh6.googleusercontent.com/f-vsu2epdm3EVnFaWvK-4K4z88fDFXZjW2D9J8zibzD0TFVMTPlK9OQsl-AWhlD1pfhllpxuLIpgYHyFMlrscIz1_OqQ6xAjnNp0zA049rWuuqaLLuab5cVcQ6HGvWx9jjm0k4JLIRqOeZzhrw" width="563" height="79" />
                              </div>
                            </td>
                          </tr></tbody> </table> </div> 
                          
                          <p>
                            &nbsp;
                          </p>
                          
                          <ol start="2">
                            <li dir="ltr">
                              <h1 dir="ltr">
                                Clone VHD to VMDK
                              </h1>
                              
                              <ol>
                                <ol>
                                  <li dir="ltr">
                                    <h3 dir="ltr">
                                      Backup Citrix vDisk
                                    </h3>
                                  </li>
                                </ol>
                              </ol>
                            </li>
                          </ol>
                          
                          <div dir="ltr">
                            <table>
                              <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                <td>
                                  <div dir="ltr">
                                    Step
                                  </div>
                                </td>
                                
                                <td>
                                  <div dir="ltr">
                                    Detail
                                  </div>
                                </td>
                              </tr>
                              
                              <tr>
                                <td>
                                </td>
                                
                                <td>
                                  <div dir="ltr">
                                    RDP into the staging server and mount the VHD file you want to update:
                                  </div>
                                  
                                  <div dir="ltr">
                                    Cvhdmount.exe -p 1 \serversharevDisks-&&65Tn01.13.avhd
                                  </div>
                                  
                                  <p>
                                    &nbsp;
                                  </p>
                                  
                                  <div dir="ltr">
                                    <img src="https://lh3.googleusercontent.com/rhTJYx3a-hdIworNGBGnMV4pm_9Bofn1wlQYDXbop_FH9VPhLFRsvNeKDseOITBagjGJJiWpLXfdQmREdMLcDcpZVV0nAiek0e_PKIkoftT7DWW9i4YB2YWq2GXgiVtP7Z8L5BoMFDIacIvntA" width="563" height="275" />
                                  </div>
                                </td>
                              </tr>
                              
                              <tr>
                                <td>
                                </td>
                                
                                <td>
                                  <div dir="ltr">
                                    Open Disk Management and confirm your Citrix VHD is mounted and the new VMWare disk is present
                                  </div>
                                  
                                  <div dir="ltr">
                                    <img src="https://lh6.googleusercontent.com/PDOkigKUqRNaOfDtsDXxxNf-0v4Dmkw4nxqpwVVzKk7ZR2mU_lWqkK3VDp17XYcoOFRsEK1nIQvA6NzzvHd3I3cJFwQu4G3dk70MtiH5CZUcnojnlXJ2q1uEsSILzw42LvBmspRsf1to91LdlA" width="379" height="305" />
                                  </div>
                                  
                                  <p>
                                    &nbsp;</td> </tr> 
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Open 'Windows Server Backup'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh4.googleusercontent.com/Am5rzty36hItghvQbwXVaWW9XDGWHzymX-s_rIlGBloyEojnmkR3r62nbWNBp050sUWC1UKQnP4Vkqu4-6Vq35Z8hTv2dM2u7fxGdZN7Uk2vAUH_yYjbMFa5RltxrdR6nnvs5FFvRofGpFyJ3w" width="237" height="39" />
                                        </div>
                                        
                                        <div dir="ltr">
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Click 'Backup Once...'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh3.googleusercontent.com/xmzxfEW8Ns8aH6h1rcXJtr-g59Z0wxdHOQSqxqUNFqmD9prSiujTyys-G0pF1YqhPSgvPitl8wOCkReod1zRm3dODfMtF1wXOSpTuku0U-cwX3Wn5PKyOUgxZVKi-6OBNiUM9TQzVaMfhVvHxA" width="268" height="264" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Select 'Different options' then 'Next'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh6.googleusercontent.com/AXqHbkwXxZOvx9HAROSonMGD8sAYnwug4Lx-Hvku0yogQISkSncP14lACovDFavFZQX6gjajMgfhZv4709QHYf7WKppHoi9nKTvL7kkFQZm9DMF8IPfY4TGPr7TAKQku112UjCL1cMZslqp_Rw" width="496" height="407" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Select 'Custom' than 'Next'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh5.googleusercontent.com/FuMbzV9D9vc2fByBltuZyIuizPepvRiynohMCPfftRMbobstqtYeuZ9_9IOWQLqBLgVXcK6U8_Il5dzXHmD7eqaQ5eBqFBZ0OLWJvyy1rodrrmxi5YWG8K6XTRU6bW7aNK5xIyBbSvEmX7Xevw" width="427" height="352" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Select 'Add Items'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh3.googleusercontent.com/9gqz5tw1PpP19yEyhO-wyN9h7CxLUKMd0pxFNjs9pXdOek9Dcw4haraRT4D48GVczInkMDQXcgFK_VsgLrSfyI74vKMbWcVvuCYa4mc1yGdLF7uzpxotdzPx0MxCoM1fSrxQbLOgBK6cqxsa2Q" width="430" height="355" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Select the PVS disk and click 'OK'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh4.googleusercontent.com/tzuTbCyhUJ5TTEQhpwYPZ8hhJSDMt2TvEPHlBjCnJLT1AxSESXsH_57hGKmm0L8XxX2c7bmmB7tL3pS79QHGOwhHDpSUDcJetYhoDSIfRgNmOjFos2NYrQpRCt5XvmFEug117T8gZtzQesHh3A" width="436" height="361" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Click 'Next'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh6.googleusercontent.com/VUwOugCIEWt8AJOrneIv9oQj8nEmC-QBeWbq9fh8Qs0EYL8iHgMFxC9UksaxzqKGNvw4yv8sncjEi5D_nTQk5pGL9N54wSsJAs_FUL67ydFgzFzVXzOUXJd5MZRFII58vVLrM1UOcL1LUuZbEQ" width="424" height="349" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Select 'Local drives' and click 'Next'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh3.googleusercontent.com/4thR3UaQgMeO_-Zb_nwcWfwSRHbWH2_dYGV2gGZPMNBEUHZKnlpZdRZsPro7vCQQ3I_wI3v4Bpd3ubcgSkG95oWxh-AoXFJTD0DXxHj5WhLSQeagdcmZPFGmdU0davCnyHdWXL_B7WCCJJ6qPw" width="347" height="287" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Select the 'Backup Destination' and click 'Next'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh5.googleusercontent.com/6oYvoLN3mdtG_kfypEof0KZoG8OGYhL4ZpaISWRCZ4bSBc-_dS08E2IkYDai0armfFm2Nw_uqxnKxUiy5tPzVGJy5BTIgMYd5S3xL6he9zRE1ji2kBksw_-GJk38Bj8EMXAu-WgMBdrDHuwz2g" width="367" height="304" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Click 'Backup'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh5.googleusercontent.com/VUAd0yewTwuvzVlig5Bz_snunys9fCDxT88WCRAEDRiiefrE_W4xQEP4vJK07HdeRo8YxHTqfNC3b9d1_rzcsfMOzY-rS5m0482UrjdsoWjV2BmNX1ksFT3Z6R4VEj_y51Qh-k6GUAxX-uo1bg" width="362" height="301" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Wait for the backup to complete<br /> <img src="https://lh3.googleusercontent.com/acFl-CBgsdzgD-VviArAyi0Vq1JUbhxAVWezU1xSIeAhTptPk9pZLvRNcn_8KUTgVdiUx5ftLH0cv6fETF6wgxjrh75f4lchp-5af16Opu_ZQxbX7BoOlaMKtNfTK96GipabIw4aOenGtsCOhQ" width="378" height="312" />
                                        </div>
                                      </td>
                                    </tr>
                                    
                                    <tr>
                                      <td>
                                      </td>
                                      
                                      <td>
                                        <div dir="ltr">
                                          Click 'Close'
                                        </div>
                                        
                                        <div dir="ltr">
                                          <img src="https://lh6.googleusercontent.com/5D-1hMDSbnYYsQU72kA28w7w2zdw1JSLQC0SucHHhOJCJp9ZJ_ePQYWrl89cSivwgomnDrph2vH0o2jeARObooCNK2ono3xzDZkgGu6d15YaF5maA7lMmELuIViZHwTeIn1zF4I0EfYUx-V0tg" width="563" height="463" />
                                        </div>
                                      </td>
                                    </tr></tbody> </table> </div> 
                                    
                                    <p>
                                      &nbsp;
                                    </p>
                                    
                                    <ol start="2">
                                      <ol start="2">
                                        <ol start="2">
                                          <li dir="ltr">
                                            <h3 dir="ltr">
                                              Recover backup to VMDK
                                            </h3>
                                          </li>
                                        </ol>
                                      </ol>
                                    </ol>
                                    
                                    <div dir="ltr">
                                      <table>
                                        <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                          <td>
                                            <div dir="ltr">
                                              Step
                                            </div>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Detail
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Recover'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/9q46SLhQB2WB0e7qUfOx162NhosrV3q1HBqiG6HAYmPLy9c4UDd7IzqGP3OM4XyMCH7fqEQUupkdl5dUk1EUB6y4syZy2pux4MLHoRfeuOFNk6eHElEOokF6beGJvufRMTBlb51uEteXc8_Q2A" width="264" height="229" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select 'This server' and click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/b2kbeNVw7pDT0tkb337mLIvBt9NfJcSYQ3SfwM9hEHRkikaGcRGLCNiFcu2mD6tHkug1qTthjMYt4WArbLpfhZT1FKBoKlsEFRjH9L9hqqIt0QE73tK_16_RGbb99O6y8Qfa24o5Zmx80Y40yA" width="355" height="276" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/oCaDEyvLHTgCIPuwItScqQDBO2ph9FeRwASs4p5lWhEOE12XCB-_PCSnSBLYNinxk-yFb0qRmx886uOPeTSoZ_-VtQ18h-Q3h9LU5XiJjLPXwG9gzIWH2EM94ext0X6AsO0Xq5klSFyFglTQfw" width="363" height="283" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select ''Volumes' and click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/-JPKXJYLy_Id3_UZNVik7Y1hiJomao_TTV6b8OdR4ydm7Yvvy2o_iPE7EFIrCcHNmp1zc0o5AtMXiJmPll8GeLMiseGl8PnvkwVOt2hUTjZ_JAMMvjKUh9S5o1GBHGZrgHn7ZQu_EUkrmWlF_A" width="324" height="253" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select the checkbox beside the volume and choose the 'VMDK' for the destination volume and click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/O4WSmt3W5f_KVEi8t8NuEuZSVdV5VKR0yV4pBurIqyrd6EuVHCDjLypacyYhT7L6vT9l-D5-dMKElTYin5wCEFr8azwFv9dnpH-SwOZCqICI6fILf4BPdjtBLqaLFLBbCnxpw6pn1jOrBb0Ryw" width="370" height="288" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Yes'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/Jcp6mGkSF4GDVx8dqZDJS3mxfQznfNFpJC8i4WOpmo4MWYfpXDbT_sHOYF9NttR8c7qic5CuhO9Y4_BoULCvvLqJM1b6obKJT_wBI7C4pv1aXrOjrD52b7R8KDigf1-Xxo5EeTScYFv1WvsYyw" width="417" height="215" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Recover'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/6gNGNR54D_p0rVmqGqdYYT_MImAwo0FgSXgTgJxggX7nQb8ZSdj-DUgJLQ7tvewbIv7ZLCawsY23q7zfef3z03DU5uccXpjXlN8xhtgtY528dOzwLlZF3l-EPkYcDKLnlUFDkDW1IFl1Zhj8Yw" width="563" height="440" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Wait for the Recovery to finish
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/G71kTfj0TFQTrRisE-TTTsq8dkzEpqpkY5svK60aY_1Pi_MOHdYbLb7TKZEVUUYA38X9c1xsmbNk8gCE2wIwspLcCWZa65hk4AuD53qak_PRLBobac9pJDKpg1KyhcWaWeNzan2nlSyWOuRjvA" width="368" height="287" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Close'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/vVfpcNG081X70umH3vQNvhETIq1MeGUaYdEAmSEEq7VyFUu6O1g8OI-croiiHUQKPd1Mcj0fRbyWW9sZHL3e3akXk3rIoXxujxektjjD07ZzJ0SFsocVQbI9mCmegsNRXb45E3uzOyK8XRSY1g" width="367" height="287" />
                                            </div>
                                          </td>
                                        </tr>
                                      </table>
                                    </div>
                                    
                                    <p>
                                      &nbsp;
                                    </p>
                                    
                                    <ol start="3">
                                      <ol start="3">
                                        <ol start="3">
                                          <li dir="ltr">
                                            <h3 dir="ltr">
                                              Fix BCD file for VMDK
                                            </h3>
                                          </li>
                                        </ol>
                                      </ol>
                                    </ol>
                                    
                                    <div dir="ltr">
                                      <table>
                                        <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                          <td>
                                            <div dir="ltr">
                                              Step
                                            </div>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Detail
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Unmount the Citrix vDisk. Â Cvhdmount -U 0
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/Hf60zqlqKesgKg-Tsq1x0D4YeavF0D5ikOGjCwlT9n7r8Q-lVdGJPqvkJgeo3WOhwCFSfzPXnLgz3zu_WJ1W52Tbjm5KgvvYP2gw5Aj1MIj_IZ1Sv7wj4xnca1O5ayaLa0CPLYCzB-IrgCmJYQ" width="563" height="139" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              In the command prompt, switch to the 'Destination' drive and check the BCD file:
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/EDwx7LHd-T-uZeT9rI1bbGLe20atltPxahj5aq-nN6FlYM7udxbyBclMHrC1jP_mI2-y-0MBIrrYwqkY-n7iz__18IwRp_N0itbGiGlDgRcHLGC3qwD9WSoYXNGqWkrthD2UwL_30XkLR7wScQ" width="542" height="508" />
                                            </div>
                                            
                                            <p>
                                              &nbsp;
                                            </p>
                                            
                                            <div dir="ltr">
                                              Notice there are 3 entries that need to be corrected.
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Execute the following commands, substituting the 'E:' for the proper drive letter:
                                            </div>
                                            
                                            <p>
                                              &nbsp;
                                            </p>
                                            
                                            <div dir="ltr">
                                              bcdedit /store bcd /set {bootmgr} device partition=E:
                                            </div>
                                            
                                            <div dir="ltr">
                                              bcdedit /store bcd /set {default} device partition=E:
                                            </div>
                                            
                                            <div dir="ltr">
                                              bcdedit /store bcd /set {default} osdevice partition=E:
                                            </div>
                                            
                                            <p>
                                              &nbsp;
                                            </p>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/mkQ_xrPFQg544N23Rko_g43q3-12hgOlqpUN1G9FbnpY4YS9uii8nYO7Gsmr1Wyp0HfnKP-5JL05ZFcNA-9hh2qnCjIFzI8cVfd0RX1ep_Whap1mKMow2r30xi-yB7UOgmbvNXvZZ6lSihy3UQ" width="528" height="549" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Confirm the BCD file now contains the correct entries:
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/1ew5PofqOk_EWcj68t_rKssb8brh-oayvN7qxIWCg8aPQDp28lLD-kksD2jOJ2DUFpmGUrqZkxitAZIx9u4gMQ3bFkmSWOJdknahjH-qy4IEvzZ7LGlR0rBCu1yuyXH6lV7Xbb6n1LtPO1tYiw" width="505" height="380" />
                                            </div>
                                          </td>
                                        </tr>
                                      </table>
                                    </div>
                                    
                                    <p>
                                      &nbsp;
                                    </p>
                                    
                                    <ol start="4">
                                      <ol start="4">
                                        <ol start="4">
                                          <li dir="ltr">
                                            <h3 dir="ltr">
                                              Configure BLD Virtual Machine and attach the VMDK
                                            </h3>
                                          </li>
                                        </ol>
                                      </ol>
                                    </ol>
                                    
                                    <div dir="ltr">
                                      <table>
                                        <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                          <td>
                                            <div dir="ltr">
                                              Step
                                            </div>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Detail
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Open the vCenter console, select the staging server and 'Right-click' and select 'Edit Settings...'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/XCriOv3kLZWNO0nY2dFVBn3Sxs4GxGTC0l9MU01rMTOpABNGzYRGTI7daDK12KBNbk-VcdoMUliOeBruuwt7zPcu11tCgRMirPifyoEZKxuOHRVFrKbKnNBtEEc-AIKTkfZkVsEd" width="222" height="262" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select the VMDK file, note the path of the Disk File and click 'Remove'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/Se01C8tuz2vASGHaZAp-JlaGy_cMW4FJstemMyhiPfL9nI2maGcUamCHaXV2VsW4Txx_-DR4AmmgaBn19SEUCLt_u-N7SBL5ba4Mz1ca4G7AfKFYFXLh92N28XvQPQopu5UZbFn-" width="560" height="293" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Under 'Removal Options' select 'Remove form virtual machine' and click 'OK'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/sORN7Kk1_6Jz-eHKGv_lHuyFBDJcF13MPlTe3--pZYNZ4_0QB5rVexUX1EB7AeipfO8Tq-3ZOoiKie8mT3gkbqkGcyjru16IFJJRU7fTRbzCiMuhMsfmqT1lO4l-HWp101RFe9hPwI2GMOcu6g" width="563" height="284" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select the associated BLD server of this vDisk and right-click and select 'Edit Settings...' Â In this example, the vDisk I am modifying is &65Tn01 which is associated with BLD server WSCTXBLD351T
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/zQH2oGVQuWis2GXWhLNGCXh-TmLtixrghqN_8lNYYCH5Op6Vm2zNjB-PfbM3X1csVYg2_YhXcsEs1Hj2JKtFJ0xsuqJE1vXuVqdPA42ZdYWSsMICi-hSdQXYSZrIcOod1fa6m9lwdPZ4S1Xoyw" width="355" height="127" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Add...'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/ZloeqqwK_a2LLM9AbWuJgUKrn1Y7f1HGRBlEyQEYHBmWr77EltUwF6-9rz2IZlolzQY9Nu0iHRV9GQIvzqg84TEGP9uaVspOZ4f_BUuwbYIncOVHB8ePb77EiucmNuuIB5_3fEG_PtqkFZc6ww" width="371" height="316" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select 'Hard Disk' and click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/h0MDczofOuztf8Jm4JS1M2EB1M2Tlq31Aq45iitsr2GOns24HfyCKQHiUccloriyU5qGC0E7RMv7AiifW0jqMEg8iS5AeJ7VRZdd3WsEC4g2jQrja8MZ1tBegTLems3ds91EXZkcJCf6360AtA" width="563" height="439" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select 'Use an existing virtual disk' and click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/T-E_-DNBt6-aFYo-APLen1NWXhcMOLvH2fxj6E9gkBKbfTLoahCE2Uf9zMHLXDEJD47ORcRSJN_8OkWaD5U0D2mDv835XGN6Vp24AyitOvTKdvshzbVdL86Bvj3Lz6-uvXK07vqyQQKg1DA9GQ" width="563" height="438" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select 'Browse'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/dq67kU91-WbzMvmB9URl7kKL-jcmkIXZ31NCVoFvintp7ci5COgBdvaPHpcyYRyXQK9TzznWLfPbp5S_b5eLag6xfPNQk8FkoxZun0LW3x35Da12dMTjUleiaO5VQKe12gtlJIG79hQ-2zSwMw" width="563" height="179" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Navigate to the path noted earlier, select the disk and click 'Open'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/f9NjFXQo2N2YhljKMSyGSr9dwjYdhOoh5U932g9z7dto-X2QYUQ6SNgOQ0PmJx-WXlOubaZIn7Hwda1OfEyNsZF7q7MyMem8jifzL56-SeP9uEDg5D8SMvNQmOLB7Q2Ygc3yPnH9YSA5awPeHw" width="467" height="332" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/V3L1ZnuopM5dFDAZgXxUGABkm3M8LUygpWquoVxHat_QhvZVIdgw9waxsFjwxHtJnC49nfRc-EgzXMFXwtzlWJFpyPPhpRHlE-hW96V_ev-rkNP_1cc4c2Vo4MeCcEO9cTjxpc5L" width="430" height="338" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Next'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/MdToOc7uQo9j_H7-a_hahTXrVDzuIJ4wMQhmMFiVZOEmYxiLou7ZX0ZJaSh1OcJnU-fhVRrDFbcNPCK0-ZYTEByeIacQdVfZQ_7rW82qx3JcGTtWv1cIooNx1ojOSbZgGQqeQ8JbJBeAlrpR5A" width="427" height="332" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'Finish'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/6b1sS_ajVVjmZFOX92kgb8zYfQft_AqMjuZr4E9vMc74JyX6t8Z4XdjVz3-oQCwM5oGX-8G_nnlL_1xko-lhK-XLnhJkqda_aqrMBQk1PTXtXOihEPM7aPVC5uIZ6QxV-nimuvRY" width="408" height="320" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click 'OK'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/4UVC5pJb0nt2ChCo76d1MwvMdXbtZkC2dUWfTcSKXLDtgdg-pZjih_g-zm13FK8ztNKw82XTKxSBcQVU96leaWSmZGqnu9yiUKj--O8Jpqcbp_MyK4U8Rq8K1OVrYX8IqFASaJuN" width="560" height="492" />
                                            </div>
                                          </td>
                                        </tr>
                                      </table>
                                    </div>
                                    
                                    <ol start="5">
                                      <ol start="5">
                                        <ol start="5">
                                          <li dir="ltr">
                                            <h3 dir="ltr">
                                              Disable CDROM attachment on bootup
                                            </h3>
                                          </li>
                                        </ol>
                                      </ol>
                                    </ol>
                                    
                                    <div dir="ltr">
                                      <table>
                                        <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                          <td>
                                            <div dir="ltr">
                                              Step
                                            </div>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Detail
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select the associated BLD server of this vDisk and right-click and select 'Edit Settings...' Â In this example, the vDisk I am modifying is &65Tn01 which is associated with BLD server WSCTXBLD351T
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/zQH2oGVQuWis2GXWhLNGCXh-TmLtixrghqN_8lNYYCH5Op6Vm2zNjB-PfbM3X1csVYg2_YhXcsEs1Hj2JKtFJ0xsuqJE1vXuVqdPA42ZdYWSsMICi-hSdQXYSZrIcOod1fa6m9lwdPZ4S1Xoyw" width="355" height="127" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Select the 'CD/DVD drive 1' and 'Uncheck' the 'Connect at power on' and click OK
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/jneDr0O8RXlOw74cFbQrRDkH0S1c5QysoA5a7VM_WRyZJO-w_upVOeYR41LlCwpPyRy5PRHawNRVl7JqmeoNv4Lot3RXwhQOjdwHS1sUzqEulj-hyvQJrtOlK2f4VBciFTd1tZ48N1nNEtvjyw" width="563" height="498" />
                                            </div>
                                          </td>
                                        </tr>
                                      </table>
                                    </div>
                                    
                                    <p>
                                      &nbsp;
                                    </p>
                                    
                                    <h2 dir="ltr">
                                      Start the VM and uninstall the target device software
                                    </h2>
                                    
                                    <div dir="ltr">
                                      <table>
                                        <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                          <td>
                                            <div dir="ltr">
                                              Step
                                            </div>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Detail
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Right-click on the VM and select "Power > Power On"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/93MkOu2fmnLsyKKJtD5IAM1GptY_p2dZLDQhweR5zhHy08NgP3wIt8sQtAH1KUpYDUVD3tuwDClShq3ij4UfTrxIJZESpHWT2_3mfRl7L8GDSA5_j9ypV-FWXtVgiEAmgD5Y8Lm-" width="551" height="181" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Right-click on the VM and select "Open Console"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/N9tBZfPkFYaqjEogR8T5LFnFuWGN5bANlWrPoT6jsxJhRYOIP1eoHIEiJew6DokxvaMDTNRYGztFVDgfXwLcR2nsDzHjVhjocZBDaqu3s6FFxN3gTl-aSAVEh6Nca3oCJqEAT63a_ZkpLGDkPQ" width="359" height="104" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Login to the VM once it boots
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/00ELY_7bQOoSEsWFkoMWeU1sAHgKnly79o-yGfeoS0MUXxu1SpkvD1jq2XCWqu1ou_b9QDXsrcjZo6Nlo6qO-qtN5B8LkPsbdPurMFEqqjw3Nwg8JIXAxJbUtuJEpsBeLs3z-Bwe" width="560" height="444" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click "Start"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/5-aA-emjC5CsWdeHq6geVe6k9mO5-HM8A-7Rey6O_qIWOA1fPAsfbwpsglzDTm-7PEfW3pMrvO-sMoTU4x33lHW8Pqs27LrwTIzpnrOyJBuhU2r1otoAnkPwykjJKBieZRb3pqzc-tr39mjKrA" width="157" height="74" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click "Control Panel"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/Bi9mTOtT0wTUcbF5WCG53Vo-kCEXatq3SfGkJWRjMK1pGVWRbloWZ2TwHXgrMOC4vKZEt9LVhgBp88GaY9JlrfNzO_dZcUrXQb--PsY-A3rf4lyRFpz7OjNg2q-zbSwpHynEnl22TU7_GW9YSw" width="305" height="331" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click "Program and Features"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh6.googleusercontent.com/BIoxJVuGcykMIA7cGQtyzJj2LxAtc2OvoMf9BXSJRZqMuNgo8t6Q6nW_n9sN2Tx5cCakj8c_e0lRzkeCy800T2Y75gqpt1brLBfxw41jEBcTG7y0WzvA06M8s_1pIiIVKy1rrx07eh4u7xdXAQ" width="563" height="368" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click on the "Citrix Provisioning Services Target Device x64"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/iEp_wMm0CoMUff_aE0BtIHMLmmNZNY8LCBsMyNEvF3s199PdHXE1WZCULjecQEf7-7ilIEZBTpbq_gSUkD-Ti-ueE2xqPbBhAk1gzbEbLseIRs71NTmHg0QKnt34FQUIOrYMhVNSrhlRfD5PZQ" width="440" height="322" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Right-click and choose "Uninstall"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/gG93xW7VEKCZEsttzEVVaug_HyVDWyfOxmttPNEg7AuCuT-k6vTGY7S3EP7y8PPUTySEmqaxSJU2I3P5-PzHGsxopj-TAVtvxD8CZGem-EgXxZ3sayyjo0CSa1aTX08wY0bh-f-RMrkPFbWctw" width="563" height="124" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click "Yes"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/cHPOsiggNwaEzKoUvlthfPAPFepR07jYgqxhs8DfKM8ymlb-8ZWXbLypCNb5IRgFnTZFWIUmY0lgi0AJfzBFUHEOvcc5dgFX-1Z4OnFsnsLE4R6lTSdkZsoLCMl4B50mB31qzo-Jj1vjL1i78g" width="337" height="89" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Click "OK"
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/AwVxoybMr9GwPsyniYoibL5PZKAHpL94RUz1z0dBn2i8uxPFT73ph2vnnCgMFqi0n6slKDw9rJ8G1tZyyCvnhvmIT47fTWQjkCdDZOtlB9p5Ci4dLc-W_1H-yGSkcW0mjwI8YZer81MnOApbAQ" width="253" height="100" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Wait for the uninstall to complete then restart the computer
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh5.googleusercontent.com/y9ISRBO4ZjvFLCoWaHh16TfhvzbIut1ky2xwucDwOusWFAYpg9U8xhZ9dG3IbORqmmXtuAeuE45yBchC-1HhpVnEvpPDSrsdik-T4_9CEsja_lZAT3jWbHfcU0aSbnBqs-Zh8bca67uYOZx1fQ" width="292" height="110" />
                                            </div>
                                          </td>
                                        </tr>
                                      </table>
                                    </div>
                                    
                                    <p>
                                      &nbsp;
                                    </p>
                                    
                                    <h2 dir="ltr">
                                      Upgrade VMWare Tools
                                    </h2>
                                    
                                    <div dir="ltr">
                                      <table>
                                        <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                          <td>
                                            <div dir="ltr">
                                              Step
                                            </div>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Detail
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Login to the VM once it boots
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh3.googleusercontent.com/FqLBY1oso8JHxBI_-WrvzFyMyf0qv9SmD7Klz2LBsx4t7swkSeBb6eENSeYZsxtPJPml11E4w7EpIZcQ-l2dytrnGSc-IprEeXIZ8TwVGdaZoAEyWfSOw-u8D7k3W7rDjLf-wYDF" width="560" height="444" />
                                            </div>
                                          </td>
                                        </tr>
                                        
                                        <tr>
                                          <td>
                                          </td>
                                          
                                          <td>
                                            <div dir="ltr">
                                              Browse to the VMWare Tools install and open 'setup64.exe'
                                            </div>
                                            
                                            <div dir="ltr">
                                              <img src="https://lh4.googleusercontent.com/wx92NJ24rWe6fxttRQpwDQx6bqOVrDwRT-RQTz1TOXL8kUTuaTShMHdiOHcJU3caZIinNm_gLMNw043ymlM4Hx9oR2aObHDrneH5LlMh4tUUsvB-HGJZ2Cif0-3opefhG9oWJRbwn7b47jd6SQ" width="563" height="234" />
                                            </div>
                                            
                                            <p>
                                              &nbsp;</td> </tr> 
                                              
                                              <tr>
                                                <td>
                                                </td>
                                                
                                                <td>
                                                  <div dir="ltr">
                                                    Select 'Next'
                                                  </div>
                                                  
                                                  <div dir="ltr">
                                                    <img src="https://lh4.googleusercontent.com/6-La1liO2Go-ugh4Lmi4QFxYgmZtkXz9Z2EDyLabIWL-r68WZuEY5eZro8kEAXBkRhq984ZqUCBluPFpJfRVVodXUUphFP6y7vPOq3gRsyaXEXT5LZDrMMyNI8PhBht1MBSYtXwHEKhXNBUxQw" width="381" height="293" />
                                                  </div>
                                                </td>
                                              </tr>
                                              
                                              <tr>
                                                <td>
                                                </td>
                                                
                                                <td>
                                                  <div dir="ltr">
                                                    Select 'Custom' and click 'Next'
                                                  </div>
                                                  
                                                  <div dir="ltr">
                                                    <img src="https://lh4.googleusercontent.com/DXdfyU1QJS8AJi04xDvP8dvxDzxDqCWyZFRExD-HbUyMF92oA5kZBnBYUzc7sRUeMp02saJtXAZHLFQOiXyJinAH6JIN8bnK2r_yr893pRBvPdbAVICtXWm3dgydki0Uk0Qqg31yQKsjISisZQ" width="397" height="305" />
                                                  </div>
                                                </td>
                                              </tr>
                                              
                                              <tr>
                                                <td>
                                                </td>
                                                
                                                <td>
                                                  <div dir="ltr">
                                                    Ensure the 'NSX' options are set to 'Entire feature will be unavailable' and click 'Next'
                                                  </div>
                                                  
                                                  <div dir="ltr">
                                                    <img src="https://lh6.googleusercontent.com/B0b2smP13hnp0M0EzJncHBY0Fae4rpLFdFKCxH_KBH7H2A5Vm5wmvt8sWf5DVmcl7hfJ5ZUQL_7q2zmANbTKb_2Qwza4uMCyFwTzzufoxnMrw8mIBeiKkdZnQ9t7r7RMjPeb50zEjsoet7OpgQ" width="407" height="314" />
                                                  </div>
                                                </td>
                                              </tr>
                                              
                                              <tr>
                                                <td>
                                                </td>
                                                
                                                <td>
                                                  <div dir="ltr">
                                                    Select 'Close the applications and attempt to restart them' and click 'OK'
                                                  </div>
                                                  
                                                  <div dir="ltr">
                                                    <img src="https://lh3.googleusercontent.com/5FLLa7uof4x8f42vAWUnYR_Dr-LBHGHf_z3HJIvHPmg2qbz1rFcXs6MMf6GM-8qEAaIkTNHLsYGuIDsAcoAyQ1MFBRIvB4gmYzLwahwQ1IaKPBMcJC4M064duTFVaKuXkbrCmOUDsaMS0YpqPg" width="407" height="312" />
                                                  </div>
                                                </td>
                                              </tr>
                                              
                                              <tr>
                                                <td>
                                                </td>
                                                
                                                <td>
                                                  <div dir="ltr">
                                                    Click 'Finish'
                                                  </div>
                                                  
                                                  <div dir="ltr">
                                                    <img src="https://lh6.googleusercontent.com/4sP5JEQPB7KPLQwjOwO1MsfU-9M4vOoIlqPGPgx0nXrP9tQrOrxqMwNSSKoU5VLEXAWv1_o4w2Rt3zF-jErKbUF0rx7ZedN3gm25VnsZoys4MyGa73HOsD2H0S0lUnDE-agVU3hmegERHUEDIw" width="498" height="382" />
                                                  </div>
                                                </td>
                                              </tr>
                                              
                                              <tr>
                                                <td>
                                                </td>
                                                
                                                <td>
                                                  <div dir="ltr">
                                                    Click 'Yes' to restart
                                                  </div>
                                                  
                                                  <div dir="ltr">
                                                    <img src="https://lh6.googleusercontent.com/19Qbu7TVvwgoWPTfLbLryaNQXjrbS76kD-EpCZ4oStqiTRP9PSNNoO_t7_6y06wnBZV2VoSivJH_wVCiCkHd8HD4DCpk7DYO188oJENP4ZrKmvsM0RHAWl_2NbxJ3QWVpNK-FeyfCpLBGSlkug" width="366" height="163" />
                                                  </div>
                                                </td>
                                              </tr></tbody> </table> </div> 
                                              
                                              <p>
                                                &nbsp;
                                              </p>
                                              
                                              <h2 dir="ltr">
                                                Install new Citrix Provisioning Services Target Device software
                                              </h2>
                                              
                                              <p>
                                                &nbsp;
                                              </p>
                                              
                                              <div dir="ltr">
                                                <table>
                                                  <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                                    <td>
                                                      <div dir="ltr">
                                                        Step
                                                      </div>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Detail
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Login to the VM once it boots
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/eWIkbnoXH1I8A9VCSlzLViABjU87WIY_mPhy2nyBOuTk6FTT2Iv35Qv_uKzW2RhH7_cI-DQVKkRs0nfrOCjQxMBhoc743gNW66lxnznTUPIlNgrZ-fn5T0qxGO_BO-8pryaT6mj6" width="560" height="444" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Browse to the share that holds the updated software and open 'PVS_Device_x64.exe'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/JbWq-bQdZjPrbxhnC97wU-aKajmVTmAnUySbRAaFDZx6Hi7vmIYhcT9_zJmxHIoE4loR3mvfiSWEGrH5lweLecadcNClP9ttrqOzdRLm26Uz44spwtK7LhiJft7q3ZYZd-gBLOb3l2HduRgJ5A" width="563" height="190" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click 'Install'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/MJQEKR6eiMP2VHCutQcCjXiKuurnBCc9ocLPMtuAKcsiGsHRB8HLHSokZFokpl_J5my3FDvRntOcPlbdnZdxdN-0lGZDQzdQletGUHp90GrFG7EQc19KTOHD69OpjXuVdNVUuau2dktBIgP9RQ" width="504" height="376" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click "Next"
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/lPvelxLBBo2uO20xjI9jkIJ2nyzTc4zHKWqDcmwzYTIbVMx-oxrvVV_AAwUGuhHICIPR4nViOMWZtF6TfCB4XiY2DcuZeze4KolvjViBILixveJlKefeJVelYdA8KkrvzL7dMDEJMSL7MFLq_Q" width="409" height="305" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select 'Acknowledged' and click 'Next'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/geKt7akKG56s82E1BTrjVT-hCE-Kn8K-cwUvMMRLVonPyxjlaHFozAMmf3uVYcqfPBaYtHU6nLMJG4wXQ9u_JmUvJ9WaY0grvBEYOHG9WHjPYGHfO_MYYljSjopzIsiNDn0N2ucf7t3mcW2t4g" width="503" height="377" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Choose "I accept the terms in the license agreement" and click "Next"
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/J9OGneschYD6cQz8BbM97Z1snUGLzJskXQKycvPWy301KrGHa2sCqVqOHjDpTdur6n9Ng3Kb73xeDx2KQ5oOO3ELyj__ujn6F5uL2_NHh2U-zuC4fuEw8J1YyYZ0jv4R06gzLLWOxPYZKfNL4A" width="500" height="375" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click "Next"
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/dXpsXM0EJFyETEnIY6fxnaiamn7Blf5ikN9wNAe2ToSJARI1n1hGeyZvA-02CTwvPZm06Yo8QI1jVWIYuM8ezfv5o7wzBmeN1cEnaZPskiVVrdE-7c44aG3J-mG_50mCOGl1DTk3QJJ3J3xPew" width="502" height="375" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click "Next"
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/XMq5-WBrO_7bCKRK8BxnQS0IgSv2wPFpE-RXIrP4zLQdbR2iG68diBYcYa31dQK1aynncS4Hq3FjZYSt7U5AVZqJImNztU99-ZKKKFObz0LaEyyADLnFEisB8mXgHOnB9KAzedlMaI5FuOMtaw" width="445" height="333" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select 'Complete' and click 'Next'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh6.googleusercontent.com/soDsDE00pBZMSCvEj_z1Xex2d7Q4iIK3fkpTqxuW0bkndJvkVSD-0Yua5luFKcySz5VfcKkrdLD_Z-Hb0yLX1-WA_p1-k2WFNWKDJHSFu0KN5wY_Z9hlpSXhLNY2wzq1hm5OSA2623do681x_Q" width="503" height="375" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click "Install"
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/kHChppXZ2XAxZxyiT3pjWvtpb3DyYVPxe0zMMa65f-TvjUZPjdf10RN5iJxQL-1eCjQ9zDiQnCt7Aey5fXUlfqOjtupeUU2nCzfWUnrGOxIPhVCIpFdTNeNQdWcK71ikFl6ZdO2y8e14RbpePg" width="445" height="335" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Uncheck "Launch Imaging Wizard" and click "Finish"
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/si68YfxZ4Me_665cHtA6_dBIXY1LxpWc014DOYx00uC2n5wNM7UxKdImFSOyXWAP1zTrVjiC0wrzZ5lJCKIKNr2LKyKk_ShdBMQf_FarCHKuULZ7tq2t2k_ZplN5NKm9J8f-XaohKe0mUZgOdQ" width="501" height="374" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click "Yes" To restart the computer.
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/XwXPmgn6EvdAGwvNqJ7_OSaNsSmePBvr4AhPyPbNRlGb_DQk6UkUWvOVfG1-Hf8t1bVMMyN7iLv_v6VFwztjelCPG0VN-iBVre4LsXmIICbLsFaMHFUPiZF6zo-eteWl535ZoO2mjW6p2O6QwA" width="364" height="167" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                </table>
                                              </div>
                                              
                                              <ol start="6">
                                                <ol start="6">
                                                  <ol start="6">
                                                    <li dir="ltr">
                                                      <h3 dir="ltr">
                                                        Remove VMWare Disk from BLD server
                                                      </h3>
                                                    </li>
                                                  </ol>
                                                </ol>
                                              </ol>
                                              
                                              <div dir="ltr">
                                                <table>
                                                  <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                                    <td>
                                                      <div dir="ltr">
                                                        Step
                                                      </div>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Detail
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Right-click the VM and select 'Edit Settings...'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/x9GjMVdKt_3p__8MNYj9isVGwhhbLnCdbVgnaUKUZ9ZDzrS6qFRk0on0O8EbOkLGw0wK5FNAsFXsfTbAomuYMmh08NYRxtgkdCcRm7cqAF0dSWH0BmIEW33corNoDKJl2WKcsaScSowLsF3FWQ" width="355" height="117" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select the VMDK disk, note the 'Disk File' path and click 'Remove'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/C0CcmALiTuGwJflsOiA-UALaqSnn6esDPAToRj79wzRH4dUPtaI0O8JIzSbgb73sLfa-RXT3bQDPE-9B2laPf6pEtjMqNQpDJqycus0r-xux2i5veBNX2k1WopjOl1NgoqUieKN_" width="558" height="236" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Ensure 'Remove from virtual machine' is selected and click 'OK'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/hSkFKG3-Cb1lE1s3JuzSCgluqKx1TzOBzqOExIbIuEYQ-FjeybbXbcEA0QTE8S7s1zaGhRQxGNMgsRsr7hXN1jzzfnN3sSl7B985_uyVmtpC01-9kqURnwTJ4kfsgJzaZe7pz3si_ooV2xJ8MQ" width="563" height="263" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select the CD/DVD Drive and check the 'Connect at power on' box and click 'OK'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/J2ADuq_1Jr6slTB_vSHqYeE4UzjsLuUipN6N-zPOHBIlE0cFpy8ns53gdw-t9RGblV0irV61PZT9kvi8m0rBI6664_kWzie04cW8ZTQACQFEf1T8aPwELJBc5zPhqQYM-7YHRRT6STjlWA7TUQ" width="563" height="227" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                </table>
                                              </div>
                                              
                                              <p>
                                                &nbsp;
                                              </p>
                                              
                                              <ol start="3">
                                                <li dir="ltr">
                                                  <h1 dir="ltr">
                                                    Clone VMDK to VHD
                                                  </h1>
                                                  
                                                  <ol>
                                                    <ol>
                                                      <li dir="ltr">
                                                        <h3 dir="ltr">
                                                          Backup VMWare Disk
                                                        </h3>
                                                      </li>
                                                    </ol>
                                                  </ol>
                                                </li>
                                              </ol>
                                              
                                              <div dir="ltr">
                                                <table>
                                                  <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                                    <td>
                                                      <div dir="ltr">
                                                        Step
                                                      </div>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Detail
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Right-click on the staging server and select 'Edit Settings...'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/Y6CTP3JxnLf2WNiFVWFoLKCm6xsMSpzQ5SrIE_FuJcvuUmcAyQZN8WmE0zeq2F6xcJgSVMEFPoUvXGeotMHFsorLzPQcZfWZJMXX3Ed6__vIwojDWf1ThgOwa2ht-5JH3PrD2_WlOFqfa3N-AQ" width="341" height="141" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select 'Add...'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh6.googleusercontent.com/WAGPTxzKiKYPIXrEtE2PErqXf3Yw8jwE85BHMvyhBKvsMBYvim2EeSqOtGyFULP2qwnGp0Udylq2WIU1H6A5sm5YCYfPhbG2YnIvFE-Z840mN6hdz1_X7NIRqmhMQQB2H48V8sTNqKDU3CzdJg" width="295" height="183" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select 'Hard Disk' and click 'Next'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/dMmz443ltizBIr3wZGDjxy4GkEzKTypxCiyMcsz4ZygjN8vo8MUA8jamRwiC-fFKzVh7HQQgBDv2k9DYtvnMqBAULm1DdpHbG_DEjQcMItRcVx7cBNt3_vga4nhWuNE4OhR_b6asz4kQxH3Fow" width="414" height="320" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Select 'Use an existing virtual disk' and click 'Next'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/xGo9LLDbIDb-XNXQFdCp0hUanfle7Q_OLv4iXblcQwOfkow3XqaqOttwZE3pckIubExG5_f2EGoKoCPFlKZvKnDoBUSjQfDolo4CZCekpl1gAogE77tZx2CM8StPcPhpyF9PciL_TuhOTK7l4Q" width="460" height="360" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click 'Browse' and select the disk you noted earlier
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh5.googleusercontent.com/ED3NnjgTRp3GUajaNPMgYwM1H0kErvhboupzmHnxdN50EZ4L7A_-8PnhFvdK4b6SysQ5vB6SMx0B1yO0UrxUNfwmgAlb2wEyayuRLnrnxLgE09Hqda30werDqY-G_2tOPYg2zQDww5N_LbaERA" width="563" height="209" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click OK
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/zjZaptJIQBdGD6sKZaOb-cbsGXXhd-UJ5_rrCCTH7KG86JbTA_FJ0Gj7EySzC4EB3rHz0_vVUOEhAyoPKydS8UHY3szetsTOcvZAAsqiw7RaFwOJ5HN6mMlDisdDyNUvn795HfUaL5KPeyrw2A" width="353" height="249" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click 'Next'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/5ybRhW4xIYzxqIbYxnWqzgmMqJ4NWBOyGOdH2ois9g_spuvl_HwLHLeQTqw_LUZt5OnjXBRPa5NaZPAb6qJ4b4evGtPLBfq6N1TWPQzHgd5b5VJGWKm4JZatwJDjd09JTmSWC-Ou" width="357" height="282" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click 'Next'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh6.googleusercontent.com/jywrjDuvO0_vku5TXWqI2BBZGSSIwZNb50vkSwJMhsa0SXtMwOBXk5vHzsTOfGhQsz_qvL6wIA35Rz1RwRI5IQK-eoXIX3cYiRTgtY1UmLfzANL6yQFIazXg2ZORmeai6ytruC7YmEdBvUqKaQ" width="359" height="279" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click 'Finish'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh6.googleusercontent.com/wfTk0IBov1eHfzo3JJ-SAaqw7kyrEESMEaKy5VqU8SC5Dom5YxKlwFzobM_DcKHZDHYlnOsvvRAX4qa8DdXDfHVw_72LOiB5Xg3j0tKddTwV14ohAYbeiyhhkjKNvCRSWMmtSrlh" width="414" height="325" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        Click 'OK'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh3.googleusercontent.com/YtXI4f5VsOV7bPfL6LiwaS9uj76EY55oiQquWVpKjh8w_Y7PFzkvsH4iYEgak4Hm-GtsW7_d-oRVlaoEQ1WQVl-pKdgJ85RbfRsfej1a9TyCkMeHJKXXZ2wIgOkF2nl9ygoi1kfY" width="351" height="312" />
                                                      </div>
                                                    </td>
                                                  </tr>
                                                  
                                                  <tr>
                                                    <td>
                                                    </td>
                                                    
                                                    <td>
                                                      <div dir="ltr">
                                                        RDP into the staging server, browse the backup drive and delete the contents of 'WindowsImageBackup'
                                                      </div>
                                                      
                                                      <div dir="ltr">
                                                        <img src="https://lh4.googleusercontent.com/lXqwQ93wBk5tQYFZIxyGENF0p-WGx_PdKlBMCl4ZpZv_tq5uVWiQ1uwKIn0jzGG1s8HtuWKY8e4mJtBRK7UjWfBy9kmtn7IUJAbG7X5EEMEtXigkUYhbb_EM1mAafhGi8J0ZnNC5HY5XFsaBRg" width="355" height="364" />
                                                      </div>
                                                      
                                                      <p>
                                                        &nbsp;</td> </tr> 
                                                        
                                                        <tr>
                                                          <td>
                                                          </td>
                                                          
                                                          <td>
                                                            <div dir="ltr">
                                                              Open Disk Management and confirm your VMDK is mounted
                                                            </div>
                                                            
                                                            <div dir="ltr">
                                                              <img src="https://lh6.googleusercontent.com/n4klreeW48UMt_y92zlfzrCpmybxqOrWlO6LuplcNW41J8uJiorCAUtqCdsbMJyRR4umXJgNVLEG42V_aWcfWipLyh5Lf54WuxdO_jc2Xfsfty3M6-kc_12p0yW_gpGHsDbnpn8owNFUfaWpFg" width="401" height="364" />
                                                            </div>
                                                            
                                                            <p>
                                                              &nbsp;</td> </tr> 
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Right-click on the VMDK and select 'Shrink Volume...'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh4.googleusercontent.com/r6IQdlIa0YqK4_joDYBYEn03biLOTceP1alHhRmaTUsX-MHCJeRm5adD-p7iCBqWvlXbKDe7FMJZGTXaffgbZpcv8r1yiMzoDlHcdC4ZnrWRsbVgOAAdHpmfByA4oJYzgl61H0P-xbxQm2y69w" width="563" height="163" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Enter a number to shrink the partition so it is *smaller* then your Citrix VHD disk size
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh3.googleusercontent.com/Dcks2yTQ70LJ01ArR-b573CQp5wXRMVuZ3kdg_Qu9ImNGZBmRVKR1OpCD1PFXnk0POPapsy3QeBOLopkN9MPsly7JshHugriJzYKmWzXfNDRoYQE4ez6H3DPrLN6veBTk7UOXe9B9HO8ajbedQ" width="454" height="300" />
                                                                  </div>
                                                                  
                                                                  <p>
                                                                    &nbsp;
                                                                  </p>
                                                                  
                                                                  <div dir="ltr">
                                                                    NOTE If you do NOT shrink the partition you will be unable to restore the partition to the smaller Citrix VHD file.
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Confirm the shrink worked successfully
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh6.googleusercontent.com/dWilRwohritaVO759ng0crRQ6_E_POLRGcbBU5CUbR6h_ZeMcmqUI81nbJoC6BmP48znpcflzwXElgAERi8V9P_Dx36-Zwm2_gw7L55pFu186_oDhQDlpzDK3lZlykFYVWBgxsR8ClIGqiIQyQ" width="563" height="83" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Open 'Windows Server Backup'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh4.googleusercontent.com/Am5rzty36hItghvQbwXVaWW9XDGWHzymX-s_rIlGBloyEojnmkR3r62nbWNBp050sUWC1UKQnP4Vkqu4-6Vq35Z8hTv2dM2u7fxGdZN7Uk2vAUH_yYjbMFa5RltxrdR6nnvs5FFvRofGpFyJ3w" width="237" height="39" />
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Click 'Backup Once...'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh3.googleusercontent.com/xmzxfEW8Ns8aH6h1rcXJtr-g59Z0wxdHOQSqxqUNFqmD9prSiujTyys-G0pF1YqhPSgvPitl8wOCkReod1zRm3dODfMtF1wXOSpTuku0U-cwX3Wn5PKyOUgxZVKi-6OBNiUM9TQzVaMfhVvHxA" width="268" height="264" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select 'Different options' then 'Next'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh6.googleusercontent.com/AXqHbkwXxZOvx9HAROSonMGD8sAYnwug4Lx-Hvku0yogQISkSncP14lACovDFavFZQX6gjajMgfhZv4709QHYf7WKppHoi9nKTvL7kkFQZm9DMF8IPfY4TGPr7TAKQku112UjCL1cMZslqp_Rw" width="357" height="292" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select 'Custom' than 'Next'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh5.googleusercontent.com/FuMbzV9D9vc2fByBltuZyIuizPepvRiynohMCPfftRMbobstqtYeuZ9_9IOWQLqBLgVXcK6U8_Il5dzXHmD7eqaQ5eBqFBZ0OLWJvyy1rodrrmxi5YWG8K6XTRU6bW7aNK5xIyBbSvEmX7Xevw" width="427" height="352" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select 'Add Items'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh3.googleusercontent.com/9gqz5tw1PpP19yEyhO-wyN9h7CxLUKMd0pxFNjs9pXdOek9Dcw4haraRT4D48GVczInkMDQXcgFK_VsgLrSfyI74vKMbWcVvuCYa4mc1yGdLF7uzpxotdzPx0MxCoM1fSrxQbLOgBK6cqxsa2Q" width="430" height="355" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select the VMDK disk and click 'OK'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh3.googleusercontent.com/ZHQwlnSB4g0471E2HilBIqIgGqXCl583kQMYC4JVVEsBBwJdMi7NqxNYotREMm-Qf6VJV5H3y7IJHmgw8UvaV3-kWRlctbEM3d-9kGr5FxhtloFQpPOVlKmuJvkjFrtKcnMA2X_6CHnIZKVvnQ" width="357" height="295" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Click 'Next'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh5.googleusercontent.com/REUmJb1xX2cK9U3qRz_V2C4hssdcfqyMMqE663BzsCR4K3OYUX_Xf6sZmKmG_CFSDelApbGzCZAk6bVU4JFM_3ayh4RM91Bw8Sch6rviBCcXCLZtqqszuPaWTnIx9nllDhKkZiudJex8TASs4Q" width="358" height="296" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select 'Local drives' and click 'Next'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh3.googleusercontent.com/4thR3UaQgMeO_-Zb_nwcWfwSRHbWH2_dYGV2gGZPMNBEUHZKnlpZdRZsPro7vCQQ3I_wI3v4Bpd3ubcgSkG95oWxh-AoXFJTD0DXxHj5WhLSQeagdcmZPFGmdU0davCnyHdWXL_B7WCCJJ6qPw" width="347" height="287" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select the 'Backup Destination' and click 'Next'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh5.googleusercontent.com/6oYvoLN3mdtG_kfypEof0KZoG8OGYhL4ZpaISWRCZ4bSBc-_dS08E2IkYDai0armfFm2Nw_uqxnKxUiy5tPzVGJy5BTIgMYd5S3xL6he9zRE1ji2kBksw_-GJk38Bj8EMXAu-WgMBdrDHuwz2g" width="367" height="304" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Click 'Backup'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh3.googleusercontent.com/xvuUUHV2gDLlDGFMXEC1_UUVJTM5ZeGWrLD3BjjmAQ3ysD-l6tFsuRRw5C8NPE0iyVaiX4pi7U2LS4H5ul_MyWW4f0KF68-pi83lf9eS7QOdjzLM8WWnJQtpk3y1HKxeBc8ixpXcLJ7dpbzAYg" width="375" height="313" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Wait for the backup to complete<br /> <img src="https://lh3.googleusercontent.com/CAapfUtcNwpdBNIzXb6nz8rhinzFarngLOlLunr4g4t1tOBosFA5RCA8ydJbeoZL_ebwB_UGOnfyamjPvCbzpeZX_geGYYLk8I78yqRa-DfhWxxPSA3oDXTPw1_X9DPR3kOZkPLSfmgRl6TrAQ" width="339" height="280" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Click 'Close'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh4.googleusercontent.com/w6XsiXcCLdBRznwNT9_Visp0GDfQxlbeF6-Z9lRAJytr4boTROvhx6rNSvxLPmvMlCBSGPpyhblv17z__UK9YspxPUX2iZMP8RbiG4m3_A89nfTm3dKc5EFvROg4yvJQzHEvgnw-ZiE2nKxPow" width="563" height="467" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Go to the vCenter console and Right-click on the staging server and select 'Edit Settings...'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh5.googleusercontent.com/Y6CTP3JxnLf2WNiFVWFoLKCm6xsMSpzQ5SrIE_FuJcvuUmcAyQZN8WmE0zeq2F6xcJgSVMEFPoUvXGeotMHFsorLzPQcZfWZJMXX3Ed6__vIwojDWf1ThgOwa2ht-5JH3PrD2_WlOFqfa3N-AQ" width="341" height="141" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select the VMDK used for updating VMWare Tools/Target Device software '' and click 'Remove'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh5.googleusercontent.com/UiO7tiLkm86GhBOfveMYp41oOOI2ifxxxOgz4hyLOQbUvfMw-Q-XdRq07dvYz0E_M_tDmiekjf4_uBvPxzTq9eOHPhTud5DLLIpYe8AJ2Sh5NMqFAwZ8DqDNP35VdsUZ5LODIUtO" width="560" height="280" />
                                                                  </div>
                                                                </td>
                                                              </tr>
                                                              
                                                              <tr>
                                                                <td>
                                                                </td>
                                                                
                                                                <td>
                                                                  <div dir="ltr">
                                                                    Select 'Remove from virtual machine and delete files from disk'
                                                                  </div>
                                                                  
                                                                  <div dir="ltr">
                                                                    <img src="https://lh6.googleusercontent.com/Ttwl59OznVtbLxantV-nCA_RMtIkdPMdSMItLfdHYKmrpusx7CY9XSNjqTwKhy9zinzeXZhH7IzZuG9fSLqnb1QNp09zvF-MJXbV3JO8zPA8H76OV8LTNCEW-5X0MandRN-Rj1fptA0y9VGx_w" width="563" height="280" />
                                                                  </div>
                                                                </td>
                                                              </tr></tbody> </table> </div> 
                                                              
                                                              <p>
                                                                &nbsp;
                                                              </p>
                                                              
                                                              <ol start="2">
                                                                <ol start="2">
                                                                  <ol start="2">
                                                                    <li dir="ltr">
                                                                      <h3 dir="ltr">
                                                                        Recover backup to VMDK
                                                                      </h3>
                                                                    </li>
                                                                  </ol>
                                                                </ol>
                                                              </ol>
                                                              
                                                              <div dir="ltr">
                                                                <table>
                                                                  <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Step
                                                                      </div>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Detail
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        RDP into the staging server and mount the VHD file you want to update:
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        Cvhdmount.exe -p 1 \serversharevDisks-&&65Tn01.13.avhd
                                                                      </div>
                                                                      
                                                                      <p>
                                                                        &nbsp;
                                                                      </p>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh3.googleusercontent.com/rhTJYx3a-hdIworNGBGnMV4pm_9Bofn1wlQYDXbop_FH9VPhLFRsvNeKDseOITBagjGJJiWpLXfdQmREdMLcDcpZVV0nAiek0e_PKIkoftT7DWW9i4YB2YWq2GXgiVtP7Z8L5BoMFDIacIvntA" width="563" height="275" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Open 'Windows Server Backup'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh4.googleusercontent.com/Am5rzty36hItghvQbwXVaWW9XDGWHzymX-s_rIlGBloyEojnmkR3r62nbWNBp050sUWC1UKQnP4Vkqu4-6Vq35Z8hTv2dM2u7fxGdZN7Uk2vAUH_yYjbMFa5RltxrdR6nnvs5FFvRofGpFyJ3w" width="237" height="39" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Click 'Recover'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh3.googleusercontent.com/9q46SLhQB2WB0e7qUfOx162NhosrV3q1HBqiG6HAYmPLy9c4UDd7IzqGP3OM4XyMCH7fqEQUupkdl5dUk1EUB6y4syZy2pux4MLHoRfeuOFNk6eHElEOokF6beGJvufRMTBlb51uEteXc8_Q2A" width="264" height="229" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Select 'This server' and click 'Next'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh3.googleusercontent.com/b2kbeNVw7pDT0tkb337mLIvBt9NfJcSYQ3SfwM9hEHRkikaGcRGLCNiFcu2mD6tHkug1qTthjMYt4WArbLpfhZT1FKBoKlsEFRjH9L9hqqIt0QE73tK_16_RGbb99O6y8Qfa24o5Zmx80Y40yA" width="355" height="276" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Click 'Next'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh6.googleusercontent.com/Yn5gqyaD3MYnnTtiVLiV3cGWvdgMDgOP6x6B-S4RhyqZbORplJ0P1kluYYZQSNNc0KMrjw9UQkqUCFGLNVBxu2IEY4tlYM_Hbe4-WvvBMVnzQ0-E8oFl-27OCFwQpRnPNLwc2SGR8V3bYKzArw" width="359" height="281" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Select ''Volumes' and click 'Next'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh6.googleusercontent.com/-JPKXJYLy_Id3_UZNVik7Y1hiJomao_TTV6b8OdR4ydm7Yvvy2o_iPE7EFIrCcHNmp1zc0o5AtMXiJmPll8GeLMiseGl8PnvkwVOt2hUTjZ_JAMMvjKUh9S5o1GBHGZrgHn7ZQu_EUkrmWlF_A" width="324" height="253" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Select the checkbox beside the volume and choose the 'Citrix vDisk' for the destination volume and click 'Next'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh4.googleusercontent.com/V0U6PnTUck50Zyrdvh5URJd8PI6al9EC5woxkdm7dIyOAoRCOZKVTtuoLJVz3py4-fqaGcbYxEa558ITDZ0wpVK3qrjmVeNuUS1yxPgxsunORJGkOvWi5agW1BYkUsH4ZEKeBJSRcRi-cJhk_w" width="326" height="255" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Click 'Yes'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh3.googleusercontent.com/rZw2cgUcy_ZdCYl41D8Y5QftdBYkNFf0B_g2T5ch1aE1lSUpgV_DaC4yseM0-X9_C7_q0VFEEk_6iI6gOPxQ5DnTS83yRN3BUAQtYmM27UbJweJlkTAQ0fp7-LoIwI5aDW4ohwUvyDO0W3ssqg" width="415" height="214" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Click 'Recover'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh4.googleusercontent.com/pioZtiW91xgpNSCF16hsOuWfwN8XbSGn_vSgSe8KT3MQIaUswuLSh5693KbVxpsC1-BGPOY6yCzr-POfpP0q2pKjgPCBFQSCOOleb6PnXmMtHa8gcFNXx1_9JNcQVmckrhM5KNflJw211lIa_g" width="380" height="297" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Wait for the Recovery to finish
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh6.googleusercontent.com/UvPV-grG1mVVXq0fm9ylHiYtAjB51HEwVkff0sJLLuCE3EB0hKGtLv5K4GDKArrKXZkZrVn_vu7X2CNS1Jt9Rt-TyBtk9UlTe7iCsx-2b0dTN37VYq3HlP0Vrq1J-rmu8vk8GV9IA0T-o345Fw" width="375" height="294" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Click 'Close'
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh6.googleusercontent.com/vVfpcNG081X70umH3vQNvhETIq1MeGUaYdEAmSEEq7VyFUu6O1g8OI-croiiHUQKPd1Mcj0fRbyWW9sZHL3e3akXk3rIoXxujxektjjD07ZzJ0SFsocVQbI9mCmegsNRXb45E3uzOyK8XRSY1g" width="367" height="287" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                </table>
                                                              </div>
                                                              
                                                              <p>
                                                                &nbsp;
                                                              </p>
                                                              
                                                              <ol start="3">
                                                                <ol start="3">
                                                                  <ol start="3">
                                                                    <li dir="ltr">
                                                                      <h3 dir="ltr">
                                                                        Fix BCD file for PVS vDisk
                                                                      </h3>
                                                                    </li>
                                                                  </ol>
                                                                </ol>
                                                              </ol>
                                                              
                                                              <div dir="ltr">
                                                                <table>
                                                                  <colgroup> <col width="54" /> <col width="577" /></colgroup> <tr>
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Step
                                                                      </div>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Detail
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        In the command prompt, switch to the 'Destination' drive and check the BCD file:
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh4.googleusercontent.com/EDwx7LHd-T-uZeT9rI1bbGLe20atltPxahj5aq-nN6FlYM7udxbyBclMHrC1jP_mI2-y-0MBIrrYwqkY-n7iz__18IwRp_N0itbGiGlDgRcHLGC3qwD9WSoYXNGqWkrthD2UwL_30XkLR7wScQ" width="357" height="335" />
                                                                      </div>
                                                                      
                                                                      <p>
                                                                        &nbsp;
                                                                      </p>
                                                                      
                                                                      <div dir="ltr">
                                                                        Notice there are 3 entries that need to be corrected.
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Execute the following commands, substituting the 'E:' for the proper drive letter:
                                                                      </div>
                                                                      
                                                                      <p>
                                                                        &nbsp;
                                                                      </p>
                                                                      
                                                                      <div dir="ltr">
                                                                        bcdedit /store bcd /set {bootmgr} device partition=E:
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        bcdedit /store bcd /set {default} device partition=E:
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        bcdedit /store bcd /set {default} osdevice partition=E:
                                                                      </div>
                                                                      
                                                                      <p>
                                                                        &nbsp;
                                                                      </p>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh6.googleusercontent.com/mkQ_xrPFQg544N23Rko_g43q3-12hgOlqpUN1G9FbnpY4YS9uii8nYO7Gsmr1Wyp0HfnKP-5JL05ZFcNA-9hh2qnCjIFzI8cVfd0RX1ep_Whap1mKMow2r30xi-yB7UOgmbvNXvZZ6lSihy3UQ" width="528" height="549" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                  
                                                                  <tr>
                                                                    <td>
                                                                    </td>
                                                                    
                                                                    <td>
                                                                      <div dir="ltr">
                                                                        Confirm the BCD file now contains the correct entries:
                                                                      </div>
                                                                      
                                                                      <div dir="ltr">
                                                                        <img src="https://lh5.googleusercontent.com/1ew5PofqOk_EWcj68t_rKssb8brh-oayvN7qxIWCg8aPQDp28lLD-kksD2jOJ2DUFpmGUrqZkxitAZIx9u4gMQ3bFkmSWOJdknahjH-qy4IEvzZ7LGlR0rBCu1yuyXH6lV7Xbb6n1LtPO1tYiw" width="505" height="380" />
                                                                      </div>
                                                                    </td>
                                                                  </tr>
                                                                </table>
                                                              </div>
                                                              
                                                              <p>
                                                                This process could be scripted to make it less manual, faster, and less error prone, but because of the frequency we actually do these type of updates, I have just created a manual document for now.
                                                              </p>
                                                              
                                                              <!-- AddThis Advanced Settings generic via filter on the_content -->
                                                              
                                                              <!-- AddThis Share Buttons generic via filter on the_content -->