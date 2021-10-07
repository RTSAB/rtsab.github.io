---
layout: default
title:  "Nätverkskommunikation i Kubernetes"
parent: "Kubernetes"
nav_order: 2
---
# Nätverkskommunikation i Kubernetes

## Services
För att en användare eller klient ska komma åt en applikation behöver den ansluta till applikationens Pod. Då Pods inte är statiska utan kommer och går, innebär det att de heller inte kan ha statiska ip-adresser. För att lösa detta använder man virtuella ip (VIP) som man tilldelar applikationerna via en så kallad Service. Med hjälp av Labels och Selectors kan man koppla en Deployment med en Service. I exemplet nedan syns att ```run: to-do-app``` både finns i labels Deployment och selector för Service.

```
kind: Deployment                            kind: Service
...                                         ...
  template:                                 spec:
    metadata:                                 type: ClusterIP
      labels:                                 selector:
        run: to-do-app                          run: to-do-app
    spec:                                     ports:
         containers:                            - port: 80
...
```

Vi ger sedan ett namn till vår Service. Detta namn registreras med en VIP i klustrets interna DNS-tjänst. Därmed kan man nu komma åt applikationen genom att anropa Service name.
Om inget annat anges så kommer Service VIP att vara ett ClusterIP som routas endast internt i klustret.

## Kube-proxy
På varje node i ett kluster finns det en tjänst (daemon) som kallas **kube-proxy**. Dess syfte är att hålla koll på API servern och lägga till, ta bort och ändra Services och endpoints. Till exempel så är det kube-proxy som konfigurerar **iptables** regler för att ta emot trafik för sitt ClusterIP och skicka vidare till en av Service endpoints.






Container-to-Container Communication Inside Pods

## Pod-to-Pod Communication Across Nodes
The Kubernetes network model aims to reduce complexity, and it treats Pods as VMs on a network, where each VM is equipped with a network interface - thus each Pod receiving a unique IP address. This model is called "IP-per-Pod" and ensures Pod-to-Pod communication, just as VMs are able to communicate with each other on the same network.

## Pod-to-External World Communication
Kubernetes enables external accessibility through Services, complex encapsulations of network routing rule definitions stored in iptables on cluster nodes and implemented by kube-proxy agents. By exposing services to the external world with the aid of kube-proxy, applications become accessible from outside the cluster over a virtual IP address.