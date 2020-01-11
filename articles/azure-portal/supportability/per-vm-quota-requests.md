---
title: Een toename van vCPU-quotum limieten per Azure-VM-serie aanvragen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de verhoging van de quotum limiet per VM-vCPU aanvraagt.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897245"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standaard quotum: limieten verhogen per VM-serie

Azure Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines:
* Voor *vm's met betalen per gebruik* en *gereserveerde VM-instanties* gelden een *standaard vCPU-quotum*.
* Voor de *Spot-vm's* geldt een *vCPU-quotum*. 

Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde VM-instanties wordt afgedwongen op twee lagen voor elk abonnement in elke regio:
* De eerste laag is de *totale regionale vcpu's limiet* (in alle VM-reeksen).
* De tweede laag is de *limiet voor de serie vcpu's per VM* (zoals de vcpu's van de Dv3-serie). 

Wanneer u een nieuwe spot-VM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor die VM-reeks niet groter zijn dan het goedgekeurde vCPU-quotum voor die bepaalde VM-reeks. Daarnaast mag het totale aantal nieuwe en bestaande Vcpu's dat in alle VM-reeksen worden geïmplementeerd, niet groter zijn dan het totale goedgekeurde regionale vCPU-quotum voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.

U kunt een verhoging van de vCPU-quotum limiet voor de VM-serie aanvragen met behulp van de Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale vCPU limiet met hetzelfde bedrag. 

Zie vCPU voor [virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en Azure-abonnementen en- [service limieten](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)voor meer informatie over standaard-quota quota's. 

Zie voor meer informatie over het verhogen van de limiet voor vCPU per regio voor standaard quotum de [standaard quota: limieten per regio verhogen](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Zie [Spot quota: limieten voor alle VM-reeksen verhogen voor](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)meer informatie over het verhogen van de vCPU limieten voor de VM.

U kunt op twee manieren een verhoging van de standaard vCPU quotum limieten per VM-reeks aanvragen, zoals beschreven in de volgende secties.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Een standaard quotum toename aanvragen via het deel venster "Help + ondersteuning"

Ga als volgt te werk als u een standaard vCPU-quotum toename per VM-serie wilt aanvragen in het deel venster **Help + ondersteuning** : 

> [!NOTE]
> U kunt ook een toename van de quotum limiet voor meerdere regio's aanvragen via één ondersteunings aanvraag. Zie voor meer informatie stap 8.

1. Selecteer **Help en ondersteuning**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com).

   ![De koppeling Help + ondersteuning](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. Selecteer in het deel venster **Help + ondersteuning** de optie **nieuwe ondersteunings aanvraag**. 

    ![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Selecteer in de vervolg keuzelijst **probleem type** de optie **service-en abonnements limieten (quota's)** .

   ![De vervolg keuzelijst probleem type](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarvan u het quotum wilt verhogen.

   ![De vervolg keuzelijst abonnement](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Selecteer in de vervolg keuzelijst **quotum type** **Compute-VM (kernen-vcpu's) de limiet voor abonnementen**. 

   ![De vervolg keuzelijst quotum type](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Op het tabblad **Details** , onder Details van **probleem**, selecteert u **Details opgeven**en voert u aanvullende informatie in om uw aanvraag te verwerken.

   ![De koppeling Details opgeven](./media/resource-manager-core-quotas-request/provide-details.png)

1. Ga als volgt te werk in het deel venster **quotum Details** rechtsboven:

   ![Het deel venster quotum Details](./media/resource-manager-core-quotas-request/1-7.png)

   a. Selecteer in de vervolg keuzelijst **implementatie model** het juiste model.

   b. Selecteer een locatie in de vervolg keuzelijst **locaties** . Voor de geselecteerde locatie, onder **typen**, voert u in het vak **een type selecteren de optie** **standaard**in.

   ![Het deel venster quotum Details-quotum typen](./media/resource-manager-core-quotas-request/1-8.png)

   Onder **typen**kunt u zowel de standaard-als steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties.
   
   Zie [Azure spot-vm's voor virtuele-machine schaal sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)voor meer informatie over het verhogen van de limieten voor steun quota.

   c. Selecteer onder de vervolg keuzelijst **standaard** de SKU-serie waarvan u de quota's wilt verg Roten.

   ![Het deel venster quotum Details-SKU-reeks](./media/resource-manager-core-quotas-request/1-9.png)

   d. Geef de nieuwe quotum limieten op die u voor dit abonnement wilt. Als u een SKU uit de lijst wilt verwijderen, schakelt u het selectie vakje naast de SKU uit of selecteert u het pictogram **verwijderen** (X). 

   ![Het tekstvak ' nieuwe vCPU-limiet '](./media/resource-manager-core-quotas-request/1-10.png)

1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie in de vervolg keuzelijst en selecteert u vervolgens een geschikt VM-type. U kunt vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Aanvullende locaties in het deel venster quotum Details](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Een standaard quotum toename aanvragen via het deel venster abonnementen

Ga als volgt te werk als u een standaard vCPU-quotum toename per VM-reeks wilt aanvragen in het deel venster **abonnementen** :

> [!NOTE]
> U kunt ook een toename van de quotum limiet voor meerdere regio's aanvragen via één ondersteunings aanvraag. Zie stap 7 voor meer informatie.

1. Selecteer in het linkerdeel venster van de [Azure Portal](https://portal.azure.com) **abonnementen**.

   ![De koppeling abonnementen](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Het deel venster abonnementen](./media/resource-manager-core-quotas-request/select-subscription.png)

1. Selecteer in het linkerdeel venster van de naam van uw **\<-abonnement >** pagina de optie **gebruik en quota's**.

   ![De koppeling gebruik en quota's](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. Selecteer in de rechter bovenhoek de optie **aanvraag verg Roten**.

   ![Toename aanvragen](./media/resource-manager-core-quotas-request/request-increase.png)

1. Selecteer in de vervolg keuzelijst **quotum type** **Compute-VM (kernen-vcpu's) de limiet voor abonnementen**.

   ![De vervolg keuzelijst quotum type](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. Ga als volgt te werk in het deel venster **quotum Details** rechtsboven:

   ![Het deel venster quotum Details](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. Selecteer in de vervolg keuzelijst **implementatie model** het juiste model.

   b. Selecteer een locatie in de vervolg keuzelijst **locaties** . 
   
   c. Selecteer voor de geselecteerde locatie onder **typen**de optie **Selecteer een type**en schakel vervolgens het selectie vakje **standaard** in.

   ![Het selectie vakje standaard](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   Onder **typen**kunt u de quota typen standaard en lage prioriteit aanvragen bij één ondersteunings aanvraag via ondersteuning voor meerdere selecties.
   
   Zie [Azure spot-vm's voor virtuele-machine schaal sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)voor meer informatie over het verhogen van de limieten voor steun quota.

   d. Selecteer onder de vervolg keuzelijst **standaard** de SKU-serie waarvan u de quota's wilt verg Roten.

   ![Het deel venster quotum Details-SKU-reeks](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Geef de nieuwe quotum limieten op die u voor dit abonnement wilt. Als u een SKU uit de lijst wilt verwijderen, schakelt u het selectie vakje naast de SKU uit of selecteert u **verwijderen** (X). 

   ![Het tekstvak ' nieuwe vCPU-limiet '](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie in de vervolg keuzelijst en selecteert u vervolgens een geschikt VM-type. 

   Met deze stap wordt de SKU-serie die u hebt geselecteerd voor eerdere locaties vooraf geladen. Voer de quotum limieten in die u wilt Toep assen op de extra serie.
   
   ![Aanvullende locaties in het deel venster quotum Details](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.
