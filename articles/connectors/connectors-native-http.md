---
title: HTTP-en HTTPS-eind punten aanroepen-Azure Logic Apps
description: Uitgaande aanvragen verzenden naar HTTP-en HTTPS-eind punten met behulp van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: df856e0d76dbd5903964bc80aa01b97b7461128a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122704"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Uitgaande oproepen verzenden naar HTTP-of HTTPS-eind punten met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde http-trigger of-actie kunt u geautomatiseerde taken en werk stromen maken die regel matig aanvragen verzenden naar een HTTP-of https-eind punt. Als u in plaats daarvan binnenkomende HTTP-of HTTPS-aanroepen wilt ontvangen en hierop wilt reageren, gebruikt u de ingebouwde [aanvraag-of reactie actie](../connectors/connectors-native-reqres.md).

U kunt bijvoorbeeld het service-eind punt voor uw website bewaken door dat eind punt op een opgegeven schema te controleren. Wanneer er een specifieke gebeurtenis plaatsvindt op dat eind punt, zoals uw website, activeert de gebeurtenis de werk stroom van uw logische app en worden de opgegeven acties uitgevoerd.

Als u een eind punt volgens een regel matig schema wilt controleren of pollen, kunt u de http-trigger als eerste stap in de werk stroom gebruiken. Bij elke controle verzendt de trigger een aanroep of *aanvraag* naar het eind punt. Het antwoord van het eind punt bepaalt of de werk stroom van uw logische app wordt uitgevoerd. De trigger geeft alle inhoud van het antwoord op de acties in uw logische app door.

U kunt de HTTP-actie als een andere stap in uw werk stroom gebruiken om het eind punt te bellen wanneer u wilt. Het antwoord van het eind punt bepaalt hoe de resterende acties van uw werk stroom worden uitgevoerd.

De HTTP-connector ondersteunt de Transport Layer Security (TLS) versies 1,0, 1,1 en 1,2, op basis van de mogelijkheid van het doel eindpunt. Logic Apps onderhandelt met het eind punt met behulp van de hoogst ondersteunde versie die mogelijk is. Als het eind punt bijvoorbeeld 1,2 ondersteunt, gebruikt de connector eerst 1,2. Anders gebruikt de connector de eerstvolgende hoogste ondersteunde versie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor het doel eindpunt dat u wilt aanroepen

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* De logische app waarvan u het doel eindpunt wilt aanroepen. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met de http-trigger. Als u de HTTP-actie wilt gebruiken, start u de logische app met een wille keurige trigger. In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

## <a name="add-an-http-trigger"></a>Een HTTP-trigger toevoegen

Deze ingebouwde trigger maakt een HTTP-aanroep van de opgegeven URL voor een eind punt en retourneert een antwoord.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw lege logische app in de ontwerp functie voor logische apps.

1. Voer op de ontwerp functie in het zoekvak ' http ' in als uw filter. Selecteer in de lijst **Triggers** de **http-** trigger.

   ![HTTP-trigger selecteren](./media/connectors-native-http/select-http-trigger.png)

   In dit voor beeld wordt de naam van de trigger gewijzigd in ' HTTP trigger ', zodat de stap een meer beschrijvende naam heeft. Daarnaast voegt het voor beeld een HTTP-actie toe en beide namen moeten uniek zijn.

1. Geef de waarden voor de [http-trigger parameters](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) op die u wilt opnemen in de aanroep van het doel eindpunt. Stel het terugkeer patroon in om te bepalen hoe vaak het doel eindpunt moet worden gecontroleerd met de trigger.

   ![HTTP-trigger parameters invoeren](./media/connectors-native-http/http-trigger-parameters.png)

   Zie [HTTP-triggers en-acties verifiëren](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)voor meer informatie over de beschik bare verificatie typen voor http.

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

1. Ga door met het bouwen van de werk stroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, kunt u uw logische app opslaan. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

## <a name="add-an-http-action"></a>Een HTTP-actie toevoegen

Met deze ingebouwde actie wordt een HTTP-aanroep naar de opgegeven URL voor een eind punt gemaakt en wordt een antwoord geretourneerd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

1. Selecteer **nieuwe stap**onder de stap waar u de http-actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer op de ontwerp functie in het zoekvak ' http ' in als uw filter. Selecteer in de lijst **acties** de **http-** actie.

   ![HTTP-actie selecteren](./media/connectors-native-http/select-http-action.png)

   In dit voor beeld wordt de naam van de actie gewijzigd in ' HTTP Action ' zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de [http-actie parameters](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) die u wilt opnemen in de aanroep van het doel eindpunt.

   ![HTTP-actie parameters invoeren](./media/connectors-native-http/http-action-parameters.png)

   Zie [HTTP-triggers en-acties verifiëren](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)voor meer informatie over de beschik bare verificatie typen voor http.

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

1. Wanneer u klaar bent, moet u uw logische app opslaan. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

## <a name="content-with-multipartform-data-type"></a>Inhoud met meerdelige/formulier-gegevens type

Als u inhoud wilt afhandelen die van het type HTTP-aanvragen heeft `multipart/form-data` , kunt u een JSON-object met de `$content-type` kenmerken en `$multipart` toevoegen aan de hoofd tekst van de HTTP-aanvraag met deze indeling.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Stel dat u een logische app hebt waarmee een HTTP POST-aanvraag voor een Excel-bestand naar een website wordt verzonden met behulp van de API van die site `multipart/form-data` , die het type ondersteunt. Deze actie kan er als volgt uitzien:

![Meerdelige formulier gegevens](./media/connectors-native-http/http-action-multipart.png)

Hier volgt hetzelfde voor beeld waarin de JSON-definitie van de HTTP-actie in de onderliggende werk stroom definitie wordt weer gegeven:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Connector-verwijzing

Zie de volgende secties voor meer informatie over trigger-en actie parameters:

* [HTTP-trigger parameters](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP-actie parameters](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Uitvoergegevens

Hier vindt u meer informatie over de uitvoer van een HTTP-trigger of actie, waarmee deze gegevens worden geretourneerd:

| Naam van eigenschap | type | Description |
|---------------|------|-------------|
| Koppen | object | De headers van de aanvraag |
| body | object | JSON-object | Het object met de inhoud van de hoofd tekst van de aanvraag |
| Status code | int | De status code van de aanvraag |
|||

| Statuscode | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Ongeldig verzoek |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne server fout. Er is een onbekende fout opgetreden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
