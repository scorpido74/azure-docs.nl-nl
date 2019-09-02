---
title: Afhandeling van fouten en uitzonde ringen-Azure Logic Apps
description: Meer informatie over patronen voor het afhandelen van fouten en uitzonde ringen in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.date: 01/31/2018
ms.topic: article
ms.openlocfilehash: 828bea50a66b90f35843901ae2d7c703ffa58f2d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208187"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Fouten en uitzonde ringen in Azure Logic Apps afhandelen

De manier waarop elke integratie architectuur op de juiste wijze downtime of problemen verwerkt die door afhankelijke systemen worden veroorzaakt, kan een uitdaging vormen. Logic Apps biedt een topervaring voor het afhandelen van fouten en uitzonde ringen, om u te helpen bij het maken van robuuste en robuuste integraties die problemen en storingen op de juiste manier afhandelen. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Beleid opnieuw proberen

Voor de meest eenvoudige uitzonde ring en fout afhandeling kunt u een *beleid voor opnieuw proberen* gebruiken in elke actie of trigger waarbij dit wordt ondersteund. Met een beleid voor opnieuw proberen wordt aangegeven of en hoe de actie of de trigger een aanvraag opnieuw probeert wanneer de oorspronkelijke aanvraag een time-out heeft of mislukt. Dit is een aanvraag die resulteert in een 408-, 429-of 5xx-antwoord. Als er geen ander beleid voor opnieuw proberen wordt gebruikt, wordt het standaard beleid gebruikt. 

Dit zijn de beleids typen voor opnieuw proberen: 

| type | Description | 
|------|-------------| 
| **Standaard** | Met dit beleid worden Maxi maal vier nieuwe pogingen verzonden met *exponentieel toenemende* intervallen. deze worden geschaald op 7,5 seconden, maar worden tussen 5 en 45 seconden gelimiteerd. | 
| **Exponentieel interval**  | Dit beleid wacht een wille keurig interval dat is geselecteerd uit een exponentieel groeiend bereik voordat de volgende aanvraag wordt verzonden. | 
| **Vast interval**  | Met dit beleid wordt het opgegeven interval gewacht voordat de volgende aanvraag wordt verzonden. | 
| **Geen**  | De aanvraag niet opnieuw verzenden. | 
||| 

