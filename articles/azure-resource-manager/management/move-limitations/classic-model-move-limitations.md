---
title: Azure Classic-implementatiebronnen verplaatsen
description: Gebruik Azure Resource Manager om de implementatiebronnen van Classic te verplaatsen naar een nieuwe brongroep of -abonnement.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485284"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Richtlijnen verplaatsen voor klassieke implementatiemodelbronnen

De stappen om resources die via het klassieke model worden geïmplementeerd, te verplaatsen, verschillen afhankelijk van de vraag of u de resources binnen een abonnement of naar een nieuw abonnement verplaatst.

## <a name="move-in-the-same-subscription"></a>Bewegen in hetzelfde abonnement

Wanneer resources van de ene resourcegroep naar een andere resourcegroep binnen hetzelfde abonnement worden verplaatst, gelden de volgende beperkingen:

* Virtuele netwerken (klassiek) kunnen niet worden verplaatst.
* Virtuele machines (klassiek) moeten worden verplaatst met de cloudservice.
* Cloudservice kan alleen worden verplaatst wanneer de verhuizing al zijn virtuele machines omvat.
* Er kan slechts één cloudservice tegelijk worden verplaatst.
* Er kan slechts één opslagaccount (klassiek) tegelijk worden verplaatst.
* Opslagaccount (klassiek) kan niet in dezelfde bewerking worden verplaatst met een virtuele machine of een cloudservice.

Als u klassieke resources wilt verplaatsen naar een nieuwe brongroep binnen hetzelfde abonnement, gebruikt u de [standaardverplaatsingsbewerkingen](../move-resource-group-and-subscription.md) via de portal, Azure PowerShell, Azure CLI of REST API. U gebruikt dezelfde bewerkingen als u gebruikt voor het verplaatsen van Resource Manager-resources.

## <a name="move-across-subscriptions"></a>Over abonnement heen gaan

Bij het verplaatsen van resources naar een nieuw abonnement gelden de volgende beperkingen:

* Alle klassieke bronnen in het abonnement moeten in dezelfde bewerking worden verplaatst.
* Het doelabonnement mag geen andere klassieke bronnen hebben.
* De verhuizing kan alleen worden aangevraagd via een aparte REST API voor klassieke bewegingen. De standaard opdrachten voor verplaatsen van Resource Manager werken niet bij het verplaatsen van klassieke resources naar een nieuw abonnement.

Als u klassieke resources wilt verplaatsen naar een nieuw abonnement, gebruikt u de REST-bewerkingen die specifiek zijn voor klassieke resources. Ga als volgt te werk om REST te gebruiken:

1. Controleer of het bronabonnement kan deelnemen aan een cross-subscription move. Gebruik de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     In de aanvraaginstantie:

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

1. Controleer of het bestemmingsabonnement kan deelnemen aan een cross-subscription move. Gebruik de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     In de aanvraaginstantie:

   ```json
   {
    "role": "target"
   }
   ```

     Het antwoord is in dezelfde indeling als de validatie van het bronabonnement.
1. Als beide abonnementen zijn gevalideerd, verplaatst u alle klassieke bronnen van het ene abonnement naar een ander abonnement met de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    In de aanvraaginstantie:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

De bewerking kan enkele minuten duren.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het verplaatsen van klassieke bronnen, neemt u contact op met [Ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement](../move-resource-group-and-subscription.md)voor opdrachten om resources te verplaatsen.
