---
title: Eindpunten van service bellen met HTTP of HTTPS
description: Uitgaande HTTP- of HTTPS-aanvragen verzenden naar serviceeindpunten vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 6c52f2df34faf441ab70b48b11bbc393ebcecb65
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617605"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Eindpunten van oproepservice via HTTP of HTTPS vanuit Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de ingebouwde HTTP-trigger of actie u geautomatiseerde taken en werkstromen maken die aanvragen verzenden naar serviceeindpunten via HTTP of HTTPS. U bijvoorbeeld het serviceeindpunt voor uw website controleren door dat eindpunt op een specifiek schema te controleren. Wanneer de opgegeven gebeurtenis op dat eindpunt plaatsvindt, zoals de daling van uw website, activeert de gebeurtenis de werkstroom van uw logische app en worden de acties in die werkstroom uitgevoerd. Als u in plaats daarvan binnenkomende HTTPS-oproepen wilt ontvangen en erop wilt reageren, gebruikt u de ingebouwde [trigger of reactieactie van verzoek.](../connectors/connectors-native-reqres.md)

> [!NOTE]
> Op basis van de mogelijkheden van het doeleindpunt ondersteunt de HTTP-connector TLS-versies 1.0, 1.1 en 1.2 (Transport Layer Security). Logic Apps onderhandelt met het eindpunt over het gebruik van de hoogst ondersteunde versie mogelijk. Als het eindpunt bijvoorbeeld 1.2 ondersteunt, gebruikt de connector eerst 1.2. Anders gebruikt de connector de op één na hoogste ondersteunde versie.

Als u een eindpunt wilt controleren of *peilen* in een terugkerend schema, [voegt u de HTTP-trigger toe](#http-trigger) als de eerste stap in uw werkstroom. Elke keer dat de trigger het eindpunt controleert, roept of verzendt de trigger een *verzoek* naar het eindpunt. Het antwoord van het eindpunt bepaalt of de werkstroom van uw logische app wordt uitgevoerd. De trigger geeft alle inhoud door van de reactie van het eindpunt op de acties in uw logische app.

Als u een eindpunt wilt aanroepen vanaf elke andere locatie in uw werkstroom, [voegt u de HTTP-actie toe.](#http-action) De respons van het eindpunt bepaalt hoe de resterende acties van uw werkstroom worden uitgevoerd.

> [!IMPORTANT]
> Als een HTTP-trigger of actie deze kopteksten bevat, verwijdert Logic Apps deze koppen uit het gegenereerde aanvraagbericht zonder enige waarschuwing of fout weer te geven:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`met deze `Content-Disposition`uitzonderingen: , `Content-Encoding`en`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Hoewel Logic Apps u er niet van weerhoudt logische apps op te slaan die een HTTP-trigger of actie met deze kopteksten gebruiken, negeert Logic Apps deze kopteksten.

In dit artikel ziet u hoe u een HTTP-trigger of actie toevoegt aan de werkstroom van uw logische app.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor het doeleindpunt dat u wilt aanroepen

* Basiskennis over [het maken van logische apps.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* De logische app van waaruit u het doeleindpunt wilt aanroepen. Als u wilt beginnen met de HTTP-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u de HTTP-actie wilt gebruiken, start u uw logische app met de gewenste trigger. In dit voorbeeld wordt de HTTP-trigger als eerste stap gebruikt.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Een HTTP-trigger toevoegen

Deze ingebouwde trigger maakt een HTTP-aanroep naar de opgegeven URL voor een eindpunt en retourneert een antwoord.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Open uw lege logische app in Logic App Designer.

1. Selecteer **ingebouwd**onder het zoekvak van de ontwerper. Voer in het `http` zoekvak in als filter. Selecteer in de lijst **Triggers** de **HTTP-trigger.**

   ![HTTP-trigger selecteren](./media/connectors-native-http/select-http-trigger.png)

   In dit voorbeeld wordt de naam van de trigger gewijzigd in 'HTTP-trigger', zodat de stap een meer beschrijvende naam heeft. In het voorbeeld wordt later ook een HTTP-actie toegevoegd en moeten beide namen uniek zijn.

1. Geef de waarden op voor de [HTTP-triggerparameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) die u wilt opnemen in de aanroep naar het doeleindpunt. Stel de herhaling in voor hoe vaak u wilt dat de trigger het doeleindpunt controleert.

   ![HTTP-triggerparameters invoeren](./media/connectors-native-http/http-trigger-parameters.png)

   Als u een ander verificatietype dan **Geen**selecteert, verschillen de verificatie-instellingen op basis van uw selectie. Zie de volgende onderwerpen voor meer informatie over verificatietypen die beschikbaar zijn voor HTTP:

   * [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Toegang tot bronnen met beheerde identiteiten verifiëren](../logic-apps/create-managed-service-identity.md)

1. Als u andere beschikbare parameters wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste parameters.

1. Ga door met het bouwen van de werkstroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, moet u uw logica-app opslaan. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Een HTTP-actie toevoegen

Met deze ingebouwde actie wordt een HTTP-aanroep naar de opgegeven URL voor een eindpunt gemaakt en wordt een antwoord geretourneerd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Open uw logische app in Logic App Designer.

   In dit voorbeeld wordt de HTTP-trigger als eerste stap gebruikt.

1. Selecteer Onder de stap waarin u de HTTP-actie wilt toevoegen de optie **Nieuwe stap**.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `http` zoekvak in als filter. Selecteer in de lijst **Acties** de **HTTP-actie.**

   ![HTTP-actie selecteren](./media/connectors-native-http/select-http-action.png)

   In dit voorbeeld wordt de naam van de actie gewijzigd in 'HTTP-actie' zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de [HTTP-actieparameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) die u wilt opnemen in de aanroep naar het doeleindpunt.

   ![Http-actieparameters invoeren](./media/connectors-native-http/http-action-parameters.png)

   Als u een ander verificatietype dan **Geen**selecteert, verschillen de verificatie-instellingen op basis van uw selectie. Zie de volgende onderwerpen voor meer informatie over verificatietypen die beschikbaar zijn voor HTTP:

   * [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Toegang tot bronnen met beheerde identiteiten verifiëren](../logic-apps/create-managed-service-identity.md)

1. Als u andere beschikbare parameters wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste parameters.

1. Wanneer u klaar bent, moet u uw logica-app opslaan. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

## <a name="content-with-multipartform-data-type"></a>Inhoud met meerdelige/formuliergegevenstype

Als u inhoud `multipart/form-data` wilt verwerken die tekst heeft in HTTP-aanvragen, u met deze indeling een JSON-object met de `$content-type` kenmerken en `$multipart` kenmerken aan de hoofdtekst van het HTTP-verzoek toevoegen.

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

Stel dat u een logische app hebt die een HTTP POST-aanvraag voor een Excel-bestand naar `multipart/form-data` een website verzendt met behulp van de API van die site, die het type ondersteunt. Zo ziet deze actie eruit:

![Gegevens over meerdere delen](./media/connectors-native-http/http-action-multipart.png)

Hier volgt hetzelfde voorbeeld dat de JSON-definitie van de HTTP-actie weergeeft in de onderliggende werkstroomdefinitie:

```json
"HTTP_action": {
   "inputs": {
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

Zie de volgende secties voor meer informatie over trigger- en actieparameters:

* [HTTP-triggerparameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-actieparameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Uitvoerdetails

Hier vindt u meer informatie over de uitvoer van een HTTP-trigger of actie, die deze informatie retourneert:

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