Zie [Logic apps limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#request-limits)voor meer informatie over limieten voor het beleid voor opnieuw proberen. 

### <a name="change-retry-policy"></a>Beleid voor opnieuw proberen wijzigen

Voer de volgende stappen uit om een ander beleid voor opnieuw proberen te selecteren: 

1. Open uw logische app in de ontwerp functie voor logische apps. 

2. Open de **instellingen** voor een actie of trigger.

3. Als de actie of trigger het beleid voor nieuwe pogingen ondersteunt, selecteert u het gewenste type onder **beleid voor opnieuw proberen**. 

Of u kunt het beleid voor opnieuw proberen hand matig opgeven `inputs` in de sectie voor een actie of trigger die beleids regels voor opnieuw proberen ondersteunt. Als u geen beleid voor opnieuw proberen opgeeft, gebruikt de actie het standaard beleid.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Vereist*

| Value | type | Description |
|-------|------|-------------|
| <*opnieuw proberen-beleid-type*> | Tekenreeks | Het type beleid voor opnieuw proberen dat u wilt `default`gebruiken `none`: `fixed`,,, of`exponential` | 
| <*retry-interval*> | Tekenreeks | Het interval voor nieuwe pogingen waarbij de waarde de [ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)moet gebruiken. Het minimale standaard interval is `PT5S` en het maximum `PT1D`interval. Wanneer u het beleid voor exponentiële intervallen gebruikt, kunt u verschillende minimum-en maximum waarden opgeven. | 
| <*retry-attempts*> | Integer | Het aantal nieuwe pogingen, dat tussen 1 en 90 ligt | 
||||

*Beschrijving*

| Value | type | Description |
|-------|------|-------------|
| <*minimum-interval*> | Tekenreeks | Voor het beleid voor exponentiële intervallen wordt het kleinste interval voor het wille keurig geselecteerde interval in [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximum-interval*> | Tekenreeks | Voor het beleid voor exponentiële intervallen is het grootste interval voor het wille keurig geselecteerde interval in [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Hier vindt u meer informatie over de verschillende beleids typen.

<a name="default-retry"></a>

### <a name="default"></a>Standaard

Als u geen beleid voor opnieuw proberen opgeeft, gebruikt de actie het standaard beleid, dat eigenlijk een [exponentieel interval beleid](#exponential-interval) is dat Maxi maal vier nieuwe pogingen verzendt met exponentieel toenemende intervallen die met 7,5 seconden worden geschaald. Het interval wordt tussen 5 en 45 seconden gelimiteerd. 

Hoewel niet expliciet is gedefinieerd in uw actie of trigger, is dit het standaard beleid gedraagt in een voor beeld van een HTTP-actie:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Geen

Als u wilt opgeven dat de actie of de trigger geen mislukte aanvragen opnieuw probeert uit te voeren, stelt u het `none`*type < opnieuw proberen*> in op.

### <a name="fixed-interval"></a>Vast interval

Als u wilt opgeven dat de actie of trigger het opgegeven interval moet wachten voordat de volgende aanvraag wordt verzonden, stelt u het > <*opnieuw proberen*in op `fixed`.

*Voorbeeld*

Dit beleid voor opnieuw proberen probeert het laatste nieuws twee keer na de eerste mislukte aanvraag met een vertraging van 30 seconden tussen elke poging te verkrijgen:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponentieel interval

Als u wilt opgeven dat de actie of trigger een wille keurig interval moet wachten voordat de volgende aanvraag wordt verzonden, stelt u het > < `exponential`*opnieuw proberen*in. Het wille keurig interval wordt geselecteerd uit een exponentieel groeiend bereik. U kunt eventueel ook de standaard minimum-en maximum intervallen onderdrukken door uw eigen minimum-en maximum intervallen op te geven.

**Bereiken voor wille keurige variabele**

In deze tabel ziet u hoe Logic Apps een uniforme wille keurige variabele in het opgegeven bereik genereert voor elke nieuwe poging tot en met het aantal nieuwe pogingen:

| Aantal nieuwe pogingen | Minimum interval | Maximum interval |
|--------------|------------------|------------------|
| 1 | Max (0, <*Mini maal interval*>) | min (interval, <*maximum-interval*>) |
| 2 | max(interval, <*minimum-interval*>) | min (2 * interval, <*maximum-interval*>) |
| 3 | Max (2 * interval, <*Mini maal interval*>) | min (4 * interval, <*maximum-interval*>) |
| 4 | Max (4 * interval, <*Mini maal interval*>) | min (8 * interval, <*maximum-interval*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Ondervangen en afhandelen van fouten met de eigenschap RunAfter

Met elke actie van een logische app worden de acties gedeclareerd die moeten worden voltooid voordat deze actie wordt gestart, vergelijkbaar met hoe u de volg orde van de stappen in uw werk stroom opgeeft. In een actie definitie definieert de eigenschap **runAfter** deze volg orde en is dit een object dat beschrijft welke acties en actie status de actie uitvoeren.

Standaard zijn alle acties die u toevoegt in de ontwerp functie voor logische apps zo ingesteld dat ze worden uitgevoerd na de vorige stap wanneer het resultaat van de vorige stap is **voltooid**. U kunt de waarde van **runAfter** echter aanpassen zodat acties worden gestart wanneer de vorige acties resulteren in **mislukt**, **overgeslagen**of een combi natie van deze waarden. Als u bijvoorbeeld een item wilt toevoegen aan een specifiek Service Bus onderwerp nadat een bepaalde **Insert_Row** -actie is mislukt, kunt u dit voor beeld gebruiken **runAfter** definitie:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

De eigenschap **runAfter** wordt ingesteld om te worden uitgevoerd wanneer de status van de **Insert_Row** -actie **mislukt**. Als u de actie wilt uitvoeren als de actie status **geslaagd**, **mislukt**of **overgeslagen**is, gebruikt u de volgende syntaxis:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Acties die worden uitgevoerd en voltooid nadat een vorige actie is mislukt, zijn gemarkeerd als **geslaagd**. Dit gedrag betekent dat als u alle fouten in een werk stroom hebt onderschept, de run zelf is gemarkeerd als **geslaagd**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Acties met bereiken en hun resultaten evalueren

Net als bij het uitvoeren van stappen na afzonderlijke acties met de eigenschap **runAfter** kunt u acties groeperen binnen een [bereik](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). U kunt bereiken gebruiken als u acties logisch wilt groeperen, de aggregatie status van het bereik wilt beoordelen en acties wilt uitvoeren op basis van deze status. Nadat alle acties in een bereik zijn uitgevoerd, krijgt de scope zelf een eigen status. 

Als u de status van een bereik wilt controleren, kunt u dezelfde criteria gebruiken die u gebruikt om de uitvoerings status van een logische app te controleren, zoals **geslaagd**, **mislukt**, enzovoort. 

Wanneer alle acties van het bereik slagen, is de status van het bereik standaard gemarkeerd als **geslaagd**. Als de laatste actie in een bereik als **mislukt** of afgebroken resulteert, wordt de status van het bereik gemarkeerd als **mislukt**. 

Als u uitzonde ringen in een **mislukte** scope wilt ondervangen en acties wilt uitvoeren die deze fouten verwerken, kunt u de eigenschap **RunAfter** voor die **mislukte** scope gebruiken. Op die manier, als *er* acties in de scope mislukken en u de eigenschap **runAfter** voor dat bereik gebruikt, kunt u één actie maken om fouten te ondervangen.

Zie [limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md)voor limieten voor scopes.

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Context en resultaten ophalen voor fouten

Hoewel het niet nuttig is om een bereik te bereiken, is het mogelijk dat u ook wilt weten welke acties zijn mislukt plus eventuele fouten of status codes die zijn geretourneerd.

De [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) functie biedt context over de resultaten van alle acties in een bereik. De `result()` functie accepteert één para meter, de naam van het bereik en retourneert een matrix die alle actie resultaten van binnen dat bereik bevat. Deze actie objecten bevatten dezelfde kenmerken als het `@actions()` object, zoals de begin tijd van de actie, de eind tijd, status, invoer, correlatie-id's en uitvoer. Als u context wilt verzenden voor acties die zijn mislukt binnen een bereik, kunt u eenvoudig `@result()` een expressie koppelen `runAfter` aan de eigenschap.

Als u een actie wilt uitvoeren voor elke actie in een bereik met een **mislukt** resultaat en als u de matrix met resultaten wilt filteren op de mislukte acties, kunt u een `@result()` expressie koppelen met een [**filter matrix**](../connectors/connectors-native-query.md) actie en een [**voor elke**](../logic-apps/logic-apps-control-flow-loops.md) lus. U kunt de gefilterde resultaat matrix nemen en een actie uitvoeren voor elke fout met behulp **van de voor elke** lus.

Hier volgt een voor beeld, gevolgd door een gedetailleerde uitleg die een HTTP POST-aanvraag verzendt met de antwoord tekst voor acties die zijn mislukt binnen het bereik ' My_Scope ':

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Hier volgt een gedetailleerde beschrijving van wat er in dit voor beeld gebeurt:

1. Als u het resultaat wilt ophalen uit alle acties in ' My_Scope ', maakt de **filter matrix** actie gebruik van deze filter expressie:`@result('My_Scope')`

2. De voor waarde voor de **filter matrix** is `@result()` een item waarvan de status gelijk is aan **mislukt**. Met deze voor waarde wordt de matrix met alle actie resultaten van ' My_Scope ' naar een matrix met alleen de mislukte actie resultaten gefilterd.

3. Voer **voor elke** lus een actie uit op de gefilterde *matrix* uitvoer. Met deze stap voert u een actie uit voor elk mislukt actie resultaat dat eerder is gefilterd.

   Als één actie in het bereik is mislukt, worden de acties in de lus **voor elke** herhalingslus slechts eenmaal uitgevoerd. 
   Meerdere mislukte acties veroorzaken één actie per fout.

4. Verzend een HTTP post op de tekst **van elk** item antwoord, wat de `@item()['outputs']['body']` expressie is. 

   De `@result()` shape item is hetzelfde als de `@actions()` shape en kan op dezelfde manier worden geparseerd.

5. Voeg twee aangepaste headers toe met de mislukte actie naam`@item()['name']`() en de mislukte tracking-ID (`@item()['clientTrackingId']`) van de client.

Ter referentie: Hier volgt een voor beeld van één `@result()` item, met de **naam**, de **hoofd tekst**en de **clientTrackingId** -eigenschappen die in het vorige voor beeld worden geparseerd. Buiten een **voor elke** actie `@result()` retourneert een matrix van deze objecten.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Als u andere patronen voor uitzonde ringen wilt verwerken, kunt u de eerder beschreven expressies in dit artikel gebruiken. U kunt ervoor kiezen om één actie voor het afhandelen van uitzonde ringen uit te voeren buiten het bereik dat de volledige gefilterde matrix van fouten accepteert en de **voor elke** actie verwijderen. U kunt ook andere nuttige eigenschappen van het  **\@resultaat ()** -antwoord toevoegen zoals eerder is beschreven.

## <a name="azure-diagnostics-and-metrics"></a>Azure Diagnostics en metrische gegevens

De vorige patronen zijn een uitstekende manier om fouten en uitzonde ringen binnen een uitvoering af te handelen, maar u kunt ook fouten onafhankelijk van de uitvoering identificeren en erop reageren. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) biedt een eenvoudige manier om alle werk stroom gebeurtenissen te verzenden, inclusief alle uitvoerings-en actie statussen, naar een Azure Storage account of een event hub dat is gemaakt met Azure Event hubs. 

Als u de uitvoerings status wilt evalueren, kunt u de logboeken en metrische gegevens controleren of ze publiceren in elk bewakings programma dat u wilt gebruiken. Een mogelijke mogelijkheid is om alle gebeurtenissen via Event Hubs naar [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/)te streamen. In Stream Analytics kunt u live-query's schrijven op basis van eventuele afwijkingen, gemiddelden of fouten uit de diagnostische Logboeken. U kunt Stream Analytics gebruiken om gegevens te verzenden naar andere gegevens bronnen, zoals wacht rijen, onderwerpen, SQL, Azure Cosmos DB of Power BI.

## <a name="next-steps"></a>Volgende stappen

* [Bekijk hoe een klant fout afhandeling bouwt met Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Meer Logic Apps-voor beelden en-scenario's zoeken](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
