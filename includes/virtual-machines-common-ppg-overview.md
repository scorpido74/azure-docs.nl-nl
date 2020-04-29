---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422022"
---
Het plaatsen van Vm's in één regio vermindert de fysieke afstand tussen de exemplaren. Als u ze in één beschikbaarheids zone plaatst, worden deze ook fysiek dichter bij elkaar gebracht. Naarmate de Azure-footprint groeit, kan één beschikbaarheids zone echter meerdere fysieke data centers omvatten, wat kan leiden tot een netwerk latentie die van invloed is op uw toepassing. 

Als u virtuele machines zo dicht mogelijk wilt ophalen, moet u deze implementeren in een proximity-plaatsings groep.

Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Proximity-plaatsings groepen zijn handig voor werk belastingen waarbij lage latentie een vereiste is.


- Lage latentie tussen zelfstandige Vm's.
- Lage latentie tussen virtuele machines in één beschikbaarheidsset of een schaalset voor virtuele machines. 
- Lage latentie tussen zelfstandige Vm's, Vm's in meerdere beschikbaarheids sets of meerdere schaal sets. U kunt meerdere reken bronnen in één plaatsings groep hebben om een toepassing met meerdere lagen samen te brengen. 
- Lage latentie tussen meerdere toepassings lagen met verschillende typen hardware. U kunt bijvoorbeeld de back-end uitvoeren met de M-serie in een beschikbaarheidsset en de front-end op een exemplaar van de D-serie, in een schaalset, in één proximity-plaatsings groep.


![Afbeelding voor proximity-plaatsings groepen](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Proximity-plaatsings groepen gebruiken 

Een proximity-plaatsings groep is een nieuw resource type in Azure. U moet er een maken voordat u deze met andere resources kunt gebruiken. Zodra de app is gemaakt, kan deze worden gebruikt met virtuele machines, beschikbaarheids sets of virtuele-machine schaal sets. U geeft een proximity-plaatsings groep op bij het maken van reken resources die de Proximity-groeps-ID opgeven. 

U kunt een bestaande resource ook verplaatsen naar een plaatsings groep voor nabijheid. Wanneer u een resource naar een plaatsings groep verplaatst, moet u de activa eerst stoppen (toewijzing ongedaan maken), omdat deze mogelijk opnieuw wordt geïmplementeerd in een ander Data Center in de regio. 

In het geval van beschikbaarheids sets en virtuele-machine schaal sets moet u de Proximity-plaatsings groep instellen op het resource niveau in plaats van de afzonderlijke virtuele machines. 

Een proximity-plaatsings groep is een beperking voor de co-locatie in plaats van een vastmakend mechanisme. Het wordt vastgemaakt aan een specifiek Data Center met de implementatie van de eerste resource om het te gebruiken. Wanneer alle resources die gebruikmaken van de Proximity-plaatsings groep zijn gestopt (toewijzing opgeheven) of verwijderd, wordt deze niet langer vastgemaakt. Daarom is het belang rijk dat u bij het gebruik van een proximity-plaatsings groep met meerdere VM-reeksen alle vereiste typen vooraf in een sjabloon opgeeft, of u kunt een implementatie volgorde volgen waarmee u de kans op een geslaagde implementatie kunt verbeteren. Als uw implementatie mislukt, start u de implementatie opnieuw met de VM-grootte die is mislukt als de eerste grootte die moet worden geïmplementeerd.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Wat u kunt verwachten wanneer u proximity-plaatsings groepen gebruikt 
Proximity-plaatsings groepen bieden een co-locatie in hetzelfde Data Center. Omdat proximity-plaatsings groepen echter een extra implementatie beperking vertegenwoordigen, kunnen toewijzings fouten optreden. Er zijn een paar situaties waarin mogelijk toewijzings fouten worden weer geven wanneer proximity-plaatsings groepen worden gebruikt:

- Wanneer u voor de eerste virtuele machine in de plaatsings groep voor nabijheid vraagt, wordt het Data Center automatisch geselecteerd. In sommige gevallen kan een tweede aanvraag voor een andere SKU van de virtuele machine mislukken als deze niet bestaat in dat data centrum. In dit geval wordt een **OverconstrainedAllocationRequest** -fout geretourneerd. U kunt dit voor komen door de volg orde te wijzigen waarin u uw Sku's implementeert of als u beide resources hebt geïmplementeerd met één ARM-sjabloon.
-   In het geval van elastische workloads, waarbij u VM-instanties toevoegt en verwijdert en een proximity-beperking voor de plaatsings groep in uw implementatie kan ertoe leiden dat de aanvraag niet kan voldoen aan de **AllocationFailure** -fout. 
- Het is een andere manier om de virtuele machine te onderbreken (de toewijzing ongedaan te maken) en te starten als dat nodig is. Omdat de capaciteit niet wordt bewaard nadat u een virtuele machine hebt gestopt (de toewijzing ongedaan te maken), kan dit een **AllocationFailure** -fout veroorzaken.


## <a name="best-practices"></a>Aanbevolen procedures 
- Voor de laagste latentie gebruikt u proximity-plaatsings groepen samen met versneld netwerken. Zie [een virtuele Linux-machine maken met versneld netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [een virtuele Windows-machine maken met versneld netwerken](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.
- Implementeer alle VM-grootten in één sjabloon. Als u wilt voor komen dat er op hardware wordt gelandd die geen ondersteuning biedt voor alle VM-Sku's en-groottes die u nodig hebt, moet u alle toepassings lagen in één sjabloon toevoegen, zodat ze allemaal tegelijk worden geïmplementeerd.
- Als u uw implementatie met Power shell, CLI of de SDK bijwerkt, wordt er mogelijk een `OverconstrainedAllocationRequest`toewijzings fout weer geven. In dit geval moet u de toewijzing van alle bestaande Vm's stoppen of ongedaan maken en de volg orde wijzigen in het implementatie script om te beginnen met de VM-SKU/-grootten die zijn mislukt. 
- Wanneer u een bestaande plaatsings groep hergebruikt van waaruit Vm's zijn verwijderd, wacht u totdat de verwijdering volledig is voltooid voordat u virtuele machines toevoegt.
- Als latentie uw eerste prioriteit is, plaatst u Vm's in een proximity-plaatsings groep en de volledige oplossing in een beschikbaarheids zone. Maar als tolerantie uw hoogste prioriteit is, kunt u uw instanties verspreiden over meerdere beschikbaarheids zones (een enkele proximity-plaatsings groep kan geen zones omvatten).
