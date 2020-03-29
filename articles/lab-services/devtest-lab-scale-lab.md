---
title: Quota en limieten schalen in uw lab in Azure DevTest Labs | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u uw lab schalen in Azure DevTest Labs. Bekijk uw gebruiksquota en -limieten en vraag om een verhoging.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: f3299c24bc751263ccd284a4fddb492fba5dd1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761182"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quota en limieten schalen in DevTest Labs
Terwijl u in DevTest Labs werkt, merkt u mogelijk dat er bepaalde standaardlimieten zijn voor sommige Azure-resources, die van invloed kunnen zijn op de DevTest Labs-service. Deze grenswaarden worden **quota**genoemd .

> [!NOTE]
> De DevTest Labs-service legt geen quota op. Eventuele quota die u tegenkomt, zijn standaardbeperkingen van het totale Azure-abonnement.

U elke Azure-bron gebruiken totdat u het quotum hebt bereikt. Elk abonnement heeft afzonderlijke quota en het gebruik wordt bijgehouden per abonnement.

Elk abonnement heeft bijvoorbeeld een standaardquotum van 20 cores. Dus als u VM's maakt in uw lab met elk vier cores, u slechts vijf VM's maken.

[Azure Subscription and Service Limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) bevat enkele van de meest voorkomende quota voor Azure-resources. De resources die het meest worden gebruikt in een lab en waarvoor u quota tegenkomen, omvatten VM-cores, openbare IP-adressen, netwerkinterface, beheerde schijven, RBAC-roltoewijzing en ExpressRoute-circuits.

## <a name="view-your-usage-and-quotas"></a>Uw gebruik en quota weergeven
In deze stappen ziet u hoe u de huidige quota in uw abonnement voor specifieke Azure-bronnen weergeven en welk percentage van elk quotum dat u hebt gebruikt, bekijken.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Meer services**en selecteer Vervolgens **Facturering** in de lijst.
1. Selecteer in het blad Facturering een abonnement.
4. Selecteer **Gebruik + quota**.

   ![Knop Gebruik en quota](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Het blade Gebruik + quota wordt weergegeven, waarin verschillende bronnen worden vermeld die beschikbaar zijn in dat abonnement en het percentage van het quotum dat per resource wordt gebruikt.

   ![Quota en gebruik](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Meer resources aanvragen in uw abonnement
Als u een quotumlimiet bereikt, kan de standaardlimiet van een resource in een abonnement worden verhoogd tot een maximumlimiet, zoals beschreven in [Azure-abonnements- en servicelimieten.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

In deze stappen ziet u hoe u een quotumverhoging aanvragen via de [Azure-portal.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Selecteer **Meer services,** selecteer **Facturering**en selecteer Vervolgens Gebruik **+ quota**.
1. Selecteer in het mes Gebruik + quota de knop **Verhoging aanvragen.**

   ![Knop Verhoging aanvragen](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Als u het verzoek wilt invullen en indienen, vult u de vereiste informatie in op alle drie de tabbladen van het formulier **Nieuw ondersteuningsaanvraag.**

   ![Formulier voor het verhogen van aanvragen](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Inzicht in Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) biedt meer informatie over het contact opnemen met Azure-ondersteuning om een quotumverhoging aan te vragen.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Volgende stappen
* Bekijk [de sjabloongalerie voor Azure Resource Manager QuickStart van DevTest Labs.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
