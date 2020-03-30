---
title: Verplaatsingsfouten oplossen
description: Gebruik Azure Resource Manager om resources naar een nieuwe brongroep of -abonnement te verplaatsen.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891264"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Problemen oplossen met het verplaatsen van Azure-resources naar een nieuwe resourcegroep of een nieuw abonnement

In dit artikel vindt u suggesties om problemen op te lossen bij het verplaatsen van resources.

## <a name="upgrade-a-subscription"></a>Een abonnement upgraden

Als u uw Azure-abonnement daadwerkelijk wilt upgraden (zoals overstappen van gratis naar betalen per gebruik), moet u uw abonnement converteren.

* Zie [Uw gratis proefversie of Microsoft Imagine Azure-abonnement upgraden naar betalen per gebruik](../../billing/billing-upgrade-azure-subscription.md)als u een gratis proefversie wilt uitvoeren.
* Zie [Uw Azure Pay-As-You-Go-abonnement wijzigen in een andere aanbieding](../../billing/billing-how-to-switch-azure-offer.md)als u een betalen per gebruik-account wilt wijzigen.

Als u het abonnement niet converteren, [maakt u een Azure-ondersteuningsaanvraag](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecteer **Abonnementsbeheer** voor het type probleem.

## <a name="service-limitations"></a>Servicebeperkingen

Sommige services vereisen aanvullende overwegingen bij het verplaatsen van resources. Als u de volgende services verplaatst, controleert u de richtlijnen en beperkingen.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassiek implementatiemodel](./move-limitations/classic-model-move-limitations.md)
* [Networking](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuele machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Grote aanvragen

Wanneer mogelijk, breek grote bewegingen in afzonderlijke verplaatsingen. Resourcemanager retourneert onmiddellijk een fout wanneer er meer dan 800 resources in één bewerking zijn. Echter, het verplaatsen van minder dan 800 middelen kan ook mislukken door timing uit.

## <a name="resource-not-in-succeeded-state"></a>Resource niet in de geslaagde status

Wanneer u een foutbericht ontvangt dat aangeeft dat een resource niet kan worden verplaatst omdat deze niet is opgevolgd, kan het in feite een afhankelijke bron zijn die de verplaatsing blokkeert. Doorgaans is de foutcode **MoveCannotProceedWithResourcesNotInSucceededState**.

Als de bron- of doelgroepeen virtueel netwerk bevat, worden de statussen van alle afhankelijke bronnen voor het virtuele netwerk tijdens de verhuizing gecontroleerd. De controle omvat deze bronnen die direct en indirect afhankelijk zijn van het virtuele netwerk. Als een van deze resources zich in een mislukte status bevindt, wordt de verplaatsing geblokkeerd. Als een virtuele machine die het virtuele netwerk gebruikt bijvoorbeeld is mislukt, wordt de verplaatsing geblokkeerd. De verplaatsing wordt geblokkeerd, zelfs wanneer de virtuele machine niet een van de resources is die wordt verplaatst en zich niet in een van de resourcegroepen voor de verhuizing bevindt.

Wanneer u deze fout ontvangt, hebt u twee opties. Verplaats uw resources naar een resourcegroep die geen virtueel netwerk heeft of [neem contact op met ondersteuning.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement](move-resource-group-and-subscription.md)voor opdrachten om resources te verplaatsen.
