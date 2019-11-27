---
title: Toename aanvragen voor Azure-vCPU per VM-quota | Microsoft Docs
description: aanvragen voor vCPU per VM-quotum verhogen
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531753"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Standaard quotum: limiet voor vCPU per VM-serie

Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines. Voor de **vm's voor betalen per gebruik en gereserveerde VM-instanties** wordt standaard quotum gebruikt. **Virtuele machines met lage prioriteit** gebruiken een quotum met lage prioriteit. Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde VM-instanties wordt afgedwongen op twee lagen voor elk abonnement in elke regio

De eerste laag is de **totale regionale vcpu's limiet** (in alle VM-reeksen) en de tweede laag is de **limiet van de VM-reeks vcpu's** (zoals de Dv3-Series vcpu's). Telkens wanneer een nieuwe VM moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor die VM-reeks niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor die bepaalde VM-reeks. Verder mag het totale nieuwe en bestaande vCPU aantal dat is geïmplementeerd in alle VM-reeksen, niet groter zijn dan het totale regionale Vcpu's-quotum dat is goedgekeurd voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.
U kunt een toename van de Vcpu's-quotum limiet voor de VM-serie aanvragen van Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale Vcpu's limiet met hetzelfde bedrag. 

Meer informatie over Standard vCPU-quota's op de pagina [vCPU quota voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en [Azure-abonnement en service limieten](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Meer informatie over het verhogen van de regionale vCPU limiet voor [standaard quotum.](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests) 

Meer informatie over het verhogen van de **limieten voor VM-vCPUs met een lage prioriteit** [.](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)

U kunt een verhoging van de **standaard vCPU-quotum limieten per VM-serie** aanvragen via de Blade **Help en ondersteuning** of de Blade gebruiks gegevens en **quotum** in de portal.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Standaard vCPU-quotum verhoging per VM-reeks op abonnements niveau aanvragen via de Blade Help en ondersteuning

Volg de onderstaande instructies om een ondersteunings aanvraag te maken via de Blade Help en ondersteuning van Azure die beschikbaar zijn in de Azure Portal. 

U kunt ook een quotum aanvragen voor meerdere regio's via één ondersteunings aanvraag. Raadpleeg stap 11 hieronder voor meer informatie.

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

7. Selecteer in het deel venster **quotum Details** het **implementatie model** en selecteer een **locatie.**

   ![Quotum Details DM](./media/resource-manager-core-quotas-request/1-7.png)

8. Selecteer voor de geselecteerde locatie de optie **type** waarde als **standaard**. U kunt de quota typen standaard en lage prioriteit aanvragen bij één ondersteunings aanvraag via ondersteuning voor meerdere selecties in het veld **type** . Meer informatie over het **verg Roten van quotum limieten met lage prioriteit** op de **pagina < >** .

   ![SKU-familie](./media/resource-manager-core-quotas-request/1-8.png)

9. De **SKU-families** selecteren die moeten worden verhoogd

   ![SKU-familie](./media/resource-manager-core-quotas-request/1-9.png)

10. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de SKU uit in de vervolg keuzelijst SKU-familie of klikt u op het pictogram ' x ' negeren. 

   ![Nieuwe limieten](./media/resource-manager-core-quotas-request/1-10.png)

11. Als u een quotum wilt aanvragen voor meer dan één locatie, kunt u op een andere **locatie** controleren in de vervolg keuzelijst en selecteert u het juiste VM-type. Met deze stap worden SKU-families die zijn geselecteerd voor een eerdere **locatie** vooraf geladen met de nieuwe locatie. u hoeft alleen de nieuwe limieten in te voeren.

   ![Meerdere locaties](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Nadat u het gewenste quotum voor elke SKU-serie hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met de ondersteunings aanvraag maken.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Standaard vCPU-quotum verhoging per VM-reeks op abonnements niveau aanvragen met gebruik en de Blade quotum

Volg de onderstaande instructies voor het maken van een ondersteunings aanvraag via de Blade gebruik en quotum van Azure die beschikbaar is in de Azure Portal.

U kunt ook een **quotum aanvragen voor meerdere regio's** via één ondersteunings aanvraag. Raadpleeg stap 10 hieronder voor meer informatie

1. Selecteer in https://portal.azure.com**abonnementen**.

   ![Abonnementen](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

   ![Abonnement selecteren](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Gebruik en quota's** selecteren

   ![Gebruik en quota's selecteren](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Selecteer in de rechter bovenhoek **aanvraag verhoging**.

   ![Toename aanvragen](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecteer **Compute-VM (kernen-vcpu's) de limiet** voor het offerte type wordt verhoogd. 

   ![Formulier invullen](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Selecteer in het deel venster **quotum Details** het implementatie model en selecteer een locatie.

   ![Blade met quotum problemen](./media/resource-manager-core-quotas-request/1-1-6.png)

7. Selecteer voor de geselecteerde locatie de optie **type** waarde als **standaard**. U kunt de quota typen standaard en lage prioriteit aanvragen bij één ondersteunings aanvraag via ondersteuning voor meerdere selecties in het veld **type** . Meer informatie over het verhogen van de **vcpu's limieten met lage prioriteit** op deze [pagina](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

   ![SKU-serie geselecteerd](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. De **SKU-families** selecteren die moeten worden verhoogd

   ![SKU-serie geselecteerd](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de SKU uit in de vervolg keuzelijst SKU-familie of klikt u op het pictogram ' x ' negeren. 

   ![SKU nieuwe quotum aanvraag](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Als u een quotum wilt aanvragen voor meer dan één locatie, kunt u op een andere **locatie** controleren in de vervolg keuzelijst en selecteert u het juiste VM-type. Met deze stap worden SKU-families die zijn geselecteerd voor een eerdere **locatie** vooraf geladen met de nieuwe locatie. u hoeft alleen de nieuwe limieten in te voeren.
   
    ![SKU nieuwe quotum aanvraag](./media/resource-manager-core-quotas-request/1-1-10.png)
 
