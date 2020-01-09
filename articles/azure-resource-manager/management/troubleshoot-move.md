---
title: Fouten bij verplaatsen oplossen
description: Azure Resource Manager gebruiken voor resources verplaatsen naar een nieuwe resourcegroep of abonnement.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 422ecb92e264d7e728282bbe8ee491a642cf013f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478342"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Problemen oplossen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement

Dit artikel bevat suggesties voor het oplossen van problemen bij het verplaatsen van resources.

## <a name="upgrade-a-subscription"></a>Een abonnement upgraden

Als u een upgrade wilt uitvoeren van uw Azure-abonnement (zoals overschakelen van gratis naar betalen per gebruik), moet u uw abonnement converteren.

* Zie het upgraden van een gratis proefversie [uw gratis proefversie of Azure voor Microsoft Imagine-abonnement upgraden naar betalen per gebruik](../../billing/billing-upgrade-azure-subscription.md).
* Als u wilt wijzigen in een betalen per gebruik-account, Zie [wijzigen van uw Azure-betalen per gebruik-abonnement naar een andere aanbieding](../../billing/billing-how-to-switch-azure-offer.md).

Als u het abonnement niet kan omzetten [maken van een Azure-ondersteuningsaanvraag](../../azure-supportability/how-to-create-azure-support-request.md). Selecteer **Abonnementsbeheer** voor het probleemtype.

## <a name="service-limitations"></a>Service beperkingen

Voor sommige services zijn aanvullende overwegingen vereist bij het verplaatsen van resources. Als u de volgende services wilt verplaatsen, controleert u of u de richt lijnen en beperkingen hebt gecontroleerd.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassiek implementatiemodel](./move-limitations/classic-model-move-limitations.md)
* [Netwerken](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuele machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Grote aanvragen

Indien mogelijk is grote break verplaatst naar afzonderlijke verplaatsingsbewerkingen. Resource Manager retourneert onmiddellijk een fout wanneer er meer dan 800 resources zijn in één bewerking. Verplaatsen van minder dan 800 resources kan echter ook mislukken door een time-out optreedt.

## <a name="resource-not-in-succeeded-state"></a>De resource heeft de status niet voltooid

Wanneer er een fout bericht wordt weer gegeven dat aangeeft dat een resource niet kan worden verplaatst, omdat deze niet de status geslaagd heeft, is het mogelijk een afhankelijke resource die de verplaatsing blokkeert. Normaal gesp roken is de fout code **MoveCannotProceedWithResourcesNotInSucceededState**.

Als de bron-of doel resource groep een virtueel netwerk bevat, worden de statussen van alle afhankelijke resources voor het virtuele netwerk gecontroleerd tijdens de verplaatsing. De controle bevat de resources rechtstreeks en indirect afhankelijk van het virtuele netwerk. Als een van deze resources een mislukte status heeft, wordt de verplaatsing geblokkeerd. Als bijvoorbeeld een virtuele machine die gebruikmaakt van het virtuele netwerk is mislukt, wordt de verplaatsing geblokkeerd. De verplaatsing wordt geblokkeerd, zelfs wanneer de virtuele machine niet een van de resources is die worden verplaatst en die zich niet in een van de resource groepen bevindt voor de verplaatsing.

Wanneer deze fout wordt weer gegeven, hebt u twee opties. Verplaats uw resources naar een resource groep die geen virtueel netwerk heeft of [Neem contact op met de ondersteuning](../../azure-supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.
