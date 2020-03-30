---
title: Aanbevolen procedures voor Azure Kubernetes Service (AKS)
description: Verzameling van de aanbevolen procedures voor de clusteroperator en ontwikkelaar voor het bouwen en beheren van toepassingen in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596314"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor clusteroperator en ontwikkelaar voor het bouwen en beheren van toepassingen op Azure Kubernetes Service (AKS)

Om toepassingen succesvol te bouwen en uit te voeren in Azure Kubernetes Service (AKS), zijn er enkele belangrijke overwegingen om te begrijpen en te implementeren. Deze gebieden omvatten functies voor meerdere huur- en planners, cluster- en podbeveiliging of bedrijfscontinuïteit en herstel na noodgevallen. De volgende aanbevolen procedures zijn gegroepeerd om clusteroperators en ontwikkelaars te helpen de overwegingen voor elk van deze gebieden te begrijpen en de juiste functies te implementeren.

Deze best practices en conceptuele artikelen zijn geschreven in samenwerking met de AKS-productgroep, engineeringteams en veldteams, waaronder global black belts (GBB's).

## <a name="cluster-operator-best-practices"></a>Aanbevolen procedures voor clusteroperator

Werk als clusteroperator samen met toepassingseigenaren en ontwikkelaars om hun behoeften te begrijpen. U vervolgens de volgende aanbevolen procedures gebruiken om uw AKS-clusters zo nodig te configureren.

**Multitenancy**

* [Aanbevolen procedures voor clusterisolatie](operator-best-practices-cluster-isolation.md)
    * Bevat kerncomponenten met meerdere huurovereenkomsten en logische isolatie met naamruimten.
* [Aanbevolen procedures voor standaard Scheduler-functies](operator-best-practices-scheduler.md)
    * Inclusief het gebruik van resourcequota en pod-onderbrekingsbudgetten.
* [Aanbevolen procedures voor geavanceerde Scheduler-functies](operator-best-practices-advanced-scheduler.md)
    * Omvat het gebruik van taints en toleranties, knooppuntselectors en affiniteit, en interpod affiniteit en anti-affiniteit.
* [Aanbevolen procedures voor verificatie en autorisatie](operator-best-practices-identity.md)
    * Inclusief integratie met Azure Active Directory, met behulp van op rollen gebaseerde toegangscontroles (RBAC) en podidentiteiten.

**Beveiliging**

* [Aanbevolen procedures voor beveiliging en upgrades van clusters](operator-best-practices-cluster-security.md)
    * Inclusief het beveiligen van toegang tot de API-server, het beperken van containertoegang en het beheren van upgrades en het opnieuw opstarten van knooppunten.
* [Aanbevolen procedures voor het beheer en beveiliging van containerafbeeldingen](operator-best-practices-container-image-management.md)
    * Inclusief het beveiligen van de afbeelding en runtimes en geautomatiseerde builds op basisvan image-updates.
* [Aanbevolen procedures voor podbeveiliging](developer-best-practices-pod-security.md)
    * Omvat het beveiligen van toegang tot bronnen, het beperken van de blootstelling aan referenties en het gebruik van pod-identiteiten en digitale sleutelkluizen.

**Netwerk en opslag**

* [Aanbevolen procedures voor netwerkconnectiviteit](operator-best-practices-network.md)
    * Omvat verschillende netwerkmodellen, met behulp van inbinnendringen en webapplicatie firewalls (WAF), en het beveiligen van knooppunt SSH toegang.
* [Best practices voor opslag en back-ups](operator-best-practices-storage.md)
    * Inclusief het kiezen van het juiste opslagtype en knooppuntgrootte, het dynamisch inrichten van volumes en back-ups van gegevens.

**Bedrijfsklare workloads uitvoeren**

* [Best practices voor bedrijfscontinuïteit en disaster recovery](operator-best-practices-multi-region.md)
    * Inclusief het gebruik van regioparen, meerdere clusters met Azure Traffic Manager en geo-replicatie van containerafbeeldingen.

## <a name="developer-best-practices"></a>Best practices voor ontwikkelaars

Als ontwikkelaar of eigenaar van toepassingen u uw ontwikkelingservaring vereenvoudigen en de prestatiebehoeften van vereisende toepassingen definiëren.

* [Aanbevolen procedures voor toepassingsontwikkelaars om resources te beheren](developer-best-practices-resource-management.md)
    * Omvat het definiëren van pod resource aanvragen en limieten, het configureren van ontwikkeltools en het controleren op toepassingsproblemen.
* [Aanbevolen procedures voor podbeveiliging](developer-best-practices-pod-security.md)
    * Omvat het beveiligen van toegang tot bronnen, het beperken van de blootstelling aan referenties en het gebruik van pod-identiteiten en digitale sleutelkluizen.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS concepten

Om een aantal van de functies en onderdelen van deze aanbevolen procedures te begrijpen, u ook de volgende conceptuele artikelen voor clusters in Azure Kubernetes Service (AKS) bekijken:

* [Kubernetes kernconcepten](concepts-clusters-workloads.md)
* [Toegang en identiteit](concepts-identity.md)
* [Beveiligingsconcepten](concepts-security.md)
* [Netwerkconcepten](concepts-network.md)
* [Opslagopties](concepts-storage.md)
* [Opties voor schalen](concepts-scale.md)

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met AKS, volgt u een van de snel gestarten om een AKS-cluster (Azure Kubernetes Service) te implementeren met behulp van de Azure CLI- of [Azure-portal.](kubernetes-walkthrough-portal.md) [Azure CLI](kubernetes-walkthrough.md)
