---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111648"
---
We bieden verschillende sjablonen voor het oplossen van specifieke onboardingscenario's. Kies de optie die het beste werkt en zorg ervoor dat u het parameterbestand wijzigt, zodat dit overeenkomt met uw omgeving. Raadpleeg [Een klant onboarden in Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md) voor meer informatie over het gebruik van deze bestanden in uw implementatie.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Onboard het abonnement van een klant in Azure Lighthouse. Voor elk abonnement moet een afzonderlijke implementatie worden uitgevoerd. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Plaatst een of meer resourcegroepen van een klant in Azure Lighthouse. Gebruik **rgDelegatedResourceManagement** voor één resourcegroep of **multipleRgDelegatedResourceManagement**  om meerdere resourcegroepen in hetzelfde abonnement vrij te geven. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Als u [een aanbieding voor beheerde services hebt gepubliceerd naar Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), kunt u deze sjabloon eventueel gebruiken om resources vrij te geven voor klanten die de aanbieding hebben geaccepteerd. De Marketplace-waarden in het parameterbestand moeten overeenkomen met de waarden die u hebt gebruikt bij het publiceren van uw aanbieding. |

Normaal gesproken is een afzonderlijke implementatie vereist voor elk abonnement dat wordt vrijgegeven, maar u kunt ook sjablonen voor meerdere abonnementen implementeren.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implementeert Azure Resource Manager-sjablonen in meerdere abonnementen. |
