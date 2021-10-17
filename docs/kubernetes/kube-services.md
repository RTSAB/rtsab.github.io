---
layout: default
title:  "Services"
parent: "Kubernetes"
nav_order: 3
---

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