---
layout: default
title: Kubernetes
nav_order: 3
has_children: true
permalink: /docs/kubernetes
has_toc: false
---

![kubernetes-logo](/assets/images/kubernetes_logo.png){:height="30%" width="30%"}

# Kubernetes
{: .no_toc }

Kubernetes är programvara med öppen källkod för distribution och hantering av containrar i stor skala. Ibland ser man Kubernetes kallas k8s vilket kommer från att det finns 8 bokstäver mellan k och s i namnet. 

## Master node

Alla administrativa uppgifter sköts av **kube-apiserver** som är en central control plane komponent som kör på master node. API servern är ansvarig för att validera och processa alla kommandon och uppdatera etcd med status. Den är skalbar horisontellt men det är också möjligt att konfigurera den med sekundära API servrar och låta den primära servern skicka kommandon baserat på custom defined rules.

Schemaläggaren i ett multi-node Kubernetes cluster, **kube-scheduler**, är mycket viktig och får information från etcd via API servern för att koppla workload objects, t ex pods, till noder. Den tar hänsyn till Quality of Service krav och flera andra krav för att räkna ut hur och var objektet ska placeras.

Det finns två processers, kallade controller managers,  som kontinuerligt kontrollerar systemets status med det önskade, dvs det som är specificerat i konfigurationen för varje objekt. De är:

- **kube-controller-manager** ansvarar för noder, poddar, endpoints, service accounts och API access tokens
- **cloud-controller-manager** ansvarar för interaktionen med underliggande infrastruktur, hantera storage volumes och lastbalansering och routing.

All information om ett Kubernetes clusters tillstånd lagras i en key-value **data store** kallad **etcd**.

I produktionsmiljö är det av yttersta vikt att man replikerar data stores i HA-läge. För ytterligare säkerhet kan man konfigurera etcd att köra på en egen host, skild från övriga control plane komponenter.

Förutom att spara cluster status används etcd även för subnets, ConfigMaps och Secrets m.m.

## Worker node

Applikationer paketerade som containers körs på en eller flera **worker node**. Dessa containers är i sin tur inkapslade i en Pod. Det är dessa Pod som schemaläggs på worker nodes med de compute, memory och storage resurser som behövs, tillsammans med nätverk för att kunna kommunicera inom och med världen utanför.

I ett kluster med flera worker nodes hanteras nätverkstrafiken direkt av dessa och går inte via master node.

En worker node består av:

- Container Runtime
- Node Agent - kubelet
- Proxy - kube-proxy
- Diverse addons för DNS, monitorering och loggning m.m.

### Container runtime

Kubernetes har ingen egen container runtime utan supporterar istället flera olika lösningar som containerd, CRI-O, frakti och Docker.

### Kubelet

På varje node finns det en **kubelet** som är en agent vars uppgift det är att kommunicera med control plane men också att monitorera de Pods som körs på noden.

En kubelet kommunicerar med container runtime via ett [Container Runtime Interface (CRI)](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md). Tack vare detta interface är det möjligt att använda Kubernetes med flera olika container runtimes.

### Kube-proxy

**kube-proxy** finns på varje node och ansvarar för nätverk och kommunikation.

{: .fs-6 .fw-300 }