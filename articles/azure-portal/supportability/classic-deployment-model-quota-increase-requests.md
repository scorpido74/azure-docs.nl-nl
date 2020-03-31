---
title: Azure-klassiek implementatiemodel
description: Het klassieke implementatiemodel, dat nu wordt vervangen door het Resource Manager-model, dwingt een wereldwijde vCPU-quotumlimiet af voor VM's en virtuele machineschaalsets.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835572"
---
# <a name="classic-deployment-model"></a>Klassiek implementatiemodel

Het klassieke implementatiemodel is het azure-implementatiemodel van de oudere generatie. Het dwingt een wereldwijde vCPU-quotumlimiet af voor virtuele machines en virtuele machineschaalsets. Het klassieke implementatiemodel wordt niet langer aanbevolen en wordt nu vervangen door het Resource Manager-model.

Zie [Resourcemanager en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)voor meer informatie over deze twee implementatiemodellen en de voordelen van het gebruik van Resource Manager.

Wanneer een nieuw abonnement wordt gemaakt, wordt er een standaardquotum met vCPU's aan toegewezen. Wanneer een nieuwe virtuele machine moet worden geïmplementeerd met behulp van het klassieke implementatiemodel, mag de som van nieuw en bestaand vCPU-gebruik in alle regio's het vCPU-quotum dat is goedgekeurd voor het klassieke implementatiemodel niet overschrijden.

Zie [Azure-abonnements- en servicelimieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over quota.

U een verhoging van de vCPU-quotumlimiet voor het klassieke implementatiemodel aanvragen. Gebruik **Help + ondersteuning** of **Gebruik + quota** in de Azure-portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Aanvraag vCPU-quotumverhoging per VM-serie op abonnementsniveau met Behulp van Help + ondersteuning

Volg de onderstaande instructies om een ondersteuningsaanvraag te maken met Behulp van **Help + ondersteuning** in de Azure-portal.

1. Selecteer **Help + ondersteuning**in het menu Azure [portal.](https://portal.azure.com)

   ![Selecteer Help + ondersteuning in de Azure-portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecteer **Nieuw ondersteuningsverzoek**.

   ![Een nieuwe ondersteuningsaanvraag maken in de Azure-portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Kies **in Het type Probleem** **service- en abonnementslimieten (quota)**.

   ![Quota selecteren als het type probleem](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer een abonnement waarvoor u een quotum wilt verhogen](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Selecteer **voor quotumtype** **de maximumsnelheid voor Compute -VM (cores-vCPU's).**

   ![Selecteer quotumtype dat u wilt verhogen](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecteer **Volgende: Oplossingen** om PROBLEEMGEGEVENS te **openen.** Selecteer **Details opgeven** om aanvullende informatie te verstrekken.

   ![Geef details op om uw verzoek te helpen](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Selecteer **in Quotadetails** **Klassiek** en selecteer een **locatie**.

   ![Details toevoegen, waaronder implementatiemodel en locatie](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecteer voor **SKU-familie**een of meer SKU-gezinnen om te verhogen.

   ![Geef de SKU-familie op die moet worden verhoogd](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Voer de nieuwe limieten in die u op het abonnement wilt hebben. Als u een regel wilt verwijderen, schakelt u de selectie uit de **SKU-familie** uit of selecteert u het pictogram 'X verwijderen'. Nadat u een quotum hebt ingevoerd voor elke SKU-familie, selecteert u **Opslaan en Doorgaan** in **quotadetails** om door te gaan met de ondersteuningsaanvraag.

   ![Nieuwe limieten aanvragen](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Aanvraag vCPU-quotumverhoging per VM-serie op abonnementsniveau met gebruik + quota

Volg de onderstaande instructies om een ondersteuningsaanvraag te maken met **gebruikgebruik gebruiks + quota** in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **Abonnementen**.

   ![Naar Abonnementen in de Azure-portal gaan](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer abonnement dat u wilt wijzigen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecteer **Gebruik + quota**.

   ![Gebruiken en quota voor een abonnement selecteren](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Selecteer In de rechterbovenhoek de optie **Aanvraag verhogen**.

   ![Selecteren om het quotum te verhogen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecteer **de abonnementslimiet voor Compute-VM (cores-vCPU's) wordt verhoogd** naarmate het type Quotum wordt **verhoogd**.

   ![Quotumtype selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecteer **Volgende: Oplossingen** om PROBLEEMGEGEVENS te **openen.** Selecteer **Details opgeven** om aanvullende informatie te verstrekken.

   ![Details geven voor uw aanvraag](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Selecteer **in Quotadetails** **Klassiek** en een **locatie**.

   ![Quotadetails selecteren, inclusief implementatiemodel en locatie](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecteer een of meer SKU-families voor een toename.

   ![SKU-familie selecteren voor uitbreiding](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Voer de nieuwe limieten in die u op het abonnement wilt hebben. Als u een regel wilt verwijderen, schakelt u de selectie uit de **SKU-familie** uit of selecteert u het pictogram 'X verwijderen'. Nadat u een quotum hebt ingevoerd voor elke SKU-familie, selecteert u **Opslaan en Doorgaan** in **quotadetails** om door te gaan met de ondersteuningsaanvraag.

   ![Nieuw quotum invoeren](./media/resource-manager-core-quotas-request/new-limits-classic.png)

