---
layout: default
title:  "Nätverkskommunikation i Kubernetes"
parent: "Kubernetes"
nav_order: 2
---
# Nätverkskommunikation i Kubernetes

## Inledning
I Kubernetes strävar men efter enkelhet för applikationer att kommunicera internt och externt. Det ska vara enkelt att med kod (YAML) konfigurera applikationer och använda Service Discovery. En administratör ska kunna dölja underliggande komplexitet med subnets, availability zones osv. För att åstadkomma detta finns det några grundläggande regler man måste ta hänsyn till när vi sätter upp nätverkstopologi för vårt kluster.

Den första regeln är att:

- Alla Pods kan kommunicera med varandra på alla noder. Det innebär att oavsett var i ett kluster en pod befinner sig ska den kunna kommunicera med andra pods via nätverket.

Den andra regeln säger att:

- Agenter på en Node kan kommunicera med alla Pods på samma Node. Dvs Kubelet och Kube-proxy kan orkestrera de Pods som kör på samma Node som dem.

Tredje regeln är:

- Ingen Network Address Translation (NAT) är tillåten. Alla Pods ska kommunicera med varandra genom varje Pods egen IP-adress.

## Kubernetes nätverkstopologi
Kubernetes nätverkstopologi består av i huvudsaklig 3 nät. I mitten finns Node Network vilket är det nätverk som alla Nodes är anslutna till. Till detta nätverk kan man även ansluta Pods men det vanliga är att man tilldelar ett dedikerat Pod Network som varje Pod får en egen ip-adress från. Det sista nätverket är Cluster Network och det är från detta nätverk som olika services som t ex HTTP får en extern ip-adress som gör det möjligt att kommunicera med applikationerna.

![Kubernetes nätverkstopologi](/assets/images/kube_net_topo.png)


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

