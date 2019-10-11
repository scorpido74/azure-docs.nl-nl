---
title: HTTPS-aanroepen ontvangen en erop reageren-Azure Logic Apps
description: HTTPS-aanvragen en-gebeurtenissen in realtime verwerken door gebruik te maken van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewers: klam, LADocs
manager: carmonm
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.topic: conceptual
ms.date: 10/11/2019
tags: connectors
ms.openlocfilehash: 6062ca1ce09eb243825b1fb9ae4ecb3d5ac95d1a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264346"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Binnenkomende HTTPS-aanroepen ontvangen en erop reageren met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde aanvraag-of reactie actie, kunt u geautomatiseerde taken en werk stromen maken die binnenkomende HTTPS-aanvragen ontvangen en erop reageren. U kunt bijvoorbeeld uw logische app:

* Ontvangen en reageren op een HTTPS-aanvraag voor gegevens in een on-premises data base.
* Activeer een werk stroom wanneer een externe webhook-gebeurtenis plaatsvindt.
* Ontvangen en reageren op een HTTPS-aanroep vanuit een andere logische app.

> [!NOTE]
> De trigger voor aanvragen ondersteunt *alleen* Transport Layer Security (TLS) 1,2 voor binnenkomende oproepen. Uitgaande oproepen blijven ondersteuning bieden voor TLS 1,0, 1,1 en 1,2. Als er SSL-Handshake-fouten worden weer geven, moet u ervoor zorgen dat u TLS 1,2 gebruikt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [Logic apps](../logic-apps/logic-apps-overview.md). Als u geen ervaring hebt met Logic apps, kunt u leren [hoe u uw eerste logische app maakt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Aanvraag trigger toevoegen

Deze ingebouwde trigger maakt een hand matig aanroep bare HTTPS-eind punt dat *alleen* binnenkomende HTTPS-aanvragen kan ontvangen. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en wordt de logische app uitgevoerd. Voor meer informatie over de onderliggende JSON-definitie van de trigger en hoe u deze trigger aanroept, raadpleegt u het [type trigger voor aanvragen](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) en [roept u werk stromen met http-eind punten aan in azure Logic apps](../logic-apps/logic-apps-http-endpoint.md).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Een lege, logische app maken.

1. Wanneer Logic app Designer wordt geopend, voert u in het zoekvak ' HTTP-aanvraag ' in als uw filter. Selecteer in de lijst triggers de trigger **Wanneer een HTTP-aanvraag wordt ontvangen** . Dit is de eerste stap in de werk stroom van uw logische app.

   ![Aanvraag trigger selecteren](./media/connectors-native-reqres/select-request-trigger.png)

   Met de aanvraag trigger worden deze eigenschappen weer gegeven:

   ![Trigger voor aanvragen](./media/connectors-native-reqres/request-trigger.png)

   | Naam van eigenschap | JSON-eigenschaps naam | Verplicht | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST-URL** | geen | Ja | De eind punt-URL die wordt gegenereerd na het opslaan van de logische app en wordt gebruikt voor het aanroepen van uw logische app |
   | **JSON-schema van aanvraag tekst** | `schema` | Nee | Het JSON-schema dat de eigenschappen en waarden in de hoofd tekst van de binnenkomende aanvraag beschrijft |
   |||||

1. Voer desgewenst in het vak **JSON-schema van aanvraag tekst** een JSON-schema in dat de hoofd tekst in de binnenkomende aanvraag beschrijft, bijvoorbeeld:

   ![Voor beeld van JSON-schema](./media/connectors-native-reqres/provide-json-schema.png)

   De ontwerp functie gebruikt dit schema om tokens te genereren voor de eigenschappen in de aanvraag. Op die manier kan uw logische app de gegevens van de aanvraag parseren, gebruiken en door geven via de trigger in uw werk stroom.

   Dit is het voorbeeld schema:

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

   Wanneer u een JSON-schema invoert, wordt in de ontwerp functie een herinnering weer gegeven voor de `Content-Type`-header in uw aanvraag en wordt de waarde voor de header ingesteld op `application/json`. Zie voor meer informatie [inhouds typen verwerken](../logic-apps/logic-apps-content-type.md).

   ![Herinnering voor het toevoegen van de header content-type](./media/connectors-native-reqres/include-content-type.png)

   Deze koptekst ziet er als volgt uit in de JSON-indeling:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Als u een JSON-schema wilt genereren dat is gebaseerd op de verwachte Payload (gegevens), kunt u een hulp programma zoals [JSONSchema.net](https://jsonschema.net)gebruiken, of u kunt de volgende stappen uitvoeren:

   1. Selecteer in de trigger voor de aanvraag een **voor beeld-nettolading gebruiken om een schema te genereren**.

      ![Schema genereren vanuit nettolading](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Voer de voor beeld-nettolading in en selecteer **gereed**.

      ![Schema genereren vanuit nettolading](./media/connectors-native-reqres/enter-payload.png)

      Dit is de nettolading van de steek proef:

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

1. Als u aanvullende eigenschappen wilt opgeven, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de para meters die u wilt toevoegen.

   | Naam van eigenschap | JSON-eigenschaps naam | Verplicht | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **Methode** | `method` | Nee | De methode die de inkomende aanvraag moet gebruiken om de logische app aan te roepen |
   | **Relatief pad** | `relativePath` | Nee | Het relatieve pad voor de para meter die door de eind punt-URL van de logische app kan worden geaccepteerd |
   |||||

   In dit voor beeld wordt de eigenschap **Method** toegevoegd:

   ![Methode parameter toevoegen](./media/connectors-native-reqres/add-parameters.png)

   De eigenschap **Method** wordt weer gegeven in de trigger zodat u een methode kunt selecteren in de lijst.

   ![Methode selecteren](./media/connectors-native-reqres/select-method.png)

1. Voeg nu een andere actie toe als de volgende stap in uw werk stroom. Selecteer de **volgende stap** onder de trigger zodat u de actie kunt vinden die u wilt toevoegen.

   U kunt bijvoorbeeld reageren op de aanvraag door [een reactie actie toe te voegen](#add-response), die u kunt gebruiken om een aangepast antwoord te retour neren en dit later in dit onderwerp wordt beschreven.

   Met uw logische app wordt de inkomende aanvraag voor één minuut geopend. Ervan uitgaande dat uw logische app-werk stroom een reactie actie bevat, als de logische app geen antwoord retourneert nadat deze tijd is verstreken, retourneert uw logische app een `504 GATEWAY TIMEOUT` naar de aanroeper. Als uw logische app geen reactie actie bevat, retourneert uw logische app onmiddellijk een `202 ACCEPTED`-antwoord naar de aanroeper.

1. Wanneer u klaar bent, slaat u de logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie. 

   Met deze stap wordt de URL gegenereerd die moet worden gebruikt voor het verzenden van de aanvraag die de logische app activeert. Als u deze URL wilt kopiëren, selecteert u het Kopieer pictogram naast de URL.

   ![URL die moet worden gebruikt om de logische app te activeren](./media/connectors-native-reqres/generated-url.png)

1. Als u uw logische app wilt activeren, verzendt u een HTTP POST naar de gegenereerde URL. U kunt bijvoorbeeld een hulp programma gebruiken zoals [postman](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Trigger uitvoer

Hier volgt meer informatie over de uitvoer van de aanvraag trigger:

| JSON-eigenschaps naam | Gegevenstype | Beschrijving |
|--------------------|-----------|-------------|
| `headers` | Object | Een JSON-object dat de headers van de aanvraag beschrijft |
| `body` | Object | Een JSON-object waarmee de inhoud van de hoofd tekst van de aanvraag wordt beschreven |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Een reactie actie toevoegen

U kunt de reactie actie gebruiken om te reageren met een Payload (gegevens) naar een binnenkomende HTTPS-aanvraag, maar alleen in een logische app die wordt geactiveerd door een HTTPS-aanvraag. U kunt de reactie actie op elk gewenst moment in uw werk stroom toevoegen. Zie het [actie type reactie](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)voor meer informatie over de onderliggende JSON-definitie voor deze trigger.

Met uw logische app wordt de inkomende aanvraag voor één minuut geopend. Ervan uitgaande dat uw logische app-werk stroom een reactie actie bevat, als de logische app geen antwoord retourneert nadat deze tijd is verstreken, retourneert uw logische app een `504 GATEWAY TIMEOUT` naar de aanroeper. Als uw logische app geen reactie actie bevat, retourneert uw logische app onmiddellijk een `202 ACCEPTED`-antwoord naar de aanroeper.

1. Selecteer in de ontwerp functie voor logische apps onder de stap waarin u een reactie actie wilt toevoegen de optie **nieuwe stap**.

   Bijvoorbeeld met behulp van de trigger voor aanvragen van eerder:

   ![Nieuwe stap toevoegen](./media/connectors-native-reqres/add-response.png)

   Als u een actie tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen deze stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer onder **Kies een actie**in het zoekvak het woord "antwoord" in als uw filter en selecteer de **reactie** actie.

   ![De reactie actie selecteren](./media/connectors-native-reqres/select-response-action.png)

   De trigger voor aanvragen wordt in dit voor beeld samengevouwen voor eenvoud.

1. Voeg waarden toe die vereist zijn voor het antwoord bericht. 

   In sommige velden wordt de lijst met dynamische inhoud geopend wanneer u in hun vakken klikt. Vervolgens kunt u tokens selecteren die beschik bare uitvoer van de vorige stappen in de werk stroom vertegenwoordigen. Eigenschappen uit het schema dat in het vorige voor beeld is opgegeven, worden nu weer gegeven in de lijst met dynamische inhoud.

   Bijvoorbeeld, voor het vak **kopteksten** , `Content-Type` als sleutel naam, en stel de sleutel waarde in op `application/json` zoals eerder in dit onderwerp is genoemd. Voor het vak **hoofd tekst** kunt u de uitvoer van de trigger hoofdtekst selecteren in de lijst met dynamische inhoud.

   ![Details van reactie actie](./media/connectors-native-reqres/response-details.png)

   Selecteer **overschakelen naar tekst weergave**om de kopteksten in JSON-indeling weer te geven.

   ![Headers: overschakelen naar tekst weergave](./media/connectors-native-reqres/switch-to-text-view.png)

   Hier vindt u meer informatie over de eigenschappen die u kunt instellen in de reactie actie. 

   | Naam van eigenschap | JSON-eigenschaps naam | Verplicht | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **Statuscode** | `statusCode` | Ja | De status code die in het antwoord moet worden geretourneerd |
   | **Headers** | `headers` | Nee | Een JSON-object dat een of meer headers beschrijft die in het antwoord moeten worden meegenomen |
   | **Hoofdtekst** | `body` | Nee | De antwoord tekst |
   |||||

1. Als u aanvullende eigenschappen, zoals een JSON-schema voor de antwoord tekst, wilt opgeven, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de para meters die u wilt toevoegen.

1. Wanneer u klaar bent, slaat u de logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie. 

## <a name="next-steps"></a>Volgende stappen

* [Connectors voor Logic Apps](../connectors/apis-list.md)
