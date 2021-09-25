---
layout: default
title:  "Driftsätta container i vSphere Pod"
parent: VMware Tanzu
nav_order: 5
author: "Anders Nilsson"
---
Under arbete
{: .label}


# Driftsätta container i vSphere Pod

Enligt VMwares egen dokumentation så rekommderar man driftsättning av containers i vSphere Pods om:

- det inte finns behov att specialanpassa Kubernetes cluster.
- man har behov av extra stark resurs och säkerhetsseparation.
- man enkelt vill köra pods direkt på ESXi hostar.

Vi har tidigare visat hur man [paketerar](/docs/containers/container-build/) en container och därefter laddar upp den i Tanzu Registry, [Harbor](/docs/vmware-tanzu/harbor/). Nu ska vi visa hur vi driftsätter vår applikation i en vSphere Pod.

## Installera vsphere-plugin för kubectl

Precis som vi gjorde för Docker-klienten så behöver vi installera en plugin så vi kan autentisera och logga in mot VMware. Från vår Control Plane Node IP kan vi ladda ner kubectl-vsphere.exe och kubectl.exe (om vi behöver det). Extrahera och placera filerna i sökvägen, förslagsvis samma som för [vSphere Docker Credential Helper](/docs/vmware-tanzu/harbor/#vsphere-docker-credential-helper). Nu kan vi logga in mot Kubernetes och välja context.

```
C:\Users\anders.nilsson>kubectl vsphere login --server=10.30.150.1

Username: anders@int.rtsvl.se
KUBECTL_VSPHERE_PASSWORD environment variable is not set. Please enter the password below
Password:
Logged in successfully.

You have access to the following contexts:
   10.30.150.1
   docker-to-do-demo
   tkg01
   tkg01-cl01

If the context you wish to use is not in this list, you may need to try
logging in again later, or contact your cluster administrator.

To change context, use `kubectl config use-context <workload name>`
```

Vi vill driftsätta i vårt namespace som vi tidigare [skapat](/docs/vmware-tanzu/namespace-setup/). Därför byter context:
```
C:\Users\anders.nilsson>kubectl config use-context docker-to-do-demo
Switched to context "docker-to-do-demo".
```
Nu är vi redo att driftsätta vår container.

## Skapa en deployment-fil

För att publicera en applikation i Kubernetes behöver vi skapa en beskrivning av hur applikationen ska driftsättas. Det gör vi i form av en YAML-fil. 