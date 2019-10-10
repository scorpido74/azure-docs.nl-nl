---
title: Toename aanvragen voor Azure-vCPU per VM-quota | Microsoft Docs
description: aanvragen voor vCPU per VM-quotum verhogen
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e99bdd92d4a1aab833a95943b22aaabe7f0daa1d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248667"
---
# <a name="vm-series-vcpu-limit-increase"></a>De vCPU-limiet voor een VM-reeks verhogen

Resource Manager-vCPU quota voor virtuele machines en virtuele-machine schaal sets worden afgedwongen op twee lagen voor elk abonnement, in elke regio. 

De eerste laag is de **totale regionale vcpu's limiet** (in alle VM-reeksen) en de tweede laag is de **limiet van de VM-reeks vcpu's** (zoals de vcpu's van de D-serie). Telkens wanneer een nieuwe VM moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor die VM-reeks niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor die bepaalde VM-reeks. Verder mag het totale nieuwe en bestaande vCPU aantal dat is geïmplementeerd in alle VM-reeksen, niet groter zijn dan het totale regionale Vcpu's-quotum dat is goedgekeurd voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.
U kunt een toename van de Vcpu's-quotum limiet voor de VM-serie aanvragen van Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale Vcpu's limiet met hetzelfde bedrag. 

Meer informatie over quota's vindt u op de pagina [vCPU quota van de virtuele machine](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en de pagina [Azure-abonnement en service limieten](https://aka.ms/quotalimits) . 

U kunt nu een toename aanvragen via de Blade **Help en ondersteuning** of de Blade **gebruik + quotum** in de portal. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Aanvraag per VM-serie vCPU quotum verhoging op abonnements niveau met behulp van de Blade **Help en ondersteuning**

Volg de onderstaande instructies om een ondersteunings aanvraag te maken via de Blade Help en ondersteuning van Azure die beschikbaar zijn in de Azure Portal. 

1. Selecteer in https://portal.azure.com **Help + ondersteuning**.

   ![Help en ondersteuning](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecteer **Nieuwe ondersteuningsaanvraag**. 

     ![Nieuw ondersteuningsverzoek](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Kies in de vervolg keuzelijst probleem type de optie **service-en abonnements limieten (quota's)** .

   ![Vervolg keuzelijst probleem type](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

   ![Abonnement-Nieuwsset selecteren](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecteer **Compute-VM (cores-vcpu's) de limiet voor het aantal verg Roten in het** **quotum type** . 

   ![Quotum type selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Geef bij Details van het **probleem**aanvullende informatie op voor het verwerken van uw aanvraag door te klikken op **Details opgeven**.

   ![Details opgeven](./media/resource-manager-core-quotas-request/provide-details.png)

7. Selecteer in het deel venster **quotum Details** het implementatie model en selecteer een locatie.

   ![Quotum Details DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Selecteer de **SKU-families** die moeten worden verhoogd. 

   ![SKU-familie](./media/resource-manager-core-quotas-request/sku-family.png)

9. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de SKU uit in de vervolg keuzelijst SKU-familie of klikt u op het pictogram ' x ' negeren. Nadat u het gewenste quotum voor elke SKU-serie hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met de ondersteunings aanvraag maken.

   ![Nieuwe limieten](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Aanvraag per VM-serie vCPU quotum verhoging op abonnements niveau met **gebruik +** Blade quotum

Volg de onderstaande instructies voor het maken van een ondersteunings aanvraag via de Blade gebruik en quotum van Azure die beschikbaar is in de Azure Portal. 

1. Selecteer in https://portal.azure.com **abonnementen**.

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

   ![Blade met quotum problemen](./media/resource-manager-core-quotas-request/quota-details.png)

7. Selecteer de **SKU-families** die moeten worden verhoogd.

   ![SKU-serie geselecteerd](./media/resource-manager-core-quotas-request/sku-family.png)

8. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de SKU uit in de vervolg keuzelijst SKU-familie of klikt u op het pictogram ' x ' negeren. Nadat u het gewenste quotum voor elke SKU-familie hebt ingevoerd, klikt u op **opslaan en door gaan** op de pagina probleem stap om door te gaan met het maken van de ondersteunings aanvraag.

   ![SKU nieuwe quotum aanvraag](./media/resource-manager-core-quotas-request/new-limits.png)
 
