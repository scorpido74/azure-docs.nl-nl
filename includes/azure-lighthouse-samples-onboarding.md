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
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456722"
---
We bieden verschillende sjablonen voor het oplossen van specifieke onboardingscenario's. Kies de optie die het beste werkt en zorg ervoor dat u het parameterbestand wijzigt, zodat dit overeenkomt met uw omgeving. Zie [Een klant in gedelegeerd Azure-resourcebeheer vrijgeven](../articles/lighthouse/how-to/onboard-customer.md) voor meer informatie over het gebruik van deze bestanden in uw implementatie.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Plaatst het abonnement van een klant in gedelegeerd Azure-resourcebeheer. Voor elk abonnement moet een afzonderlijke implementatie worden uitgevoerd. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Plaatst een of meer resourcegroepen van een klant in gedelegeerd Azure-resourcebeheer. Gebruik **rgDelegatedResourceManagement** voor één resourcegroep of **multipleRgDelegatedResourceManagement**  om meerdere resourcegroepen in hetzelfde abonnement vrij te geven. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Als u [een aanbieding voor beheerde services hebt gepubliceerd naar Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), kunt u deze sjabloon eventueel gebruiken om resources vrij te geven voor klanten die de aanbieding hebben geaccepteerd. De Marketplace-waarden in het parameterbestand moeten overeenkomen met de waarden die u hebt gebruikt bij het publiceren van uw aanbieding. |

Normaal gesproken is een afzonderlijke implementatie vereist voor elk abonnement dat wordt vrijgegeven, maar u kunt ook sjablonen voor meerdere abonnementen implementeren.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implementeert Azure Resource Manager-sjablonen in meerdere abonnementen. |
