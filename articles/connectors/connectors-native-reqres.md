---
title: Oproepen ontvangen en beantwoorden via HTTPS
description: Binnenkomende HTTPS-aanvragen van externe services verwerken met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: d65b81f18d4dcb0ee97a21a7edec885e308bd8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297289"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Binnenkomende HTTPS-verzoeken ontvangen en beantwoorden in Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de ingebouwde trigger- of reactieactie Van verzoek u geautomatiseerde taken en werkstromen maken die binnenkomende HTTPS-verzoeken ontvangen en erop reageren. U bijvoorbeeld uw logica-app hebben:

* Een HTTPS-verzoek om gegevens ontvangen en beantwoorden in een on-premises database.
* Activeer een werkstroom wanneer een externe webhook-gebeurtenis plaatsvindt.
* Een HTTPS-oproep ontvangen en beantwoorden vanuit een andere logische app.

> [!NOTE]
> De trigger van De aanvraag ondersteunt *alleen* TLS (Transport Layer Security) 1.2 voor inkomende oproepen. Uitgaande gesprekken blijven TLS 1.0, 1.1 en 1.2 ondersteunen. Zie [Het TLS 1.0-probleem oplossen](https://docs.microsoft.com/security/solving-tls1-problem)voor meer informatie.
>
> Als u SSL-handshakefouten ziet, moet u TLS 1.2 gebruiken. Voor inkomende oproepen, hier zijn de ondersteunde cipher suites:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, u [zich aanmelden voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Basiskennis over [logische apps.](../logic-apps/logic-apps-overview.md) Als u nieuw bent in logische apps, leest u [hoe u uw eerste logische app maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Trigger voor aanvragen toevoegen

Deze ingebouwde trigger maakt een handmatig aanroepbaar HTTPS-eindpunt dat *alleen* binnenkomende HTTPS-verzoeken kan ontvangen. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en wordt de logische app uitgevoerd. Zie het [triggertype van de trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) en Het activeren van de [triggermeting met HTTP-eindpunten in Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie over de onderliggende JSON-definitie van de trigger en hoe u deze trigger aanroepen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Een lege, logische app maken.

1. Nadat Logic App Designer is geopend, voert u in het zoekvak 'http-aanvraag' in als filter. Selecteer in de lijst triggers de **trigger Wanneer een HTTP-aanvraag wordt ontvangen,** de eerste stap in de werkstroom van uw logische app.

   ![Trigger van aanvragen selecteren](./media/connectors-native-reqres/select-request-trigger.png)

   De trigger Van aanvraag geeft de volgende eigenschappen weer:

   ![Trigger van aanvragen](./media/connectors-native-reqres/request-trigger.png)

   | Naam van eigenschap | Json-eigendomsnaam | Vereist | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **HTTP-BERICHT-URL** | {none} | Ja | De URL van het eindpunt die is gegenereerd nadat u de logische app hebt opgeslagen en wordt gebruikt voor het aanroepen van uw logische app |
   | **Aanvraag Body JSON Schema** | `schema` | Nee | Het JSON-schema dat de eigenschappen en waarden in de binnenkomende aanvraaginstantie beschrijft |
   |||||

1. Voer in het vak **JSON-schema van aanvraaginstantie** optioneel een JSON-schema in dat de hoofdtekst in de binnenkomende aanvraag beschrijft, bijvoorbeeld:

   ![Voorbeeld JSON-schema](./media/connectors-native-reqres/provide-json-schema.png)

   De ontwerper gebruikt dit schema om tokens te genereren voor de eigenschappen in de aanvraag. Op die manier kan uw logische app gegevens van de aanvraag ontleeden, consumeren en doorgeven via de trigger in uw werkstroom.

   Hier is het voorbeeldschema:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Wanneer u een JSON-schema invoert, toont `Content-Type` de ontwerper een herinnering om `application/json`de koptekst in uw aanvraag op te nemen en die kopwaarde in te stellen op . Zie [Inhoudstypen afhandelen](../logic-apps/logic-apps-content-type.md)voor meer informatie .

   ![Herinnering om de koptekst 'Inhoudstype' op te nemen](./media/connectors-native-reqres/include-content-type.png)

   Zo ziet deze koptekst eruit in JSON-indeling:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Als u een JSON-schema wilt genereren dat is gebaseerd op de verwachte payload (gegevens), u een hulpprogramma zoals [JSONSchema.net](https://jsonschema.net)gebruiken of u de volgende stappen volgen:

   1. Selecteer in de trigger Van aanvraag de optie **Voorbeeldpayload gebruiken om schema te genereren**.

      ![Schema genereren uit payload](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Voer de payload van het monster in en selecteer **Gereed**.

      ![Schema genereren uit payload](./media/connectors-native-reqres/enter-payload.png)

      Hier is het monster payload:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Als u extra eigenschappen wilt opgeven, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de parameters die u wilt toevoegen.

   | Naam van eigenschap | Json-eigendomsnaam | Vereist | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **Methode** | `method` | Nee | De methode die de binnenkomende aanvraag moet gebruiken om de logische app aan te roepen |
   | **Relatief pad** | `relativePath` | Nee | Het relatieve pad voor de parameter die de URL van het eindpunt van de logische app kan accepteren |
   |||||

   In dit voorbeeld wordt de eigenschap **Methode** toegevoegd:

   ![Parameter Methode toevoegen](./media/connectors-native-reqres/add-parameters.png)

   De eigenschap **Methode** wordt weergegeven in de trigger, zodat u een methode in de lijst selecteren.

   ![Methode selecteren](./media/connectors-native-reqres/select-method.png)

1. Voeg nu een andere actie toe als de volgende stap in uw werkstroom. Selecteer volgende **stap** onder de trigger, zodat u de actie vinden die u wilt toevoegen.

   U bijvoorbeeld op de aanvraag reageren door [een reactieactie toe](#add-response)te voegen, die u gebruiken om een aangepast antwoord te retourneren en wordt later in dit onderwerp beschreven.

   Uw logica-app houdt het binnenkomende verzoek slechts één minuut open. Ervan uitgaande dat uw logische app-werkstroom een reactieactie bevat, als de logische app `504 GATEWAY TIMEOUT` geen antwoord retourneert nadat deze tijd is afgelopen, retourneert uw logische app een naar de beller. Als uw logische app anders geen reactieactie bevat, retourneert uw logische app onmiddellijk een `202 ACCEPTED` antwoord op de beller.

1. Wanneer u klaar bent, slaat u uw logica-app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**. 

   Met deze stap genereert u de URL die moet worden gebruikt voor het verzenden van het verzoek dat de logische app activeert. Als u deze URL wilt kopiëren, selecteert u het kopieerpictogram naast de URL.

   ![URL om uw logische app te activeren](./media/connectors-native-reqres/generated-url.png)

1. Als u uw logische app wilt activeren, stuurt u een HTTP-bericht naar de gegenereerde URL. U bijvoorbeeld een tool zoals [Postman](https://www.getpostman.com/)gebruiken.

### <a name="trigger-outputs"></a>Triggeruitgangen

Hier vindt u meer informatie over de uitvoer van de trigger van Request:

| Json-eigendomsnaam | Gegevenstype | Beschrijving |
|--------------------|-----------|-------------|
| `headers` | Object | Een JSON-object dat de kopteksten van de aanvraag beschrijft |
| `body` | Object | Een JSON-object dat de inhoud van de hoeveelheid body van het verzoek beschrijft |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Een reactieactie toevoegen

U de actie Respons gebruiken om met een payload (gegevens) te reageren op een binnenkomend HTTPS-verzoek, maar alleen in een logische app die wordt geactiveerd door een HTTPS-verzoek. U de actie Respons op elk punt in uw werkstroom toevoegen. Zie het [actietype Reactie](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)voor meer informatie over de onderliggende JSON-definitie voor deze trigger .

Uw logica-app houdt het binnenkomende verzoek slechts één minuut open. Ervan uitgaande dat uw logische app-werkstroom een reactieactie bevat, als de logische app `504 GATEWAY TIMEOUT` geen antwoord retourneert nadat deze tijd is afgelopen, retourneert uw logische app een naar de beller. Als uw logische app anders geen reactieactie bevat, retourneert uw logische app onmiddellijk een `202 ACCEPTED` antwoord op de beller.

> [!IMPORTANT]
> Als een reactieactie deze kopteksten bevat, verwijdert Logic Apps deze koppen uit het gegenereerde antwoordbericht zonder enige waarschuwing of fout te tonen:
>
> * `Allow`
> * `Content-*`met deze `Content-Disposition`uitzonderingen: , `Content-Encoding`en`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Hoewel Logic Apps u er niet van weerhoudt logische apps op te slaan die een reactieactie met deze koppen hebben, negeert Logic Apps deze koppen.

1. Selecteer in de Logic App Designer onder de stap waarin u een reactieactie wilt toevoegen, de optie **Nieuwe stap**.

   Bijvoorbeeld met de trigger Van eerder aanvragen:

   ![Nieuwe stap toevoegen](./media/connectors-native-reqres/add-response.png)

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen deze stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer **onder Een actie kiezen**in het zoekvak 'antwoord' in als filter en selecteer de actie **Antwoord.**

   ![De actie Reactie selecteren](./media/connectors-native-reqres/select-response-action.png)

   De trigger van Aanvraag wordt in dit voorbeeld samengevouwen voor eenvoud.

1. Voeg alle waarden toe die nodig zijn voor het antwoordbericht. 

   Als u in sommige velden in de vakken klikt, wordt de lijst met dynamische inhoud geopend. U vervolgens tokens selecteren die beschikbare uitvoer uit eerdere stappen in de werkstroom vertegenwoordigen. Eigenschappen van het schema dat in het eerdere voorbeeld is opgegeven, worden nu weergegeven in de lijst met dynamische inhoud.

   Neem bijvoorbeeld voor het vak **Kopteksten** op `Content-Type` als de sleutelnaam en stel de sleutelwaarde in op `application/json` zoals eerder in dit onderwerp is vermeld. Voor het vak **Hoofdtekst** u de triggerbody-uitvoer selecteren in de lijst met dynamische inhoud.

   ![Details reactieactie](./media/connectors-native-reqres/response-details.png)

   Als u de kopteksten in JSON-indeling wilt weergeven, selecteert **u Overschakelen naar tekstweergave**.

   ![Kopteksten - Overschakelen naar tekstweergave](./media/connectors-native-reqres/switch-to-text-view.png)

   Hier vindt u meer informatie over de eigenschappen die u instellen in de actie Respons. 

   | Naam van eigenschap | Json-eigendomsnaam | Vereist | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **Statuscode** | `statusCode` | Ja | De statuscode die moet worden teruggegeven in het antwoord |
   | **Headers** | `headers` | Nee | Een JSON-object dat een of meer kopteksten beschrijft die in het antwoord moeten worden opgenomen |
   | **Hoofdtekst** | `body` | Nee | Het reactieorgaan |
   |||||

1. Als u extra eigenschappen wilt opgeven, zoals een JSON-schema voor de antwoordtekst, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de parameters die u wilt toevoegen.

1. Wanneer u klaar bent, slaat u uw logica-app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen

* [Connectors voor Logic Apps](../connectors/apis-list.md)
