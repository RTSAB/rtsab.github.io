---
layout: default
title:  "Terminologi"
nav_order: 8
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

## Independent and Identically Distributed Data (IID)
IID är en term inom statistiken (och därmed också Machine Learning) som innebär att den data vi analyserar är oberoende (independent) och identiskt fördelad (distributed). Att den är oberoende betyder att det inte finns någon koppling mellan olika observationer eller datapunkter. Med identiskt distribuerad menar vi att alla datapunkter följer samma sannolikhetsfördelning.

---

## Ingress
Ingress är en del av nätverksresursena i Kubernetes och hanterar nätverkstrafik (HTTP och HTTPS) från utsidan av ett kluster in till de olika services som finns inom klustret. För att Ingress ska fungera krävs att man sätter upp en extern Ingress Controller. Det finns många olika lösningar att välja mellan där Avi Kubernetes Operator är VMwares lösning baserat på [NSX Advanced Load Balancer](https://avinetworks.com/).

---

## Kubelet
Kubelet är den primära "agenten" som körs på varje nod i ett Kubernetes-kluster. Det är den som kommunicerar med apiserver för att säkerställa att containers körs som de ska enligt PodSpec.

---

## Pod
En pod består av en eller flera containers som delar storage och nätverksresurser och en beskrivning hur dessa körs. Det är den minsta enhet som skapas och hanteras av Kubernetes.

---

## Spherelet
Spherelet är baserat på Kubernetes Kubelet, dvs det man installerar på varje Worker Node i Kubernetes. Genom denna Spherelet kan nu ESXi-hosten ta rollen som Worker Node.

---

## Supervisor Cluster
Supervisor Cluster är ett vSphere Cluster som har Workload Management aktiverat för att man ska kunna skapa Kubernetes kluster. Ett Supervisor Cluster kan i sin tur delas upp i namespaces som motsvarar Resource Pools i vSphere Cluster.