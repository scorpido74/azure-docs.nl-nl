---
title: Problemen met Azure VM-toewijzings fouten oplossen in het klassieke implementatie model | Microsoft Docs
description: Toewijzings fouten oplossen wanneer u een klassieke virtuele machine in azure maakt, opnieuw opstart of de grootte ervan wijzigt
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913375"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Probleemoplossings stappen die specifiek zijn voor toewijzing van fout scenario's in het klassieke implementatie model

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Hieronder vindt u algemene toewijzings scenario's die ervoor zorgen dat een toewijzings aanvraag wordt vastgemaakt. Verderop in dit artikel gaan we in op elk scenario.

- Het formaat van een virtuele machine wijzigen of Vm's of rolinstanties toevoegen aan een bestaande Cloud service
- Gedeeltelijk gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
- Volledig gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
- Fase ring en productie-implementaties (alleen platform als een service)
- Affiniteits groep (VM of service nabij)
- Affiniteit: virtueel netwerk op basis van een groep

Wanneer u een toewijzings fout ontvangt, controleert u of een van de vermelde scenario's van toepassing is op uw fout. Gebruik de toewijzings fout die door het Azure-platform wordt geretourneerd om het bijbehorende scenario te identificeren. Als uw aanvraag is vastgemaakt, verwijdert u enkele van de vastgezette beperkingen om uw aanvraag te openen voor meer clusters, waardoor de kans op succes van de toewijzing wordt verg root.
In het algemeen geldt dat als de fout niet wordt vermeld dat ' de aangevraagde VM-grootte niet wordt ondersteund ', u altijd op een later tijdstip opnieuw kunt proberen. Dit komt doordat er in het cluster voldoende resources zijn vrijgemaakt om uw aanvraag te kunnen verwerken. Als het probleem is dat de aangevraagde VM-grootte niet wordt ondersteund, probeert u een andere VM-grootte. Anders is de enige optie voor het verwijderen van de ontkoppelings beperking.

Twee veelvoorkomende fout scenario's zijn gerelateerd aan affiniteits groepen. In het verleden werd een affiniteits groep gebruikt voor het sluiten van de Vm's en service-exemplaren, of is deze gebruikt om het maken van een virtueel netwerk in te scha kelen. Met de introductie van regionale virtuele netwerken zijn affiniteits groepen niet langer vereist voor het maken van een virtueel netwerk. Door de netwerk latentie in de Azure-infra structuur te verminderen, is de aanbeveling voor het gebruik van affiniteits groepen voor Vm's of service nabijheid gewijzigd.

Het volgende diagram toont de taxonomie van de (vastgemaakte) toewijzings scenario's. 

