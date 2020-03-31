---
title: Aanbevolen procedures voor operatoren - Clusterisolatie in Azure Kubernetes Services (AKS)
description: Lees de aanbevolen procedures voor de clusteroperator voor isolatie in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 228b856d5c5ffa2bfac7df12094667e02f797690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594852"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor clusterisolatie in Azure Kubernetes Service (AKS)

Terwijl u clusters beheert in Azure Kubernetes Service (AKS), moet u vaak teams en workloads isoleren. AKS biedt flexibiliteit in hoe u clusters met meerdere tenant's uitvoeren en resources isoleren. Om uw investering in Kubernetes te maximaliseren, moeten deze functies voor multi-tenancy en isolatie worden begrepen en geïmplementeerd.

Dit best practices-artikel richt zich op isolatie voor clusteroperators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Plannen voor clusters met meerdere tenants en scheiding van resources
> * Logische of fysieke isolatie gebruiken in uw AKS-clusters

## <a name="design-clusters-for-multi-tenancy"></a>Ontwerpclusters voor multi-tenancy

Kubernetes biedt functies waarmee u teams en workloads in hetzelfde cluster logisch isoleren. Het doel is om het minste aantal bevoegdheden op te geven, die aangepast zijn aan de resources die nodig zijn voor elk team. Een [naamruimte][k8s-namespaces] in Kubernetes creëert een logische isolatiegrens. Aanvullende Kubernetes-functies en overwegingen voor isolatie en multi-tenancy omvatten de volgende gebieden:

* **Planning** omvat het gebruik van basisfuncties zoals resourcequota en podonderbrekingsbudgetten. Zie Aanbevolen procedures voor [basisplannerfuncties in AKS voor][aks-best-practices-scheduler]meer informatie over deze functies.
  * Meer geavanceerde planner functies omvatten taints en toleranties, knooppunt selectors, en knooppunt en pod affiniteit of anti-affiniteit. Zie Aanbevolen procedures voor [geavanceerde plannerfuncties in AKS voor][aks-best-practices-advanced-scheduler]meer informatie over deze functies.
* **Netwerken** omvat het gebruik van netwerkbeleid om de verkeersstroom in en uit pods te controleren.
* **Verificatie en autorisatie** omvatten de gebruiker van rbac-integratie (Role-based access control) en Azure Active Directory (AD), pod-identiteiten en geheimen in Azure Key Vault. Zie Aanbevolen procedures voor [verificatie en autorisatie in AKS voor][aks-best-practices-identity]meer informatie over deze functies.
* **Containers** omvatten pod beveiligingsbeleid, pod security contexten, het scannen van afbeeldingen en runtimes voor kwetsbaarheden. Ook gaat het gebruik van App Armor of Seccomp (Secure Computing) om container toegang tot het onderliggende knooppunt te beperken.

## <a name="logically-isolate-clusters"></a>Clusters logisch isoleren

**Richtlijnen voor beste praktijken** - Gebruik logische isolatie om teams en projecten te scheiden. Probeer het aantal fysieke AKS-clusters dat u implementeert om teams of toepassingen te isoleren tot een minimum te beperken.

Met logische isolatie kan één AKS-cluster worden gebruikt voor meerdere workloads, teams of omgevingen. Kubernetes [Namespaces][k8s-namespaces] vormen de logische isolatiegrens voor workloads en resources.

![Logische isolatie van een Kubernetes-cluster in AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logische scheiding van clusters biedt meestal een hogere poddichtheid dan fysiek geïsoleerde clusters. Er is minder overcapaciteit die niet actief is in het cluster. In combinatie met de Kubernetes cluster autoscaler u het aantal knooppunten omhoog of omlaag schalen om aan de eisen te voldoen. Met deze best practice-benadering voor automatisch schalen u alleen het aantal vereiste knooppunten uitvoeren en de kosten minimaliseren.

Kubernetes-omgevingen, in AKS of elders, zijn niet helemaal veilig voor vijandig multi-tenant gebruik. In een multi-tenant omgeving werken meerdere tenants aan een gemeenschappelijke, gedeelde infrastructuur. Als alle tenants niet kunnen worden vertrouwd, moet u extra planning maken om te voorkomen dat de ene tenant de beveiliging en service van een andere tenant beïnvloedt. Extra beveiligingsfuncties zoals *Pod Security Policy* en meer fijnmazige role-based access controls (RBAC) voor knooppunten maken exploits moeilijker. Echter, voor echte beveiliging bij het uitvoeren van vijandige multi-tenant workloads, een hypervisor is het enige niveau van beveiliging die u moet vertrouwen. Het beveiligingsdomein voor Kubernetes wordt het hele cluster, geen individueel knooppunt. Voor dit soort vijandige multi-tenant workloads moet u fysiek geïsoleerde clusters gebruiken.

## <a name="physically-isolate-clusters"></a>Clusters fysiek isoleren

**Richtlijnen voor aanbevolen procedures** - Minimaliseer het gebruik van fysieke isolatie voor elke afzonderlijke team- of toepassingsimplementatie. Gebruik in plaats daarvan *logische* isolatie, zoals besproken in de vorige sectie.

Een veelvoorkomende benadering van clusterisolatie is het gebruik van fysiek gescheiden AKS-clusters. In dit isolatiemodel krijgen teams of workloads hun eigen AKS-cluster toegewezen. Deze aanpak lijkt vaak de eenvoudigste manier om workloads of teams te isoleren, maar voegt extra beheer en financiële overhead toe. U moet deze meerdere clusters nu onderhouden en moet afzonderlijk toegang bieden en machtigingen toewijzen. U wordt ook gefactureerd voor alle afzonderlijke knooppunten.

![Fysieke isolatie van individuele Kubernetes-clusters in AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiek gescheiden clusters hebben meestal een lage poddichtheid. Omdat elk team of workload zijn eigen AKS-cluster heeft, is het cluster vaak over-ingericht met rekenresources. Vaak zijn er een klein aantal pods gepland op deze knooppunten. Ongebruikte capaciteit op de knooppunten kan niet worden gebruikt voor toepassingen of services in ontwikkeling door andere teams. Deze overtollige middelen dragen bij aan de extra kosten in fysiek gescheiden clusters.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op clusterisolatie. Zie de volgende aanbevolen procedures voor meer informatie over clusterbewerkingen in AKS:

* [Basisfuncties kubernetes scheduler][aks-best-practices-scheduler]
* [Geavanceerde functies voor Kubernetes scheduler][aks-best-practices-advanced-scheduler]
* [Verificatie en autorisatie][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
