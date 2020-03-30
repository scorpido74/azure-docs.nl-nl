---
title: Logische apps aanroepen, triggeren of nesten
description: HTTP-eindpunten instellen voor het aanroepen, activeren of nesten van logische app-werkstromen in Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191335"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Logische apps aanbellen, activeren of nesten met HTTP-eindpunten in Azure Logic Apps

Als u uw logische app bereikbaar wilt maken via een URL, zodat uw logische app binnenkomende aanvragen van andere services kan ontvangen, u een synchrone HTTP-eindpunt als trigger op die logische app native weergeven. Wanneer u deze mogelijkheid instelt, u uw logische app ook nesten in andere logische apps, waarmee u een patroon van aanroepbare eindpunten maken.

Als u een HTTP-eindpunt wilt instellen, u een van deze triggertypen gebruiken waarmee logische apps binnenkomende aanvragen kunnen ontvangen:

* [Aanvraag](../connectors/connectors-native-reqres.md)
* [HTTP-webhook](../connectors/connectors-native-webhook.md)
* Beheerde connectortriggers met het [type ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) en kunnen binnenkomende HTTP-aanvragen ontvangen

> [!NOTE]
> In deze voorbeelden wordt de trigger van Request gebruikt, maar u elke http-trigger op basis van aanvragen gebruiken die in de vorige lijst staat. Alle principes zijn identiek van toepassing op deze andere triggertypes.

Zie Wat is Azure Logic [Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken als](../logic-apps/quickstart-create-first-logic-app-workflow.md)u nieuw bent in logische apps.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waarin u het HTTP-eindpunt als trigger wilt instellen. U beginnen met een lege logische app of een bestaande logische app waarbij u de huidige trigger wilt vervangen. Dit voorbeeld begint met een lege logische app.

## <a name="create-a-callable-endpoint"></a>Een callable endpoint maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Maak en open een lege logische app in de Logic App Designer.

   In dit voorbeeld wordt de trigger Van aanvraag gebruikt, maar u elke trigger gebruiken die binnenkomende HTTP-aanvragen kan ontvangen. Alle principes zijn identiek van toepassing op deze triggers. Zie [Binnenkomende HTTPS-oproepen ontvangen en reageren met Azure Logic Apps](../connectors/connectors-native-reqres.md)voor meer informatie over de trigger van Request.

