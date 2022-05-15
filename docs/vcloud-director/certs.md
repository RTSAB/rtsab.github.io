---
layout: default
title:  "Change Certificates"
parent: VMware vCloud Director
author: "Anders Nilsson"
nav_order: 1
---
## Introduction
{: .no_toc }

Replacing the certificates in vCloud Director is required when the old certificates expire or you want to change the URL for your service. If you only need to replace a certificate that is about to expire you can go straight ahead. 
However, if you need to change URL it is important that you update the Public Addresses in the provider before you replace the certificate. The reason is your browser might prevent you from accessing the site on the old URL and the service will fail to start.
So if you want to change the URL as well as certificate, go ahead and update the Public Addresses first before continuing and change the certificates.

## Extracting from PFX
If your certificate comes in the form of a .pfx you need to extract the certificate and private key. A .pfx file is a PKCS12 #12 archive that usually holds both the certificate and the private key while protecting them using a password.

We start by extracting the key by using openssl.
```
$ openssl pkcs12 -in new_cert.pfx -nocerts -out new_cert.key
```
You will be prompted to enter a PEM Pass Phrase that you will need when you import the certificate.

Next we continue to extract the certificate and any intermediates we need.

```
$ openssl pkcs12 -in new_cert.pfx -clcerts -nokeys -out new_cert.pem
```
Now we have the files we need, including the pass phrase the private key, and can continue to replace the certificate.

## Importing the Certificate
To replace the certificate you need to login in to each vCloud Director cell on the command line and use the cell-management-tool.
Start by copying the .pem and .key files to the cell for example by usinc scp. Save copies of the existing .pem and .key files in the /opt/vmware/vcloud-director/etc folder. Then you replace the certificate and key files with your newly created files.

Now we are ready to replace the certificates. Go to the /opt/vmware/vcloud-director/bin folder where you find the cell-management-tool. For the portal, execute the following command:
```
$ ./cell-management-tool certificates -j --cert /opt/vmware/vcloud-director/etc/user.http.pem --key /opt/vmware/vcloud-director/etc/user.http.key --key-password <PEM Pass Phrase>
```
Now go ahead and do the same for the console proxy. Notice the -j option in the command:
```
$ ./cell-management-tool certificates -p --cert /opt/vmware/vcloud-director/etc/user.consoleproxy.pem --key /opt/vmware/vcloud-director/etc/user.consoleproxy.key --key-password <PEM Pass Phrase>
```
Now we need to restart the service in order to activate the new certificate.

## Restart the Service
From the command line execute:
```
root@vcd01 [ /opt/vmware/vcloud-director/bin ]# service vmware-vcd restart
```
