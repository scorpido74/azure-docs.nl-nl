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
ms.openlocfilehash: 2bd9430fc6f48d72faa2c1850af0bb8432a7c5f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149500"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Veelgestelde vragen over Azure vertrouwelijk computing

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [werk belastingen voor vertrouwelijke computers op Azure](overview.md).

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en stack overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht plaatsen [ @AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een ondersteunings aanvraag voor Azure indienen. Als u een ondersteunings aanvraag wilt indienen, selecteert u op de [pagina ondersteuning voor Azure](https://azure.microsoft.com/support/options/)de optie ondersteuning ophalen.

## <a name="confidential-computing-virtual-machines"></a>Virtual Machines voor vertrouwelijke computing<a id="vm-faq"></a>

1. **Hoe kunt u beginnen met het implementeren van virtuele machines uit de DCsv2-serie?**

   Hier volgen enkele manieren waarop u een DCsv2-VM kunt implementeren:
   - Een [Azure Resource Manager-sjabloon](../virtual-machines/windows/template-description.md) gebruiken
   - Van de [Azure Portal](https://portal.azure.com/#create/hub)
   - In de sjabloon voor Marketplace-oplossing van [Azure vertrouwelijk Compute (Virtual Machine)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) . De oplossings sjabloon voor Marketplace helpt een klant te beperken tot de ondersteunde scenario's (regio's, installatie kopieën, Beschik baarheid, schijf versleuteling). 

1. **Werken alle besturingssysteem installatie kopieën met Azure vertrouwelijk computing?**

   Nee. De virtuele machines kunnen alleen worden geïmplementeerd op virtuele machines van de tweede generatie. We bieden ondersteuning voor generatie 2 voor Ubuntu Server 18,04, Ubuntu Server 16,04 en Windows Server 2016 Data Center. Meer informatie over virtuele machines van generatie 2 in [Linux](../virtual-machines/linux/generation-2.md) en [Windows](../virtual-machines/windows/generation-2.md)

1. **Virtuele DCsv2-machines zijn niet beschikbaar in de portal en ik kan er geen selecteren**

   Op basis van de informatie ballon naast de virtuele machine zijn er verschillende acties die u moet uitvoeren:
    -   **UnsupportedGeneration**: Wijzig de generatie van de installatie kopie van de virtuele machine in ' Gen2 '.
    -   **NotAvailableForSubscription** : de regio is nog niet beschikbaar voor uw abonnement. Selecteer een beschikbare regio.
    -   **InsufficientQuota**: [Maak een ondersteunings aanvraag om uw quotum te verg Roten](../azure-portal/supportability/per-vm-quota-requests.md). Abonnementen voor een gratis proef abonnement hebben geen quota voor het gebruik van de virtuele machines van het werk. 

1. **Virtuele DCsv2-machines worden niet weer gegeven wanneer ik deze probeer te zoeken in de portal formaat kiezer**

   Zorg ervoor dat u een beschik bare regio hebt geselecteerd. Zorg er ook voor dat u ' alle filters wissen ' selecteert in de optie grootte. 

1. **Wat is het verschil tussen Vm's uit de DCsv2-serie en DC-serie?**

   Vm's uit de DC-serie worden uitgevoerd op oudere 6-core Intel-processors met SGX. Deze hebben minder totaal geheugen, minder EPC-geheugen (enclave pagina container) en zijn beschikbaar in minder regio's. Deze Vm's zijn alleen beschikbaar in VS Oost en Europa-West zijn verkrijgbaar in twee grootten: Standard_DC2s en Standard_DC4s. Ze gaan niet verder en kunnen alleen worden geïmplementeerd in het Marketplace-exemplaar van de [vertrouwelijk Compute DC-serie [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

1. **Zijn virtuele DCsv2-machines wereld wijd beschikbaar?**

   Nee, deze virtuele machines zijn alleen beschikbaar in bepaalde regio's. Controleer de [pagina producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor de meest recente beschik bare regio's. 

1. **Hoe kan ik de open enclave-SDK installeren?**
   
   Volg de instructies in de [Open ENCLAVE SDK github](https://github.com/openenclave/openenclave)voor instructies over het installeren van de OE SDK op een computer of in azure of on-premises.
     
   U kunt ook de open enclave SDK GitHub voor het besturings systeem-specifieke installatie-instructies:
     - [De OE SDK installeren in Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [De OE SDK installeren op Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [De OE SDK installeren op Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
