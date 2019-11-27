---
title: Quota met lage prioriteit | Microsoft Docs
description: Quota aanvragen met lage prioriteit
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 49ac478330cf73dff050a3edcc15933692fa6448
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535169"
---
# <a name="low-priority-quota-limit-increase-for-all-vm-series"></a>Quota met lage prioriteit: toename beperken voor alle VM-reeksen

Virtuele machines met lage prioriteit bieden een afwijkend model van Azure-gebruik en verlaagt Azure een lagere kosten voor het verwijderen van een virtuele machine als nodig voor betalen per gebruik of gereserveerde implementaties van VM-exemplaren. Lees [hier](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority)meer over vm's met een lage prioriteit.

Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines. Voor de **vm's voor betalen per gebruik en gereserveerde VM-instanties** wordt standaard quotum gebruikt. **Virtuele machines met lage prioriteit** gebruiken een quotum met lage prioriteit. 

Voor het **quotum type lage prioriteit** worden vCPU-quota van Resource Manager afgedwongen voor alle beschik bare VM-reeksen als één regionale limiet.

Wanneer er een nieuwe VM met lage prioriteit moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor alle VM-exemplaren met een lage prioriteit niet groter zijn dan de goedgekeurde vCPU quotum limiet voor lage prioriteit. Als het quotum met lage prioriteit wordt overschreden, is de VM-implementatie met lage prioriteit niet toegestaan. U kunt een toename van de Vcpu's-quotum limiet van lage prioriteit aanvragen bij Azure Portal. 

Meer informatie over Standard vCPU-quota's op de pagina vCPU quota voor virtuele machines en Azure-abonnement en service limieten. Meer informatie over het verhogen van de regionale vCPU limiet voor standaard quotum op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

U kunt nu een toename van de **quotum limieten met lage prioriteit voor alle VM-reeksen** aanvragen via de Blade **Help en ondersteuning** of de Blade **gebruik + quotum** in de portal.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Toename van quotum limiet voor lage prioriteit aanvragen voor alle VM-reeksen per abonnement met behulp van de Blade Help en ondersteuning

Volg de onderstaande instructies om een ondersteunings aanvraag te maken via de Blade Help en ondersteuning van Azure die beschikbaar zijn in de Azure Portal.

U kunt ook een **quotum aanvragen voor meerdere regio's** via één ondersteunings aanvraag. Raadpleeg stap 10 hieronder voor meer informatie. 


1. Selecteer in https://portal.azure.com**Help + ondersteuning**.

   ![Help en ondersteuning](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecteer **Nieuwe ondersteuningsaanvraag**. 

     ![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Kies in de vervolg keuzelijst probleem type de optie **service-en abonnements limieten (quota's)** .

   ![Vervolg keuzelijst probleem type](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

   ![Abonnement-Nieuwsset selecteren](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecteer **Compute-VM (kernen-vcpu's) de limiet voor het abonnement** voor het **quotum type** in vervolg keuzelijst. 

   ![Quotum type selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Geef bij Details van het **probleem**aanvullende informatie op voor het verwerken van uw aanvraag door te klikken op **Details opgeven**.

   ![Details opgeven](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  In het deel venster **quotum Details*** selecteert u **implementatie model** en selecteert u een **locatie**.

![Details opgeven](./media/resource-manager-core-quotas-request/3-7.png)

8. Selecteer voor de geselecteerde locatie **type** waarde als **' lage prioriteit '** . U kunt de quota typen standaard en lage prioriteit aanvragen bij één ondersteunings aanvraag via ondersteuning voor meerdere selecties in het veld **type** . Meer informatie over het verhogen van het **standaard quotum per VM-serie** op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Details opgeven](./media/resource-manager-core-quotas-request/3-8.png)

9.  Voer de nieuwe limiet in voor het abonnement. 
 
 ![Details opgeven](./media/resource-manager-core-quotas-request/3-9.png)

10. Als u een quotum wilt aanvragen voor meer dan één locatie, kunt u op een andere locatie controleren in de vervolg keuzelijst en selecteert u het juiste VM-type. U kunt vervolgens de nieuwe limieten invoeren die u wilt.

![Details opgeven](./media/resource-manager-core-quotas-request/3-10.png)

11. Nadat u het gewenste quotum hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met het maken van de ondersteunings aanvraag.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Toename van quotum limiet voor lage prioriteit aanvragen voor alle VM-reeksen per abonnement met gebruik en de Blade quotum

Volg de onderstaande instructies voor het maken van een ondersteunings aanvraag via de Blade gebruik en quotum van Azure die beschikbaar is in de Azure Portal.

U kunt ook een **quotum aanvragen voor meerdere regio's** via één ondersteunings aanvraag. Raadpleeg stap 9 hieronder voor meer informatie. 

1.  Selecteer in https://portal.azure.com**abonnementen**.

 ![Abonnementen](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

 ![Abonnement selecteren](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Selecteer **gebruik en quota's**.

 ![Gebruik en quota's selecteren](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  Selecteer in de rechter bovenhoek **aanvraag verhoging.**

   ![Toename aanvragen](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Selecteer **Compute-VM (kernen-vcpu's) de limiet** voor het offerte type wordt verhoogd.

  ![Formulier invullen](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  Selecteer in het deel venster **quotum Details** het implementatie model en selecteer een locatie.

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Selecteer voor de geselecteerde locatie **type** waarde als **' lage prioriteit '.** U kunt de quota typen standaard en lage prioriteit aanvragen bij één ondersteunings aanvraag via ondersteuning voor meerdere selecties in het veld **type** . Meer informatie over het verhogen van het **standaard quotum per VM-serie** op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Voer de nieuwe limiet in voor het abonnement.

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Als u een quotum wilt aanvragen voor meer dan één locatie, kunt u op een andere **locatie** controleren in de vervolg keuzelijst en selecteert u het juiste VM-type. U kunt vervolgens de nieuwe limieten invoeren die u wilt.

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Nadat u het gewenste quotum hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met het maken van de ondersteunings aanvraag.

