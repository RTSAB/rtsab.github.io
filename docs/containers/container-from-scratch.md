---
layout: default
title:  "Bygg en container-runtime från scratch"
parent: "Containers"
nav_order: 4
---
# Container runtime från scratch

En container runtime är en applikation (och flera komponenter) som tar en container image och bygger ihop och kör själva containern, dvs applikationen. Det finns några olika container runtimes. Förutom Docker Engine finns t ex [containerd](https://containerd.io/) och cri-o. En container runtime gör en massa saker för att göra hanteringen av containern enkel, men i grund och botten så måste ju containern exekveras och det gör vanligtvis av [runC](https://github.com/opencontainers/runc).

## Kicka igång processen

För att få en bättre förståelse för vad en container runtime, eller runc, gör så ska vi här gå igenom hur man i Linux skapar en container.

Eftersom en container-process är helt isolerad på värd-maskinen behöver den ha åtkomst till egna versioner av binärer, skript, konfigurationer och andra beroenden som krävs för att köra applikationen. Alla dessa komponenter, plus annan information som miljövariabler, kommandon osv, samlas i en container image.

Vi kan ta ett exempel här där vi har en kopia på ett Linux filsystem ligger i /home/container/fs-image mapp på vår host.
![Linux filsystem](/assets/images/fs-image.jpg)

Låt oss testa vad som händer om vi försöker starta ett bash-shell med hjälp av chroot. 
![Chroot](/assets/images/chroot.jpg)

Det vi gör är att genom chroot-kommandot specificera får nya root (/) till fs-image-mappen och att vi ska exekvera /bin/bash i den mappen. Och det fungerar ju utmärkt, vi ser att vi har en bash-prompt, vi är i root-mappen och våra filer är där.

Nu kör vi alltså ett nytt shell med egna binärer. Vad händer om vi kollar vilka processer som körs?
![proc](/assets/images/proc.jpg)

Kommandot ps ska ge oss svaret på frågan men istället får vi ett felmeddelande. Som tur är får vi hjälp på traven med vilket kommando vi ska köra för att lösa problemet. Allting i Linux är filer, även processer som finns i /proc mappen. Men den är ju tom i vårt filsystem. Så med hjälp av mount-kommandot så kan vi nu läsa systemets /proc och därmed ser vilka processer som kör. Och nu visar ps alla processer på systemet.

Om vi tar ett steg tillbaks, hoppar ur vårt shell med exit-kommandot, och kollar vilka mounts vi har så får vi följande om vi filtrerar på proc:
![mount](/assets/images/mount.jpg)

Vi ser att vi har två stycken mounts på proc. Om vi tänker att vi ska köra många containers på samma host och varje image ska få en mount för proc och kanske även andra så inser man att detta lätt kan bli svårt att få grepp om. Detta problem var det första man gav sig i kast med. Lösningen blev namespaces. Att visa hur detta går till kräver en liten programmeringsinsats så att vi kan modifiera hur vi exekverar en process med hjälp av olika system call (syscall). Vi använder en funktion i Linux som heter clone() för att skapa en "child"-process. När vi gör det kan vi specificera olika flaggor för att i detalj styra vad child-processen kan se och dela med den anropande processen. Flaggan för att skapa ett namespace heter CLONE_NEWNS.

I följande exempel kör vi ett program i Go (main.go) som likt Docker tar ett kommando (run) och därefter ett argument (/bin/bash) att exekvera. (Vi har utgått från Liz Rice's lysande exempel som finns på github om du vill testa själv[^1])

![newns](/assets/images/newns.jpg)

I "container"-bilden till vänster så börjar vi med att anropa vårt program main.go som startar en child-process med clone() flag = CLONE_NEWNS. Programmet kör även chroot-kommandot så vi inte behöver göra det. Vi får vår nya bash-prompt som väntat. När vi kör ps-kommandot för att se vilka processer som kör så får vi exakt samma problem som tidigare och lösningen är densamma.

Kollar vi nu på den högra bilden, som vi kallar Host, ser vi den anropande processen (pid 10039 - det är det kompilerade programmet som vi anropar med go, pid 9937 ). Men vi ser också vår klonade child-process (pid 10044) som kör /bin/bash. Så allt verkar fungera som det ska.

Det intressanta inträffar när vi nu kör `mount | grep proc` igen. Då ser vi bara en proc! Den mount vi gjort i vår "container" är nu privat för den och syns inte utanför det namespace vi skapat. Great, vi har alltså lyckats skapa en container anno 2002!

Låt oss gå vidare i listan. Nästa sak att städa upp är att vi vill ju inte se alla processer på vår host när vi är i vår container. För det kan vi använda en flagga som heter CLONE_NEWPID. Vi uppdaterar vårt program, lägger till mount proc-kommandot så vi inte behöver köra det manuellt, och får då:

![newpid](/assets/images/newpid.jpg)
Perfekt! I vår container ser vi nu bara de processer som vi skapat i vårt nya namespace. Tittar vi däremot i vår host så ser vi alla processer som körs. Notera även att vår child-process fått PID 1, dvs den första processen i namespacet.

Näst på listan över systemresurser att dela upp i namespaces är Network. Men innan vi kan göra det så har vi ett annat litet problem. Om vi uppdaterar prompten att visa hostname så ser vi:
![prompt](/assets/images/prompt.jpg)

Vårt hostname i containern är samma som i "host"! Det gör det lite knepigt med nätverkskommunikation osv, alltså måste vi fixa detta. Vi lägger därför till flaggan CLONE_NEWUTS (Unix Time Sharing) och startar om.
![newuts](/assets/images/newuts.jpg)

Nu kan vi byta hostname på vår container utan att vår host ändras!

Vi har 3 systemresurser kvar att lägga in i egna namespaces men nu förstår ni principen och vi hoppar över dem här. Det som sedan återstår för att få en komplett container anno 2006 är cgroups så att vi kan styra hur mycket resurser en enskild container får utnyttja av vår host. I princip är detta "magin" bakom en container-hanterare som LXC eller Docker. Förutom att man lagt till en mängd olika funktioner som underlättar hanteringen och utvecklingen av containers. Även Microsoft har utvecklat stöd för att köra Windows-baserade containers och gränserna mellan Linux och Windows suddas ut mer och mer. Men mer om detta någon annan gång. 


## Läs vidare

[^1]: https://github.com/lizrice/containers-from-scratch/blob/master/main.go
