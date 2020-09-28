---
title: Veelgestelde vragen over Service Fabric beheerde clusters
description: Veelgestelde vragen over Service Fabric beheerde clusters, met inbegrip van mogelijkheden, use cases en algemene scenario's.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 73815e793357dd38244dd429f9056c4c6a0bfff1
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410309"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Veelgestelde vragen over het Service Fabric van beheerde clusters

Er zijn veel Veelgestelde vragen over wat Service Fabric beheerde clusters (preview) kan doen en hoe ze moeten worden gebruikt. In dit document worden veel van deze veelgestelde vragen en de antwoorden beschreven.

## <a name="general"></a>Algemeen

### <a name="what-are-service-fabric-managed-clusters"></a>Wat zijn Service Fabric beheerde clusters?

Service Fabric Managed clusters zijn een evolutie van het Service Fabric cluster resource model dat is ontworpen om het implementeren en beheren van clusters eenvoudiger te maken. Een door Service Fabric beheerd cluster maakt gebruik van het Azure Resource Manager encapsulation-model, zodat een gebruiker slechts één cluster bron hoeft te definiëren en implementeren in vergelijking met de vele onafhankelijke resources die ze vandaag moeten implementeren (virtuele-machine Schaalset, Load Balancer, IP en meer).

### <a name="what-regions-are-supported-in-the-preview"></a>Welke regio's worden in de preview-versie ondersteund?

Ondersteunde regio's voor de service Fabric Managed clusters preview includes `centraluseuap` ,,,, `eastus2euap` `eastasia` `northeurope` `westcentralus` en `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Kan ik een in-place migratie uitvoeren van mijn bestaande Service Fabric cluster naar een beheerde cluster resource?

Op dit moment moet u een nieuwe Service Fabric cluster bron maken om het nieuwe Service Fabric beheerde cluster resource type te gebruiken.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Zijn er extra kosten verbonden aan Service Fabric beheerde clusters?

Nee, er zijn geen extra kosten verbonden aan een door Service Fabric beheerd cluster, naast de kosten van de onderliggende compute-, opslag-en netwerk bronnen die nodig zijn voor het cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Is er een nieuwe SLA geïntroduceerd door de Service Fabric beheerde cluster resource?

De SLA wordt niet gewijzigd ten opzichte van het huidige Service Fabric resource model.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Wat is het verschil tussen een basis-en standaard SKU-cluster?

Een Basic SKU-cluster betekent dat de meeste configuraties worden verschaft door de resource provider van Service Fabric. Basis-SKU-clusters zijn bedoeld om te worden gebruikt voor test-en pre-productie omgevingen. Met een standaard SKU-cluster kunnen gebruikers het cluster zo configureren dat ze specifiek aan hun behoeften voldoen. Zie [service Fabric Managed cluster sku's](overview-managed-cluster.md#service-fabric-managed-cluster skus.md) voor meer informatie.

## <a name="cluster-deployment-and-management"></a>Cluster implementatie en-beheer

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Ik voer aangepaste script extensies uit op mijn Schaalset voor virtuele machines, kan ik toch door gaan met een beheerde Service Fabric resource?

Ja, u kunt nog steeds VM-extensies opgeven voor een knooppunt type. Zie voor meer informatie het voor beeld van een type extensie voor een knoop punt voor meer informatie.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Ik wil alleen een interne load balancer, dat is mogelijk?

Het is momenteel niet mogelijk om alleen een interne load balancer te hebben. We raden u aan de regels voor de netwerk beveiligings groep te vergren delen om ongewenst inkomend/uitgaand verkeer te blok keren.

### <a name="can-i-autoscale-my-cluster"></a>Kan ik mijn cluster automatisch schalen? 
Automatisch schalen is momenteel niet beschikbaar in de preview-versie.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Kan ik mijn cluster implementeren in verschillende beschikbaarheids zones? 
Zone clusters met meerdere Beschik baarheid zijn momenteel niet beschikbaar in de preview-versie.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Kan ik kiezen tussen automatische en hand matige upgrades voor mijn cluster runtime? 
In de preview-versie worden alle runtime-upgrades automatisch voltooid.

## <a name="applications"></a>Toepassingen

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Is er een lokale ontwikkel ervaring voor Service Fabric beheerde clusters?

De lokale ontwikkel ervaring blijft ongewijzigd ten opzichte van bestaande Service Fabric clusters. Zie [een .NET-toepassing maken](https://docs.microsoft.com/azure/service-fabric/service-fabric-quickstart-dotnet) voor meer informatie over de lokale ontwikkel ervaring.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Kan ik mijn toepassingen implementeren als een Azure Resource Manager resource?

In de preview-versie kunt u geen toepassingen implementeren als een Azure Resource Manager bron. Toepassingen moeten worden geïmplementeerd door rechtstreeks verbinding te maken met het cluster via Power shell of CLI. Deze functionaliteit wordt toegevoegd voordat beheerd Service Fabric-clusters algemene Beschik baarheid invoeren.
