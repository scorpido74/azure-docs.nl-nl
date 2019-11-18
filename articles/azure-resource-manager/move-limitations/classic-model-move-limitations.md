---
title: Klassieke Azure-implementatie resources verplaatsen
description: Gebruik Azure Resource Manager om klassieke implementatie resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150852"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Richt lijnen voor klassieke implementatie model resources verplaatsen

De stappen voor het verplaatsen van resources die via het klassieke model zijn geïmplementeerd, zijn afhankelijk van het feit of u de resources in een abonnement verplaatst of naar een nieuw abonnement.

## <a name="move-in-the-same-subscription"></a>In hetzelfde abonnement verplaatsen

Bij het verplaatsen van resources van een resourcegroep naar een andere resourcegroep binnen hetzelfde abonnement, gelden de volgende beperkingen:

* Virtuele netwerken (klassiek) kunnen niet worden verplaatst.
* Virtuele machines (klassiek) moeten worden verplaatst met de cloudservice.
* Cloudservice kan alleen worden verplaatst wanneer de verplaatsing alle virtuele machines bevat.
* Slechts één cloudservice kan tegelijk worden verplaatst.
* Slechts één opslagaccount (klassiek) kan tegelijk worden verplaatst.
* Opslagaccount (klassiek) kan niet worden verplaatst in dezelfde bewerking met een virtuele machine of een service in de cloud.

Als u klassieke resources wilt verplaatsen naar een nieuwe resource groep binnen hetzelfde abonnement, gebruikt u de [standaard verplaatsings bewerkingen](../resource-group-move-resources.md) via de portal, Azure PowerShell, Azure CLI of rest API. U dezelfde bewerkingen als die u gebruikt voor het verplaatsen van Resource Manager-resources.

## <a name="move-across-subscriptions"></a>Scha kelen tussen abonnementen

Wanneer u resources verplaatst naar een nieuw abonnement, gelden de volgende beperkingen:

* Alle klassieke resources in het abonnement moeten worden verplaatst in dezelfde bewerking.
* Het doel abonnement mag geen andere klassieke resources hebben.
* De overstap kan alleen worden aangevraagd door een afzonderlijke REST-API voor klassieke verplaatst. De standaardopdrachten verplaatsen van Resource Manager werken niet bij het klassieke resources verplaatsen naar een nieuw abonnement.

Om klassieke resources verplaatsen naar een nieuw abonnement, de REST-bewerkingen die specifiek voor klassieke resources zijn te gebruiken. Voer de volgende stappen uit om REST te gebruiken:

1. Controleer als van het bronabonnement deel uitmaken van een verplaatsing van kruislings abonnement. Gebruik de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     In de hoofdtekst van de aanvraag, zijn onder andere:

   ```json
   {
    "role": "source"
   }
   ```

     Het antwoord voor de validatiebewerking is in de volgende indeling:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Controleer als het doelabonnement deel uitmaken van een verplaatsing van kruislings abonnement. Gebruik de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     In de hoofdtekst van de aanvraag, zijn onder andere:

   ```json
   {
    "role": "target"
   }
   ```

     Het antwoord is in dezelfde indeling als de validatie van het abonnement.
1. Als beide abonnementen zijn gevalideerd, alle klassieke resources wilt verplaatsen van één abonnement naar een ander abonnement met de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    In de hoofdtekst van de aanvraag, zijn onder andere:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

De bewerking kan enkele minuten uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Neem contact op met de [ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)als u problemen ondervindt bij het verplaatsen van klassieke resources.

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.
