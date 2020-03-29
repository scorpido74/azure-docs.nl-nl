---
title: WebAPI's implementeren en oproepen & REST-API's van Azure Logic Apps
description: Web API's implementeren en aanroepen & REST API's voor systeemintegratieworkflows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790749"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Aangepaste API's implementeren en aanroepen vanuit werkstromen in Azure Logic Apps

Nadat u [aangepaste API's hebt gemaakt](./logic-apps-create-api-app.md) voor gebruik in logische app-werkstromen, moet u uw API's implementeren voordat u ze bellen. U uw API's implementeren als [web-apps,](../app-service/overview.md)maar overwegen uw API's te implementeren als [API-apps](../app-service/app-service-web-tutorial-rest-api.md), die uw werk gemakkelijker maken wanneer u API's bouwt, hosten en gebruiken in de cloud en on-premises. U hoeft geen code in uw API's te wijzigen - implementeer uw code alleen in een API-app. U uw API's hosten op [Azure App Service](../app-service/overview.md), een platform-as-a-service (PaaS) aanbod dat zeer schaalbare, eenvoudige API-hosting biedt.

Hoewel u elke API vanuit een logische app aanroepen, voegt u voor de beste ervaring [OpenAPI-metagegevens (voorheen Swagger)](https://swagger.io/specification/) toe die de bewerkingen en parameters van uw API beschrijven. Met dit OpenAPI-bestand uw API gemakkelijker worden geïntegreerd en beter werken met logische apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Uw API implementeren als web-app of API-app

Voordat u uw aangepaste API vanuit een logische app aanroepen, implementeert u uw API als web-app of API-app naar Azure App Service. Als u uw OpenAPI-bestand leesbaar wilt maken voor de Logic Apps Designer, stelt u de eigenschappen van api-definitie in en schakelt u [cors (cross-origin resource sharing)](../app-service/overview.md) in voor uw web-app of API-app.

1. Selecteer in de [Azure-portal](https://portal.azure.com)uw web-app of API-app.

2. Kies **API-definitie**in het app-menu dat wordt geopend onder **API.** Stel de locatie van de **API-definitie in** op de URL voor uw OpenAPI-swagger.json-bestand.

   Meestal wordt de URL in deze indeling weergegeven:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Koppeling naar OpenAPI-bestand voor uw aangepaste API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Kies **onder API** **CORS**. Stel het CORS-beleid voor **toegestane oorsprong** in op **'*'** (sta alles toe).

   Met deze instelling kunnen aanvragen van Logic App Designer worden ingediend.

   ![Aanvragen van Logic App Designer toestaan voor uw aangepaste API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Zie [Host een RESTful API met CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md)voor meer informatie.

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Uw aangepaste API aanroepen vanuit logische app-werkstromen

Nadat u de eigenschappen api-definitie en CORS hebt ingesteld, moeten de triggers en acties van uw aangepaste API beschikbaar zijn om op te nemen in uw interface voor logische apps. 

*  Als u websites met OpenAPI-URL's wilt bekijken, u door uw abonnementswebsites bladeren in de Logic Apps Designer.

*  Als u beschikbare acties en invoer wilt weergeven door een OpenAPI-document aan te wijzen, gebruikt u de [actie HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Als u een API wilt aanroepen, inclusief API's die een OpenAPI-document niet hebben of blootleggen, u altijd een aanvraag maken met de [HTTP-actie](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van aangepaste connectors](../logic-apps/custom-connector-overview.md)