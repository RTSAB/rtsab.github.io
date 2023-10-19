---
layout: page
title:  "Basic Kubernetes Architecture"
nav_exclude: true
author: "Elias Dardaghani"
date: 2023-10-19
---

# Grundläggande Kubernetes Arkitektur

## Beskrivning
 En grundläggande Kubernetes-kluster består vanligtvis av flera centrala komponenter som arbetar tillsammans för att hantera containeriserade applikationer. Här är en förenklad översikt över hur en grundläggande Kubernetes-konfiguration kan se ut:

1. Master-nod:
   - Kube-apiserver: Denna komponent exponerar Kubernetes API, som används av administratörer och utvecklare för att interagera med klustret.
   - Etcd: En distribuerad nyckel-värde-lagringsplats som lagrar all klusterdata, inklusive konfigurationsdetaljer och klustrets nuvarande tillstånd.
   - Kube-controller-manager: Hanterar olika kontrollers som reglerar tillståndet för olika resurser i klustret, som poddar, tjänster och noder.
   - Kube-scheduler: Avgör på vilka arbetsnoder poddar bör schemaläggas baserat på resurskrav, policys och andra faktorer.

2. Arbetsnoder:
   - Kubelet: Säkerställer att containrar körs i en pod, rapporterar nodens status till kontrollplanet och hanterar containerns livscykeloperationer.
   - Kube-proxy: Underhåller nätverksregler på varje nod, möjliggör kommunikation till och från pod-IP-adresser.
   - Container Runtime: En containerkörning, som Docker eller containerd, är ansvarig för att köra containrar inom poddar.

3. Poddar: De minsta distribuerbara enheterna i Kubernetes. En pod kan innehålla en eller flera containrar som delar samma nätverksnamnrymd och kan kommunicera med varandra med hjälp av `localhost`. Poddar schemaläggs för att köras på arbetsnoder.

4. Tjänster: Definierar en logisk uppsättning poddar och en policy för att komma åt dem. Tjänster tillhandahåller en stabil IP-adress och DNS-namn för åtkomst till poddar, vilket möjliggör belastningsutjämning och tjänstupptäckt.

5. Namnrymd: Ger en metod för att isolera resurser inom en kluster. Namnrymd används ofta för att partitionera program eller team i en fleranvändaromgivning.

6. Distribution: En högnivåresurs som hanterar önskat tillstånd för poddar. Distributioner kan användas för enkelt att skala och uppdatera applikationer.

7. Konfigurationskartor och Hemligheter: Används för att hantera konfigurationsdata och känslig information (t.ex. lösenord, API-nycklar) separat från applikationskoden.

8. Volym: Tillhandahåller varaktig lagring för containrar. Kubernetes stöder olika typer av volymer, inklusive lokala, nätverksanslutna och molnbaserade lagringslösningar.

9. Ingress: Hanterar extern åtkomst till tjänster inom klustret, vanligtvis används för att dirigera HTTP- och HTTPS-trafik.

10. Namnrymd: Namnrymd används för att logiskt dela upp klustret i flera virtuella kluster. Det hjälper till med resursisolering och organisering av applikationer eller miljöer.

11. RBAC (Rollbaserad åtkomstkontroll): Kubernetes tillhandahåller ett flexibelt RBAC-system för att kontrollera vem som kan få åtkomst och utföra åtgärder inom klustret.

12. Övervakning och loggning: Det är viktigt att ha övervaknings- och loggningslösningar på plats för att få insikter om klustrets hälsa och applikationsprestanda. Verktyg som Prometheus, Grafana och Elasticsearch används oftast.

13. Nätverk: Kubernetes förlitar sig på en nätverkslösning (t.ex. Flannel, Calico eller Cilium) för att möjliggöra kommunikation mellan poddar över noderna.

14. Lastbalansering: I en molnomgivning kan du behöva en extern lastbalanserare för att fördela inkommande trafik till tjänster inom klustret.
