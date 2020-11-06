---
title: Azure-app configuratie REST API-HMAC-autorisatie
description: Gebruik HMAC voor verificatie op basis van Azure-app configuratie met behulp van de REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424002"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC-autorisatie-REST API referentie

Als HMAC-verificatie wordt gebruikt, worden bewerkingen in een van de twee categorieën uitgevoerd, lezen of schrijven. Toegangs sleutels voor lezen/schrijven verlenen machtiging voor het aanroepen van alle bewerkingen. Alleen-lezen toegangs sleutels geven toestemming om alleen lees bewerkingen aan te roepen. Hiermee wordt aangegeven of een toegangs sleutel alleen-lezen is of dat lezen-schrijven wordt bepaald door de bijbehorende `readOnly` eigenschap. Een poging om een schrijf aanvraag met een alleen-lezen toegangs sleutel te maken, leidt ertoe dat de aanvraag niet wordt geautoriseerd.

## <a name="obtaining-access-keys"></a>Toegangs sleutels verkrijgen

De specificatie van de toegangs sleutels en de API die wordt gebruikt om deze te verkrijgen, wordt in de specificatie van de Azure-app Configuration resource provider [hier](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)beschreven. Toegangs sleutels worden verkregen via de bewerking ' ConfigurationStores_ListKeys '.

## <a name="errors"></a>Fouten

```http
HTTP/1.1 403 Forbidden
```

**Reden:** De toegangs sleutel die wordt gebruikt om de aanvraag te verifiëren, biedt niet de vereiste machtigingen om de aangevraagde bewerking uit te voeren.
**Oplossing:** Verkrijg een toegangs sleutel die toestemming biedt om de aangevraagde bewerking uit te voeren en deze te gebruiken om de aanvraag te verifiëren.
