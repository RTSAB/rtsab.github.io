---
layout: default
title:  "Lägga till containers i Tanzu"
parent: VMware Tanzu
nav_order: 4
---

# Lägga till containers i Tanzu

För att kunna använda vårt Kubernetes-kluster behöver vi givetvis applikationer paketerade som containers. När man startar en container som inte finns lokalt sparad så sker det alltid en kontroll mot ett registry. Om du använder Docker eller Kubernetes så är det Docker Hub. Men av säkerhetsskäl kanske man inte vill använda ett publikt registry för sina egna containers. Därför kommer Tanzu med registry som standard. Detta registry kallas [Harbor](https://goharbor.io/) och det är i grunden ett Open Source projekt just för att hantera container images.

## Harbor

Du aktiverar Harbor för ett Supervisor Cluster direkt i vSphere Client:

![harbor](/assets/images/harbor.md.png)

När du sedan skapar ett [nytt namespace](/docs/vmware-tanzu/namespace-setup/) kommer ett projekt att läggas upp i Harbor automatiskt:

![harbor project](/assets/images/harbor-project.png)

## vSphere Docker Credential Helper

För att kunna ladda upp en container image med Docker så [rekommenderar VMware](https://kb.vmware.com/s/article/78026) att man använder vSphere Docker Credential Helper. Den kan du ladda ner direkt från den Control Plane Node IP Address som är uppsatt för varje kluster. Lägg .exe filen i förslagsvis samma folder som övriga Docker binärer:

![docker-vsphere](/assets/images/docker-vsphere.png)

För att kunna logga in måste vi även ladda ner ett certifikat från Harbor. Antingen loggar man i  Harbor via webgränssnittet eller så laddar man ner det direkt via API:

```powershell
> wget https://10.30.150.4/api/systeminfo/getcert -O ca.crt
```

Lägg ca.crt filen i en mapp som du döpt till hostnamnet för registry och som ligger under certs.d. Till exempel:

```
C:\ProgramData\Docker\certs.d\10.30.150.4\ca.crt
```

Nu kan vi logga in i vårt registry:

![harbor-login](/assets/images/harbor-login.png)

## Ladda upp en container image till Harbor

För att kunna ladda upp en container image så behöver vi ändra TAG på vår image. I Harbor får vi direkt hjälp med vad vi ska tagga med:

![tag-image](/assets/images/tag-image.png)

Applicerat på den container vi skapat i [Bygg en container från scratch](/docs/containers/container-build/) kan vi nu skapa en ny TAG som pekar på vårt registry. Med `docker image ls` ser vi att vi nu har två TAG som pekar mot samma Image. Nu kan vi köra `docker push` och ladda upp vår container.

```
C:\Users\anders.nilsson>docker tag to-do 10.30.150.4/docker-to-do-demo/to-do

C:\Users\anders.nilsson>docker image ls
REPOSITORY                            TAG         IMAGE ID       CREATED        SIZE
10.30.150.4/docker-to-do-demo/to-do   latest      f5a335e9e60b   7 days ago     449MB
to-do                                 latest      f5a335e9e60b   7 days ago     449MB


C:\Users\anders.nilsson>docker push 10.30.150.4/docker-to-do-demo/to-do
Using default tag: latest
The push refers to repository [10.30.150.4/docker-to-do-demo/to-do]
23cbacbde02d: Pushed
9f7138617997: Pushed
593c7af5324a: Pushed
725c6a691297: Pushed
446ec7c50f08: Pushed
b8f0e895f520: Pushed
f8700d3a252f: Pushed
39982b2a789a: Pushed
latest: digest: sha256:65d29213f7763c4f285b715fd834ab1a13be6c5b6615cfc97be21eb9a85633f6 size: 2001
```

Loggar vi in i Harbor kan vi se vår container image i vårt projekt som väntat:

![harbor-upload](/assets/images/harbor-upload.png)


