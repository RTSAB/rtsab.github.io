---
layout: default
title:  "Nätverkskommunikation"
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

## Nätverkstopologi
Kubernetes nätverkstopologi består av i huvudsakligen 3 nät. I mitten finns Node Network vilket är det nätverk som alla Nodes är anslutna till. Till detta nätverk kan man även ansluta Pods men det vanliga är att man tilldelar ett dedikerat Pod Network som varje Pod får en egen ip-adress från, ett så kallat Overlay Network. Det sista nätverket är Cluster Network och det är från detta nätverk som olika services som t ex HTTP får en extern ip-adress som gör det möjligt att kommunicera med applikationerna.

![Kubernetes nätverkstopologi](/assets/images/kube_net_topo.png)

## Kommunikation
Nu när vi har en överblick över nätverkstopologin kan vi gå lite djupare och se hur pods kommunicerar i klustret.

### Inom en Pod
Alla containers i en och samma Pod delar på IP-adress och kommunikation dem emellan sker via localhost och loopback interface.

### Pod till Pod på samma Node
För kommunikation mellan Pods på samma Node så sker detta via Pod nätverksinterface som är anslutet till ett Tunnel interface som är konfigurerat på Noden.

### Pod-trafik mellan olika noder
I detta fall kommunicerar Pods på med sina ip-adresser antingen direkt via Layer 2/3 eller ett overlay network. När man konfigurerar en Pod i Kubernetes kommer en extra container automatiskt att skapas. Denna speciella container har ett syfte och det är att tillhandahålla ett nätverksinterface till övriga containers i Poden. Då alla containers i en Pod därmed delar interface kommer de att ha samma IP-adress. Därmed kan inte två containers ha samma port öppen precis som att man inte kan köra två processer på samma port på en singel host.

Om man inte vill tilldela ip-adresser till Pods på samma nät som noderna så kan man i Kubernetes skapa ett **overlay network**. Vad detta gör är att skapa en custom bridge, cbr0, på varje Node som tilldelas ett subnet från en större ip-range skapat för klustret. Routing regler konfigureras för routern så att varje Node vet hur trafiken ska skickas vidare mellan noderna. 

![Overlay Network](/assets/images/kube_net_overlay.png)

För att implementera denna nätverksmodell i verkligheten finns det en mängd olika lösningar för att underlätta konfiguration. I princip vill man kunna skapa ett VXLAN för OVN och använda agenter som kommunicerar med Kubernetes för att konfigurera subnäten och tilldela ip-adresser till Pods. Om man bygger sitt Kubernetes kluster på VMware kan man t ex använda vSphere Distributed Switch för VXLAN.