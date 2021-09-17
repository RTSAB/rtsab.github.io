---
layout: default
title: Containers
nav_order: 2
has_children: true
permalink: /docs/containers
has_toc: true
---

# Containers

## Introduktion

Om du läser detta antar vi att du har kunskap om servrar och virtuella maskiner. Du vet att virtuella maskiner delar underliggande hårdvara genom att den virtualiserats av en så kallad hypervisor. Med hjälp av hypervisorn kan vi skapa en virtuell representation av t ex en CPU och den virtuella maskinen vet inte ens om att den delar den fysiska CPUn med andra virtuella maskiner. I och med detta kan man köra olika operativsystem samtidigt på samma hårdvara. 

> Ett operativsystem består av en Kernel som ofta kallas supervisor. Denna sköter om användare och applikationers åtkomst till disk, minne och cpu efter behov. En hypervisor kallas så för att den "står över" supervisors och kontrollerar dessa. Namnet hypervisor togs i bruk redan på 1970-talet av IBM i sina mainframes och adopterades av VMware vid millenie-skiftet.   

Containers å andra sidan, är inte små virtuella maskiner även om man kan säga att det är en virtualiseringsteknik. En container är en process i din maskin (som kanske är virtuell) som är isolerad från alla andra processer på värdmaskinen. För att kunna göra detta använder man kernel namespaces och cgroups. 

{: .fs-6 .fw-300 }
