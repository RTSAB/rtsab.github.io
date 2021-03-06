---
layout: default
title: GPU Virtualization
nav_order: 2
parent: Machine Learning och AI
permalink: /docs/gpu-virtualization
has_toc: true
---

## Latency vs. Throughput
Jämför man arkitekturen för en CPU med en GPU så ser man tydliga skillnader.
![CPU Architecture](/assets/images/cpu_arch.png)
*CPU Architecture*[^1]

En CPU är optimerad för att snabbt avsluta operationer och kunna hoppa mellan dem. För att klara det strävar man efter att ha så låg latency som möjligt för L1 och L2 Cache.
![GPU Architecture](/assets/images/gpu_arch.png)
*GPU Architecture*[^1]

För en GPU är fokus på Throughput vilket man åstadkommer genom att utföra många operationer parallellt. Istället för minnescache med låg latency så ligger kraften i förmågan att hantera stora mängder beräkningar.

När vi ser på fallet med [Deep Learning](/docs/deep-learning) är det uppenbart att ju fler beräkningar vi kan göra parallellt, desto snabbare kommer vi att uppnå ett resultat.

## GPU skala vertikalt, eller horisontellt
Att använda GPUer är kostsamt så det gäller att anpassa infrastrukturen efter behov. Om man utgår från en GPU kan man skala neråt genom att dela upp denna i fraktioner och dela dessa mellan olika jobb. Behöver man mer kraft kan man skala uppåt genom att skaffa kraftfullare GPU. Slutligen kan man skala ut och dela upp jobbet på flera processorer över flera hostar genom att använda [Horovod](https://horovod.ai), ett open source projekt som är en del av [Linux Foundations AI](https://lfaidata.foundation/) innovation.

<video muted autoplay controls width="100%" loop>
    <source src="/assets/videos/GPUscaleout.mp4" type="video/mp4">
</video>
*Konceptuell beskrivning av vertikal kontra horisontell skalning av GPU*

---

[^1]: Bild från VMware - "GPUs for Machine Learning on VMware vSphere" September 2019

{: .fs-6 .fw-300 }