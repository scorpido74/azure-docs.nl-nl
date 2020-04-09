---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986597"
---
We bieden verschillende sjablonen voor het oplossen van specifieke onboardingscenario's. Kies de optie die het beste werkt en zorg ervoor dat u het parameterbestand wijzigt, zodat dit overeenkomt met uw omgeving. Zie [Een klant in gedelegeerd Azure-resourcebeheer vrijgeven](../articles/lighthouse/how-to/onboard-customer.md) voor meer informatie over het gebruik van deze bestanden in uw implementatie.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Plaatst het abonnement van een klant in gedelegeerd Azure-resourcebeheer. Voor elk abonnement moet een afzonderlijke implementatie worden uitgevoerd. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Plaatst een of meer resourcegroepen van een klant in gedelegeerd Azure-resourcebeheer. Gebruik **rgDelegatedResourceManagement** voor één resourcegroep of **multipleRgDelegatedResourceManagement**  om meerdere resourcegroepen in hetzelfde abonnement vrij te geven. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Als u [een aanbieding voor beheerde services hebt gepubliceerd naar Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), kunt u deze sjabloon eventueel gebruiken om resources vrij te geven voor klanten die de aanbieding hebben geaccepteerd. De Marketplace-waarden in het parameterbestand moeten overeenkomen met de waarden die u hebt gebruikt bij het publiceren van uw aanbieding. |

Normaal gesproken is een afzonderlijke implementatie vereist voor elk abonnement dat wordt vrijgegeven, maar u kunt ook sjablonen voor meerdere abonnementen implementeren.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implementeert Azure Resource Manager-sjablonen in meerdere abonnementen. |
