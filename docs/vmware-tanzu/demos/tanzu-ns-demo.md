---
layout: default
title:  Nytt namespace i vSphere Client
parent: VMware Tanzu Demos
grand_parent: VMware Tanzu
nav_order: 1
---

## Skapa ett nytt namespace i vSphere Tanzu
När du har aktiverat Tanzu i vSphere 7 är det snabbt och enkelt att skapa ett nytt namespace som kan delas ut till devops.

1. Gå till Workload Management-fliken.
2. Klicka på New Namespace.
3. Välj vSphere-kluster och sätt ett namn på ditt nya namespace.
4. Det nya namespace är tillgängligt direkt och det går att ansluta med hjälp av kubectl + vSphere plugin för att skapa ett nytt kluster eller deploya en pod ( men först måste du ge [behörigheter](#lägg-till-en-ägare-av-namespace)).


<video muted autoplay controls width="100%">
    <source src="/assets/videos/createnamespace.mp4" type="video/mp4">
</video>

## Lägg till en ägare av namespace
Innan man kan ansluta till namespace och skapa nya resurser måste man ange en ägare:

1. På namespace-fliken klicka på Add Permissions.
2. Välj identity source, user/group och role.
3. Klicka på ok.


<video muted autoplay controls width="100%">
    <source src="/assets/videos/addowner.mp4" type="video/mp4">
</video>