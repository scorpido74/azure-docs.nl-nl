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
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414458"
---
In dit artikel vindt u een overzicht van de beschik baarheid van Azure virtual machines (Vm's).

## <a name="high-availability"></a>Hoge beschikbaarheid

Werk belastingen worden meestal verspreid over verschillende virtuele machines voor hoge door Voer, prestaties en het maken van redundantie voor het geval dat een virtuele machine wordt beïnvloed door een update of een andere gebeurtenis. 

Azure biedt een aantal opties die voor hoge Beschik baarheid kunnen worden gerealiseerd. Eerst bespreken we de basis constructies. 

### <a name="availability-zones"></a>Beschikbaarheidszones

[Beschikbaarheids zones](../articles/availability-zones/az-overview.md) breiden het beheer niveau uit dat u nodig hebt om de beschik baarheid van de toepassingen en gegevens op uw vm's te behouden. Een beschikbaarheids zone is een fysiek gescheiden zone binnen een Azure-regio. Er zijn drie Beschikbaarheidszones per ondersteunde Azure-regio. 

Elke beschikbaarheidszone heeft een afzonderlijke voedingsbron en koeling, en een afzonderlijk netwerk. Door uw oplossingen te ontwikkelen voor het gebruik van gerepliceerde Vm's in zones, kunt u uw apps en gegevens beveiligen tegen verlies van een Data Center. Als er een zone wordt aangetast, zijn de gerepliceerde apps en gegevens direct beschikbaar in een andere zone. 

![Beschikbaarheidszones](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Meer informatie over het implementeren van een virtuele [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) -of [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) -machine in een beschikbaarheids zone.


### <a name="fault-domains"></a>Foutdomeinen

Een foutdomein is een logische groep onderliggende hardware met een gemeenschappelijke voeding en netwerkswitch, vergelijkbaar met een rack in een on-premises datacenter. 

### <a name="update-domains"></a>Updatedomeinen

Een updatedomein is een logische groep onderliggende hardware die op hetzelfde moment onderhoud kan ondergaan of opnieuw kan worden opgestart. 

Deze aanpak zorgt ervoor dat altijd ten minste één exemplaar van uw toepassing beschikbaar blijft wanneer er periodiek onderhoud wordt uitgevoerd aan het Azure-platform. De volg orde waarin update domeinen opnieuw worden opgestart, wordt mogelijk niet sequentieel voortgezet tijdens het onderhoud, maar er wordt slechts één update domein tegelijk opnieuw opgestart.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines schaal sets 

Met de virtuele-machine schaal sets van Azure kunt u een groep met virtuele machines met taak verdeling maken en beheren. Het aantal VM-exemplaren kan automatisch toenemen of afnemen in reactie op vraag of een ingesteld schema. Schaal sets bieden een hoge Beschik baarheid voor uw toepassingen en u kunt een groot aantal virtuele machines centraal beheren, configureren en bijwerken. We raden u aan twee of meer virtuele machines te maken binnen een schaalset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten verbonden aan de schaalset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt. Wanneer één virtuele machine gebruikmaakt van [Azure Premium ssd's](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), is de Azure-sla van toepassing op niet-geplande onderhouds gebeurtenissen. Virtuele machines in een schaalset kunnen worden geïmplementeerd in meerdere update domeinen en fout domeinen om de beschik baarheid en tolerantie te maximaliseren voor uitval vanwege Data Center-storingen en geplande of niet-geplande onderhouds gebeurtenissen. Virtuele machines in een schaalset kunnen ook in één beschikbaarheids zone of regio worden geïmplementeerd. De implementatie opties voor de beschikbaarheids zone kunnen verschillen op basis van de Orchestration-modus.

### <a name="preview-orchestration-mode-preview"></a>Preview: voor beeld van Orchestration-modus
Met schaal sets voor virtuele machines kunt u de Orchestration-modus opgeven.  Met de Orchestration-modus (preview) voor de virtuele-machine Scale set kunt u nu kiezen of de schaalset virtuele machines moet organiseren die expliciet worden gemaakt buiten een configuratie model van een schaalset, of dat er impliciet virtuele-machine-instanties zijn gemaakt op basis van het configuratie model. Kies de indelings modus die door het VM-indelings model expliciet gedefinieerde Virtual Machines samen in een regio of in een beschikbaarheids zone. Virtuele machines die zijn geïmplementeerd in een beschikbaarheids zone, bieden zonegebonden-isolatie voor Vm's aan de grenzen van de beschikbaarheids zone en worden niet onderhevig aan storingen die zich kunnen voordoen in een andere beschikbaarheids zone in de regio. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| VM-configuratie model| Geen. VirtualMachineProfile is niet gedefinieerd in het model voor schaal sets. | Vereist. VirtualMachineProfile wordt ingevuld in het model voor schaal sets. |
| Nieuwe VM toevoegen aan Schaalset| Vm's worden expliciet toegevoegd aan de schaalset wanneer de virtuele machine wordt gemaakt. | Vm's worden impliciet gemaakt en toegevoegd aan de schaalset op basis van het VM-configuratie model, het aantal instanties en de regels voor automatisch schalen. |
| Beschikbaarheidszones| Ondersteunt regionale implementaties of virtuele machines in één beschikbaarheids zone| Ondersteunt regionale implementatie of meerdere Beschikbaarheidszones; Kan de strategie voor zone verdeling definiëren |
| Foutdomeinen| Kan aantal fout domeinen definiëren. 2 of 3 op basis van regionale ondersteuning en 5 voor de beschikbaarheids zone. Het toegewezen VM-fout domein blijft in de levens cyclus van de VM, inclusief de toewijzing en opnieuw starten. | Kan 1, 2 of 3 fout domeinen definiëren voor niet-zonegebonden implementaties en 5 voor implementaties van de beschikbaarheids zone. Het toegewezen VM-fout domein blijft niet behouden met de VM-levens cyclus, aan virtuele machines wordt een fout domein toegewezen op het moment van de toewijzing. |
| Updatedomeinen| N.v.t. Update domeinen worden automatisch toegewezen aan fout domeinen| N.v.t. Update domeinen worden automatisch toegewezen aan fout domeinen |

**Fout domeinen en update domeinen**

Virtuele-machine schaal sets vereenvoudigen het ontwerpen voor hoge Beschik baarheid door het uitlijnen van fout domeinen en update domeinen. U hoeft alleen het aantal fout domeinen voor de schaalset te definiëren. Het aantal fout domeinen dat beschikbaar is voor de schaal sets kan per regio verschillen. Zie [aantal fout domeinen per regio](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Beschikbaarheidssets
Een beschikbaarheidsset is een logische groepering van Vm's binnen een Data Center waarmee Azure kan begrijpen hoe uw toepassing is gebouwd om te voorzien in redundantie en beschik baarheid. We raden u aan twee of meer virtuele machines te maken binnen een beschikbaarheidsset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten voor de Beschikbaarheidsset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt. Wanneer één virtuele machine gebruikmaakt van [Azure Premium ssd's](../articles/virtual-machines/windows/disks-types.md#premium-ssd), is de Azure-sla van toepassing op niet-geplande onderhouds gebeurtenissen.

In een beschikbaarheidsset worden Vm's automatisch gedistribueerd in deze fout domeinen. Deze aanpak beperkt de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen.

Voor virtuele machines die gebruikmaken van [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) en deel uitmaken van een beheerde beschikbaarheidsset, worden de virtuele machines afgestemd op Managed Disk-foutdomeinen. Deze afstemming zorgt ervoor dat alle beheerde schijven die zijn gekoppeld aan een virtuele machine, zich binnen hetzelfde Managed Disk-foutdomein bevinden. 

In een beheerde beschikbaarheidsset kunnen alleen virtuele machines met beheerde schijven worden gemaakt. Het aantal Managed Disk-foutdomeinen verschilt per regio: er zijn twee of drie Managed Disk-foutdomeinen per regio. Meer informatie over deze Managed Disk-fout domeinen voor [Linux-vm's](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) of [Windows-vm's](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)vindt u hier.

![Beheerde beschikbaarheidsset](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Vm's binnen een beschikbaarheidsset worden ook automatisch gedistribueerd in update domeinen. 

![Beschikbaarheidssets](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Volgende stappen
U kunt nu deze functies voor beschikbaarheid en redundantie gaan gebruiken om uw eigen Azure-omgeving te bouwen. Zie voor informatie over aanbevolen procedures de [aanbevolen procedures voor Azure-beschikbaarheid](/azure/architecture/checklist/resiliency-per-service).

