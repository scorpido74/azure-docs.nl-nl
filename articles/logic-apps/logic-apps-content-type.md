---
title: Inhoudstypen verwerken
description: Meer informatie over hoe Logic Apps inhouds typen tijdens ontwerp tijd en uitvoerings tijd verwerkt
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 75d9285c4a838c2057c0f23841c3a2f465789c7c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791533"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Inhouds typen in Azure Logic Apps verwerken

Verschillende inhouds typen kunnen stromen via een logische app, zoals JSON, XML, platte bestanden en binaire gegevens. Hoewel Logic Apps alle inhouds typen ondersteunt, hebben sommige systeem eigen ondersteuning en hoeven ze geen casting of conversie in uw Logic apps te hebben. Andere typen kunnen indien nodig casts of conversie vereisen. In dit artikel wordt beschreven hoe Logic Apps inhouds typen verwerkt en hoe u deze typen correct kunt casten of omzetten wanneer dat nodig is.

Voor het bepalen van de juiste manier voor het afhandelen van inhouds typen, is Logic Apps afhankelijk van de `Content-Type` header waarde in HTTP-aanroepen, bijvoorbeeld:

* [Application/JSON](#application-json) (systeem eigen type)
* [tekst/zonder opmaak](#text-plain) (systeem eigen type)
* [Application/XML en application/octet-stream](#application-xml-octet-stream)
* [Andere inhouds typen](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps elke aanvraag met het inhouds type *Application/JSON* wordt opgeslagen en verwerkt als een JSON-object (Java script Notation). Standaard kunt u JSON-inhoud parseren zonder casting. Voor het parseren van een aanvraag met een kop met het inhouds type application/json kunt u een expressie gebruiken. In dit voor beeld wordt de waarde `dog` uit de `animal-type` matrix geretourneerd zonder casting: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Als u met JSON-gegevens werkt die geen koptekst opgeven, kunt u die gegevens hand matig naar JSON casten met behulp van de [functie JSON ()](../logic-apps/workflow-definition-language-functions-reference.md#json), bijvoorbeeld: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tokens maken voor JSON-eigenschappen

Logic Apps biedt de mogelijkheid om gebruikers vriendelijke tokens te genereren die de eigenschappen in JSON-inhoud vertegenwoordigen zodat u deze eigenschappen gemakkelijker kunt raadplegen en gebruiken in de werk stroom van uw logische app.

* **Trigger voor aanvragen**

  Wanneer u deze trigger gebruikt in de ontwerp functie voor logische apps, kunt u een JSON-schema opgeven dat de payload beschrijft die u verwacht te ontvangen. 
  De ontwerper parseert JSON-inhoud door gebruik te maken van dit schema en genereert gebruikers vriendelijke tokens die de eigenschappen in uw JSON-inhoud vertegenwoordigen. 
  U kunt deze eigenschappen vervolgens eenvoudig raadplegen en gebruiken in de werk stroom van de logische app. 
  
  Als u geen schema hebt, kunt u het schema genereren. 
  
  1. Selecteer in de trigger voor de aanvraag een **voor beeld-nettolading gebruiken om een schema te genereren**.  
  
  2. Geef onder een voor **beeld van een JSON-nettolading op of plak**een nettolading voor beeld op en kies vervolgens **gereed**. Bijvoorbeeld: 

     ![Voor beeld van JSON-nettolading opgeven](./media/logic-apps-content-type/request-trigger.png)

     Het gegenereerde schema wordt nu weer gegeven in de trigger.

     ![Voor beeld van JSON-nettolading opgeven](./media/logic-apps-content-type/generated-schema.png)

     Dit is de onderliggende definitie voor de aanvraag trigger in de code weergave-editor:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. Zorg ervoor dat u in uw aanvraag een `Content-Type`-header opneemt en stel de waarde van de header in op `application/json`.

* **JSON-actie parseren**

  Wanneer u deze actie gebruikt in de ontwerp functie voor logische apps, kunt u JSON-uitvoer parseren en gebruikers vriendelijke tokens genereren die de eigenschappen in uw JSON-inhoud vertegenwoordigen. 
  U kunt deze eigenschappen vervolgens eenvoudig raadplegen en gebruiken in de werk stroom van de logische app. Net als bij de aanvraag trigger kunt u een JSON-schema opgeven of genereren met een beschrijving van de JSON-inhoud die u wilt parseren. 
  Op die manier kunt u gemakkelijker gegevens gebruiken uit Azure Service Bus, Azure Cosmos DB, enzovoort.

  ![JSON parseren](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>tekst/zonder opmaak

Wanneer uw logische app HTTP-berichten ontvangt waarvan de `Content-Type` header is ingesteld op `text/plain`, slaat uw logische app deze berichten op in onbewerkte vorm. Als u deze berichten opneemt in volgende acties zonder Casting, worden aanvragen uitgevoerd met de `Content-Type` header ingesteld op `text/plain`. 

Als u bijvoorbeeld met een plat bestand werkt, krijgt u mogelijk een HTTP-aanvraag met de `Content-Type` header ingesteld op `text/plain` inhouds type:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Als u deze aanvraag vervolgens verzendt naar een latere actie als de hoofd tekst van een andere aanvraag, bijvoorbeeld `@body('flatfile')`, heeft die tweede aanvraag ook een `Content-Type`-header die is ingesteld op `text/plain`. Als u werkt met onbewerkte gegevens, maar geen koptekst hebt opgegeven, kunt u die gegevens hand matig naar tekst casten met behulp van de [functie String ()](../logic-apps/workflow-definition-language-functions-reference.md#string) zoals deze expressie: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Application/XML en application/octet-stream

Met Logic Apps wordt de `Content-Type` altijd in een ontvangen HTTP-aanvraag of-antwoord bewaard. Als uw logische app bijvoorbeeld inhoud ontvangt met `Content-Type` ingesteld op `application/octet-stream`, en u die inhoud opneemt in een latere actie zonder te casten, heeft de uitgaande aanvraag ook `Content-Type` ingesteld op `application/octet-stream`. Op die manier kan Logic Apps garanderen dat er geen gegevens verloren gaan tijdens het door lopen van de werk stroom. De actie status, of invoer en uitvoer, wordt echter opgeslagen in een JSON-object terwijl de status door de werk stroom wordt verplaatst. 

## <a name="converter-functions"></a>Conversie functies

Als u sommige gegevens typen wilt behouden, wordt met Logic Apps inhoud geconverteerd naar een binaire, base64-gecodeerde teken reeks met de juiste meta gegevens die zowel de `$content` payload als de `$content-type`behouden, die automatisch worden geconverteerd. 

In deze lijst wordt beschreven hoe Logic Apps inhoud converteert wanneer u deze [functies](../logic-apps/workflow-definition-language-functions-reference.md)gebruikt:

* `json()`: cast gegevens naar `application/json`
* `xml()`: cast gegevens naar `application/xml`
* `binary()`: cast gegevens naar `application/octet-stream`
* `string()`: cast gegevens naar `text/plain`
* `base64()`: inhoud converteren naar een base64-gecodeerde teken reeks
* `base64toString()`: converteert een met base64 gecodeerde teken reeks naar `text/plain`
* `base64toBinary()`: converteert een met base64 gecodeerde teken reeks naar `application/octet-stream`
* `dataUri()`: converteert een teken reeks naar een gegevens-URI
* `dataUriToBinary()`: converteert een gegevens-URI naar een binaire teken reeks
* `dataUriToString()`: converteert een gegevens-URI naar een teken reeks

Als u bijvoorbeeld een HTTP-aanvraag ontvangt waarbij `Content-Type` ingesteld op `application/xml`, zoals deze inhoud:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

U kunt deze inhoud casten met behulp van de `@xml(triggerBody())`-expressie met de functies `xml()` en `triggerBody()` en vervolgens deze inhoud later gebruiken. U kunt ook de `@xpath(xml(triggerBody()), '/CustomerName')`-expressie met de functies `xpath()` en `xml()` gebruiken. 

## <a name="other-content-types"></a>Andere inhouds typen

Logic Apps werkt met en ondersteunt andere inhouds typen, maar u moet er mogelijk voor zorgen dat u de bericht tekst hand matig hebt opgehaald door de `$content` variabele te decoderen.

Stel bijvoorbeeld dat uw logische app wordt geactiveerd door een aanvraag met het inhouds type `application/x-www-url-formencoded`. Om alle gegevens te behouden, heeft de `$content` variabele in de hoofd tekst van de aanvraag een nettolading die is gecodeerd als een base64-teken reeks:

`CustomerName=Frank&Address=123+Avenue`

Omdat de aanvraag niet tekst zonder opmaak of JSON is, wordt de aanvraag als volgt in de actie opgeslagen:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps biedt systeem eigen functies voor het verwerken van formulier gegevens, bijvoorbeeld: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

U kunt de gegevens ook hand matig openen met behulp van een expressie, zoals in dit voor beeld:

`@string(body('formdataAction'))` 

Als u wilt dat de uitgaande aanvraag dezelfde `application/x-www-url-formencoded` inhouds type-header heeft, kunt u de aanvraag toevoegen aan de hoofd tekst van de actie zonder dat u deze hoeft te casten met behulp van een expressie als `@body('formdataAction')`. Deze methode werkt echter alleen wanneer de hoofd tekst de enige para meter is in de `body` invoer. Als u de `@body('formdataAction')`-expressie in een `application/json` aanvraag probeert te gebruiken, wordt er een runtime-fout weer gegeven, omdat de hoofd tekst gecodeerd is verzonden.
