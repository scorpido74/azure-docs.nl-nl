---
title: API-ondersteuning in statische Azure-Web Apps met Azure Functions
description: Meer informatie over de API-functies die door Azure static Web Apps worden ondersteund
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: f5f40a615bc5faab6265f42d0728403e2735aa0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791619"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-ondersteuning in de preview-versie van statische Web Apps van Azure met Azure Functions

Azure static Web Apps biedt serverloze API-eind punten via [Azure functions](../azure-functions/functions-overview.md). Door gebruik te maken van Azure Functions, worden Api's dynamisch geschaald op basis van de vraag en omvatten de volgende functies:

- **Geïntegreerde beveiliging** met rechtstreekse toegang tot gebruikers [verificatie en op rollen gebaseerde autorisatie](user-information.md) gegevens.
- **Naadloze route ring** die de _API_ -route beschikbaar maakt voor de web-app, zonder dat hiervoor aangepaste CORS-regels vereist zijn.
- **Azure functions** v3 die compatibel is met Node.js 12.
- **Http-triggers** en uitvoer bindingen.

## <a name="configuration"></a>Configuratie

API-eind punten zijn beschikbaar voor de web-app via de _API_ -route. Terwijl deze route is opgelost, hebt u de controle over de map waar u de gekoppelde Azure Functions-app vindt. U kunt deze locatie wijzigen door [het yaml-bestand van de werk stroom te bewerken](github-actions-workflow.md#build-and-deploy) dat zich in de map _. github/werk stromen_ van uw opslag plaats bevindt.

## <a name="constraints"></a>Beperkingen

Azure static Web Apps biedt een API via Azure Functions. De mogelijkheden van Azure Functions zijn gericht op een specifieke set functies waarmee u een API voor een web-app kunt maken en de web-app in staat wilt stellen om op een veilige manier een API te gebruiken. Deze functies worden geleverd met enkele beperkingen, waaronder:

- Het voor voegsel van de API-route moet _API_zijn.
- De API-functie-app moet in Java script zijn.
- Routerings regels voor API-functies bieden alleen ondersteuning voor het [omleiden](routes.md#redirects) en [beveiligen van routes met rollen](routes.md#securing-routes-with-roles).
- Triggers en bindingen zijn beperkt tot [http](../azure-functions/functions-bindings-http-webhook.md).
  - Alle andere [Azure functions-triggers en-bindingen](../azure-functions/functions-triggers-bindings.md#supported-bindings) , met uitzonde ring van uitvoer bindingen, zijn beperkt.
- Logboeken zijn alleen beschikbaar als u [Application Insights](../azure-functions/functions-monitoring.md) toevoegt aan uw functions-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)
