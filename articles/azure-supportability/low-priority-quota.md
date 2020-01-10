---
title: Het quotum van de virtuele machine naar de locatie | Microsoft Docs
description: Verhoog de quotum limieten door punten te quotum aanvragen
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b087315e02a61886fa6f4ef083c75ed6b5b60cf9
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750319"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Spot quotum: limieten voor alle VM-reeksen verhogen

Spot virtuele machines (Vm's) bieden een ander model voor Azure-gebruik. Ze laten u uitgaan van lagere kosten in Exchange, zodat Azure virtuele machines kan verwijderen als nodig is voor betalen per gebruik of gereserveerde implementaties van VM-exemplaren. Zie [Azure spot vm's voor virtuele-machine schaal sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)voor meer informatie over spot vm's.

Azure Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines:
* Voor *vm's met betalen per gebruik* en *gereserveerde VM-instanties* gelden een *standaard vCPU-quotum*.
* Voor de *Spot-vm's* geldt een *vCPU-quotum*. 

Voor het *quota type spot vCPU* worden vCPU-quota van Resource Manager afgedwongen voor alle beschik bare VM-reeksen als één regionale limiet.

Wanneer u een nieuwe spot-VM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor alle exemplaren van de virtuele machine van de VM de goedgekeurde plaatsings vCPU-quotum limiet niet overschrijden. Als het steun quotum wordt overschreden, is de implementatie van de VM met steun niet toegestaan. 

In dit artikel wordt beschreven hoe u een toename van de vCPU-quotum limiet aanvraagt met behulp van de Azure Portal. 

Zie vCPU voor [virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en Azure-abonnementen en- [service limieten](https://aka.ms/quotalimits)voor meer informatie over standaard-quota quota's. 

Zie voor meer informatie over het verhogen van de limiet voor vCPU per regio [standaard quotum: regionale vCPU-limiet verhoging](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Een verhoging van de quotum limiet aanvragen van het deel venster "Help + ondersteuning" 

Ga als volgt te werk om een verhoging van de limiet voor het aantal toegestane quota voor alle VM-Series in het deel venster **Help en ondersteuning** op te vragen:

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

1. Selecteer het tabblad **Details** en klik onder Details van **probleem**op **Details opgeven**en voer aanvullende informatie in om uw aanvraag te verwerken.

   ![De koppeling Details opgeven](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. Ga als volgt te werk in het deel venster **quotum Details** rechtsboven:

   ![Het deel venster quotum Details](./media/resource-manager-core-quotas-request/3-7.png)

   a. Selecteer in de vervolg keuzelijst **implementatie model** het juiste model.

   b. Selecteer een locatie in de vervolg keuzelijst **locaties** . Voer voor de geselecteerde locatie onder **typen**in het vak **Selecteer een type** **plaats**in. 
   
   ![Het tabblad Details van de nieuwe ondersteunings aanvraag](./media/resource-manager-core-quotas-request/3-8.png)

    Onder **typen**kunt u zowel de standaard-als steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties. 
    
    Zie voor meer informatie [standaard quotum: de limiet voor het aantal VCPU per VM-serie wordt verhoogd](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Voer de nieuwe quotum limiet in die u voor dit abonnement wilt. 
 
   ![Het tekstvak ' nieuwe vCPU-limiet '](./media/resource-manager-core-quotas-request/3-9.png)

1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie in de vervolg keuzelijst en selecteert u vervolgens een geschikt VM-type. U kunt vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Aanvullende locaties in het deel venster quotum Details](./media/resource-manager-core-quotas-request/3-10.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Een verhoging van de quotum limiet aanvragen van het deel venster abonnementen

Ga als volgt te werk om een verhoging van de limiet voor het aantal vm's voor alle VM-reeksen in het deel venster **abonnementen** aan te vragen:

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

   ![Het deel venster quotum Details](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. Selecteer in de vervolg keuzelijst **implementatie model** het juiste model.

   b. Selecteer een locatie in de vervolg keuzelijst **locaties** . 
   
   c. Voer voor de geselecteerde locatie onder **typen**in het vak **Selecteer een type** **plaats**in.

   ![Het deel venster quotum Details](./media/resource-manager-core-quotas-request/3-2-7.png)

   Zie voor meer informatie [standaard quotum: de limiet voor het aantal VCPU per VM-serie wordt verhoogd](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Voer de nieuwe quotum limiet in die u voor dit abonnement wilt.

   ![Het tekstvak ' nieuwe vCPU-limiet '](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie in de vervolg keuzelijst en selecteert u vervolgens een geschikt VM-type. U kunt vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Aanvullende locaties in het deel venster quotum Details](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.


