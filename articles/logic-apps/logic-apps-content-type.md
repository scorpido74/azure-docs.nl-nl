---
title: Inhoudstypen verwerken
description: Meer informatie over het verwerken van verschillende inhoudstypen in werkstromen tijdens ontwerptijd en runtijd in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666870"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Inhoudstypen verwerken in Azure Logic Apps

Verschillende inhoudstypen kunnen door een logische app stromen, bijvoorbeeld JSON, XML, platte bestanden en binaire gegevens. Hoewel Logic Apps alle inhoudstypen ondersteunt, hebben sommige standaardondersteuning en vereisen ze geen casten of conversie in uw logische apps. Voor andere typen kan worden gecastingof conversie nodig als dat nodig is. In dit artikel wordt beschreven hoe Logic Apps omgaat met inhoudstypen en hoe u deze typen correct casten of converteren wanneer dat nodig is.

Om te bepalen op welke manier u inhoudstypen wilt verwerken, vertrouwt Logic Apps op de `Content-Type` kopwaarde in HTTP-aanroepen, bijvoorbeeld:

* [toepassing/json](#application-json) (native type)
* [tekst/effen](#text-plain) (native type)
* [toepassing/xml en toepassing/octetstream](#application-xml-octet-stream)
* [Andere inhoudstypen](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps slaat elke aanvraag op en verwerkt elke aanvraag met het *inhoudstype application/json* als een JSON-object (JavaScript Notation). Standaard u JSON-inhoud ontleden zonder casten. Als u een verzoek wilt ontleden met een koptekst met het inhoudstype 'toepassing/json', u een expressie gebruiken. In dit voorbeeld `dog` wordt `animal-type` de waarde van de array zonder cast en resultaat geretourneerd: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Als u werkt met JSON-gegevens die geen koptekst opgeven, u die gegevens handmatig naar JSON casten met behulp van de [functie json(),](../logic-apps/workflow-definition-language-functions-reference.md#json)bijvoorbeeld: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tokens maken voor JSON-eigenschappen

Logic Apps biedt u de mogelijkheid om gebruiksvriendelijke tokens te genereren die de eigenschappen in JSON-inhoud vertegenwoordigen, zodat u deze eigenschappen gemakkelijker verwijzen en gebruiken in de workflow van uw logische app.

* **Trigger van aanvragen**

  Wanneer u deze trigger gebruikt in de Logic App Designer, u een JSON-schema geven dat de payload beschrijft die u verwacht te ontvangen. 
  De ontwerper ontleden JSON-inhoud met behulp van dit schema en genereert gebruiksvriendelijke tokens die de eigenschappen in uw JSON-inhoud vertegenwoordigen. 
  U deze eigenschappen vervolgens eenvoudig verwijzen en gebruiken in de workflow van uw logische app. 
  
  Als u geen schema hebt, u het schema genereren. 
  
  1. Selecteer in de trigger Van aanvraag de optie **Voorbeeldpayload gebruiken om schema te genereren**.  
  
  2. Geef **onder Een monster JSON-payload in of plak**deze, een monsterpayload en kies vervolgens **Gereed**. Bijvoorbeeld: 

     ![Monster JSON-payload bieden](./media/logic-apps-content-type/request-trigger.png)

     Het gegenereerde schema wordt nu weergegeven in uw trigger.

     ![Monster JSON-payload bieden](./media/logic-apps-content-type/generated-schema.png)

     Hier is de onderliggende definitie voor de trigger van aanvraag in de codeweergaveeditor:

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

  3. Zorg er in uw verzoek `Content-Type` voor dat u een `application/json`koptekst opneemt en stel de waarde van de koptekst in op .

* **Parse JSON actie**

  Wanneer u deze actie gebruikt in de Logic App Designer, u JSON-uitvoer ontleden en gebruiksvriendelijke tokens genereren die de eigenschappen in uw JSON-inhoud vertegenwoordigen. 
  U deze eigenschappen vervolgens eenvoudig verwijzen en gebruiken in de workflow van uw logische app. Net als bij de trigger Van Aanvraag u een JSON-schema aanbieden of genereren waarin de JSON-inhoud wordt beschreven die u wilt ontleden. 
  Op die manier u gemakkelijker gegevens van Azure Service Bus, Azure Cosmos DB, enzovoort gebruiken.

  ![JSON parseren](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>tekst/vlakte

Wanneer uw logica-app HTTP-berichten `Content-Type` ontvangt `text/plain`waarop de koptekst is ingesteld, slaat uw logische app deze berichten op in onbewerkte vorm. Als u deze berichten opneemt in volgende acties `Content-Type` zonder casten, gaan aanvragen uit met de koptekst ingesteld op `text/plain`. 

Wanneer u bijvoorbeeld met een plat bestand werkt, krijgt u `Content-Type` mogelijk een `text/plain` HTTP-verzoek met de koptekstingesteld op inhoudstype:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Als u dit verzoek vervolgens in een latere actie verzendt als `@body('flatfile')`de instantie voor `Content-Type` een ander verzoek, `text/plain`bijvoorbeeld, heeft dat tweede verzoek ook een koptekst die is ingesteld op . Als u werkt met gegevens die platte tekst zijn, maar geen koptekst hebben opgegeven, u die gegevens handmatig naar tekst casten met de [functie string(),](../logic-apps/workflow-definition-language-functions-reference.md#string) zoals deze expressie: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>toepassing/xml en toepassing/octetstream

Logic Apps behoudt `Content-Type` altijd de in een ontvangen HTTP-aanvraag of -antwoord. Dus als uw logica-app `Content-Type` `application/octet-stream`inhoud ontvangt met ingesteld op , en u die `Content-Type` inhoud `application/octet-stream`in een latere actie opneemt zonder te casten, is het uitgaande verzoek ook ingesteld op . Op die manier kan Logic Apps garanderen dat gegevens niet verloren gaan tijdens het doorlopen van de workflow. De actiestatus, of ingangen en uitvoer, wordt echter opgeslagen in een JSON-object terwijl de status door de werkstroom wordt verplaatst. 

## <a name="converter-functions"></a>Converterfuncties

Om bepaalde gegevenstypen te behouden, converteert Logic Apps inhoud naar een binaire base64-gecodeerde tekenreeks met de juiste metagegevens die zowel de `$content` payload als de `$content-type`, die automatisch worden geconverteerd, behouden. 

In deze lijst wordt beschreven hoe Logic Apps inhoud converteert wanneer u deze [functies](../logic-apps/workflow-definition-language-functions-reference.md)gebruikt:

* `json()`: Castgegevens naar`application/json`
* `xml()`: Castgegevens naar`application/xml`
* `binary()`: Castgegevens naar`application/octet-stream`
* `string()`: Castgegevens naar`text/plain`
* `base64()`: Converteert inhoud naar een door basis64 gecodeerde tekenreeks
* `base64toString()`: converteert een met basis64 gecodeerde tekenreeks naar`text/plain`
* `base64toBinary()`: converteert een met basis64 gecodeerde tekenreeks naar`application/octet-stream`
* `dataUri()`: Converteert een tekenreeks naar een gegevens-URI
* `dataUriToBinary()`: Converteert een gegevensuri naar een binaire tekenreeks
* `dataUriToString()`: Converteert een gegevens-URI naar een tekenreeks

Als u bijvoorbeeld een HTTP-verzoek ontvangt waar `Content-Type` u bent ingesteld, `application/xml`zoals deze inhoud:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

U deze inhoud `@xml(triggerBody())` casten `xml()` met `triggerBody()` behulp van de expressie met de en functies en deze inhoud later gebruiken. U de `@xpath(xml(triggerBody()), '/CustomerName')` expressie ook `xpath()` `xml()` gebruiken met de en functies. 

## <a name="other-content-types"></a>Andere inhoudstypen

Logic Apps werkt met en ondersteunt andere inhoudstypen, maar vereist mogelijk `$content` dat u de berichttekst handmatig krijgt door de variabele te decoderen.

Stel dat uw logische app wordt geactiveerd `application/x-www-url-formencoded` door een aanvraag met het inhoudstype. Om alle gegevens te `$content` bewaren, heeft de variabele in de aanvraaginstantie een payload die is gecodeerd als een base64-tekenreeks:

`CustomerName=Frank&Address=123+Avenue`

Omdat de aanvraag geen platte tekst of JSON is, wordt de aanvraag als volgt in de actie opgeslagen:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps biedt native functies voor het verwerken van formuliergegevens, bijvoorbeeld: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

U de gegevens ook handmatig openen met een expressie zoals dit voorbeeld:

`@string(body('formdataAction'))` 

Als u wilt dat het `application/x-www-url-formencoded` uitgaande verzoek dezelfde inhoudstekst heeft, u het verzoek toevoegen aan `@body('formdataAction')`de hoofdtekst van de actie zonder dat er wordt gecast met behulp van een expressie zoals. Deze methode werkt echter alleen wanneer de behuizing `body` de enige parameter in de invoer is. Als u de `@body('formdataAction')` expressie in `application/json` een aanvraag probeert te gebruiken, krijgt u een runtime-fout omdat de hoofdtekst gecodeerd wordt verzonden.
