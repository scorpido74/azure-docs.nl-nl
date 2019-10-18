---
title: Fouten bij verplaatsen oplossen-Azure Resource Manager
description: Gebruik Azure Resource Manager om resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: tomfitz
ms.openlocfilehash: 383098ab227013119bca668b42035c93334c8464
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533355"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Problemen oplossen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement

Dit artikel bevat suggesties voor het oplossen van problemen bij het verplaatsen van resources.

## <a name="upgrade-a-subscription"></a>Een abonnement upgraden

Als u een upgrade wilt uitvoeren van uw Azure-abonnement (zoals overschakelen van gratis naar betalen per gebruik), moet u uw abonnement converteren.

* Als u een gratis proef versie wilt upgraden, raadpleegt u [uw gratis proef versie bijwerken of Microsoft Imagine Azure-abonnement naar betalen per](../billing/billing-upgrade-azure-subscription.md)gebruik.
* Als u een betalen naar gebruik-account wilt wijzigen, raadpleegt u [uw Azure betalen naar gebruik-abonnement wijzigen in een andere aanbieding](../billing/billing-how-to-switch-azure-offer.md).

Als u het abonnement niet kunt converteren, moet u [een Azure-ondersteunings aanvraag maken](../azure-supportability/how-to-create-azure-support-request.md). Selecteer **abonnements beheer** voor het type probleem.

## <a name="service-limitations"></a>Service beperkingen

Voor sommige services zijn aanvullende overwegingen vereist bij het verplaatsen van resources. Als u de volgende services wilt verplaatsen, controleert u of u de richt lijnen en beperkingen hebt gecontroleerd.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassiek implementatiemodel](./move-limitations/classic-model-move-limitations.md)
* [Netwerken](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuele machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Grote aanvragen

Als dat mogelijk is, verbreekt u grote verplaatsingen in afzonderlijke Verplaats bewerkingen. Resource Manager retourneert onmiddellijk een fout wanneer er meer dan 800 resources zijn in één bewerking. Het verplaatsen van minder dan 800 resources kan echter ook mislukken door een time-out.

## <a name="resource-not-in-succeeded-state"></a>De resource heeft de status niet voltooid

Wanneer er een fout bericht wordt weer gegeven dat aangeeft dat een resource niet kan worden verplaatst, omdat deze niet de status geslaagd heeft, is het mogelijk een afhankelijke resource die de verplaatsing blokkeert. Normaal gesp roken is de fout code **MoveCannotProceedWithResourcesNotInSucceededState**.

Als de bron-of doel resource groep een virtueel netwerk bevat, worden de statussen van alle afhankelijke resources voor het virtuele netwerk gecontroleerd tijdens de verplaatsing. De controle bevat de resources rechtstreeks en indirect afhankelijk van het virtuele netwerk. Als een van deze resources een mislukte status heeft, wordt de verplaatsing geblokkeerd. Als bijvoorbeeld een virtuele machine die gebruikmaakt van het virtuele netwerk is mislukt, wordt de verplaatsing geblokkeerd. De verplaatsing wordt geblokkeerd, zelfs wanneer de virtuele machine niet een van de resources is die worden verplaatst en die zich niet in een van de resource groepen bevindt voor de verplaatsing.

Wanneer deze fout wordt weer gegeven, hebt u twee opties. Verplaats uw resources naar een resource groep die geen virtueel netwerk heeft of [Neem contact op met de ondersteuning](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.