1. Selecteer ingebouwd onder het zoekvak **ingebouwde**. Voer in het `request` zoekvak in als filter. Selecteer in de lijst triggers de optie **Wanneer een HTTP-aanvraag is ontvangen**.

   ![De trigger van Aanvraag zoeken en selecteren](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Optioneel u in het vak **JSON-schema van aanvraaginstantie** een JSON-schema invoeren waarin de payload of gegevens worden beschreven waarvan u verwacht dat de trigger deze ontvangt.

   De ontwerper gebruikt dit schema om tokens te genereren die triggeroutputs vertegenwoordigen. U vervolgens eenvoudig naar deze uitvoer verwijzen in de workflow van uw logische app. Meer informatie over [tokens die zijn gegenereerd uit JSON-schema's.](#generated-tokens)

   Voer voor dit voorbeeld het laatste schema in:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Json-schema voor de actie Aanvragen opvragen](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   U ook een JSON-schema genereren door een monsterpayload op te geven:

   1. Selecteer in de **trigger Van aanvraag** de optie **Voorbeeldpayload gebruiken om schema te genereren**.

   1. Voer in het **laadlaadveld van een monster VAN JSON** uw monsterpayload in, bijvoorbeeld:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Wanneer u klaar bent, selecteert u **Gereed**.

      In het vak **JSON-schema van aanvraaginstantie** wordt nu het gegenereerde schema weergegeven.

1. Sla uw logische app op.

   In het vak **HTTP-post in dit URL-vak** wordt nu de gegenereerde terugroep-URL weergegeven die andere services kunnen gebruiken om uw logische app te bellen en te activeren. Deze URL bevat een SAS-sleutel (Shared Access Signature), die wordt gebruikt voor verificatie, in de queryparameters, bijvoorbeeld:

   ![Gegenereerde terugroep-URL voor eindpunt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   U de HTTP-eindpunt-URL ook ophalen in het **overzichtsvenster** van uw logische app.

   1. Selecteer **Overzicht**in het menu van uw logische app .

   1. Selecteer in de sectie **Samenvatting** de optie **Triggergeschiedenis weergeven**.

      ![HTTP-eindpunt-URL ophalen vanuit Azure-portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. Kopieer onder **Callback-url [POST]** de URL:

      ![HTTP-eindpunt-URL kopiëren vanuit Azure-portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Of u de URL krijgen door deze oproep te doen:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Verwachte HTTP-methode instellen

Standaard verwacht de trigger Van aanvraag een HTTP POST-aanvraag. U echter een andere te verwachten methode opgeven, maar slechts één methode.

1. Open in de trigger Van Aanvraag de lijst **Nieuwe parameter toevoegen** en selecteer **Methode**, waarbij deze eigenschap aan de trigger wordt toegevoegd.

   ![Eigenschap 'Methode' toevoegen om te activeren](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Selecteer **in** de lijst Methode een andere methode die de trigger verwacht. U ook een aangepaste methode opgeven.

   Selecteer bijvoorbeeld de **GET-methode,** zodat u de URL van uw HTTP-eindpunt later testen.

   ![Selecteer HTTP-methode die u wilt gebruiken voor trigger](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Parameters in de URL van eindpunt accepteren

Wanneer u wilt dat de URL van het eindpunt parameters accepteert, geeft u het relatieve pad in de trigger op. U moet ook expliciet [de methode instellen](#set-method) die uw HTTP-aanvraag verwacht.

1. Open in de trigger Van Aanvraag de lijst **Nieuwe parameter toevoegen** en selecteer Relatief **pad,** waarbij deze eigenschap aan de trigger wordt toegevoegd.

   ![Eigenschap 'Relatief pad' toevoegen om te activeren](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Geef in de eigenschap **Relatief pad** het relatieve pad op voor de parameter in uw `address/{postalCode}`JSON-schema dat u wilt dat uw URL accepteert, bijvoorbeeld .

   ![Het relatieve pad voor de parameter opgeven](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Als u de parameter wilt gebruiken, zoekt en voegt u een **reactieactie** toe aan uw logische app.

   1. Selecteer onder de trigger Van Verzoek de optie **Nieuwe stap** > **Een actie toevoegen**.

   1. Voer **onder Een actie kiezen**in `response` het zoekvak in als filter.

   1. Selecteer in de lijst met acties de **actie Antwoord.**

1. Neem in de eigenschap Body van de actie **Antwoord** het token op dat de parameter vertegenwoordigt die u hebt opgegeven in het relatieve pad van de trigger.

   Stel dat u bijvoorbeeld wilt dat `Postal Code: {postalCode}`de actie Reactie wordt teruggegeven .

   Voer **Body** in de `Postal Code: ` eigenschap Lichaam een naloopruimte in. Selecteer in de lijst met dynamische inhoud die wordt weergegeven het **postcodetoken.**

   ![De opgegeven parameter toevoegen aan de antwoordbody](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   De eigenschap **Body** bevat nu de geselecteerde parameter:

   ![Voorbeeldreactiebody met parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Sla uw logische app op.

    Uw HTTP-eindpunt-URL bevat nu het relatieve pad, bijvoorbeeld:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Als u uw HTTP-eindpunt wilt testen, kopieert en `{postalCode}` plakt u de bijgewerkte URL in een ander browservenster, maar vervangt u `123456`door en drukt u op Enter.

   Uw browser toont deze tekst:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>App voor gesprekslogica via HTTP-eindpunt

Nadat u het HTTP-eindpunt hebt gemaakt, u `POST` de logische app activeren door een HTTP-verzoek naar de volledige URL van het eindpunt te verzenden. Logische apps hebben ingebouwde ondersteuning voor direct-access endpoints.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokens gegenereerd uit schema

Wanneer u een JSON-schema opgeeft in de trigger Van Aanvraag, genereert de Logic App Designer tokens voor de eigenschappen in dat schema. U deze tokens vervolgens gebruiken voor het doorgeven van gegevens via uw logica-app-workflow.

Als u bijvoorbeeld meer eigenschappen toevoegt, zoals `"suite"`, aan uw JSON-schema, zijn tokens voor deze eigenschappen beschikbaar die u in de latere stappen voor uw logische app gebruiken. Hier is het volledige JSON schema:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Geneste logische apps maken

U werkstromen in uw logische app nesten door andere logische apps toe te voegen die aanvragen kunnen ontvangen. Voer de volgende stappen uit om deze logische apps op te nemen:

1. Selecteer onder de stap waarin u een andere logische app wilt aanroepen, de optie **Nieuwe stap** > **Een actie toevoegen**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `logic apps` zoekvak in als filter. Selecteer in de lijst met acties de optie **Een werkstroom voor Logische apps kiezen**.

   ![Nest-logica-app in de huidige logica-app](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   De ontwerper toont de in aanmerking komende logische apps die u selecteren.

1. Selecteer de logische app die u wilt aanroepen vanuit uw huidige logica-app.

   ![Logische app selecteren om aan te bellen vanuit de huidige logische app](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Inhoud van een binnenkomende aanvraag verwijzen

Als het inhoudstype van `application/json`de binnenkomende aanvraag is, u verwijzen naar de eigenschappen in de binnenkomende aanvraag. Anders wordt deze inhoud behandeld als één binaire eenheid die u doorgeven aan andere API's. Als u wilt verwijzen naar deze inhoud in de werkstroom van uw logische app, moet u die inhoud eerst converteren.

Als u bijvoorbeeld inhoud doorgeeft `application/xml` die tekst heeft, u de [ `@xpath()` expressie](../logic-apps/workflow-definition-language-functions-reference.md#xpath) gebruiken om een XPath-extractie uit te voeren of de [ `@json()` expressie](../logic-apps/workflow-definition-language-functions-reference.md#json) gebruiken voor het converteren van XML naar JSON. Meer informatie over het werken met ondersteunde [inhoudstypen](../logic-apps/logic-apps-content-type.md).

Als u de uitvoer van een binnenkomende [ `@triggerOutputs` ](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)aanvraag wilt ophalen, u de expressie gebruiken. Stel dat u uitvoer hebt die er in het voorbeeld uitziet:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Als u `body` toegang wilt krijgen tot specifiek de eigenschap, u de [ `@triggerBody()` expressie](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) als snelkoppeling gebruiken.

## <a name="respond-to-requests"></a>Reageren op verzoeken

Soms wilt u reageren op bepaalde verzoeken die uw logische app activeren door inhoud terug te sturen naar de beller. Als u de statuscode, koptekst en hoofdtekst voor uw reactie wilt maken, gebruikt u de actie Reactie. Deze actie kan overal in uw logische app worden weergegeven, niet alleen aan het einde van uw werkstroom. Als uw logische app geen reactieactie bevat, reageert het HTTP-eindpunt *onmiddellijk* met de **status Geaccepteerd 202.**

Als de oorspronkelijke beller het antwoord wil ontvangen, moeten alle vereiste stappen voor het antwoord binnen de [tijd-outlimiet van](./logic-apps-limits-and-config.md) de aanvraag worden voltooid, tenzij de geactiveerde logische-app wordt aangeroepen als een geneste logische app. Als er binnen deze limiet geen antwoord wordt geretourneerd, is de binnenkomende aanvraag een time-out en ontvangt het **time-outantwoord van** de client.

Voor geneste logische apps blijft de bovenliggende logica-app wachten op een antwoord totdat alle stappen zijn voltooid, ongeacht hoeveel tijd er nodig is.

### <a name="construct-the-response"></a>Het antwoord samenstellen

In de reactiebody u meerdere kopteksten en elk type inhoud opnemen. De koptekst van deze reactie geeft bijvoorbeeld aan `application/json` dat het inhoudstype `town` van `postalCode` het antwoord is en dat de hoofdtekst waarden voor de eigenschappen en eigenschappen bevat, op basis van het JSON-schema dat eerder in dit onderwerp is beschreven voor de trigger van Request.

![Antwoordinhoud bieden voor http-reactieactie](./media/logic-apps-http-endpoint/content-for-response-action.png)

Reacties hebben de volgende eigenschappen:

| Eigenschap (weergave) | Eigenschap (JSON) | Beschrijving |
|--------------------|-----------------|-------------|
| **Statuscode** | `statusCode` | De HTTP-statuscode die u wilt gebruiken in het antwoord op de binnenkomende aanvraag. Deze code kan elke geldige statuscode zijn die begint met 2xx, 4xx of 5xx. 3xx-statuscodes zijn echter niet toegestaan. |
| **Headers** | `headers` | Een of meer kopteksten die in het antwoord moeten worden opgenomen |
| **Hoofdtekst** | `body` | Een hoofdobject dat een tekenreeks, een JSON-object of zelfs binaire inhoud kan zijn waarnaar uit een vorige stap wordt verwezen |
||||

Als u de JSON-definitie voor de actie Respons en de volledige JSON-definitie van uw logische app wilt weergeven, selecteert u op de werkbalk Logic App Designer de **codeweergave**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Vragen en antwoorden

#### <a name="q-what-about-url-security"></a>V: Hoe zit het met URL-beveiliging?

**A:** Azure genereert veilig url's voor terugroepen van logische apps met Behulp van [Shared Access Signature (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Deze handtekening wordt uitgevoerd als een queryparameter en moet worden gevalideerd voordat uw logische app kan worden uitgevoerd. Azure genereert de handtekening met behulp van een unieke combinatie van een geheime sleutel per logische app, de triggernaam en de bewerking die wordt uitgevoerd. Dus tenzij iemand toegang heeft tot de geheime logic app-toets, kan deze persoon geen geldige handtekening genereren.

> [!IMPORTANT]
> Voor productie- en beveiligingssystemen raden we u ten zeerste af om uw logische app rechtstreeks vanuit de browser te bellen om deze redenen:
>
> * De gedeelde toegangssleutel wordt weergegeven in de URL.
> * U het beveiligingsinhoudsbeleid niet beheren vanwege gedeelde domeinen voor Azure Logic Apps-klanten.

#### <a name="q-can-i-configure-http-endpoints-further"></a>V: Kan ik HTTP-eindpunten verder configureren?

**A:** Ja, HTTP-eindpunten ondersteunen meer geavanceerde configuratie via [Azure API Management](../api-management/api-management-key-concepts.md). Deze service biedt u ook de mogelijkheid om al uw API's consistent te beheren, inclusief logische apps, aangepaste domeinnamen in te stellen, meer verificatiemethoden te gebruiken en meer, bijvoorbeeld:

* [De aanvraagmethode wijzigen](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [De URL-segmenten van de aanvraag wijzigen](../api-management/api-management-transformation-policies.md#RewriteURL)
* Uw API-beheerdomeinen instellen in de [Azure-portal](https://portal.azure.com/)
* Beleid instellen om te controleren op basisverificatie

## <a name="next-steps"></a>Volgende stappen

* [Binnenkomende HTTPS-oproepen ontvangen en beantwoorden met Azure Logic Apps](../connectors/connectors-native-reqres.md)