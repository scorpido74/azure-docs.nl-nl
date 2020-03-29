---
title: Problemen met Azure VM-toewijzingsfouten oplossen in klassiek implementatiemodel| Microsoft Documenten
description: Toewijzingsfouten oplossen wanneer u een klassieke vm in Azure maakt, opnieuw start of het formaat ervan wijzigt
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913375"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Stappen voor probleemoplossing die specifiek zijn voor foutscenario's voor toewijzingen in het klassieke implementatiemodel

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

De volgende zijn veelvoorkomende toewijzingsscenario's die ervoor zorgen dat een toewijzingsaanvraag wordt vastgemaakt. We duiken in elk scenario later in dit artikel.

- Het formaat van een vm wijzigen of VM's of rolexemplaren toevoegen aan een bestaande cloudservice
- Gedeeltelijk gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
- Volledig gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
- Implementaties voor fasering en productie (alleen platform als service)
- Affiniteitsgroep (VM of servicenabijheid)
- Virtueel netwerk op basis van affiniteiten

Controleer bij een toewijzingsfout of een van de vermelde scenario's van toepassing is op uw fout. Gebruik de toewijzingsfout die wordt geretourneerd door het Azure-platform om het bijbehorende scenario te identificeren. Als uw aanvraag is vastgemaakt, verwijdert u een aantal van de vastenbeperkingen om uw aanvraag voor meer clusters te openen, waardoor de kans op toewijzingssucces toeneemt.
In het algemeen u het altijd opnieuw proberen als in de fout niet wordt vermeld dat de gevraagde VM-grootte niet wordt ondersteund. Dit komt omdat er mogelijk voldoende resources zijn vrijgemaakt in het cluster om aan uw aanvraag te voldoen. Als het probleem is dat de gevraagde VM-grootte niet wordt ondersteund, probeert u een andere VM-grootte. Anders is de enige optie om de koppeling voor het vastmaken te verwijderen.

Twee veelvoorkomende foutscenario's zijn gerelateerd aan affiniteitsgroepen. In het verleden werd een affiniteitsgroep gebruikt om de nabijheid van VM's en service-exemplaren te bieden, of werd deze gebruikt om het maken van een virtueel netwerk mogelijk te maken. Met de introductie van regionale virtuele netwerken zijn affiniteitsgroepen niet langer nodig om een virtueel netwerk te creëren. Met de vermindering van de netwerklatentie in azure-infrastructuur is de aanbeveling om affiniteitsgroepen te gebruiken voor VM's of servicenabijheid gewijzigd.

In het volgende diagram wordt de taxonomie van de (vastgemaakte) toewijzingsscenario's gepresenteerd. 

