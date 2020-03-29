---
title: Azure API Management-terminologie | Microsoft Documenten
description: Dit artikel bevat definities voor de termen die specifiek zijn voor API-beheer.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61074051"
---
# <a name="terminology"></a>Terminologie

Dit artikel bevat definities voor de termen die specifiek zijn voor API Management (APIM).

## <a name="term-definitions"></a>Term definities

* **Backend API** - Een HTTP-service die uw API en de bijbehorende bewerkingen implementeert. 
* **Frontend API**/**APIM API** - Een APIM API host geen API's, het maakt gevels voor uw API's om de gevel aan te passen aan uw behoeften zonder de back-end API aan te raken. Zie [Een API importeren en publiceren](import-and-publish.md)voor meer informatie.
* **APIM-product** - een product bevat een of meer API's, een gebruiksquotum en de gebruiksvoorwaarden. U een aantal API's opnemen en deze aanbieden aan ontwikkelaars via de ontwikkelaarsportal. Zie [Een product maken en publiceren](api-management-howto-add-products.md)voor meer informatie.
* **APIM API-bewerking** - Elke APIM API vertegenwoordigt een reeks bewerkingen die beschikbaar zijn voor ontwikkelaars. Elke APIM-API bevat een verwijzing naar de back-endservice die de API implementeert en de bewerkingen ervan worden toegewezen aan de bewerkingen die door de back-endservice zijn geïmplementeerd. Zie [Mock API-antwoorden voor](mock-api-responses.md)meer informatie .
* **Versie** - Soms wilt u nieuwe of andere API-functies publiceren voor sommige gebruikers, terwijl anderen willen vasthouden aan de API die momenteel voor hen werkt. Zie [Meerdere versies van uw API publiceren](api-management-get-started-publish-versions.md)voor meer informatie.
* **Revisie** - Wanneer uw API klaar is om te gaan en begint te worden gebruikt door ontwikkelaars, moet u meestal zorgen voor het aanbrengen van wijzigingen in die API en tegelijkertijd niet te verstoren bellers van uw API. Het is ook handig om ontwikkelaars te informeren over de aangebrachte wijzigingen. Zie [Revisies gebruiken voor](api-management-get-started-revise-api.md)meer informatie .
* **Ontwikkelaarsportal** - Uw klanten (ontwikkelaars) moeten de portal Ontwikkelaars gebruiken om toegang te krijgen tot uw API's. De developer portal kan worden aangepast. Zie [De portal Voor ontwikkelaars aanpassen](api-management-customize-styles.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een instantie maken](get-started-create-service-instance.md)

