---
title: Beheerde Service Fabric-clusters (preview)
description: Beheerde Service Fabric clusters zijn een evolutie van het Azure Service Fabric-clusterresourcemodel dat de implementatie en het clusterbeheer stroomlijnt.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410381"
---
# <a name="service-fabric-managed-clusters-preview"></a>Beheerde Service Fabric-clusters (preview)

Beheerde Service Fabric clusters zijn een evolutie van het Azure Service Fabric-clusterresourcemodel dat uw implementatie en clusterbeheer stroomlijnt.

De ARM-sjabloon (Azure Resource Model) voor traditionele Service Fabric-clusters vereist dat u een clusterresource definieert naast een aantal ondersteunende resources, die allemaal correct ' bekabeld' moeten zijn (na de implementatie en gedurende de hele levenscyclus van de cluster), zodat de cluster en uw services goed functioneren. Het encapsulation-model voor beheerde Service Fabric clusters bestaat daarentegen uit één resource van een *Beheerde Service Fabric-cluster*. Alle onderliggende resources voor de cluster worden namens u door Azure geabstraheerd en beheerd.

**Traditioneel Service Fabric-clustermodel**
![Traditioneel Service Fabric-clustermodel][sf-composition]

**Beheerd Service Fabric-clustermodel**
![Ingekapseld Service Fabric-clustermodel][sf-encapsulation]

Op het vlak van grootte en complexiteit is de ARM-sjabloon voor een beheerde Service Fabric-cluster ongeveer 100 regels in JSON, terwijl er ongeveer 1000 regels nodig zijn voor een typische Service Fabric-cluster:

| Service Fabric-resources | Beheerde Service Fabric-clusterresources |
|----------|-----------|
| Service Fabric-cluster | Beheerde Service Fabric-cluster |
| Schaalset(s) voor virtuele machines | |
| Load balancer | |
| Openbaar IP-adres | |
| Opslagaccount(s) | |
| Virtueel netwerk | |

Beheerde Service Fabric-clusters bieden een aantal voordelen ten opzichte van traditionele clusters:

**Vereenvoudigde clusterimplementatie en -beheer**
- Eén Azure-resources implementeren en beheren
- Certificaatbeheer en autorotatie
- Vereenvoudigde schaalbewerkingen

**Operationele fouten voorkomen**
- Configuratieproblemen met de onderliggende resources voorkomen
- Onveilige bewerkingen blokkeren (zoals het verwijderen van een seed-knooppunt)

**Standaard best practices**
- Vereenvoudigde instellingen voor betrouwbaarheid en duurzaamheid

Er zijn geen andere kosten voor beheerde Service Fabric-clusters dan de kosten van de onderliggende resources die nodig zijn voor de cluster.

## <a name="service-fabric-managed-cluster-skus"></a>SKU's voor Beheerde Service Fabric-clusters

Beheerde Service Fabric-clusters zijn beschikbaar in de Basic- en Standard-SKU's.

| Functie | Basic | Standard |
| ------- | ----- | -------- |
| Netwerkresource (SKU voor [Load Balancer](../load-balancer/skus.md), [Openbare IP](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Aantal knooppunten (VM-exemplaar) | 3 | 5 |
| Maximum aantal knooppunten per knooppunttype | 100 | 100 |
| Maximum aantal knooppunttypen | 1 | 20 |
| Typen knooppunten toevoegen of verwijderen | Nee | Ja |
| Zoneredundantie | Nee | Ja |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Functieroadmap voor een beheerde Service Fabric-cluster
Aangezien dit een vroege preview-versie is van beheerde Service Fabric-clusters zijn, moet u rekening houden met enkele gebreken. Deze functies worden beschikbaar bij toekomstige releases. 

* Toepassingen rechtstreeks vanuit Visual Studio publiceren naar clusters
* Beheerde identiteiten 
* ARM-toepassingsimplementaties 
* Beschikbaarheidszones 
* Omgekeerde proxy 
* Automatisch schalen 
* NSG-regels bijwerken 
* Automatische upgrades voor het besturingssysteem

## <a name="next-steps"></a>Volgende stappen

Probeer de snelstartgids uit om aan de slag te gaan met beheerde Service Fabric-clusters:

> [!div class="nextstepaction"]
> [Een beheerde Service Fabric-cluster (preview) maken](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png