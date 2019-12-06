---
title: Steun limiet | Microsoft Docs
description: Spot quotum aanvragen
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850564"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Spot quotum: toename beperken voor alle VM-reeksen

Spot Vm's bieden een ander model voor Azure-gebruik en verlaagt Azure een lagere kosten voor het verwijderen van een virtuele machine als nodig voor betalen per gebruik of gereserveerde implementaties van VM-exemplaren. Meer informatie over spot Vm's [vindt u hier](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines. Voor de **vm's voor betalen per gebruik en gereserveerde VM-instanties** wordt standaard quotum gebruikt. **Spot-vm's** gebruiken steun quota. 

Voor het type **Spot quotum** worden vCPU-quota van Resource Manager afgedwongen voor alle beschik bare VM-reeksen als één regionale limiet.

Telkens wanneer er een nieuwe spot-VM moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor alle exemplaren van de virtuele machine van de VM niet groter zijn dan de goedgekeurde plaatsings vCPU quotum limiet. Als het steun quotum wordt overschreden, is de implementatie van de locatie-VM niet toegestaan. U kunt een toename van de Vcpu's quotum limiet van Azure Portal aanvragen. 

Meer informatie over Standard vCPU-quota's op de pagina vCPU quota voor virtuele machines en Azure-abonnement en service limieten. Meer informatie over het verhogen van de regionale vCPU limiet voor standaard quotum op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

U kunt nu een toename van de **steun limieten voor alle VM-reeksen** aanvragen via de Blade **Help en ondersteuning** of de Blade **gebruik + quotum** in de portal.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Toename van de limiet voor het aantal van de VM-serie per abonnement verhogen met behulp van de Blade Help en ondersteuning

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

8. Selecteer voor de geselecteerde locatie waarde **type** als **' spot '** . U kunt de standaard-en steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties in het veld **type** . Meer informatie over het verhogen van het **standaard quotum per VM-serie** op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Details opgeven](./media/resource-manager-core-quotas-request/3-8.png)

9.  Voer de nieuwe limiet in voor het abonnement. 
 
 ![Details opgeven](./media/resource-manager-core-quotas-request/3-9.png)

10. Als u een quotum wilt aanvragen voor meer dan één locatie, kunt u op een andere locatie controleren in de vervolg keuzelijst en selecteert u het juiste VM-type. U kunt vervolgens de nieuwe limieten invoeren die u wilt.

![Details opgeven](./media/resource-manager-core-quotas-request/3-10.png)

11. Nadat u het gewenste quotum hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met het maken van de ondersteunings aanvraag.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Toename van de limiet voor het aantal van alle VM-reeksen per abonnement verhogen met behulp van de Blade gebruik + quotum

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
 
7.  Selecteer voor de geselecteerde locatie waarde **type** als **' spot '.** U kunt de standaard-en steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties in het veld **type** . Meer informatie over het verhogen van het **standaard quotum per VM-serie** op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Voer de nieuwe limiet in voor het abonnement.

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Als u een quotum wilt aanvragen voor meer dan één locatie, kunt u op een andere **locatie** controleren in de vervolg keuzelijst en selecteert u het juiste VM-type. U kunt vervolgens de nieuwe limieten invoeren die u wilt.

  ![Formulier invullen](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Nadat u het gewenste quotum hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met het maken van de ondersteunings aanvraag.


