---
layout: default
title:  "Terminologi"
---

# Terminologi
{: .no_toc }

<details open markdown="block">
  <summary>
    Innehåll
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Containers
En container består av en image, dvs paket av mjukvara, med allting som behövs för att köra en application. Det innebär kod, runtimes, systemfiler osv. Men även de värden för inställningar som krävs. En container körs av en speciell container runtime som t ex Docker eller containerd. Eller så skapar du en [egen från scratch ...](/docs/containers/container-from-scratch/) :) 

---

## Kubelet
Kubelet är den primära "agenten" som körs på varje nod i ett Kubernetes-kluster. Det är den som kommunicerar med apiserver för att säkerställa att containers körs som de ska enligt PodSpec.

---

## Pod
En pod består av en eller flera containers som delar storage och nätverksresurser och en beskrivning hur dessa körs. Det är den minsta enhet som skapas och hanteras av Kubernetes.

---

## Spherelet
Spherelet är baserat på Kubernetes Kubelet, dvs det man installerar på varje Worker Node i Kubernetes. Genom denna Spherelet kan nu ESXi-hosten ta rollen som Worker Node.