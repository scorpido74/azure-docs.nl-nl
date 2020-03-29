---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748926"
---
In dit artikel vindt u een overzicht van de beschikbaarheidsfuncties van virtuele Azure-machines (VM's).

## <a name="high-availability"></a>Hoge beschikbaarheid

Workloads worden meestal verspreid over verschillende virtuele machines om een hoge doorvoer, prestaties te krijgen en redundantie te maken voor het geval een VM wordt beïnvloed als gevolg van een update of andere gebeurtenis. 

Er zijn weinig opties die Azure biedt om hoge beschikbaarheid te bereiken. Laten we het eerst hebben over basisconstructies. 

### <a name="availability-zones"></a>Beschikbaarheidszones

[Beschikbaarheidszones](../articles/availability-zones/az-overview.md) breiden het controleniveau uit dat u hebt om de beschikbaarheid van de toepassingen en gegevens op uw VM's te behouden. Een beschikbaarheidszone is een fysiek gescheiden zone binnen een Azure-gebied. Er zijn drie beschikbaarheidszones per ondersteunde Azure-regio. 

Elke beschikbaarheidszone heeft een afzonderlijke voedingsbron en koeling, en een afzonderlijk netwerk. Door uw oplossingen te ontwerpen voor het gebruik van gerepliceerde VM's in zones, u uw apps en gegevens beschermen tegen het verlies van een datacenter. Als de ene zone is gecompromitteerd, zijn gerepliceerde apps en gegevens direct beschikbaar in een andere zone. 

![Beschikbaarheidszones](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Meer informatie over het implementeren van een [Windows-](../articles/virtual-machines/windows/create-powershell-availability-zone.md) of [Linux-vm](../articles/virtual-machines/linux/create-cli-availability-zone.md) in een beschikbaarheidszone.


### <a name="fault-domains"></a>Foutdomeinen

Een foutdomein is een logische groep onderliggende hardware met een gemeenschappelijke voeding en netwerkswitch, vergelijkbaar met een rack in een on-premises datacenter. 

### <a name="update-domains"></a>Updatedomeinen

Een updatedomein is een logische groep onderliggende hardware die op hetzelfde moment onderhoud kan ondergaan of opnieuw kan worden opgestart. 

Deze aanpak zorgt ervoor dat altijd ten minste één exemplaar van uw toepassing beschikbaar blijft wanneer er periodiek onderhoud wordt uitgevoerd aan het Azure-platform. De volgorde van updatedomeinen die opnieuw worden opgestart, gaat mogelijk niet opeenvolgend te werk tijdens het onderhoud, maar slechts één updatedomein wordt tegelijkertijd opnieuw opgestart.


## <a name="virtual-machines-scale-sets"></a>Schaalsets voor virtuele machines 

Met azure virtuele machineschaalsets u een groep load balanced VM's maken en beheren. Het aantal VM-exemplaren kan automatisch toenemen of afnemen in reactie op vraag of een ingesteld schema. Schaalsets bieden een hoge beschikbaarheid voor uw toepassingen en stellen u in staat om veel VM's centraal te beheren, configureren en bijwerken. We raden aan dat twee of meer VM's worden gemaakt binnen een schaalset om te zorgen voor een zeer beschikbare toepassing en om te voldoen aan de [99,95% Azure SLA.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Er zijn geen kosten verbonden aan de schaalset zelf, u betaalt alleen voor elke VM-instantie die u maakt. Wanneer één VM [Azure premium SSD's](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)gebruikt, is de Azure SLA van toepassing op ongeplande onderhoudsgebeurtenissen. Virtuele machines in een schaalset kunnen worden geïmplementeerd in meerdere updatedomeinen en foutdomeinen om de beschikbaarheid en veerkracht voor uitval als gevolg van uitval van datacenters en geplande of ongeplande onderhoudsgebeurtenissen te maximaliseren. Virtuele machines in een schaalset kunnen ook worden geïmplementeerd in één beschikbaarheidszone, of regionaal. De implementatieopties voor beschikbaarheidszones kunnen verschillen op basis van de orchestration-modus.

### <a name="preview-orchestration-mode-preview"></a>Preview: Preview van de orchestration-modus
Met virtuele schalen u de orchestration-modus opgeven.  Met de orkestratiemodus voor virtuele machineschaalsets (voorbeeld) u nu kiezen of de schaalset virtuele machines moet orkestreren die expliciet buiten een schaalsetconfiguratiemodel zijn gemaakt, of virtuele machine-exemplaren die impliciet zijn gemaakt gebaseerd op het configuratiemodel. Kies de orchestration-modus waarmee u met het VM-orchestration-model expliciet gedefinieerde virtuele machines in een regio of in een beschikbaarheidszone groeperen. Virtuele machines die zijn geïmplementeerd in een beschikbaarheidszone bieden zonale isolatie aan VM's, zijn gebonden aan de grens van de beschikbaarheidszone en worden niet blootgesteld aan storingen die zich kunnen voordoen in andere beschikbaarheidszone in de regio. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| VM-configuratiemodel| Geen. VirtualMachineProfile is niet gedefinieerd in het schaalsetmodel. | Vereist. VirtualMachineProfile wordt ingevuld in het schaalsetmodel. |
| Nieuwe VM toevoegen aan schaalset| VM's worden expliciet toegevoegd aan de schaalset wanneer de VM wordt gemaakt. | VM's worden impliciet gemaakt en toegevoegd aan de schaalset op basis van het VM-configuratiemodel, het aantal instance's en de regels Voor automatisch schalen. |
| Beschikbaarheidszones| Ondersteunt regionale implementatie of VM's in één beschikbaarheidszone| Ondersteunt regionale implementatie of meerdere beschikbaarheidszones; Kan de zone balanceringsstrategie definiëren |
| Foutdomeinen| Kan het aantal foutdomeinen definiëren. 2 of 3 op basis van regionale ondersteuning en 5 voor beschikbaarheidszone. Het toegewezen VM-foutdomein blijft bestaan in de VM-levenscyclus, inclusief detoewijzing en opnieuw starten. | Kan 1, 2 of 3 foutdomeinen definiëren voor niet-zonale implementaties en 5 voor implementaties van beschikbaarheidszones. Het toegewezen VM-foutdomein blijft niet bestaan met de VM-levenscyclus, virtuele machines krijgen een foutdomein toegewezen op het moment van toewijzing. |
| Updatedomeinen| N.v.t. Updatedomeinen worden automatisch toegewezen aan foutdomeinen| N.v.t. Updatedomeinen worden automatisch toegewezen aan foutdomeinen |

**Foutdomeinen en updatedomeinen**

Virtuele machineschaalsets vereenvoudigen het ontwerpen voor hoge beschikbaarheid door foutdomeinen en updatedomeinen uit te lijnen. U hoeft alleen foutdomeinen te definiëren die meetellen voor de schaalset. Het aantal foutdomeinen dat beschikbaar is voor de schaalsets kan per regio verschillen. Zie [De beschikbaarheid van virtuele machines beheren in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Beschikbaarheidssets
Een beschikbaarheidsset is een logische groepering van VM's in een datacenter waarmee Azure kan begrijpen hoe uw toepassing is gebouwd om redundantie en beschikbaarheid te bieden. We raden aan dat twee of meer VM's worden gemaakt binnen een beschikbaarheidsset om te voorzien in een zeer beschikbare toepassing en om te voldoen aan de [99,95% Azure SLA.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Er zijn geen kosten verbonden aan de beschikbaarheidsset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt. Wanneer één VM [Azure premium SSD's](../articles/virtual-machines/windows/disks-types.md#premium-ssd)gebruikt, is de Azure SLA van toepassing op ongeplande onderhoudsgebeurtenissen.

In een beschikbaarheidsset worden VM's automatisch verdeeld over deze foutdomeinen. Deze aanpak beperkt de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen.

Voor virtuele machines die gebruikmaken van [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) en deel uitmaken van een beheerde beschikbaarheidsset, worden de virtuele machines afgestemd op Managed Disk-foutdomeinen. Deze afstemming zorgt ervoor dat alle beheerde schijven die zijn gekoppeld aan een virtuele machine, zich binnen hetzelfde Managed Disk-foutdomein bevinden. 

In een beheerde beschikbaarheidsset kunnen alleen virtuele machines met beheerde schijven worden gemaakt. Het aantal Managed Disk-foutdomeinen verschilt per regio: er zijn twee of drie Managed Disk-foutdomeinen per regio. U meer lezen over deze beheerde schijffoutdomeinen voor [Linux VM's](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) of [Windows VM's.](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)

![Beheerde beschikbaarheidset](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


VM's binnen een beschikbaarheidsset worden ook automatisch verdeeld over updatedomeinen. 

![Beschikbaarheidssets](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Volgende stappen
U kunt nu deze functies voor beschikbaarheid en redundantie gaan gebruiken om uw eigen Azure-omgeving te bouwen. Zie voor informatie over aanbevolen procedures de [aanbevolen procedures voor Azure-beschikbaarheid](/azure/architecture/checklist/resiliency-per-service).

