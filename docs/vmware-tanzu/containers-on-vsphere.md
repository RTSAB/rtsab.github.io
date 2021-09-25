---
layout: default
title:  "Containers i vSphere"
parent: VMware Tanzu
nav_order: 1
---

# Containers i vSphere med Tanzu
När vi pratar om att köra containers så menar vi egentligen att kunna orkestrera containers genom att köra Kubernetes. VMware erbjuder lösningar för detta genom Tanzu och man har framförallt två vägar att gå.
- Den första är att köra Kubernetes direkt i vSphere 7. Då känner du som administratör direkt igen dig då du kan göra det mesta i vCenter som vanligt.
- Den andra är att köra Tanzu Kubernetes Grid som standalone. Dvs man kan köra på vSphere eller i olika moln men ändå managera allting centralt på ett ställe.

## vSphere 7 med Tanzu
Med introduktionen av vSphere 7 är det nu möjligt att köra Kubernetes direkt på ESXi. Du behöver alltså ingen Linux-host som ”Worker Node” utan den rollen tar ESXi-hosten. Fördelarna med detta är att du tar bort ett lager att managera och att prestandan blir bättre. Enligt VMware så är prestandan upp till 8% bättre än t o m Bare Metal (se länk).

## Arkitektur
Rent praktiskt går det till så att man installerar en Spherelet på varje ESXi-host. Spherelet är baserat på Kubernetes Kubelet, dvs det man installerar på varje Worker Node i Kubernetes. Genom denna Spherelet kan nu ESXi-hosten ta rollen som Worker Node.

![arkitektur](/assets/images/an_1-400x247.png)
 
För att administrera klustret implementar man ett så kallat Supervisor Cluster Control Plane. Det är genom dess API som varje Spherelet får information om konfigurationsförändringar och sedan utföra dessa för att se till att klustret är uppdaterat.
Förutom att förmedla konfigurationsinformation till Spherelets, utför Supervisor Cluster Control flera funktioner. Dels hantera ”upstream” Kubernetes funktioner men även flera ”native”-funktioner. 

-	NSX Container Plugin (NCP) sköter kommunikationen med NSX
-	Cloud Native Storage driver är ett API för att hantera lagring
-	Scheduler Extension ser till att K8 Scheduler och vSphere DRS kommunicerar
-	Authenticating Proxy säkerställer Single-Sign On mellan kubectl och vSphere
 
## Supervisor Clusters
I en VMware-plattform med vSphere 7 och Tanzu kallas ett vSphere Cluster som har Workload Management aktiverat för ett Supervisor Cluster. När vi nu har skapat ett Supervisor Cluster kan vi dela upp detta i Namespaces. Detta motsvarar en Resource Pool i vSphere och som administrator kan du därmed sätta begränsningar för hur mycket resurser du vill tilldela olika användare.
 
Med varje namespace kommer en URL till Kubernetes Control Plane. En DevOps engineer som fått access till ett namespace kan därmed skapa upp egna Tanzu Kubernetes clusters genom att använda kubectl och YAML som vanligt. Tanzu Kubernetes clusters är paketerade, signerade och supporterade av VMware men administreras i övrigt som vanliga Kubernetes cluster med samma verktyg.
Nu när vi har Tanzu Kubernetes aktiverat i vår vSphere 7 plattform så har vi alltså tack vare namespaces möjlighet att ge ut rättigheter för användare att själva skapa de resurser de behöver. Där har vi två olika alternativ att välja mellan. Det ena är vSphere Pod Service och det andra är Tanzu Kubernetes Grid. För vSphere Pod Service behöver användaren inte bry sig om att hantera virtuella maskiner eller Kubernetes cluster utan kan fokusera på att deploya sina tjänster. I det andra fallet kan användaren själv skapa ett eget Kubernetes Cluster och själv managera det. IT Operations ser i detta fall bara till vilka versioner och mallar som finns tillgängliga samt sätta eventuella begränsningar på hur mycket resurser som finns tillgängliga för användaren.

## vSphere Pod Services
vSphere Pod Services är en container runtime for ESXi och gör det möjligt att köra containers direkt på ESXi. Den består av en VM Runtime (VMX) och en digitalt signera Linux kernel som distribueras med ESXi. Det finns ingen BIOS och startup tiden mäts i millisekunder. Enligt VMware så är det bättre prestanda i denna lösningen än att köra diekt på Bare Metal.
Förutom bättre prestanda är säkerheten bättre då man inte behöver dela Linux kernel mellan olika containers. Istället sker det isolation mellan containers på ESXi/NSX nivå.
 
## Tanzu Kubernetes Cluster
Via Tanzu Kubernetes Grid Service kan alltså användare själva skapa och managera Kubernetes Clusters i vSphere. Man använder standard Kubernetes API och Tanzu Kubernetes klustret är fullt kompatibelt med standard Kubernetes. Användaren har full kontroll över livscykeln medan IT definierar mallar och vilka Kubernetes versioner som är tillgängliga. IT sätter även vilka resource quotas som gäller.