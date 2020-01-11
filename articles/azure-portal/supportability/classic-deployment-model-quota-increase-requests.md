---
title: Klassieke Azure-implementatie model | Microsoft Docs
description: Klassieke Azure-implementatie model
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: d8f4bf04251347c44ea1692cfdda2602e23117f4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897050"
---
# <a name="classic-deployment-model"></a>Klassiek implementatiemodel

Het klassieke implementatie model is het Azure-implementatie model van de oudere generatie. Hiermee wordt een globale vCPU-quotum limiet afgedwongen voor virtuele machines en virtuele-machine schaal sets. Het klassieke implementatie model wordt niet meer aanbevolen en wordt nu vervangen door het Resource Manager-model. 

Raadpleeg de pagina [Resource Manager en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md) voor meer informatie over deze twee implementatie modellen en de voor delen van het gebruik van Resource Manager.
 
Wanneer een nieuw abonnement wordt gemaakt, wordt er een standaard quotum van Vcpu's aan toegewezen. Wanneer een nieuwe VM moet worden geïmplementeerd met behulp van het klassieke implementatie model, mag de som van het nieuwe en bestaande vCPU-gebruik in alle regio's niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor het klassieke implementatie model.
 
Meer informatie over quota's op de pagina [Azure-abonnement en service limieten](https://aka.ms/quotalimits) .

U kunt een verhoging van de vCPU-quotum limiet voor het klassieke implementatie model aanvragen via de Blade Help en ondersteuning of de Blade gebruik + quotum in de portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Aanvraag per VM-serie vCPU quotum verhoging op abonnements niveau met behulp van de Blade **Help en ondersteuning**

Volg de onderstaande instructies om een ondersteunings aanvraag te maken via de Blade Help en ondersteuning van Azure die beschikbaar zijn in de Azure Portal. 

1. Selecteer in https://portal.azure.com**Help + ondersteuning**.

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

7. Selecteer in het deel venster **quotum Details** de optie klassiek en selecteer een locatie.

   ![Quotum Details DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selecteer de **SKU-families** die moeten worden verhoogd. 

   ![SKU-familie](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de SKU uit in de vervolg keuzelijst SKU-familie of klikt u op het pictogram ' x ' negeren. Nadat u het gewenste quotum voor elke SKU-serie hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met de ondersteunings aanvraag maken.

   ![Nieuwe limieten](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Aanvraag per VM-serie vCPU quotum verhoging op abonnements niveau met **gebruik +** Blade quotum

Volg de onderstaande instructies voor het maken van een ondersteunings aanvraag via de Blade gebruik en quotum van Azure die beschikbaar is in de Azure Portal. 

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
   
6. Geef bij Details van het **probleem**aanvullende informatie op voor het verwerken van uw aanvraag door te klikken op **Details opgeven**.

   ![Details opgeven](./media/resource-manager-core-quotas-request/provide-details.png)

7. Selecteer in het deel venster **quotum Details** de optie klassiek en selecteer een locatie.

   ![Quotum Details DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selecteer de **SKU-families** die moeten worden verhoogd. 

   ![SKU-familie](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, schakelt u de SKU uit in de vervolg keuzelijst SKU-familie of klikt u op het pictogram ' x ' negeren. Nadat u het gewenste quotum voor elke SKU-serie hebt ingevoerd, klikt u op **opslaan en door gaan** in het paneel quotum Details om door te gaan met de ondersteunings aanvraag maken.

   ![Nieuwe limieten](./media/resource-manager-core-quotas-request/new-limits-classic.png)

