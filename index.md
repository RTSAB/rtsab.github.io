---
layout: default
title: Hem
author: Anders Nilsson
nav_order: 1
description: ""
permalink: /
---

# Modernisera din infrastruktur
{: .fs-9 }

Bygg en Modern Applikationsplattform med containerteknik.
{: .fs-6 .fw-300 }

---

De senaste åren har präglats av en enorm teknikutveckling inom applikationsutveckling drivet av önskan att snabbare kunna utveckla nya applikationer som är skalbara, lätta att flytta mellan olika plattformar och samtidigt enkla att underhålla och vidareutveckla. Modern Applications, Cloud Native Development, Micro Services är alla namn på denna trend som letts av pionjärer som Netflix[^1], Twitter[^2] och Heroku[^3]. Oavsett vilket namn man väljer, vilka metoder man följer och vilket språk man utecklar i så är en gemensam nämnare att man nuförtiden paketerar och levererar sina applikationer som containers.

Om du jobbar med infrastruktur har du säkert redan kommit i kontakt med containers. Antingen är det någon leverantör som distribuerar sin mjukvara paketerad som en container eller så kanske det är dina kollegor på utvecklingsavdelningen som har frågat om stöd för att kunna köra containers. Eller är det rentav så att du upptäckt att antalet virtuella linux-maskiner med mycket RAM och CPU bara växer och du har ingen aning vad som körs på dem? 

## Lär dig mer
### Containers direkt på vSphere
Söker du ett enkelt sätt att köra containers på din existerande vSphere-plattform så får du en enkel överblick här:

[Att köra containers på VMware-plattformen]({% link docs/vmware-tanzu/containers-on-vsphere.md %})

### Back to basics
Om du vill ha en mer djup förståelse av containers så kan du gå vidare här:

[Introduktion till containers]({% link docs/containers/containers-101.md %})

### Kubernetes Maturity Model
Även organisationer måste lära sig att använda Kubernetes. För att underlätta förståelse och vilken resa det kan vara har vi tagit fram en [Kubernetes Maturity Model]({% link docs/kubernetes/kubernetes-maturity-model.md %}) som beskriver de olika faser en organisation kan förväntas ta sig igenom.

## Referenser
[^1]: Netflix har under flera år bloggat om sin utvecklingsresa. Här är ett bra [blogginlägg](https://netflixtechblog.com/netflix-conductor-a-microservices-orchestrator-2e8d4771bf40)
[^2]: Även Twitter delar med sig. Följ denna länk för att få en bild av [infrastrukturen bakom Twitter](https://blog.twitter.com/engineering/en_us/topics/infrastructure/2017/the-infrastructure-behind-twitter-scale.html)
[^3]: Heroku utvecklade tidigt en [plattform](https://www.heroku.com/dynos) för utvecklare att publicera sina applikationer utan att behöva bry sig om infrastrukturen.  