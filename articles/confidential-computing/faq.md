---
title: Veelgestelde vragen over Azure voor vertrouwelijke computing
description: Antwoorden op veelgestelde vragen over Azure vertrouwelijk computing.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: bb821d00a168e3b8f0636b93696376dc8b5d492e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83772895"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Veelgestelde vragen over Azure vertrouwelijk computing

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [werk belastingen voor vertrouwelijke computers op Azure](overview.md).

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en stack overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht plaatsen [ @AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een ondersteunings aanvraag voor Azure indienen. Als u een ondersteunings aanvraag wilt indienen, selecteert u op de [pagina ondersteuning voor Azure](https://azure.microsoft.com/support/options/)de optie ondersteuning ophalen.

## <a name="confidential-computing-virtual-machines"></a>Virtual Machines voor vertrouwelijke computing<a id="vm-faq"></a>

**Hoe kan ik virtuele machines uit de DCsv2-serie implementeren op Azure?**

Hier volgen enkele manieren waarop u een DCsv2-VM kunt implementeren:
   - Een [Azure Resource Manager-sjabloon](../virtual-machines/windows/template-description.md) gebruiken
   - Van de [Azure Portal](https://portal.azure.com/#create/hub)
   - In de sjabloon [Azure vertrouwelijk Computing (Virtual Machine)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace-oplossing. De oplossings sjabloon voor Marketplace helpt een klant te beperken tot de ondersteunde scenario's (regio's, installatie kopieën, Beschik baarheid, schijf versleuteling). 

**Werken alle besturingssysteem installatie kopieën met Azure vertrouwelijk computing?**

Nee. De virtuele machines kunnen alleen worden geïmplementeerd op machines van de tweede generatie met Ubuntu Server 18,04, Ubuntu Server 16,04, Windows Server 2019 Data Center en Windows Server 2016 Data Center. Meer informatie over virtuele machines van generatie 2 in [Linux](../virtual-machines/linux/generation-2.md) en [Windows](../virtual-machines/windows/generation-2.md)

**Virtuele DCsv2-machines zijn niet beschikbaar in de portal en ik kan er geen selecteren**

Op basis van de informatie ballon naast de virtuele machine zijn er verschillende acties die u moet uitvoeren:
   -    **UnsupportedGeneration**: Wijzig de generatie van de installatie kopie van de virtuele machine in ' Gen2 '.
   -    **NotAvailableForSubscription**: de regio is nog niet beschikbaar voor uw abonnement. Selecteer een beschikbare regio.
   -    **InsufficientQuota**: [Maak een ondersteunings aanvraag om uw quotum te verg Roten](../azure-portal/supportability/per-vm-quota-requests.md). Abonnementen voor een gratis proef abonnement hebben geen quota voor het gebruik van de virtuele machines van het werk. 

**Virtuele DCsv2-machines worden niet weer gegeven wanneer ik deze probeer te zoeken in de portal formaat kiezer**

Zorg ervoor dat u een [beschik bare regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)hebt geselecteerd. Zorg er ook voor dat u ' alle filters wissen ' selecteert in de optie grootte. 

**Er wordt een Azure Resource Manager fout opgetreden bij het implementeren van een sjabloon: ' de bewerking kan niet worden voltooid omdat deze resulteert in een quotum van meer dan goedgekeurd Standard DcsV2-kernen**

[Maak een ondersteunings aanvraag om uw quotum te verg Roten](../azure-portal/supportability/per-vm-quota-requests.md). Abonnementen voor een gratis proef abonnement hebben geen quota voor het gebruik van de virtuele machines van het werk. 

**Wat is het verschil tussen Vm's uit de DCsv2-serie en DC-serie?**

Vm's uit de DC-serie worden uitgevoerd op oudere 6-core Intel-processors met Intel SGX en hebben minder geheugen, minder enclave-pagina cache geheugen (EPC) en zijn alleen beschikbaar in twee regio's (VS Oost en Europa-West in Standard_DC2s en Standard_DC4s grootten). Er zijn geen plannen om deze Vm's algemeen beschikbaar te maken en ze worden niet aanbevolen voor productie gebruik. Als u deze Vm's wilt implementeren, gebruikt u het Marketplace-exemplaar van de [vertrouwelijk Compute DC-serie [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) .

**Zijn virtuele DCsv2-machines wereld wijd beschikbaar?**

Nee. Op dit moment zijn deze virtuele machines alleen beschikbaar in bepaalde regio's. Controleer de [pagina producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor de meest recente beschik bare regio's. 

**Hoe kan ik installeert u de open enclave-SDK op virtuele machines van DCsv2?**
   
Volg de instructies in de [Open ENCLAVE SDK github](https://github.com/openenclave/openenclave)voor instructies over het installeren van de OE SDK op een Azure-of on-premises computer.
     
U kunt ook zoeken in de open enclave SDK GitHub voor OS-specifieke installatie-instructies:
   - [De OE SDK installeren in Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [De OE SDK installeren op Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [De OE SDK installeren op Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