![Vastgemaakte toewijzing Taxonomie](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Het formaat van een vm wijzigen of VM's of rolexemplaren toevoegen aan een bestaande cloudservice
**Fout**

Upgrade_VMSizeNotSupported of GeneralError

**Oorzaak van clustervasten**

Een verzoek om het formaat van een vm te wijzigen of een VM of een rolinstantie toe te voegen aan een bestaande cloudservice, moet worden geprobeerd op het oorspronkelijke cluster dat de bestaande cloudservice host. Door een nieuwe cloudservice te maken, kan het Azure-platform een ander cluster vinden met gratis resources of ondersteuning voor de vm-grootte die u hebt aangevraagd.

**Oplossing**

Als de fout Upgrade_VMSizeNotSupported* is, probeert u een andere VM-grootte. Als het gebruik van een andere VM-grootte geen optie is, maar als het aanvaardbaar is om een ander virtueel IP-adres (VIP) te gebruiken, maakt u een nieuwe cloudservice om de nieuwe VM te hosten en voegt u de nieuwe cloudservice toe aan het regionale virtuele netwerk waar de bestaande VM's worden uitgevoerd. Als uw bestaande cloudservice geen gebruik maakt van een regionaal virtueel netwerk, u nog steeds een nieuw virtueel netwerk voor de nieuwe cloudservice maken en vervolgens uw bestaande virtuele netwerk verbinden met [het nieuwe virtuele netwerk.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Meer informatie over [regionale virtuele netwerken](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Als de fout GeneralError* is, is het waarschijnlijk dat het type resource (zoals een bepaalde VM-grootte) wordt ondersteund door het cluster, maar het cluster heeft op dit moment geen gratis resources. Voeg, net als het bovenstaande scenario, de gewenste compute resource toe door een nieuwe cloudservice te maken (merk op dat de nieuwe cloudservice een andere VIP moet gebruiken) en gebruik een regionaal virtueel netwerk om uw cloudservices met elkaar te verbinden.

## <a name="restart-partially-stopped-deallocated-vms"></a>Gedeeltelijk gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
**Fout**

GeneralError*

**Oorzaak van clustervasten**

Gedeeltelijke toewijzing betekent dat u een of meer, maar niet alle VM's in een cloudservice hebt gestopt (deallocated). Wanneer u een vm stopt (deallocateeren), worden de bijbehorende resources vrijgegeven. Het opnieuw opstarten van die gestopte (deallocated) VM is daarom een nieuwe toewijzingsaanvraag. Het opnieuw starten van VM's in een gedeeltelijk toegewezen cloudservice is gelijk aan het toevoegen van VM's aan een bestaande cloudservice. De toewijzingsaanvraag moet worden geprobeerd bij het oorspronkelijke cluster dat de bestaande cloudservice host. Door een andere cloudservice te maken, kan het Azure-platform een ander cluster vinden met gratis bron of ondersteuning voor de VM-grootte die u hebt aangevraagd.

**Oplossing**

Als het acceptabel is om een andere VIP te gebruiken, verwijdert u de gestopte (deallocated) VM's (maar houdt u de bijbehorende schijven) en voegt u de VM's terug via een andere cloudservice. Gebruik een regionaal virtueel netwerk om uw cloudservices met elkaar te verbinden:

* Als uw bestaande cloudservice een regionaal virtueel netwerk gebruikt, voegt u de nieuwe cloudservice eenvoudig toe aan hetzelfde virtuele netwerk.
* Als uw bestaande cloudservice geen gebruik maakt van een regionaal virtueel netwerk, maakt u een nieuw virtueel netwerk voor de nieuwe cloudservice en [verbindt u uw bestaande virtuele netwerk met het nieuwe virtuele netwerk.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Meer informatie over [regionale virtuele netwerken](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Volledig gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten
**Fout**

GeneralError*

**Oorzaak van clustervasten**

Volledige toewijzing betekent dat u alle VM's van een cloudservice hebt gestopt (verhandeld). De toewijzingsaanvragen om deze VM's opnieuw op te starten, moeten worden geprobeerd op het oorspronkelijke cluster dat de cloudservice host. Door een nieuwe cloudservice te maken, kan het Azure-platform een ander cluster vinden met gratis resources of ondersteuning voor de vm-grootte die u hebt aangevraagd.

**Oplossing**

Als het acceptabel is om een andere VIP te gebruiken, verwijdert u de oorspronkelijke gestopte (deallocated) VM's (maar bewaar de bijbehorende schijven) en verwijdert u de bijbehorende cloudservice (de bijbehorende compute resources zijn al vrijgegeven toen u de VM's stopte (deallocated). Maak een nieuwe cloudservice om de VM's terug te voegen.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Faserings-/productie-implementaties (alleen platform als service)
**Fout**

New_General* of New_VMSizeNotSupported*

**Oorzaak van clustervasten**

De implementatie van tijdelijke bestanden en de productie-implementatie van een cloudservice worden gehost in hetzelfde cluster. Wanneer u de tweede implementatie toevoegt, wordt de bijbehorende toewijzingsaanvraag geprobeerd in hetzelfde cluster met de eerste implementatie.

**Oplossing**

Verwijder de eerste implementatie en de oorspronkelijke cloudservice en implementeer de cloudservice opnieuw. Met deze actie kan de eerste implementatie worden uitgevoerd in een cluster dat voldoende gratis resources heeft om beide implementaties te passen of in een cluster dat de vm-formaten ondersteunt die u hebt aangevraagd.

## <a name="affinity-group-vmservice-proximity"></a>Affiniteitsgroep (VM/servicenabijheid)
**Fout**

New_General* of New_VMSizeNotSupported*

**Oorzaak van clustervasten**

Elke rekenbron die aan een affiniteitsgroep is toegewezen, is gekoppeld aan één cluster. Nieuwe rekenbronaanvragen in die affiniteitsgroep worden geprobeerd in hetzelfde cluster waar de bestaande resources worden gehost. Dit geldt of de nieuwe resources worden gemaakt via een nieuwe cloudservice of via een bestaande cloudservice.

**Oplossing**

Als een affiniteitsgroep niet nodig is, gebruikt u geen affiniteitsgroep of groepeert u uw rekenresources niet in meerdere affiniteitsgroepen.

## <a name="affinity-group-based-virtual-network"></a>Virtueel netwerk op basis van affiniteitsgroepen
**Fout**

New_General* of New_VMSizeNotSupported*

**Oorzaak van clustervasten**

Voordat regionale virtuele netwerken werden geïntroduceerd, moest u een virtueel netwerk koppelen aan een affiniteitsgroep. Als gevolg hiervan zijn rekenresources die in een affiniteitsgroep zijn geplaatst, gebonden aan dezelfde beperkingen als beschreven in de sectie 'Toewijzingsscenario: affiniteitsgroep (VM/servicenabijheid)'. De rekenbronnen zijn gekoppeld aan één cluster.

**Oplossing**

Als u geen affiniteitsgroep nodig hebt, maakt u een nieuw regionaal virtueel netwerk voor de nieuwe bronnen die u toevoegt en sluit u [uw bestaande virtuele netwerk aan op het nieuwe virtuele netwerk.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Meer informatie over [regionale virtuele netwerken](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

U ook [uw virtuele netwerk op basis van affiniteitsgroepen migreren naar een regionaal virtueel netwerk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)en vervolgens de gewenste resources opnieuw toevoegen.


