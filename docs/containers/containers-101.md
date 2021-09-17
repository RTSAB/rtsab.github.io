---
layout: default
title:  "Bakgrunden till containers"
parent: "Containers"
nav_order: 1
---

# Bakgrunden till containers

## Kort historik

Redan i slutet av 70-talet började man separera processer i Unix med hjälp av chroot-kommandot som gör det möjligt att "**ch**ange the **root**-directory" för enskilda processer. Men när internet-boomen satte fart vid millenieskiftet och man såg ett behov att kunna dela resurser mellan olika tjänster och kunder upptäckte man att chroot inte riktigt räckte till. Ett flertal olika lösningar togs fram men det var först när Google 2006 utvecklade "Control Groups (cgroups)" och förde in det i Linux kernel som vi fick vår första komponent i det som vi idag refererar till som containers. Därefter gick det fort och 2008 kom LXC (LinuX Containers) vilket var den första kompletta container-hanteraren som använde cgroups och Linux namespaces. Genom att Docker 2013 förenklade hela processen att skapa och hantera containers så exploderade användningen i popularitet[^2] och containers är nu närmast de-facto standard för att paketera och distribuera applikationer.

## Isolering med Linux Namespaces

Så hur går det då till att skapa en container, dvs isolera en process på en värd-maskin? Det är här Linux Namespaces kommer in i bilden. Syftet med Namespaces är att abstrahera globala systemresurser (mer om detta nedan) så att dessa kan delas mellan processer så att det ser ut som om de har sina helt egna instanser av resursen i fråga. Det är alltså ett sätt att begränsa vad du kan **se** i systemet.

Det finns för närvarande 7 olika systemresurser som kan delas upp i namespaces. De är:

- Mount (mnt) - Detta var det första namespace som implementerades (redan 2002) och gör det möjligt att isolera filsystem. Därmed kan man skapa egna miljöer likt chroot men mer säkert.
- Process ID (pid) - I princip innebär detta att processer i olika namespaces kan ha samma PID i sitt namespace. Detta är viktigt för att kunna länka alla processer i namespace till PID 1 för namespacet och inte värd-maskinen.
- Network (net) - För att kunna kommunicera med omvärlden gör net det möjligt att virtualisera nätverks-stacken och skapa egna IP-adresser, routing tables, brandväggar osv.
- Interprocess Communication (ipc) - IPC används för att hantera Shared Memory mellan processer. 
- UNIX Time-sharing System (uts) - Med hjälp av detta namespace kan man isolera domain- och hostname och därmed sätta ett unika namn på varje container vilket är en förutsättning för att kunna kommunicera i nätverk.
- User ID (user) - Ur säkerhetssynpunkt är detta en av de viktigaste namespaces. Den gör det möjligt att skilja på user och group IDs inom och utanför ett namespace.

## CGroups

Control Groups används för att begränsa vad du kan **använda** i systemet.
cgroup - Syftet med cgroups är att begränsa, prioritisera och kontrollera systemresurser likt minne, cpu osv.





## Läs vidare

[^1]: https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504
[^2]: https://blog.aquasec.com/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016
[^3]: https://github.com/lizrice/containers-from-scratch/blob/master/main.go

