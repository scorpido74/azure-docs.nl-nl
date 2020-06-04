---
title: Service-eind punten aanroepen met behulp van HTTP of HTTPS
description: Uitgaande HTTP-of HTTPS-aanvragen verzenden naar service-eind punten van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 33075173385a6e36829199c5bda854c78a4424fc
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325113"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Service-eind punten aanroepen via HTTP of HTTPS vanaf Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde http-trigger of-actie kunt u geautomatiseerde taken en werk stromen maken waarmee aanvragen worden verzonden naar service-eind punten via http of https. U kunt bijvoorbeeld het service-eind punt voor uw website bewaken door dat eind punt op een specifiek schema te controleren. Wanneer de opgegeven gebeurtenis plaatsvindt op dat eind punt, zoals uw website, activeert de gebeurtenis de werk stroom van uw logische app en worden de acties uitgevoerd in die werk stroom. Als u in plaats daarvan binnenkomende HTTPS-aanroepen wilt ontvangen en beantwoorden, gebruikt u de ingebouwde [aanvraag-of reactie actie](../connectors/connectors-native-reqres.md).

* Als u een eind punt in een terugkerend schema wilt controleren of *pollen* , [voegt u de http-trigger](#http-trigger) als eerste stap in de werk stroom toe. Telkens wanneer de trigger het eind punt controleert, wordt de trigger aangeroepen of een *aanvraag* naar het eind punt verzonden. Het antwoord van het eind punt bepaalt of de werk stroom van uw logische app wordt uitgevoerd. De trigger geeft inhoud van het antwoord van het eind punt door aan de acties in uw logische app.

* Als u een eind punt van ergens anders in uw werk stroom wilt aanroepen, [voegt u de HTTP-actie toe](#http-action). Het antwoord van het eind punt bepaalt hoe de resterende acties van uw werk stroom worden uitgevoerd.

In dit artikel wordt beschreven hoe u een HTTP-trigger of actie kunt toevoegen aan de werk stroom van uw logische app.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor het doel eindpunt dat u wilt aanroepen

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md)?

* De logische app waarvan u het doel eindpunt wilt aanroepen. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met de http-trigger. Als u de HTTP-actie wilt gebruiken, start u de logische app met een wille keurige trigger. In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Op basis van de mogelijkheid van het doel eindpunt, worden uitgaande oproepen ondersteund Transport Layer Security (TLS), dat eerder is Secure Sockets Layer (SSL), versie 1,0, 1,1 en 1,2. Logic Apps onderhandelt met het eind punt met behulp van de hoogst ondersteunde versie die mogelijk is.

Als het eind punt bijvoorbeeld 1,2 ondersteunt, gebruikt de HTTP-connector eerst 1,2. Anders gebruikt de connector de eerstvolgende hoogste ondersteunde versie.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Zelfondertekende certificaten

* Voor Logic apps in de wereld wijde Azure-omgeving met meerdere tenants, de HTTP-connector staat niet-ondertekende TLS/SSL-certificaten niet toe. Als uw logische app een HTTP-aanroep naar een server maakt en een zelfondertekend TLS/SSL-certificaat presenteert, mislukt de HTTP-aanroep met een `TrustFailure` fout.

* Voor Logic apps in een [Integration service-omgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de http-connector zelfondertekende certificaten voor TLS/SSL-Handshake toegestaan. U moet echter eerst [zelfondertekende ondersteuning voor certificaten inschakelen](../logic-apps/create-integration-service-environment-rest-api.md#request-body) voor een bestaande ISE of nieuwe ISE met behulp van de Logic apps rest API en het open bare certificaat op de `TrustedRoot` locatie installeren.

## <a name="known-issues"></a>Bekende problemen

### <a name="omitted-http-headers"></a>Wegge laten HTTP-headers

Als een HTTP-trigger of actie deze headers bevat, verwijdert Logic Apps deze headers uit het gegenereerde aanvraag bericht zonder dat er een waarschuwing of fout wordt weer gegeven:

* `Accept-*`
* `Allow`
* `Content-*`met deze uitzonde ringen: `Content-Disposition` , `Content-Encoding` en`Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Hoewel Logic Apps niet stopt met het opslaan van logische apps die gebruikmaken van een HTTP-trigger of actie met deze headers, Logic Apps deze headers negeren.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Een HTTP-trigger toevoegen

Deze ingebouwde trigger maakt een HTTP-aanroep van de opgegeven URL voor een eind punt en retourneert een antwoord.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Open uw lege logische app in de ontwerp functie voor logische apps.

1. Selecteer in het zoekvak van de ontwerp functie **ingebouwd**. Voer in het zoekvak in `http` als uw filter. Selecteer in de lijst **Triggers** de **http-** trigger.

   ![HTTP-trigger selecteren](./media/connectors-native-http/select-http-trigger.png)

   In dit voor beeld wordt de naam van de trigger gewijzigd in ' HTTP trigger ', zodat de stap een meer beschrijvende naam heeft. Daarnaast voegt het voor beeld een HTTP-actie toe en beide namen moeten uniek zijn.

1. Geef de waarden voor de [http-trigger parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) op die u wilt opnemen in de aanroep van het doel eindpunt. Stel het terugkeer patroon in om te bepalen hoe vaak het doel eindpunt moet worden gecontroleerd met de trigger.

   ![HTTP-trigger parameters invoeren](./media/connectors-native-http/http-trigger-parameters.png)

   Als u een ander verificatie type dan **geen**selecteert, verschillen de verificatie-instellingen op basis van uw selectie. Zie de volgende onderwerpen voor meer informatie over de beschik bare verificatie typen voor HTTP:

   * [Verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Toegang tot resources met beheerde identiteiten verifiëren](../logic-apps/create-managed-service-identity.md)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

1. Ga door met het bouwen van de werk stroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Als u klaar bent, kunt u uw logische app niet opslaan. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Een HTTP-actie toevoegen

Met deze ingebouwde actie wordt een HTTP-aanroep naar de opgegeven URL voor een eind punt gemaakt en wordt een antwoord geretourneerd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Open uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

1. Selecteer **nieuwe stap**onder de stap waar u de http-actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Selecteer onder **Kies een actie de**optie **ingebouwd**. Voer in het zoekvak in `http` als uw filter. Selecteer in de lijst **acties** de **http-** actie.

   ![HTTP-actie selecteren](./media/connectors-native-http/select-http-action.png)

   In dit voor beeld wordt de naam van de actie gewijzigd in ' HTTP Action ' zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de [http-actie parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) die u wilt opnemen in de aanroep van het doel eindpunt.

   ![HTTP-actie parameters invoeren](./media/connectors-native-http/http-action-parameters.png)

   Als u een ander verificatie type dan **geen**selecteert, verschillen de verificatie-instellingen op basis van uw selectie. Zie de volgende onderwerpen voor meer informatie over de beschik bare verificatie typen voor HTTP:

   * [Verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Toegang tot resources met beheerde identiteiten verifiëren](../logic-apps/create-managed-service-identity.md)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

1. Als u klaar bent, kunt u uw logische app niet opslaan. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

## <a name="content-with-multipartform-data-type"></a>Inhoud met meerdelige/formulier-gegevens type

Als u inhoud wilt afhandelen die `multipart/form-data` van het type HTTP-aanvragen heeft, kunt u een JSON-object met de `$content-type` kenmerken en toevoegen `$multipart` aan de hoofd tekst van de HTTP-aanvraag met deze indeling.

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

Stel dat u een logische app hebt waarmee een HTTP POST-aanvraag voor een Excel-bestand naar een website wordt verzonden met behulp van de API van die site, die het `multipart/form-data` type ondersteunt. Deze actie kan er als volgt uitzien:

![Meerdelige formulier gegevens](./media/connectors-native-http/http-action-multipart.png)

Hier volgt hetzelfde voor beeld waarin de JSON-definitie van de HTTP-actie in de onderliggende werk stroom definitie wordt weer gegeven:

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

Zie de volgende secties voor meer informatie over trigger-en actie parameters:

* [HTTP-trigger parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-actie parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Uitvoer Details

Hier vindt u meer informatie over de uitvoer van een HTTP-trigger of actie, waarmee deze gegevens worden geretourneerd:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| koppen | object | De headers van de aanvraag |
| body | object | JSON-object | Het object met de inhoud van de hoofd tekst van de aanvraag |
| statuscode | int | De status code van de aanvraag |
|||

| Statuscode | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Ongeldige aanvraag |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is een onbekende fout opgetreden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