![Taxonomie vaste toewijzing](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Het formaat van een virtuele machine wijzigen of Vm's of rolinstanties toevoegen aan een bestaande Cloud service
**Optreedt**

Upgrade_VMSizeNotSupported of GeneralError

**Oorzaak van het vastmaken van het cluster**

Een aanvraag om de grootte van een virtuele machine of een virtuele machine of een rolinstantie toe te voegen aan een bestaande Cloud service moet worden geprobeerd op het oorspronkelijke cluster dat als host fungeert voor de bestaande Cloud service. Als u een nieuwe Cloud service maakt, kunt u met het Azure-platform een ander cluster vinden dat gratis bronnen heeft of de door u aangevraagde VM-grootte ondersteunt.

**Tijdelijke oplossing**

Als de fout is Upgrade_VMSizeNotSupported *, probeert u een andere VM-grootte. Als u een andere VM-grootte gebruikt, maar als u een ander virtueel IP-adres (VIP) wilt gebruiken, moet u een nieuwe Cloud service maken om de nieuwe VM te hosten en de nieuwe Cloud service toe te voegen aan het regionale virtuele netwerk waar de bestaande virtuele machines worden uitgevoerd. Als uw bestaande Cloud service geen regionaal virtueel netwerk gebruikt, kunt u nog steeds een nieuw virtueel netwerk voor de nieuwe Cloud service maken en vervolgens uw [bestaande virtuele netwerk verbinden met het nieuwe virtuele netwerk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Meer informatie over [regionale virtuele netwerken](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Als de fout GeneralError * is, is het waarschijnlijk dat het type resource (zoals een bepaalde VM-grootte) door het cluster wordt ondersteund, maar dat het cluster op het moment geen vrije bronnen heeft. Net als in het bovenstaande scenario voegt u de gewenste reken resource toe via het maken van een nieuwe Cloud service (Houd er rekening mee dat de nieuwe Cloud service een andere VIP moet gebruiken) en gebruik een regionaal virtueel netwerk om uw Cloud Services te verbinden.

## <a name="restart-partially-stopped-deallocated-vms"></a>Gedeeltelijk gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
**Optreedt**

GeneralError*

**Oorzaak van het vastmaken van het cluster**

Gedeeltelijke toewijzing betekent dat u een of meer, maar niet alle virtuele machines in een Cloud service hebt gestopt (toewijzing ongedaan gemaakt). Wanneer u een virtuele machine stopt (toewijzing opheffen), worden de bijbehorende resources vrijgegeven. Het opnieuw starten van de virtuele machine is gestopt (toewijzing ongedaan gemaakt) is daarom een nieuwe aanvraag voor toewijzingen. Het opnieuw starten van Vm's in een gedeeltelijk niet-toegewezen Cloud service is gelijk aan het toevoegen van Vm's aan een bestaande Cloud service. De toewijzings aanvraag moet worden geprobeerd op het oorspronkelijke cluster dat als host fungeert voor de bestaande Cloud service. Door een andere Cloud service te maken, kan het Azure-platform een ander cluster vinden dat een gratis resource heeft of die de door u aangevraagde VM-grootte ondersteunt.

**Tijdelijke oplossing**

Als het acceptabel is om een andere VIP te gebruiken, verwijdert u de gestopte (gespreide) Vm's (maar behoudt u de gekoppelde schijven) en voegt u de virtuele machines weer toe via een andere Cloud service. Een regionaal virtueel netwerk gebruiken om verbinding te maken met uw Cloud Services:

* Als uw bestaande Cloud service gebruikmaakt van een regionaal virtueel netwerk, kunt u de nieuwe Cloud service gewoon toevoegen aan hetzelfde virtuele netwerk.
* Als uw bestaande Cloud service geen regionaal virtueel netwerk gebruikt, maakt u een nieuw virtueel netwerk voor de nieuwe Cloud service en verbindt u vervolgens [uw bestaande virtuele netwerk met het nieuwe virtuele netwerk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Meer informatie over [regionale virtuele netwerken](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Volledig gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
**Optreedt**

GeneralError*

**Oorzaak van het vastmaken van het cluster**

Bij volledige toewijzing betekent dat u alle Vm's van een Cloud service hebt gestopt (toewijzing ongedaan gemaakt). De toewijzings aanvragen voor het opnieuw opstarten van deze Vm's moeten worden geprobeerd op het oorspronkelijke cluster dat als host fungeert voor de Cloud service. Als u een nieuwe Cloud service maakt, kunt u met het Azure-platform een ander cluster vinden dat gratis bronnen heeft of de door u aangevraagde VM-grootte ondersteunt.

**Tijdelijke oplossing**

Als het acceptabel is om een andere VIP te gebruiken, verwijdert u de oorspronkelijke (gespreide) Vm's (niet-toegewezen virtuele machines) en verwijdert u de bijbehorende Cloud service (de gekoppelde reken resources zijn al vrijgegeven wanneer u de Vm's hebt gestopt (toewijzing opgeheven)). Maak een nieuwe Cloud service om de Vm's weer toe te voegen.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Fase ring/productie-implementaties (alleen platform als een service)
**Optreedt**

New_General * of New_VMSizeNotSupported *

**Oorzaak van het vastmaken van het cluster**

De faserings implementatie en de productie-implementatie van een Cloud service worden gehost in hetzelfde cluster. Wanneer u de tweede implementatie toevoegt, wordt de bijbehorende toewijzings aanvraag in hetzelfde cluster die als host fungeert voor de eerste implementatie uitgevoerd.

**Tijdelijke oplossing**

Verwijder de eerste implementatie en de oorspronkelijke Cloud service en implementeer de Cloud service opnieuw. Deze actie kan de eerste implementatie in een cluster met voldoende beschik bare bronnen in beslag brengen voor zowel implementaties als in een cluster die ondersteuning biedt voor de VM-grootten die u hebt aangevraagd.

## <a name="affinity-group-vmservice-proximity"></a>Affiniteits groep (VM/service nabij)
**Optreedt**

New_General * of New_VMSizeNotSupported *

**Oorzaak van het vastmaken van het cluster**

Alle reken resources die zijn toegewezen aan een affiniteits groep, zijn gekoppeld aan één cluster. Nieuwe reken resource-aanvragen in die affiniteits groep worden geprobeerd in hetzelfde cluster waarin de bestaande resources worden gehost. Dit is waar, ongeacht of de nieuwe resources zijn gemaakt via een nieuwe Cloud service of via een bestaande Cloud service.

**Tijdelijke oplossing**

Als een affiniteits groep niet nodig is, gebruik dan geen affiniteits groep of Groepeer uw reken resources in meerdere affiniteits groepen.

## <a name="affinity-group-based-virtual-network"></a>Affiniteit-op groep gebaseerd virtueel netwerk
**Optreedt**

New_General * of New_VMSizeNotSupported *

**Oorzaak van het vastmaken van het cluster**

Voordat regionale virtuele netwerken werden geïntroduceerd, moest u een virtueel netwerk koppelen aan een affiniteits groep. Als gevolg hiervan worden reken resources die in een affiniteits groep zijn geplaatst, gebonden aan dezelfde beperkingen zoals beschreven in de sectie ' toewijzings scenario: Affiniteits groep (VM/service nabijheid) '. De reken resources zijn gekoppeld aan één cluster.

**Tijdelijke oplossing**

Als u geen affiniteits groep nodig hebt, maakt u een nieuw regionaal virtueel netwerk voor de nieuwe resources die u toevoegt en verbindt u vervolgens [uw bestaande virtuele netwerk met het nieuwe virtuele netwerk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Meer informatie over [regionale virtuele netwerken](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

U kunt ook het [virtuele netwerk van de affiniteit op basis van een groep naar een regionaal virtueel netwerk migreren](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)en vervolgens de gewenste resources opnieuw toevoegen.


