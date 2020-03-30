---
title: Problemen oplossen van toewijzingsfout voor cloudservices | Microsoft Documenten
description: Toewijzingsfouten oplossen die zijn opgetreden bij het implementeren van Cloud Services in Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247538"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Toewijzingsfouten oplossen die zijn opgetreden bij het implementeren van Cloud Services in Azure
## <a name="summary"></a>Samenvatting
Wanneer u instanties implementeert voor een Cloud Service of nieuwe web- of werkrolexemplaren toevoegt, wijst Microsoft Azure rekenbronnen toe. U af en toe fouten ontvangen bij het uitvoeren van deze bewerkingen, zelfs voordat u de azure-abonnementslimieten bereikt. In dit artikel worden de oorzaken van enkele van de gemeenschappelijke toewijzingsfouten uitgelegd en wordt mogelijke herstel voorgesteld. De informatie kan ook nuttig zijn wanneer u de implementatie van uw services plant.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Achtergrond – Hoe toewijzing werkt
De servers in Azure-datacenters worden in clusters gepartitioneerd. Een nieuwe aanvraag voor toewijzing van cloudservices wordt in meerdere clusters uitgevoerd. Wanneer de eerste instantie wordt geïmplementeerd op een cloudservice (in fasering of productie), wordt die cloudservice vastgemaakt aan een cluster. Eventuele verdere implementaties voor de cloudservice vinden plaats in hetzelfde cluster. In dit artikel verwijzen we naar dit als "vastgemaakt aan een cluster". Onderstaanddiagram 1 illustreert het geval van een normale toewijzing die in meerdere clusters wordt geprobeerd; Diagram 2 illustreert het geval van een toewijzing die is vastgemaakt aan cluster 2, omdat daar de bestaande Cloud Service CS_1 wordt gehost.

![Toewijzingsdiagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Waarom toewijzingsfout optreedt
Wanneer een toewijzingsaanvraag is vastgemaakt aan een cluster, is de kans groter dat er geen vrije resources worden gevonden, omdat de beschikbare resourcegroep is beperkt tot een cluster. Als uw toewijzingsaanvraag is vastgemaakt aan een cluster, maar het type resource dat u hebt aangevraagd, wordt deze niet ondersteund door dat cluster, mislukt uw aanvraag zelfs als het cluster een gratis bron heeft. Diagram 3 hieronder illustreert het geval waarin een vastgemaakte toewijzing mislukt omdat het enige kandidaatcluster geen vrije resources heeft. Diagram 4 illustreert het geval waarin een vastgemaakte toewijzing mislukt omdat het enige kandidaatcluster de gevraagde VM-grootte niet ondersteunt, ook al heeft het cluster gratis resources.

![Vastgemaakte toewijzingsfout](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Fout oplossen van toewijzing voor cloudservices
### <a name="error-message"></a>Foutbericht
Mogelijk ziet u het volgende foutbericht:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Algemene problemen
Hier volgen de algemene toewijzingsscenario's die ervoor zorgen dat een toewijzingsaanvraag wordt vastgemaakt aan één cluster.

* Implementatie naar staging slot - Als een cloudservice een implementatie heeft in een van beide sleufplaatsen, wordt de hele cloudservice vastgemaakt aan een specifiek cluster.  Dit betekent dat als een implementatie al in de productiesite bestaat, dan kan een nieuwe faseringsimplementatie alleen worden toegewezen in hetzelfde cluster als de productiesite. Als de capaciteit van het cluster bijna is, kan de aanvraag mislukken.
* Schalen: wanneer nieuwe exemplaren aan een bestaande cloudservice worden toegevoegd, moet de toewijzing in hetzelfde cluster gebeuren.  Kleine schaalaanvragen kunnen doorgaans worden toegewezen, maar niet altijd. Als de capaciteit van het cluster bijna is, kan de aanvraag mislukken.
* Affiniteitsgroep - Een nieuwe implementatie voor een lege cloudservice kan worden toegewezen door de fabric in een cluster in dat gebied, tenzij de cloudservice is vastgemaakt aan een affiniteitsgroep. Implementaties naar dezelfde affiniteitsgroep worden geprobeerd op hetzelfde cluster. Als de capaciteit van het cluster bijna is, kan de aanvraag mislukken.
* Affinity Group vNet - Oudere virtuele netwerken waren gekoppeld aan affiniteitsgroepen in plaats van regio's en cloudservices in deze virtuele netwerken zouden worden vastgemaakt aan het cluster van de affiniteitsgroep. Implementaties van dit type virtueel netwerk worden geprobeerd op het vastgemaakte cluster. Als de capaciteit van het cluster bijna is, kan de aanvraag mislukken.

## <a name="solutions"></a>Oplossingen
1. Redeploy naar een nieuwe cloudservice - Deze oplossing is waarschijnlijk het meest succesvol omdat het platform hiermee kan kiezen uit alle clusters in die regio.

   * De werkbelasting implementeren voor een nieuwe cloudservice  
   * De CNAME- of A-record bijwerken om verkeer naar de nieuwe cloudservice te leiden
   * Zodra er geen verkeer naar de oude site gaat, u de oude cloudservice verwijderen. Deze oplossing moet leiden tot nul downtime.
2. Verwijder zowel productie- als faseringsruimten - Deze oplossing behoudt uw bestaande DNS-naam, maar veroorzaakt downtime voor uw toepassing.

   * Verwijder de productie- en faseringsslots van een bestaande cloudservice, zodat de cloudservice leeg is en
   * Maak een nieuwe implementatie in de bestaande cloudservice. Dit zal opnieuw proberen om toewijzing op alle clusters in de regio. Zorg ervoor dat de cloudservice niet is gekoppeld aan een affiniteitsgroep.
3. Gereserveerde IP - Deze oplossing behoudt uw bestaande IP-adres, maar zorgt voor downtime voor uw toepassing.  

   * Een reservedIP maken voor uw bestaande implementatie met Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Volg #2 van bovenaf en geef de nieuwe ReservedIP op in het CSCFG van de service.
4. Affiniteitsgroep verwijderen voor nieuwe implementaties - Affiniteitsgroepen worden niet langer aanbevolen. Volg de stappen voor 1 hierboven om een nieuwe cloudservice te implementeren. Zorg ervoor dat cloudservice zich niet in een affiniteitsgroep bevindt.
5. Converteren naar een regionaal virtueel netwerk - Zie [hoe u migreert van affiniteitsgroepen naar een regionaal virtueel netwerk (VNet).](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)
