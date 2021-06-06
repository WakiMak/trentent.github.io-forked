---
id: 2897
title: Install a certificate in a Synology using a Microsoft CA
date: 2021-06-05T22:43:00-06:00
author: trententtye
excerpt: Install a certificate in a Synology using a Microsoft CA
layout: post
permalink: /2021/06/05/install-a-certificate-in-a-synology-from-a-microsoft-ca/
image: 
categories:
  - Blog
tags:
  - Synology
  - CA
  - Certificate Authority
  - Certificates
  - Microsoft
---

Installing a certificate on your Synology so you don't have the worrisome messages about an unsecure Synology landing page isn't that difficult. But I didn't find much content on getting this done with a Microsoft certificate authority so I thought I would write one!

The first step is generating your certificate. In the Synology interface, open the Control Panel, select or search for Security and click on "certificate" in the button bar.

![](/images/certificateLocation.png)  

Click the CSR button to bring up the certificate signing request dialog. 
![](/images/CSR.png)  

Fill out the request and click Next. A file (archive.zip) will download. Leave it, we'll get to it in a bit.
![](images/requestfilledout.png)  

Now we need to configure our CA to allow for creating subject alternative names (SAN) in the Microsoft Certificate Authority Web Certificate Enrollment page. 
[Terence Luk](http://terenceluk.blogspot.com/2017/09/adding-san-subject-alternative-name.html) has a great page that links to some MS articles going over the security concerns of enabling this feature.

The gist of configuring your CA is pretty straight-forward.  Run a single command and then restart the certificate authority service.
To do so, RDP into your CA, start an elevated command prompt, then run the following:

```bash
certutil -setreg policy\EditFlags +EDITF_ATTRIBUTESUBJECTALTNAME2
net stop certsvc
net start certsvc
```

With that configured, we browse to the web enrollment page.  This will be "https://%CAServer%/certsrv"

On the main splash page select 'Request a certificate' 
![](/images/CA_Req.png)  

Choose 'advanced certificate request'
![](/images/advancedCertRequest.png)  

Now, extract the certificate request (archive.zip) you downloaded from earlier. In that archive.zip is two files, a server.csr and a server.key. Open the server.csr in notepad or some text editor and select the whole text and copy it.

Go to the advanced certificate request page and paste the request into the 'Saved Request' dialog:
![](/images/pastedCert.png)

Select a template (like Web Server) from the 'Certificate Template' dropdown.

In order for the cert to validate correctly, some browsers or platforms (*cough*Apple*cough*) require a SAN.

We just add the SAN entry to the 'Additional Attributes' dialog now:
![](/images/SANEntry.png)

```bash
san:dns=ds1813.bottheory.local
```
If you have multiple servers or just want to add more dns names, you can do so by adding each name to the SAN, seperated by an ampersand and prefixed with "dns=".

Example:
```bash
san:dns=corpdc1.fabrikam.com&dns=ldap.fabrikam.com
```
Once complete, click submit.

Now, download the newly issued certs as BASE64.
![](/images/Base64.png)

Go back to your Synology and go to the Control Panel, Security, Certificate and click on 'Add'
Select 'Add a new certificate.
![](//images/addanewCert.png)

Select 'Import Certificate' and click 'Next'
![](/images/ImportCerts.png)

On the 'Create Certificate' pane, Synology is asking for 3 files. The Private Key, the certificate and any intermediate cerficates.
The Private Key is the server.key file downloaded from the archive earlier. 
The certificate is the certificate (.cer) you downloaded from the MS CA earlier.
Lastly, the intermediate certificate should contain a intermediate certificate authoritay. If your Env does not have one, just ignore this line.
Click 'OK'
![](//images/importCertFiless.png)

Once the certificate is imported, you can examine it. Ensure it has a SAN! The Synology web interface does show this.
![](/images/ds1813SAN.png)

Now click "Configure" at the top of this security pane.
![](/images/configure.png)

From here, we can configure what services use what certificate.  For all services, choose the new CA we just added 
![](/images/services.png)

And you're done!
