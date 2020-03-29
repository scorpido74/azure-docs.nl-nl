---
title: Verbinding maken met REST-eindpunten vanuit Azure Logic Apps
description: REST-eindpunten in geautomatiseerde taken, processen en werkstromen bewaken met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787366"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Rest-eindpunten aanroepen met Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de ingebouwde HTTP + Swagger-connector u werkstromen automatiseren die regelmatig een REST-eindpunt aanroepen via een [Swagger-bestand](https://swagger.io) door logische apps te bouwen. De HTTP + Swagger trigger en actie werken hetzelfde als de [HTTP trigger en actie,](connectors-native-http.md) maar bieden een betere ervaring in de Logic App Designer door het blootstellen van de API structuur en uitgangen beschreven door de Swagger bestand. Als u een pollingtrigger wilt implementeren, volgt u het pollingpatroon dat is beschreven in [Aangepaste API's maken om andere API's, services en systemen uit logische apps aan te roepen.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor het Swagger-bestand (niet OpenAPI) waarin het doel-REST-eindpunt wordt beschreven

  Het REST-eindpunt moet doorgaans aan deze criteria voldoen om de connector te laten werken:

  * Het Swagger-bestand moet worden gehost op een HTTPS-URL die openbaar toegankelijk is.

  * Het Swagger-bestand moet [CorS (Cross-Origin Resource Sharing)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) hebben ingeschakeld.

  Als u wilt verwijzen naar een Swagger-bestand dat niet wordt gehost of dat niet voldoet aan de vereisten voor beveiliging en cross-origin, u [het Swagger-bestand uploaden naar een blobcontainer in een Azure-opslagaccount](#host-swagger)en CORS inschakelen op dat opslagaccount, zodat u naar het bestand verwijzen.

  De voorbeelden in dit onderwerp maken gebruik van de [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview), waarvoor een Cognitive [Services-account en toegangssleutel vereist](../cognitive-services/cognitive-services-apis-create-account.md)zijn.

* Basiskennis over [het maken van logische apps.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* De logische app van waaruit u het doeleindpunt wilt aanroepen. Als u wilt beginnen met de HTTP + Swagger-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u de actie HTTP + Swagger wilt gebruiken, start u uw logische app met elke gewenste trigger. In dit voorbeeld wordt de HTTP + Swagger-trigger als eerste stap gebruikt.

## <a name="add-an-http--swagger-trigger"></a>Een HTTP + Swagger-trigger toevoegen

Deze ingebouwde trigger stuurt een HTTP-verzoek naar een URL voor een Branieer-bestand waarin een REST-API wordt beschreven en een antwoord retourneert dat de inhoud van dat bestand bevat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw lege logische app in Logic App Designer.

1. Voer op de ontwerper in het zoekvak 'branie' in als filter. Selecteer in de lijst **Triggers** de **HTTP + Swagger-trigger.**

   ![SELECTEER HTTP + Swagger trigger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Voer in het vak **SWAGGER ENDPOINT URL** de URL voor het swaggerbestand in en selecteer **Volgende**.

   In dit voorbeeld wordt de URL van Swagger gebruikt die zich in de regio West-VS bevindt voor de [Cognitive Services Face API:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![URL invoeren voor Swagger-eindpunt](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Wanneer de ontwerper de bewerkingen weergeeft die zijn beschreven door het swaggerbestand, selecteert u de bewerking die u wilt gebruiken.

   ![Bewerkingen in swaggerbestand](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Geef de waarden op voor de triggerparameters, die variëren op basis van de geselecteerde bewerking, die u wilt opnemen in de eindpuntaanroep. Stel de herhaling in voor hoe vaak u wilt dat de trigger het eindpunt aanroept.

   In dit voorbeeld wordt de naam van de trigger gewijzigd in 'HTTP + Swagger trigger: Face - Detect' zodat de stap een meer beschrijvende naam heeft.

   ![Bewerkingsdetails](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Als u andere beschikbare parameters wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste parameters.

   Zie [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over verificatietypen die beschikbaar zijn voor HTTP + Swagger.

1. Ga door met het bouwen van de werkstroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, moet u uw logica-app opslaan. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

## <a name="add-an-http--swagger-action"></a>Een HTTP + Swagger-actie toevoegen

Met deze ingebouwde actie wordt een HTTP-aanvraag ingediend bij de URL voor het Swagger-bestand waarin een REST-API wordt beschreven en wordt een antwoord geretourneerd dat de inhoud van dat bestand bevat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw logische app in Logic App Designer.

1. Selecteer Onder de stap waarin u de actie HTTP + Swagger wilt toevoegen, de optie **Nieuwe stap**.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer op de ontwerper in het zoekvak 'branie' in als filter. Selecteer **in** de lijst Acties de actie **HTTP + Branie.**

    ![Selecteer HTTP + Swagger actie](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Voer in het vak **SWAGGER ENDPOINT URL** de URL voor het swaggerbestand in en selecteer **Volgende**.

   In dit voorbeeld wordt de URL van Swagger gebruikt die zich in de regio West-VS bevindt voor de [Cognitive Services Face API:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![URL invoeren voor Swagger-eindpunt](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Wanneer de ontwerper de bewerkingen weergeeft die zijn beschreven door het swaggerbestand, selecteert u de bewerking die u wilt gebruiken.

   ![Bewerkingen in swaggerbestand](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Geef de waarden op voor de actieparameters die variëren op basis van de geselecteerde bewerking, die u wilt opnemen in de eindpuntaanroep.

   In dit voorbeeld zijn geen parameters, maar wordt de actie hernoemd naar 'HTTP + Swagger action: Face - Identify' zodat de stap een meer beschrijvende naam heeft.

   ![Bewerkingsdetails](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Als u andere beschikbare parameters wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste parameters.

   Zie [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over verificatietypen die beschikbaar zijn voor HTTP + Swagger.

1. Wanneer u klaar bent, moet u uw logica-app opslaan. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Swagger hosten in Azure Storage

U verwijzen naar een Swagger-bestand dat niet wordt gehost of dat niet voldoet aan de vereisten voor beveiliging en cross-origine door dat bestand te uploaden naar blobcontainer in een Azure-opslagaccount en CORS in te schakelen op dat opslagaccount. Voer de volgende stappen uit om Swagger-bestanden in Azure Storage te maken, in te stellen en op te slaan:

1. [Maak een Azure-opslagaccount](../storage/common/storage-create-storage-account.md).

1. Schakel cors nu in voor de blob. Selecteer **CORS**in het menu van uw opslagaccount. Geef op het tabblad **Klodderservice** deze waarden op en selecteer **Opslaan**.

   | Eigenschap | Waarde |
   |----------|-------|
   | **Toegestane oorsprongen** | `*` |
   | **Toegestane methoden** | `GET`, `HEAD`, `PUT` |
   | **Toegestane headers** | `*` |
   | **Weergegeven headers** | `*` |
   | **Maximale leeftijd** (in seconden) | `200` |
   |||

   Hoewel in dit voorbeeld de [Azure-portal](https://portal.azure.com)wordt gebruikt, u een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com/)gebruiken of deze instelling automatisch configureren met behulp van dit voorbeeld [PowerShell-script](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Een blobcontainer maken.](../storage/blobs/storage-quickstart-blobs-portal.md) Selecteer access level wijzigen in het **deelvenster Overzicht** **van**de container . Selecteer **blob (alleen anonieme leestoegang voor blobs)** en selecteer **OK**in de lijst **Openbaar toegangsniveau.**

1. [Upload het Swagger-bestand naar de blobcontainer,](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)via de [Azure-portal](https://portal.azure.com) of [Azure Storage Explorer.](https://storageexplorer.com/)

1. Als u naar het bestand in de blobcontainer wilt verwijzen, gebruikt u een HTTPS-koppeling die deze indeling volgt, die hoofdlettergevoelig is:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Connector-verwijzing

Hier vindt u meer informatie over de uitgangen van een HTTP + Swagger trigger of actie. De HTTP + Swagger call retourneert deze informatie:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| Headers | object | De kopteksten van het verzoek |
| body | object | JSON-object | Het object met de inhoud van de hoofdtekst van het verzoek |
| statuscode | int | De statuscode van het verzoek |
|||

| Statuscode | Beschrijving |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Slecht verzoek |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is onbekende fout opgetreden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
