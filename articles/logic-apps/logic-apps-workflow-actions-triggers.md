---
title: Schemaverwijzing voor trigger- en actietypen
description: Schemanaringshandleiding voor trigger- en actietypen voor werkstroomdefinitiein Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 7e14cc00d1bd716b3e4880e585b05447d2e55e2b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257433"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Schema-naslaggids voor trigger- en actietypen in Azure Logic Apps

Deze verwijzing beschrijft de algemene typen die worden gebruikt voor het identificeren van triggers en acties in de onderliggende werkstroomdefinitie van uw logische app, die wordt beschreven en gevalideerd door de [taal voor werkstroomdefinitie](../logic-apps/logic-apps-workflow-definition-language.md). Zie de lijst onder het [overzicht Connectors](https://docs.microsoft.com/connectors/)voor het zoeken naar specifieke connectortriggers en -acties die u in uw logische apps gebruiken.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Overzicht van triggers

Elke werkstroom bevat een trigger, die de aanroepen definieert die de werkstroom instantiëren en starten. Hier zijn de algemene triggercategorieën:

* Een *pollingtrigger,* die het eindpunt van een service op regelmatige tijdstippen controleert

* Een trigger met *een push,* waarmee een abonnement op een eindpunt wordt gemaakt en een *terugbel-URL* wordt weergegeven, zodat het eindpunt de trigger kan melden wanneer de opgegeven gebeurtenis plaatsvindt of gegevens beschikbaar zijn. De trigger wacht dan op de reactie van het eindpunt voordat deze wordt afgevuurd.

Triggers hebben deze elementen op het hoogste niveau, hoewel sommige optioneel zijn:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*triggernaam*> | Tekenreeks | De naam voor de trigger | 
| <*trigger-type*> | Tekenreeks | Het triggertype zoals 'Http' of 'ApiConnection' | 
| <*trigger-inputs*> | JSON-object | De ingangen die het gedrag van de trigger definiëren | 
| <*tijdseenheid*> | Tekenreeks | De tijdseenheid die beschrijft hoe vaak de trigger wordt geactiveerd: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand" | 
| <*aantal tijdeenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie, het aantal tijdeenheden dat moet wachten totdat de trigger opnieuw wordt geactiveerd <p>Hier zijn de minimale en maximale intervallen: <p>- Maand: 1-16 maanden </br>- Dag: 1-500 dagen </br>- Uur: 1-12.000 uur </br>- Minuut: 1-72.000 minuten </br>- Tweede: 1-9.999.999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie 'Maand' is, is de herhaling elke 6 maanden. | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*array-met-voorwaarden*> | Matrix | Een array die een of meer [voorwaarden](#trigger-conditions) bevat die bepalen of de werkstroom moet worden uitgevoerd. Alleen beschikbaar voor triggers. | 
| <*runtime-config-opties*> | JSON-object | U het verloopvan `runtimeConfiguration` de trigger wijzigen door eigenschappen in te stellen. Zie [Configuratie-instellingen voor Runtime voor](#runtime-config-options)meer informatie . | 
| <*splitOn-expressie*> | Tekenreeks | Voor triggers die een array retourneren, u een expressie opgeven die [arrayitems splitst of *debatches* ](#split-on-debatch) in meerdere werkstroominstanties voor verwerking. | 
| <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . | 
|||| 

## <a name="trigger-types-list"></a>Lijst met triggertypen

Elk triggertype heeft een andere interface en ingangen die het gedrag van de trigger definiëren. 

### <a name="built-in-triggers"></a>Ingebouwde triggers

| Type trigger | Beschrijving | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Controleert of *peilt* elk eindpunt. Dit eindpunt moet voldoen aan een specifiek triggercontract met behulp van een asynchrone patroon van 202 of door een array terug te sturen. | 
| [**HTTPWebhook HTTPWebhook**](#http-webhook-trigger) | Hiermee maakt u een aanroepbaar eindpunt voor uw logische app, maar roept u de opgegeven URL aan om u te registreren of uit te schrijven. |
| [**Terugkeerpatroon**](#recurrence-trigger) | Branden op basis van een gedefinieerd schema. U een toekomstige datum en tijd instellen voor het afvuren van deze trigger. Op basis van de frequentie u ook tijden en dagen opgeven voor het uitvoeren van uw werkstroom. | 
| [**Aanvraag**](#request-trigger)  | Hiermee maakt u een aanroepbaar eindpunt voor uw logische app en wordt het ook wel een 'handmatige' trigger genoemd. Zie bijvoorbeeld [Werkstromen bellen, activeren of nesten met HTTP-eindpunten](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Beheerde API-triggers

| Type trigger | Beschrijving | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Hiermee controleert of *peilt u* een eindpunt met behulp van [door Microsoft beheerde API's](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Hiermee maakt u een aanroepbaar eindpunt voor uw logische app door [door Microsoft beheerde API's](../connectors/apis-list.md) te bellen om u te abonneren en zich af te melden. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggers - gedetailleerde naslaginformatie

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection-trigger  

Deze trigger controleert of *peilt* een eindpunt met behulp van [door Microsoft beheerde API's,](../connectors/apis-list.md) zodat de parameters voor deze trigger kunnen verschillen op basis van het eindpunt. Veel secties in deze triggerdefinitie zijn optioneel. Het gedrag van de trigger is afhankelijk van het al dan niet opnemen van secties.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Tekenreeks | De naam voor de trigger |
| <*verbindingsnaam*> | Tekenreeks | De naam voor de verbinding met de beheerde API die de werkstroom gebruikt |
| <*methodetype*> | Tekenreeks | De HTTP-methode voor communicatie met de beheerde API: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-bewerking*> | Tekenreeks | De API-bewerking die moet worden aanroepen |
| <*tijdseenheid*> | Tekenreeks | De tijdseenheid die beschrijft hoe vaak de trigger wordt geactiveerd: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand" |
| <*aantal tijdeenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie, het aantal tijdeenheden dat moet wachten totdat de trigger opnieuw wordt geactiveerd <p>Hier zijn de minimale en maximale intervallen: <p>- Maand: 1-16 maanden </br>- Dag: 1-500 dagen </br>- Uur: 1-12.000 uur </br>- Minuut: 1-72.000 minuten </br>- Tweede: 1-9.999.999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie 'Maand' is, is de herhaling elke 6 maanden. |
||||

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . | 
| <*queryparameters*> | JSON-object | Eventuele queryparameters die moeten worden opgenomen met de API-aanroep. Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanroep. | 
| <*max-runs*> | Geheel getal | Standaard worden werkstroomexemplaren tegelijkertijd uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen, raadpleegt u [De gelijktijdigheid van triggeroverschrijdende valuta](#change-trigger-concurrency). | 
| <*max-runs-wachtrij*> | Geheel getal | Wanneer in uw werkstroom al het maximum aantal exemplaren `runtimeConfiguration.concurrency.runs` wordt uitgevoerd, dat u wijzigen op basis van de eigenschap, worden eventuele nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. | 
| <*splitOn-expressie*> | Tekenreeks | Voor triggers die arrays retourneren, verwijst deze expressie naar de array die u moet gebruiken, zodat u een werkstroominstantie voor elk arrayitem maken en uitvoeren, in plaats van een lus 'voor elke' lus te gebruiken. <p>Deze expressie vertegenwoordigt bijvoorbeeld een item in de array dat is geretourneerd in de inhoud van de hoofdtekst van de trigger:`@triggerbody()?['value']` |
| <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . |
||||

*Uitvoer*
 
| Element | Type | Beschrijving |
|---------|------|-------------|
| Headers | JSON-object | De kopteksten van het antwoord |
| body | JSON-object | Het lichaam van de reactie |
| statuscode | Geheel getal | De statuscode van het antwoord |
|||| 

*Voorbeeld*

Met deze triggerdefinitie wordt elke dag in het postvak IN gecontroleerd op e-mail voor een Office 365 Outlook-account:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook trigger

Deze trigger stuurt een abonnementsaanvraag naar een eindpunt met behulp van een [door Microsoft beheerde API,](../connectors/apis-list.md)biedt een *terugroep-URL* waar het eindpunt een antwoord kan verzenden en wacht tot het eindpunt reageert. Zie [Eindpuntabonnementen voor](#subscribe-unsubscribe)meer informatie .

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*verbindingsnaam*> | Tekenreeks | De naam voor de verbinding met de beheerde API die de werkstroom gebruikt | 
| <*body-inhoud*> | JSON-object | Alle inhoud van berichten die als payload naar de beheerde API moet worden verzonden | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . | 
| <*queryparameters*> | JSON-object | Alle queryparameters die moeten worden opgenomen met de API-aanroep <p>Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanroep. | 
| <*max-runs*> | Geheel getal | Standaard worden werkstroomexemplaren tegelijkertijd uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen, raadpleegt u [De gelijktijdigheid van triggeroverschrijdende valuta](#change-trigger-concurrency). | 
| <*max-runs-wachtrij*> | Geheel getal | Wanneer in uw werkstroom al het maximum aantal exemplaren `runtimeConfiguration.concurrency.runs` wordt uitgevoerd, dat u wijzigen op basis van de eigenschap, worden eventuele nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. | 
| <*splitOn-expressie*> | Tekenreeks | Voor triggers die arrays retourneren, verwijst deze expressie naar de array die u moet gebruiken, zodat u een werkstroominstantie voor elk arrayitem maken en uitvoeren, in plaats van een lus 'voor elke' lus te gebruiken. <p>Deze expressie vertegenwoordigt bijvoorbeeld een item in de array dat is geretourneerd in de inhoud van de hoofdtekst van de trigger:`@triggerbody()?['value']` |
| <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . | 
|||| 

*Voorbeeld*

Deze triggerdefinitie is geabonneerd op de Office 365 Outlook API, biedt een callback-URL naar het API-eindpunt en wacht tot het eindpunt reageert wanneer een nieuwe e-mail binnenkomt.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP-trigger

Deze trigger stuurt een aanvraag naar het opgegeven HTTP- of HTTPS-eindpunt op basis van het opgegeven herhalingsschema. De trigger controleert vervolgens het antwoord om te bepalen of de werkstroom wordt uitgevoerd.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `method` | <*methodetype*> | Tekenreeks | De methode die moet worden gebruikt voor het verzenden van de uitgaande aanvraag: "GET", "PUT", "POST", "PATCH" of "DELETE" |
| `uri` | <*HTTP-of-HTTPS-EINDPUNT-URL*> | Tekenreeks | De HTTP- of HTTPS-eindpunt-URL waar u de uitgaande aanvraag wilt verzenden. Maximale tekenreeksgrootte: 2 KB <p>Voor een Azure-service of -bron bevat deze URI-syntaxis de resource-id en het pad naar de bron die u wilt openen. |
| `frequency` | <*tijdseenheid*> | Tekenreeks | De tijdseenheid die beschrijft hoe vaak de trigger wordt geactiveerd: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand" |
| `interval` | <*aantal tijdeenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie, het aantal tijdeenheden dat moet wachten totdat de trigger opnieuw wordt geactiveerd <p>Hier zijn de minimale en maximale intervallen: <p>- Maand: 1-16 maanden </br>- Dag: 1-500 dagen </br>- Uur: 1-12.000 uur </br>- Minuut: 1-72.000 minuten </br>- Tweede: 1-9.999.999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie 'Maand' is, is de herhaling elke 6 maanden. |
|||||

*Optionele*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `headers` | <*header-inhoud*> | JSON-object | Kopteksten die u bij de aanvraag moet opnemen <p>U bijvoorbeeld de taal en het type instellen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*queryparameters*> | JSON-object | Alle queryparameters die u in de aanvraag moet gebruiken <p>Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanvraag. |
| `body` | <*body-inhoud*> | JSON-object | De inhoud van het bericht te verzenden als payload met het verzoek |
| `authentication` | <*verificatie-type-en-eigenschapswaarden*> | JSON-object | Het verificatiemodel dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [Verificatie toevoegen aan uitgaande gesprekken voor](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)meer informatie . Buiten Scheduler `authority` wordt de eigenschap ondersteund. Wanneer deze niet is `https://management.azure.com/`opgegeven, is de standaardwaarde, maar u een andere waarde gebruiken. |
| `retryPolicy` > `type` | <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . |
| `runs` | <*max-runs*> | Geheel getal | Standaard worden werkstroomexemplaren tegelijkertijd uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen, raadpleegt u [De gelijktijdigheid van triggeroverschrijdende valuta](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*max-runs-wachtrij*> | Geheel getal | Wanneer in uw werkstroom al het maximum aantal exemplaren `runtimeConfiguration.concurrency.runs` wordt uitgevoerd, dat u wijzigen op basis van de eigenschap, worden eventuele nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. |
| `operationOptions` | <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . |
|||||

*Uitvoer*

| Element | Type | Beschrijving |
|---------|------|-------------| 
| Headers | JSON-object | De kopteksten van het antwoord | 
| body | JSON-object | Het lichaam van de reactie | 
| statuscode | Geheel getal | De statuscode van het antwoord | 
|||| 

*Vereisten voor binnenkomende aanvragen*

Om goed te kunnen werken met uw logische app, moet het eindpunt voldoen aan een specifiek triggerpatroon of contract en deze eigenschappen herkennen:  
  
| Antwoord | Vereist | Beschrijving | 
|----------|----------|-------------| 
| Statuscode | Ja | De statuscode "200 OK" start een run. Een andere statuscode start geen run. | 
| Koptekst opnieuw proberen | Nee | Het aantal seconden totdat uw logische app het eindpunt opnieuw peilt | 
| Locatiekoptekst | Nee | De URL die u moet aanroepen bij het volgende peilingsinterval. Als dit niet is opgegeven, wordt de oorspronkelijke URL gebruikt. | 
|||| 

*Voorbeeldgedrag voor verschillende aanvragen*

| Statuscode | Opnieuw proberen na | Gedrag | 
|-------------|-------------|----------|
| 200 | {none} | Voer de werkstroom uit en controleer opnieuw op meer gegevens na de gedefinieerde herhaling. | 
| 200 | 10 seconden | Voer de werkstroom uit en controleer na 10 seconden opnieuw op meer gegevens. |  
| 202 | 60 seconden | Activeer de werkstroom niet. De volgende poging gebeurt in een minuut, onder voorbehoud van de gedefinieerde herhaling. Als de gedefinieerde herhaling minder dan één minuut is, heeft de retry-after-header voorrang. Anders wordt de gedefinieerde herhaling gebruikt. | 
| 400 | {none} | Slecht verzoek, voer de workflow niet uit. Als `retryPolicy` er geen optie is gedefinieerd, wordt het standaardbeleid gebruikt. Nadat het aantal nieuwe pogingen is bereikt, controleert de trigger opnieuw op gegevens na de gedefinieerde herhaling. | 
| 500 | {none}| Serverfout, voer de werkstroom niet uit. Als `retryPolicy` er geen optie is gedefinieerd, wordt het standaardbeleid gebruikt. Nadat het aantal nieuwe pogingen is bereikt, controleert de trigger opnieuw op gegevens na de gedefinieerde herhaling. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook-trigger  

Deze trigger maakt uw logische app aanroepbaar door een eindpunt te maken dat een abonnement kan registreren door de opgegeven URL van eindpunt aan te roepen. Wanneer u deze trigger maakt in uw werkstroom, wordt met een uitgaande aanvraag gebeld om het abonnement te registreren. Op die manier kan de trigger beginnen te luisteren naar gebeurtenissen. Wanneer een bewerking deze trigger ongeldig maakt, wordt er automatisch gebeld om het abonnement op te zeggen. Zie [Eindpuntabonnementen voor](#subscribe-unsubscribe)meer informatie .

U ook [asynchrone limieten](#asynchronous-limits) opgeven voor een **HTTPWebhook-trigger.** Het gedrag van de trigger is afhankelijk van de secties die u gebruikt of weglaat.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Sommige waarden, zoals <*>,* zijn beschikbaar `"subscribe"` voor `"unsubscribe"` zowel de en objecten.

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*methodetype*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor de abonnementsaanvraag: 'GET', 'PUT', 'POST', 'PATCH' of 'VERWIJDEREN' | 
| <*ENDPOINT-subscribe-URL*> | Tekenreeks | De URL van het eindpunt waar de abonnementsaanvraag moet worden verzonden | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*methodetype*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor de annuleringsaanvraag: 'GET', 'PUT', 'POST', 'PATCH' of 'VERWIJDEREN' | 
| <*endpoint-unsubscribe-URL*> | Tekenreeks | De URL van het eindpunt waar de annuleringsaanvraag moet worden verzonden | 
| <*body-inhoud*> | Tekenreeks | Alle berichtinhoud die u in het abonnement of annuleringsverzoek wilt verzenden | 
| <*verificatietype*> | JSON-object | Het verificatiemodel dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [Verificatie toevoegen aan uitgaande gesprekken voor](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)meer informatie . |
| <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . | 
| <*max-runs*> | Geheel getal | Standaard worden werkstroomexemplaren allemaal tegelijk uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen, raadpleegt u [De gelijktijdigheid van triggeroverschrijdende valuta](#change-trigger-concurrency). | 
| <*max-runs-wachtrij*> | Geheel getal | Wanneer in uw werkstroom al het maximum aantal exemplaren `runtimeConfiguration.concurrency.runs` wordt uitgevoerd, dat u wijzigen op basis van de eigenschap, worden eventuele nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. | 
| <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . | 
|||| 

*Uitvoer* 

| Element | Type | Beschrijving |
|---------|------|-------------| 
| Headers | JSON-object | De kopteksten van het antwoord | 
| body | JSON-object | Het lichaam van de reactie | 
| statuscode | Geheel getal | De statuscode van het antwoord | 
|||| 

*Voorbeeld*

Deze trigger maakt een abonnement op het opgegeven eindpunt, biedt een unieke terugbel-URL en wacht op nieuw gepubliceerde technologieartikelen.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Recidieftrigger  

Deze trigger wordt uitgevoerd op basis van het opgegeven herhalingsschema en biedt een eenvoudige manier voor het maken van een regelmatig lopende werkstroom.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*tijdseenheid*> | Tekenreeks | De tijdseenheid die beschrijft hoe vaak de trigger wordt geactiveerd: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand" | 
| <*aantal tijdeenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie, het aantal tijdeenheden dat moet wachten totdat de trigger opnieuw wordt geactiveerd <p>Hier zijn de minimale en maximale intervallen: <p>- Maand: 1-16 maanden </br>- Dag: 1-500 dagen </br>- Uur: 1-12.000 uur </br>- Minuut: 1-72.000 minuten </br>- Tweede: 1-9.999.999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie 'Maand' is, is de herhaling elke 6 maanden. | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*start-date-time-met-formaat-YYYY-MM-DDThh:mm:ss*> | Tekenreeks | De begindatum en -tijd in deze indeling: <p>YYYY-MM-DDThh:mm:ss als u een tijdzone opgeeft <p>-of- <p>YYYY-MM-DDThh:mm:ssZ als u geen tijdzone opgeeft <p>Als u bijvoorbeeld 18 september 2017 om 14:00 uur wilt, geeft u bijvoorbeeld '2017-09-18T14:00:00'- op en geeft u een tijdzone op zoals 'Pacific Standard Time' of geeft u '2017-09-18T14:00:00Z' op zonder tijdzone. <p>**Let op:** Deze begintijd heeft een maximum van 49 jaar in de toekomst en moet de [ISO 8601-datumtijdspecificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [utc-datumtijdnotatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)volgen, maar zonder [utc-offset](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijdzone opgeeft, moet u de letter 'Z' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de gelijkwaardige [nautische tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de begintijd de eerste gebeurtenis, terwijl voor complexe schema's de trigger niet eerder dan de begintijd wordt geactiveerd. Zie Regelmatig lopende taken maken [en plannen](../connectors/connectors-native-recurrence.md)voor meer informatie over begindatums en -tijden. | 
| <*tijdzone*> | Tekenreeks | Geldt alleen wanneer u een begintijd opgeeft omdat deze trigger geen [UTC-verschuiving](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Geef de tijdzone op die u wilt toepassen. | 
| <*een-of-meer-uur-merken*> | Gehele of gehele array | Als u 'Dag' of 'Week' opgeven voor `frequency`, u een of meer gehele getallen van 0 tot 23, gescheiden door komma's, opgeven als de uren van de dag waarop u de werkstroom wilt uitvoeren. <p>Als u bijvoorbeeld '10', '12' en '14' opgeeft, krijgt u 10.00 uur, 12.00 uur en 14.00 uur als uurmarkeringen. | 
| <*een-of-meer-minuut-merken*> | Gehele of gehele array | Als u 'Dag' of 'Week' opgeven voor `frequency`, u een of meer gehele getallen van 0 tot 59, gescheiden door komma's, opgeven als de minuten van het uur waarop u de werkstroom wilt uitvoeren. <p>U bijvoorbeeld '30' opgeven als het minutenteken en met het vorige voorbeeld voor uren van de dag, krijgt u 10:30, 12:30 uur en 14:30 uur. | 
| weekDays | Tekenreeks- of tekenreeksarray | Als u 'Week' `frequency`opgeeft voor, u een of meer dagen opgeven, gescheiden door komma's, wanneer u de werkstroom wilt uitvoeren: 'Maandag', 'dinsdag', 'woensdag', 'donderdag', 'vrijdag', 'zaterdag' en 'zondag'. | 
| <*max-runs*> | Geheel getal | Standaard worden werkstroomexemplaren allemaal tegelijk uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen, raadpleegt u [De gelijktijdigheid van triggeroverschrijdende valuta](#change-trigger-concurrency). | 
| <*max-runs-wachtrij*> | Geheel getal | Wanneer in uw werkstroom al het maximum aantal exemplaren `runtimeConfiguration.concurrency.runs` wordt uitgevoerd, dat u wijzigen op basis van de eigenschap, worden eventuele nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. | 
| <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . | 
|||| 

*Voorbeeld 1*

Deze basisrecidieftrigger wordt dagelijks uitgevoerd:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Voorbeeld 2*

U een begindatum en -tijd opgeven voor het afvuren van de trigger. Deze recidieftrigger begint op de opgegeven datum en wordt vervolgens dagelijks geactiveerd:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Voorbeeld 3*

Deze recidieftrigger begint op 9 september 2017 om 14:00 uur en wordt wekelijks elke maandag om 10:30, 12:30 uur en 14:30 pacific Standard Time geactiveerd:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Zie [Taken maken en plannen](../connectors/connectors-native-recurrence.md)voor meer informatie en voorbeelden voor deze trigger .

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger van aanvragen

Deze trigger maakt uw logische app aanroepbaar door een eindpunt te maken dat binnenkomende aanvragen kan accepteren. Geef voor deze trigger een JSON-schema op dat de payload of ingangen beschrijft en valideert die de trigger ontvangt van de binnenkomende aanvraag. Het schema maakt triggereigenschappen ook gemakkelijker te verwijzen uit latere acties in de werkstroom.

Als u deze trigger wilt `listCallbackUrl` aanroepen, moet u de API gebruiken die wordt beschreven in de [API VOOR Werkstroomservice REST.](https://docs.microsoft.com/rest/api/logic/workflows) Zie [Werkstromen bellen, activeren of nesten met HTTP-eindpunten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie over het gebruik van deze trigger als HTTP-eindpunt.

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*eigenschappennaam*> | Tekenreeks | De naam van een eigenschap in het JSON-schema, waarin de payload wordt beschreven | 
| <*eigenschapstype*> | Tekenreeks | Het type van de accommodatie | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*methodetype*> | Tekenreeks | De methode die binnenkomende aanvragen moeten gebruiken om uw logische app te bellen: 'GET', 'PUT', 'POST', 'PATCH', 'Delete' |
| <*relatieve-pad-voor-geaccepteerde parameter*> | Tekenreeks | Het relatieve pad voor de parameter die de URL van uw eindpunt kan accepteren | 
| <*vereiste eigenschappen*> | Matrix | Een of meer eigenschappen waarvoor waarden nodig zijn | 
| <*max-runs*> | Geheel getal | Standaard worden werkstroomexemplaren allemaal tegelijk uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen, raadpleegt u [De gelijktijdigheid van triggeroverschrijdende valuta](#change-trigger-concurrency). | 
| <*max-runs-wachtrij*> | Geheel getal | Wanneer in uw werkstroom al het maximum aantal exemplaren `runtimeConfiguration.concurrency.runs` wordt uitgevoerd, dat u wijzigen op basis van de eigenschap, worden eventuele nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. | 
| <*bewerkingsoptie*> | Tekenreeks | U het standaardgedrag `operationOptions` wijzigen door de eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . | 
|||| 

*Voorbeeld*

Deze trigger geeft aan dat een binnenkomende aanvraag de HTTP POST-methode moet gebruiken om de trigger aan te roepen en bevat een schema dat invoer van de binnenkomende aanvraag valideert:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Triggervoorwaarden

Voor elke trigger en alleen triggers u een array opnemen die een of meer expressies bevat voor voorwaarden die bepalen of de werkstroom moet worden uitgevoerd. Als u `conditions` de eigenschap wilt toevoegen aan een trigger in uw werkstroom, opent u uw logische app in de codeweergaveeditor.

U bijvoorbeeld opgeven dat een trigger alleen wordt geactiveerd wanneer een website een interne serverfout retourneert door te verwijzen naar de statuscode van de trigger in de `conditions` eigenschap:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Standaard wordt een trigger alleen geactiveerd na het krijgen van een "200 OK"-reactie. Wanneer een expressie verwijst naar de statuscode van een trigger, wordt het standaardgedrag van de trigger vervangen. Dus als u wilt dat de trigger wordt geactiveerd voor meer dan één statuscode, zoals de statuscode '200' en '201', moet u deze uitdrukking als voorwaarde opnemen:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Meerdere uitvoeringen activeren

Als de trigger een array retourneert die uw logische app kan verwerken, kan het soms te lang duren voordat een lus 'voor elke' wordt verwerkt om elk arrayitem te verwerken. In plaats daarvan u de eigenschap **SplitOn** in uw trigger gebruiken om *de array te debatcheren.* Debatching splitst de arrayitems op en start een nieuwe werkstroominstantie die voor elk arrayitem wordt uitgevoerd. Deze benadering is bijvoorbeeld handig wanneer u een eindpunt wilt peilen waarmee meerdere nieuwe items tussen de polling-intervallen kunnen worden geretourneerd. Zie [Limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)voor het maximum aantal matrixitems dat **SplitOn** in één logische app kan uitvoeren. 

> [!NOTE]
> U **SplitOn** niet gebruiken met een synchrone responspatroon. Elke werkstroom die **SplitOn** gebruikt en een reactieactie `202 ACCEPTED` bevat, wordt asynchroon uitgevoerd en stuurt onmiddellijk een antwoord.
>
> Wanneer trigger gelijktijdigheid is ingeschakeld, wordt de [SplitOn-limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) aanzienlijk verlaagd. Als het aantal items deze limiet overschrijdt, wordt de spliton-mogelijkheid uitgeschakeld.
 
Als het Swagger-bestand van de trigger een payload beschrijft die een array is, wordt de eigenschap **SplitOn** automatisch aan uw trigger toegevoegd. Voeg anders deze eigenschap toe in de reactiepayload met de array die u wilt debatcheren.

*Voorbeeld*

Stel dat u een API hebt die dit antwoord retourneert: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

Uw logische app heeft alleen de `Rows`inhoud van de array in nodig, zodat u een trigger zoals dit voorbeeld maken:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Als u `SplitOn` de opdracht gebruikt, u de eigenschappen die zich buiten de array bevinden niet krijgen. Dus voor dit voorbeeld u `status` de eigenschap niet krijgen in het antwoord dat wordt geretourneerd uit de API.
> 
> Om een fout `Rows` te voorkomen als de eigenschap `?` niet bestaat, wordt in dit voorbeeld de operator gebruikt.

Uw werkstroomdefinitie `@triggerBody().name` kan nu `name` worden gebruikt `"customer-name-one"` om de `"customer-name-two"` waarden te krijgen, die van de eerste run en vanaf de tweede run zijn. Dus, uw trigger uitgangen zien eruit als de volgende voorbeelden:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Overzicht van acties

Azure Logic Apps biedt verschillende actietypen - elk met verschillende ingangen die het unieke gedrag van een actie definiëren. Acties hebben deze elementen op hoog niveau, hoewel sommige optioneel zijn:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|
| <*actienaam*> | Tekenreeks | De naam voor de actie | 
| <*actietype*> | Tekenreeks | Het actietype, bijvoorbeeld 'Http' of 'ApiConnection'| 
| <*invoernaam*> | Tekenreeks | De naam voor een invoer die het gedrag van de actie definieert | 
| <*inputwaarde*> | Verschillende | De invoerwaarde, die een tekenreeks, geheel getal, JSON-object kan zijn, enzovoort | 
| <*vorige-trigger-of-action-status*> | JSON-object | De naam en de resulterende status voor de trigger of actie die onmiddellijk moet worden uitgevoerd voordat deze huidige actie kan worden uitgevoerd | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|
| <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie Beleid opnieuw proberen voor meer informatie. | 
| <*runtime-config-opties*> | JSON-object | Voor sommige acties u het gedrag van de `runtimeConfiguration` actie tijdens het uitvoeren wijzigen door eigenschappen in te stellen. Zie [Configuratie-instellingen voor Runtime voor](#runtime-config-options)meer informatie . | 
| <*bewerkingsoptie*> | Tekenreeks | Voor sommige acties u het standaardgedrag wijzigen door de `operationOptions` eigenschap in te stellen. Zie [Bewerkingsopties](#operation-options)voor meer informatie . | 
|||| 

## <a name="action-types-list"></a>Lijst met actietypen

Hier volgen enkele veelgebruikte actietypen: 

* [Ingebouwde actietypen](#built-in-actions) zoals deze voorbeelden en meer: 

  * [**HTTP**](#http-action) voor het aanroepen van eindpunten via HTTP of HTTPS

  * [**Reactie**](#response-action) voor het reageren op verzoeken

  * [**JavaScript-code uitvoeren**](#run-javascript-code) voor het uitvoeren van JavaScript-codefragmenten

  * [**Functie**](#function-action) voor het aanroepen van Azure-functies

  * Acties voor gegevensbewerking, zoals [**Deelnemen,**](#join-action) [**Opstellen,**](#compose-action) [**Tabel,**](#table-action) [**Selecteren**](#select-action)en anderen die gegevens maken of transformeren uit verschillende ingangen

  * [**Werkstroom**](#workflow-action) voor het aanroepen van een andere logische app-werkstroom

* [Beheerde API-actietypen](#managed-api-actions) zoals [**ApiConnection**](#apiconnection-action) en [**ApiConnectionWebHook**](#apiconnectionwebhook-action) die verschillende connectors en API's aanroepen die worden beheerd door Microsoft, bijvoorbeeld Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub en meer

* [Werkstroomactietypen beheren,](#control-workflow-actions) zoals [**Als**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action)en [**Until**](#until-action), die andere acties bevatten en u helpen bij het organiseren van werkstroomuitvoering

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ingebouwde acties

| Actietype | Beschrijving | 
|-------------|-------------| 
| [**Opstellen**](#compose-action) | Hiermee maakt u één uitvoer van ingangen, die verschillende typen kunnen hebben. | 
| [**JavaScript-code uitvoeren**](#run-javascript-code) | JavaScript-codefragmenten uitvoeren die binnen specifieke criteria passen. Zie [Codefragmenten toevoegen en uitvoeren met inlinecode](../logic-apps/logic-apps-add-run-inline-code.md)voor codevereisten en meer informatie. |
| [**Functie**](#function-action) | Roept een Azure-functie aan. | 
| [**HTTP**](#http-action) | Roept een HTTP-eindpunt aan. | 
| [**Join**](#join-action) | Hiermee maakt u een tekenreeks van alle items in een array en worden deze items gescheiden met een opgegeven scheidingstekenteken. | 
| [**Parse JSON**](#parse-json-action) | Hiermee maakt u gebruiksvriendelijke tokens van eigenschappen in JSON-inhoud. U deze eigenschappen vervolgens verwijzen door de tokens op te nemen in uw logische app. | 
| [**Query’s uitvoeren**](#query-action) | Hiermee maakt u een array van items in een andere array op basis van een voorwaarde of filter. | 
| [**Reactie**](#response-action) | Hiermee maakt u een antwoord op een binnenkomend gesprek of verzoek. | 
| [**Selecteer**](#select-action) | Hiermee maakt u een array met JSON-objecten door items uit een andere array te transformeren op basis van de opgegeven kaart. | 
| [**Tabel**](#table-action) | Hiermee maakt u een CSV- of HTML-tabel op basis van een array. | 
| [**Beëindigen**](#terminate-action) | Hiermee wordt een actief actieve werkstroom gestopt. | 
| [**Wait**](#wait-action) | Hiermee wordt uw werkstroom onderbroken voor een bepaalde duur of tot de opgegeven datum en tijd. | 
| [**Werkstroom**](#workflow-action) | Nests een werkstroom in een andere werkstroom. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Beheerde API-acties

| Actietype | Beschrijving | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Roept een [HTTP-eindpunt](../connectors/apis-list.md)aan met behulp van een door Microsoft beheerde API . | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Werkt als HTTP Webhook, maar maakt gebruik van een [door Microsoft beheerde API.](../connectors/apis-list.md) | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Werkstroomacties beheren

Met deze acties u de uitvoering van de werkstroom beheren en andere acties opnemen. Van buiten een controlewerkstroomactie u rechtstreeks verwijzen naar acties binnen die werkstroomactie beheren. Als u bijvoorbeeld een `Http` actie in een bereik `@body('Http')` hebt, u de expressie overal in de werkstroom verwijzen. Acties die binnen een controlewerkstroomactie bestaan, kunnen echter alleen andere acties 'uitvoeren' die zich in dezelfde controlewerkstroomstructuur bevinden.

| Actietype | Beschrijving | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Voer dezelfde acties uit in een lus voor elk item in een array. | 
| [**Als**](#if-action) | Acties uitvoeren op basis van de vraag of de opgegeven voorwaarde waar of onwaar is. | 
| [**Scope**](#scope-action) | Acties uitvoeren op basis van de groepsstatus van een reeks acties. | 
| [**Switch**](#switch-action) | Voer acties uit die zijn georganiseerd in gevallen waarin waarden uit expressies, objecten of tokens overeenkomen met de waarden die per geval zijn opgegeven. | 
| [**Tot**](#until-action) | Acties in een lus uitvoeren totdat de opgegeven voorwaarde waar is. | 
|||  

## <a name="actions---detailed-reference"></a>Acties - Gedetailleerde referentie

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection-actie

Met deze actie wordt een [HTTP-aanvraag](../connectors/apis-list.md) naar een door Microsoft beheerde API gestuurd en is informatie over de API en parameters vereist, plus een verwijzing naar een geldige verbinding. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actienaam*> | Tekenreeks | De naam van de actie die door de connector wordt geboden | 
| <*api-naam*> | Tekenreeks | De naam van de door Microsoft beheerde API die wordt gebruikt voor de verbinding | 
| <*methodetype*> | Tekenreeks | De HTTP-methode voor het aanroepen van de API: "GET", "PUT", "POST", "PATCH" of "DELETE" | 
| <*api-bewerking*> | Tekenreeks | De API-bewerking die moet worden aanroepen | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*eigenschappen met andere actie-specifieke invoer*> | JSON-object | Alle andere invoereigenschappen die van toepassing zijn op deze specifieke actie | 
| <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . | 
| <*queryparameters*> | JSON-object | Eventuele queryparameters die moeten worden opgenomen met de API-aanroep. <p>Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanroep. | 
| <*eigenschappen met andere actie-specifieke eigenschappen*> | JSON-object | Alle andere eigenschappen die van toepassing zijn op deze specifieke actie | 
|||| 

*Voorbeeld*

In deze definitie wordt de actie **Een e-mail verzenden** voor Office 365 Outlook beschreven, een door Microsoft beheerde API: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook actie

Met deze actie wordt een abonnementsaanvraag via HTTP naar een eindpunt verzonden met behulp van een [door Microsoft beheerde API,](../connectors/apis-list.md)biedt u een *terugroep-URL* waar het eindpunt een antwoord kan verzenden en wacht tot het eindpunt kan reageren. Zie [Eindpuntabonnementen voor](#subscribe-unsubscribe)meer informatie .

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Sommige waarden, zoals <*>,* zijn beschikbaar `"subscribe"` voor `"unsubscribe"` zowel de en objecten.

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actienaam*> | Tekenreeks | De naam van de actie die door de connector wordt geboden | 
| <*methodetype*> | Tekenreeks | De HTTP-methode om te gebruiken voor het abonneren of afmelden van een eindpunt: "GET", "PUT", "POST", "PATCH" of "DELETE" | 
| <*api-subscribe-URL*> | Tekenreeks | De URI die moet worden gebruikt om u te abonneren op de API | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | Tekenreeks | De URI die moet worden gebruikt voor het afmelden van de API | 
| <*header-inhoud*> | JSON-object | Kopteksten die u wilt verzenden <p>U bijvoorbeeld de taal en het type instellen op een verzoek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-inhoud*> | JSON-object | Alle inhoud van berichten die u in het verzoek wilt verzenden | 
| <*verificatietype*> | JSON-object | Het verificatiemodel dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [Verificatie toevoegen aan uitgaande gesprekken voor](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)meer informatie . |
| <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . | 
| <*queryparameters*> | JSON-object | Alle queryparameters die moeten worden opgenomen met de API-aanroep <p>Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanroep. | 
| <*eigenschappen met andere actie-specifieke invoer*> | JSON-object | Alle andere invoereigenschappen die van toepassing zijn op deze specifieke actie | 
| <*eigenschappen met andere actie-specifieke eigenschappen*> | JSON-object | Alle andere eigenschappen die van toepassing zijn op deze specifieke actie | 
|||| 

U ook limieten opgeven voor een **ApiConnectionWebhook-actie** op dezelfde manier als [http-asynchrone limieten.](#asynchronous-limits)

<a name="compose-action"></a>

### <a name="compose-action"></a>Actie samenstellen

Met deze actie wordt één uitvoer van meerdere ingangen, inclusief expressies, mogelijk. Zowel de uitvoer als de ingangen kunnen elk type hebben dat Azure Logic Apps native ondersteunt, zoals arrays, JSON-objecten, XML en binair. U de uitvoer van de actie vervolgens gebruiken in andere acties. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*inputs-to-compose*> | Alle | De ingangen voor het maken van één uitvoer | 
|||| 

*Voorbeeld 1*

<!-- markdownlint-disable MD038 -->
Deze actiedefinitie `abcdefg ` wordt samengevoegd met een `1234`naloopruimte en de waarde:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Hier is de uitvoer die deze actie maakt:

`abcdefg 1234`

*Voorbeeld 2*

Met deze actiedefinitie wordt een `abcdefg` tekenreeksvariabele samengevoegd `1234`die een variabele geheel getal bevat die :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Hier is de uitvoer die deze actie maakt:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>JavaScript-code uitvoeren, actie

Met deze actie wordt een JavaScript-codefragment uitgevoerd en worden de resultaten geretourneerd via een `Result` token waarnaar latere acties kunnen verwijzen.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*JavaScript-code-fragment*> | Varieert | De JavaScript-code die u wilt uitvoeren. Zie [Codefragmenten toevoegen en uitvoeren met inlinecode](../logic-apps/logic-apps-add-run-inline-code.md)voor codevereisten en meer informatie. <p>In `code` het kenmerk kan uw codefragment `workflowContext` het alleen-lezen object als invoer gebruiken. Dit object heeft subeigenschappen die uw code toegang geven tot de resultaten van de trigger en eerdere acties in uw werkstroom. Zie [Naslagtriggeren en actieresultaten in uw code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)voor meer informatie over het `workflowContext` object. |
||||

*Vereist in sommige gevallen*

Het `explicitDependencies` kenmerk geeft aan dat u expliciet resultaten wilt opnemen van de trigger, eerdere acties of beide als afhankelijkheden voor uw codefragment. Zie [Parameters toevoegen voor inlinecode voor](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters)meer informatie over het toevoegen van deze afhankelijkheden . 

Voor `includeTrigger` het kenmerk kunt `true` `false` u opgeven of waarden opgeven.

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*eerdere acties*> | Stringarray | Een array met de opgegeven actienamen. Gebruik de actienamen die worden weergegeven in de werkstroomdefinitie waarin actienamen underscores (_), geen spaties (" gebruiken. |
||||

*Voorbeeld 1*

Met deze actie wordt code uitgevoerd die de naam van \<uw logische app krijgt en wordt de tekst 'Hallo wereld van logische-app-naam>' als resultaat geretourneerd. In dit voorbeeld verwijst de code naar de naam `workflowContext.workflow.name` van de werkstroom door toegang te krijgen tot de eigenschap via het alleen-lezen `workflowContext` object. Zie `workflowContext` [Referentietrigger- en actieresultaten in uw code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)voor meer informatie over het gebruik van het object.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Voorbeeld 2*

Met deze actie wordt code uitgevoerd in een logische app die wordt geactiveerd wanneer een nieuwe e-mail binnenkomt in een Office 365 Outlook-account. De logische app maakt ook gebruik van een e-mailactie voor het verzenden van goedkeuring die de inhoud van de ontvangen e-mail doorstuurt, samen met een verzoek om goedkeuring.

De code haalt de e-mailadressen `Body` uit de eigenschap van `SelectedOption` de trigger en retourneert de adressen samen met de eigenschapswaarde uit de goedkeuringsactie. De actie bevat expliciet de actie e-mail `explicitDependencies`  >  `actions` verzenden als afhankelijkheid in het kenmerk.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Functieactie

Met deze actie wordt een eerder gemaakte [Azure-functie aangeroepen.](../azure-functions/functions-create-first-azure-function.md)

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*Azure-functie-ID*> | Tekenreeks | De bron-id voor de Azure-functie die u wilt aanroepen. Hier is het formaat voor deze waarde:<p>"/abonnementen/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*methodetype*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor het aanroepen van de functie: "GET", "PUT", "POST", "PATCH" of "DELETE" <p>Als dit niet is opgegeven, is de standaardinstelling de methode 'POST'. | 
||||

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*header-inhoud*> | JSON-object | Eventuele kopteksten die u met het gesprek wilt verzenden <p>U bijvoorbeeld de taal en het type instellen op een verzoek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-inhoud*> | JSON-object | Alle inhoud van berichten die u in het verzoek wilt verzenden | 
| <*queryparameters*> | JSON-object | Alle queryparameters die moeten worden opgenomen met de API-aanroep <p>Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanroep. | 
| <*eigenschappen met andere actie-specifieke invoer*> | JSON-object | Alle andere invoereigenschappen die van toepassing zijn op deze specifieke actie | 
| <*eigenschappen met andere actie-specifieke eigenschappen*> | JSON-object | Alle andere eigenschappen die van toepassing zijn op deze specifieke actie | 
||||

Wanneer u uw logische app opslaat, voert de Logic Apps-engine deze controles uit op de functie waarnaar wordt verwezen:

* Uw werkstroom moet toegang hebben tot de functie.

* Uw werkstroom kan alleen een standaard HTTP-trigger of generieke JSON-webhooktrigger gebruiken. 

  De Logic Apps-engine krijgt en caches de URL van de trigger, die wordt gebruikt tijdens runtime. Als een bewerking echter de in de cache opgeslagen URL ongeldig maakt, mislukt de **functieactie** bij runtime. Als u dit probleem wilt oplossen, slaat u de logische app opnieuw op, zodat de logische app de trigger-URL opnieuw opslaat en in de cache opslaat.

* De functie kan geen route hebben gedefinieerd.

* Alleen "functie" en "anonieme" autorisatieniveaus zijn toegestaan. 

*Voorbeeld*

Met deze actiedefinitie wordt de eerder gemaakte functie 'GetProductID' aangeroepen:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP-actie

Met deze actie wordt een aanvraag naar het opgegeven HTTP- of HTTPS-eindpunt uitgevoerd en wordt het antwoord gecontroleerd om te bepalen of de werkstroom wordt uitgevoerd.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Vereist*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `method` | <*methodetype*> | Tekenreeks | De methode die moet worden gebruikt voor het verzenden van de uitgaande aanvraag: "GET", "PUT", "POST", "PATCH" of "DELETE" |
| `uri` | <*HTTP-of-HTTPS-EINDPUNT-URL*> | Tekenreeks | De HTTP- of HTTPS-eindpunt-URL waar u de uitgaande aanvraag wilt verzenden. Maximale tekenreeksgrootte: 2 KB <p>Voor een Azure-service of -bron bevat deze URI-syntaxis de resource-id en het pad naar de bron die u wilt openen. |
|||||

*Optionele*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `headers` | <*header-inhoud*> | JSON-object | Kopteksten die u bij de aanvraag moet opnemen <p>U bijvoorbeeld de taal en het type instellen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*queryparameters*> | JSON-object | Alle queryparameters die u in de aanvraag moet gebruiken <p>Het `"queries": { "api-version": "2018-01-01" }` object voegt `?api-version=2018-01-01` bijvoorbeeld toe aan de aanroep. |
| `body` | <*body-inhoud*> | JSON-object | De inhoud van het bericht te verzenden als payload met het verzoek |
| `authentication` | <*verificatie-type-en-eigenschapswaarden*> | JSON-object | Het verificatiemodel dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [Verificatie toevoegen aan uitgaande gesprekken voor](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)meer informatie . Buiten Scheduler `authority` wordt de eigenschap ondersteund. Wanneer deze niet is `https://management.azure.com/`opgegeven, is de standaardwaarde, maar u een andere waarde gebruiken. |
| `retryPolicy` > `type` | <*opnieuw proberen-gedrag*> | JSON-object | Past het gedrag opnieuw proberen aan voor intermitterende fouten, die de statuscode 408, 429 en 5XX en eventuele verbindingsuitzonderingen hebben. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie . |
| <*eigenschappen met andere actie-specifieke invoer*> | <*input-eigenschap*> | JSON-object | Alle andere invoereigenschappen die van toepassing zijn op deze specifieke actie |
| <*eigenschappen met andere actie-specifieke eigenschappen*> | <*waarde van onroerend goed*> | JSON-object | Alle andere eigenschappen die van toepassing zijn op deze specifieke actie |
|||||

*Voorbeeld*

Met deze actiedefinitie wordt het laatste nieuws ontvangen door een verzoek naar het opgegeven eindpunt te sturen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Meedoen actie

Met deze actie wordt een tekenreeks aanmaakt van alle items in een array en worden deze items gescheiden van het opgegeven scheidingstekenteken. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*Array*> | Matrix | De array of expressie die de bronitems biedt. Als u een expressie opgeeft, sluit u die expressie in met dubbele aanhalingstekens. | 
| <*Scheidingsteken*> | Tekenreeks met één teken | Het teken dat elk item in de tekenreeks scheidt | 
|||| 

*Voorbeeld*

Stel dat u een eerder gemaakte variabele "myIntegerArray" hebt die deze gehele array bevat: 

`[1,2,3,4]` 

Met deze actiedefinitie worden de waarden `variables()` uit de variabele verwijderd door de functie in een expressie te gebruiken en wordt deze tekenreeks gemaakt met die waarden, die worden gescheiden door een komma:`"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Parse JSON actie

Met deze actie worden gebruiksvriendelijke velden of *tokens gemaakt* van de eigenschappen in JSON-inhoud. U deze eigenschappen vervolgens openen in uw logische app door de tokens in plaats daarvan te gebruiken. Als u bijvoorbeeld JSON-uitvoer wilt gebruiken vanuit services zoals Azure Service Bus en Azure Cosmos DB, u deze actie opnemen in uw logische app, zodat u gemakkelijker verwijzen naar de gegevens in die uitvoer.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*JSON-bron*> | JSON-object | De JSON-inhoud die u wilt ontleden | 
| <*JSON-schema*> | JSON-object | Het JSON-schema dat de onderliggende JSON-inhoud beschrijft, die de actie gebruikt voor het ontleden van de bron-JSON-inhoud. <p>**Tip:** In Logic Apps Designer u het schema opgeven of een voorbeeldpayload bieden, zodat de actie het schema kan genereren. | 
|||| 

*Voorbeeld*

Met deze actiedefinitie worden deze tokens gemaakt die u in uw werkstroom gebruiken, maar alleen in acties die worden uitgevoerd na de **actie Parse JSON:**

`FirstName`, `LastName`, en`Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

In dit voorbeeld geeft de eigenschap 'inhoud' de JSON-inhoud op voor de te ontleden actie. U deze JSON-inhoud ook leveren als de voorbeeldpayload voor het genereren van het schema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

De eigenschap 'schema' geeft het JSON-schema op dat wordt gebruikt voor het beschrijven van de JSON-inhoud:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Queryactie

Met deze actie wordt een array gemaakt van items in een andere array op basis van een opgegeven voorwaarde of filter.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*Array*> | Matrix | De array of expressie die de bronitems biedt. Als u een expressie opgeeft, sluit u die expressie in met dubbele aanhalingstekens. |
| <*conditie-of-filter*> | Tekenreeks | De voorwaarde die wordt gebruikt voor het filteren van items in de bronarray <p>**Opmerking:** Als er geen waarden aan de voorwaarde voldoen, wordt met de actie een lege array gemaakt. |
|||| 

*Voorbeeld*

Met deze actiedefinitie wordt een matrix met waarden die groter zijn dan de opgegeven waarde, twee:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Reactieactie  

Met deze actie wordt de payload voor het antwoord op een HTTP-aanvraag. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*statuscode van responsstatus*> | Geheel getal | De HTTP-statuscode die naar het binnenkomende verzoek wordt verzonden. De standaardcode is "200 OK", maar de code kan elke geldige statuscode zijn die begint met 2xx, 4xx of 5xx, maar niet met 3xxx. | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*response-headers*> | JSON-object | Een of meer kopteksten die bij het antwoord moeten worden opgenomen | 
| <*response-body*> | Verschillende | De antwoordtekst, die een tekenreeks, JSON-object of zelfs binaire inhoud van een eerdere actie kan zijn | 
|||| 

*Voorbeeld*

Met deze actiedefinitie wordt een antwoord op een HTTP-aanvraag met de opgegeven statuscode, berichttekst en berichtkoppen:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Beperkingen*

In tegenstelling tot andere acties heeft de **actie Respons** speciale beperkingen: 

* Uw werkstroom kan de **actie Respons** alleen gebruiken wanneer de werkstroom begint met een HTTP-aanvraagtrigger, wat betekent dat uw werkstroom moet worden geactiveerd door een HTTP-aanvraag.

* Uw werkstroom kan de **actie Respons** overal *gebruiken, behalve* in **Foreach-lussen,** **Tot** lussen, inclusief opeenvolgende lussen en parallelle branches. 

* De oorspronkelijke HTTP-aanvraag krijgt het antwoord van uw werkstroom alleen wanneer alle acties die vereist zijn voor de **actie Antwoord** binnen de [http-aanvraagtime-outlimiet](../logic-apps/logic-apps-limits-and-config.md#request-limits)zijn voltooid.

  Als uw werkstroom echter een andere logische app aanroept als een geneste werkstroom, wacht de bovenliggende werkstroom totdat de geneste werkstroom is voltooid, ongeacht hoe lang de tijd verstrijkt voordat de geneste werkstroom is voltooid.

* Wanneer uw werkstroom de **actie Respons** en een synchrone responspatroon gebruikt, kan de werkstroom de opdracht **splitOn** in de triggerdefinitie niet gebruiken omdat met die opdracht meerdere uitvoeringen worden gemaakt. Controleer op dit geval wanneer de PUT-methode wordt gebruikt en als dit waar is, retourneer een antwoord op een "slecht verzoek".

  Als uw werkstroom anders de opdracht **splitOn** en een **actie Respons** gebruikt, wordt de werkstroom asynchroon uitgevoerd en wordt onmiddellijk een antwoord '202 GEACCEPTEERD' geretourneerd.

* Wanneer de uitvoering van uw werkstroom de **actie Antwoord** bereikt, maar de binnenkomende aanvraag al een antwoord heeft ontvangen, wordt de **actie Antwoord** gemarkeerd als 'Mislukt' vanwege het conflict. En als gevolg hiervan wordt uw logische app-run ook gemarkeerd met de status 'Mislukt'.

<a name="select-action"></a>

### <a name="select-action"></a>Actie selecteren

Met deze actie wordt een array met JSON-objecten gemaakt door items uit een andere array te transformeren op basis van de opgegeven kaart. De uitvoerarray en de bronarray hebben altijd hetzelfde aantal items. Hoewel u het aantal objecten in de uitvoerarray niet wijzigen, u eigenschappen en hun waarden in die objecten toevoegen of verwijderen. De `select` eigenschap geeft ten minste één sleutelwaardepaar op dat de kaart definieert voor het transformeren van items in de bronarray. Een sleutelwaardepaar vertegenwoordigt een eigenschap en de waarde ervan voor alle objecten in de uitvoerarray.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*Array*> | Matrix | De array of expressie die de bronitems biedt. Zorg ervoor dat u een expressie met dubbele aanhalingstekens omsluit. <p>**Opmerking:** Als de bronarray leeg is, wordt met de actie een lege array gemaakt. | 
| <*sleutelnaam*> | Tekenreeks | De eigenschapsnaam die is toegewezen aan het resultaat van <*expressie*> <p>Als u een nieuwe eigenschap wilt toevoegen voor alle objecten in de uitvoerarray, geeft u een <*sleutelnaam*> voor die eigenschap en een <*expressie*> voor de eigenschapswaarde. <p>Als u een eigenschap wilt verwijderen uit alle objecten in de array, laat u de <*sleutelnaam*> voor die eigenschap weg. | 
| <*Expressie*> | Tekenreeks | De expressie die het item in de bronarray transformeert en het resultaat toewijst aan <*sleutelnaam*> | 
|||| 

Met de actie **Selecteren** wordt een array als uitvoer gemaakt, dus elke actie die deze uitvoer wil gebruiken, moet een array accepteren of u moet de array converteren naar het type dat de actie van de consument accepteert. Als u bijvoorbeeld de uitvoerarray wilt converteren naar een tekenreeks, u die array doorgeven aan de actie **Samenstellen** en vervolgens verwijzen naar de uitvoer van de actie **Samentrekken** in uw andere acties.

*Voorbeeld*

Met deze actiedefinitie wordt een JSON-objectarray gemaakt van een gehele array. De actie wordt door de bronarray geïteerd, krijgt elke gehele waarde met behulp van de `@item()` expressie en wijst elke waarde toe aan de eigenschap "`number`" in elk JSON-object:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Hier is de array die deze actie maakt:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Als u deze arrayuitvoer wilt gebruiken in andere acties, geeft u deze uitvoer door in een actie **Samenstellen:**

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

U vervolgens de uitvoer van de actie **Samenstellen** gebruiken in uw andere acties, bijvoorbeeld de **Office 365 Outlook - Een e-mailactie verzenden:**

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Tabelactie

Met deze actie wordt een CSV- of HTML-tabel gemaakt vanuit een array. Voor arrays met JSON-objecten maakt deze actie automatisch de kolomkoppen van de eigenschapnamen van de objecten. Voor arrays met andere gegevenstypen moet u de kolomkoppen en -waarden opgeven. Deze array bevat bijvoorbeeld de eigenschappen 'ID' en 'Product_Name' die deze actie kan gebruiken voor de namen van de kolomkop:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| \<CSV- *of* HTML->| Tekenreeks | De indeling voor de tabel die u wilt maken | 
| <*Array*> | Matrix | De array of expressie die de bronitems voor de tabel biedt <p>**Opmerking:** Als de bronarray leeg is, maakt de actie een lege tabel. | 
|||| 

*Optionele*

Als u kolomkoppen en -waarden `columns` wilt opgeven of aanpassen, gebruikt u de array. Wanneer `header-value` paren dezelfde kopnaam hebben, worden hun waarden weergegeven in dezelfde kolom onder die kopnaam. Anders definieert elke unieke koptekst een unieke kolom.

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*kolomnaam*> | Tekenreeks | De kopnaam voor een kolom | 
| <*kolomwaarde*> | Alle | De waarde in die kolom | 
|||| 

*Voorbeeld 1*

Stel dat u een eerder gemaakte variabele 'myItemArray' hebt die momenteel deze array bevat:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Met deze actiedefinitie wordt een CSV-tabel gemaakt op basis van de variabele 'myItemArray'. De expressie die `from` door de eigenschap wordt gebruikt, krijgt `variables()` de array van 'myItemArray' met behulp van de functie:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Hier is de CSV-tabel die deze actie maakt: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Voorbeeld 2*

Met deze actiedefinitie wordt een HTML-tabel gemaakt op basis van de variabele 'myItemArray'. De expressie die `from` door de eigenschap wordt gebruikt, krijgt `variables()` de array van 'myItemArray' met behulp van de functie:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Hier is de HTML-tabel die deze actie maakt: 

<table><thead><tr><th>Id</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Appels</td></tr><tr><td>1</td><td>Sinaasappelen</td></tr></tbody></table>

*Voorbeeld 3*

Met deze actiedefinitie wordt een HTML-tabel gemaakt op basis van de variabele 'myItemArray'. In dit voorbeeld worden echter de standaardnamen van kolomkoppen overschreven met 'Stock_ID' en 'Beschrijving' en wordt het woord 'Organisch' toegevoegd aan de waarden in de kolom Beschrijving.

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Hier is de HTML-tabel die deze actie maakt: 

<table><thead><tr><th>Stock_ID</th><th>Beschrijving</th></tr></thead><tbody><tr><td>0</td><td>Biologische appelen</td></tr><tr><td>1</td><td>Biologische sinaasappelen</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Actie beëindigen

Met deze actie wordt de uitvoering voor een werkstroominstantie gestopt, worden alle lopende acties geannuleerd, worden de resterende acties overgeslagen en wordt de opgegeven status geretourneerd. U bijvoorbeeld de actie **Beëindigen** gebruiken wanneer uw logische app een foutstatus volledig moet afsluiten. Deze actie heeft geen invloed op reeds voltooide acties en kan niet worden weergegeven in **Foreach-** en **Until-lussen,** inclusief opeenvolgende lussen.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*Status*> | Tekenreeks | De status om terug te keren voor de run: 'Mislukt', 'Geannuleerd' of 'Geslaagd' |
|||| 

*Optionele*

De eigenschappen voor het object RunStatus zijn alleen van toepassing wanneer de eigenschap RunStatus is ingesteld op de status 'Mislukt'.

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*foutcode of-naam*> | Tekenreeks | De code of naam voor de fout |
| <*foutbericht*> | Tekenreeks | Het bericht of de tekst waarin de fout wordt beschreven en eventuele acties die de app-gebruiker kan uitvoeren | 
|||| 

*Voorbeeld*

Met deze actiedefinitie wordt een werkstroom uitgevoerd, wordt de runstatus ingesteld op 'Mislukt' en worden de status, een foutcode en een foutbericht geretourneerd:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Wachtactie

Met deze actie wordt de werkstroomuitvoering onderbroken voor het opgegeven interval of tot de opgegeven tijd, maar niet beide.

*Opgegeven interval*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Opgegeven tijd*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*aantal eenheden*> | Geheel getal | Voor de **actie Vertraging** moet het aantal eenheden dat moet wachten | 
| <*Interval*> | Tekenreeks | Voor de **actie Vertraging** moet het interval wachten: 'Tweede', 'Minuut', 'Uur', 'Dag', 'Week', 'Maand'. | 
| <*datum-tijdstempel*> | Tekenreeks | Voor de **actie Vertraging tot,** de datum en tijd om de uitvoering te hervatten. Deze waarde moet de [utc-datumtijdnotatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)gebruiken . | 
|||| 

*Voorbeeld 1*

Met deze actiedefinitie wordt de werkstroom gedurende 15 minuten onderbroken:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Voorbeeld 2*

Met deze actiedefinitie wordt de werkstroom onderbroken tot de opgegeven tijd:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Werkstroomactie

Met deze actie wordt een andere eerder gemaakte logische app aangeroepen, wat betekent dat u andere logische app-werkstromen opnemen en opnieuw gebruiken. U de uitvoer van de onderliggende of *geneste* logica-app ook gebruiken in acties die de geneste logica-app volgen, op voorwaarde dat de onderliggende logica-app een antwoord retourneert.

De Logic Apps-engine controleert de toegang tot de trigger die u wilt bellen, dus zorg ervoor dat u toegang hebt tot die trigger. De geneste logica-app moet ook aan de volgende criteria voldoen:

* Een trigger maakt de geneste logische app aanroepbaar, zoals een [Trigger](#request-trigger) of [HTTP-trigger](#http-trigger)

* Hetzelfde Azure-abonnement als de bovenliggende logica-app

* Als u de uitvoer van de geneste logica-app in de bovenliggende logica-app wilt gebruiken, moet de geneste logica-app een [reactieactie](#response-action) hebben

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*geneste-logica-app-naam*> | Tekenreeks | De naam voor de logische app die u wilt bellen | 
| <*triggernaam*> | Tekenreeks | De naam voor de trigger in de geneste logica-app die u wilt bellen | 
| <*Azure-abonnement-ID*> | Tekenreeks | De Azure-abonnements-id voor de geneste logica-app |
| <*Azure-resourcegroep*> | Tekenreeks | De naam van de Azure-brongroep voor de geneste logica-app |
| <*geneste-logica-app-naam*> | Tekenreeks | De naam voor de logische app die u wilt bellen |
||||

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*header-inhoud*> | JSON-object | Eventuele kopteksten die u met het gesprek wilt verzenden | 
| <*body-inhoud*> | JSON-object | Alle inhoud van berichten die u met het gesprek wilt verzenden | 
||||

*Uitvoer*

De uitvoer van deze actie is afhankelijk van de reactieactie van de geneste logica-app. Als de geneste logica-app geen reactieactie bevat, zijn de uitvoer leeg.

*Voorbeeld*

Nadat de actie 'Start_search' is voltooid, roept deze definitie van werkstroomactie een andere logische app genaamd "Get_product_information" aan, die in de opgegeven ingangen wordt doorgegeven:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Details van werkstroomactie beheren

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Voor elke actie

Deze lusactie wordt herhaald door een array en voert acties uit op elk arrayitem. Standaard wordt de lus "voor elke" parallel uitgevoerd tot een maximum aantal lussen. Zie [Limieten en config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)voor dit maximum. Meer informatie over [het maken van "voor elke" lussen.](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actie-1... N*> | Tekenreeks | De namen van de acties die op elk arrayitem worden uitgevoerd | 
| <*actie-definitie-1... N*> | JSON-object | De definities van de acties die worden uitgevoerd | 
| <*voor elke expressie*> | Tekenreeks | De expressie die verwijst naar elk item in de opgegeven array | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*Tellen*> | Geheel getal | Standaard worden de 'voor elke' lusiteraties tegelijkertijd uitgevoerd (gelijktijdig of parallel) tot de [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Zie ['Voor elke' lusgelijktijdigheid wijzigen](#change-for-each-concurrency)als u deze limiet wilt wijzigen door een nieuw <*aantal*> waarde in te stellen. | 
| <*bewerkingsoptie*> | Tekenreeks | Als u een lus 'voor elke' lus achtereenvolgens wilt uitvoeren, `Sequential` in plaats van parallel, stelt u <*bewerkingsoptie* in> of <*>* tellen op, `1`maar niet beide. Zie ['Voor elke looploop selopen' achtereenvolgens uitvoeren](#sequential-for-each)voor meer informatie. | 
|||| 

*Voorbeeld*

Deze lus 'voor elke' stuurt een e-mail voor elk item in de array, die bijlagen bevat van een binnenkomende e-mail. De lus stuurt een e-mail, inclusief de bijlage, naar een persoon die de bijlage bekijkt.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Als u alleen een array wilt opgeven die wordt doorgegeven als uitvoer van de trigger, krijgt deze expressie de <*arraynaam*> array van de triggerbody. Om een fout te voorkomen als de array `?` niet bestaat, gebruikt de expressie de operator:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Als actie

Deze actie, een *voorwaardelijke instructie,* evalueert een expressie die een voorwaarde vertegenwoordigt en voert een andere vertakking uit op basis van of de voorwaarde waar of onwaar is. Als de voorwaarde waar is, wordt de voorwaarde gemarkeerd met de status Geslaagd. Meer informatie over [het maken van voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*Voorwaarde*> | JSON-object | De voorwaarde, die een uitdrukking kan zijn, om te evalueren | 
| <*actie-1*> | JSON-object | De actie die moet worden uitgevoerd wanneer <*voorwaarde*> evalueert | 
| <*actiedefinitie*> | JSON-object | De definitie voor de actie | 
| <*actie-2*> | JSON-object | De actie die moet worden uitgevoerd wanneer <*voorwaarde*> evalueert tot valse | 
|||| 

De acties `actions` in `else` de of objecten krijgen de volgende statussen:

* "Geslaagd" wanneer ze lopen en slagen
* 'Mislukt' wanneer ze draaien en mislukken
* 'Overgeslagen' wanneer de betreffende branch niet wordt uitgevoerd

*Voorbeeld*

Deze voorwaarde geeft aan dat wanneer de gehele variabele een waarde heeft die groter is dan nul, de werkstroom een website controleert. Als de variabele nul of minder is, controleert de werkstroom een andere website.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Hoe voorwaarden expressies gebruiken

Hier volgen enkele voorbeelden die laten zien hoe u expressies in omstandigheden gebruiken:
  
| JSON | Resultaat | 
|------|--------| 
| "expressie":@parameters" ('<*heeftSpecialAction*>')" | Alleen voor Booleaanse expressies wordt de voorwaarde doorgegeven voor elke waarde die wordt geëvalueerd op waar. <p>Als u andere typen wilt converteren `empty()` naar `equals()`Boolean, gebruikt u de volgende functies: of . | 
| "expressie":@greater" (acties('<*actie*>').output.waarde, parameters('<*drempel*>')". | Voor vergelijkingsfuncties wordt de actie alleen uitgevoerd wanneer de uitvoer van <*actie>* meer is dan de <*drempelwaarde*> waarde. | 
| "expressie":@or" (groter(acties('<*actie*>').output.waarde, parameters('<*drempel*>')), minder(acties('<> van dezelfde actie').output.waarde, 100)". *same-action* | Voor logische functies en het maken van geneste Booleaanse expressies wordt de actie uitgevoerd wanneer de uitvoer van <*actie*> meer is dan de <*drempelwaarde*> waarde of minder dan 100. | 
| "expressie":@equals" (lengte(acties('<*actie*>').outputs.errors), 0)". | U arrayfuncties gebruiken om te controleren of de array items heeft. De actie wordt `errors` uitgevoerd wanneer de array leeg is. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Scope-actie

Deze actie groepeert acties logischerwijs in *scopes,* die hun eigen status krijgen nadat de acties in dat bereik zijn uitgevoerd. U vervolgens de status van het bereik gebruiken om te bepalen of andere acties worden uitgevoerd. Meer informatie over [het maken van scopes](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*inner-action-1... N*> | JSON-object | Een of meer acties die binnen het bereik worden uitgevoerd |
| <*actie-ingangen*> | JSON-object | De ingangen voor elke actie |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>De actie Switch

Deze actie, ook wel een *switchinstructie*genoemd, organiseert andere acties in *aanvragen*en kent een waarde toe aan elk geval, behalve voor de standaardcase als er een bestaat. Wanneer uw werkstroom wordt uitgevoerd, vergelijkt de actie **Switch** de waarde van een expressie, object of token met de waarden die voor elk geval zijn opgegeven. Als in de **actie Switch** een overeenkomende aanvraag wordt gevonden, worden in uw werkstroom alleen de acties voor die aanvraag uitgevoerd. Elke keer dat de **Switch-actie** wordt uitgevoerd, bestaat er slechts één overeenkomende case of bestaan er geen overeenkomsten. Als er geen overeenkomsten bestaan, worden in de actie **Switch** de standaardacties uitgevoerd. Meer informatie over [het maken van switchstatements](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*expressie-object-of-token*> | Varieert | De expressie, het JSON-object of token dat moet worden geëvalueerd | 
| <*actienaam*> | Tekenreeks | De naam van de actie die moet worden uitgevoerd voor de overeenkomende aanvraag | 
| <*actiedefinitie*> | JSON-object | De definitie voor de actie die moet worden uitgevoerd voor de overeenkomende aanvraag | 
| <*matching-waarde*> | Varieert | De waarde die moet worden vergeleken met het geëvalueerde resultaat | 
|||| 

*Optionele*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*standaardactienaam*> | Tekenreeks | De naam van de standaardactie die moet worden uitgevoerd wanneer er geen overeenkomende aanvraag bestaat | 
| <*standaardactie-definitie*> | JSON-object | De definitie voor de actie die moet worden uitgevoerd wanneer er geen overeenkomende aanvraag bestaat | 
|||| 

*Voorbeeld*

Met deze actiedefinitie wordt beoordeeld of de persoon die reageert op de e-mail met goedkeuringsaanvraag de optie Goedkeuren of de optie Afwijzen heeft geselecteerd. Op basis van deze keuze voert de **Switch-actie** de acties uit voor de overeenkomende aanvraag, namelijk het verzenden van een andere e-mail naar de responder, maar met verschillende bewoordingen in elk geval. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Tot actie

Deze lusactie bevat acties die worden uitgevoerd totdat de opgegeven voorwaarde is waar. De lus controleert de voorwaarde als de laatste stap nadat alle andere acties zijn uitgevoerd. U meer dan één `"actions"` actie in het object opnemen en de actie moet ten minste één limiet definiëren. Meer informatie over [het maken van "tot" lussen.](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actienaam*> | Tekenreeks | De naam voor de actie die u binnen de lus wilt uitvoeren | 
| <*actietype*> | Tekenreeks | Het actietype dat u wilt uitvoeren | 
| <*actie-ingangen*> | Verschillende | De ingangen voor de actie uit te voeren | 
| <*Voorwaarde*> | Tekenreeks | De voorwaarde of expressie om te evalueren na alle acties in de lus afwerking uitgevoerd | 
| <*aantal lussen*> | Geheel getal | De limiet voor het meeste aantal lussen dat de actie kan uitvoeren. Zie Limieten en configuratie voor Azure [Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)meer informatie over de standaardlimiet en maximumlimiet. | 
| <*loop-time-out*> | Tekenreeks | De limiet voor de langste tijd die de lus kan uitvoeren. De `timeout` standaardwaarde `PT1H`is , dat is de vereiste [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Voorbeeld*

Met deze lusactiedefinitie wordt een HTTP-verzoek naar de opgegeven URL geleid totdat aan een van deze voorwaarden is voldaan:

* Het verzoek krijgt een antwoord met de statuscode "200 OK".
* De lus heeft 60 keer gelopen.
* De lus heeft een uur gelopen.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks en abonnementen

Webhook-gebaseerde triggers en acties controleren niet regelmatig eindpunten, maar wachten in plaats daarvan op specifieke gebeurtenissen of gegevens op die eindpunten. Deze triggers en acties *abonneren zich op* de eindpunten door een *terugbel-URL* op te geven waar het eindpunt reacties kan verzenden.

De `subscribe` aanroep vindt plaats wanneer de werkstroom op enigerlei wijze verandert, bijvoorbeeld wanneer referenties worden vernieuwd of wanneer de invoerparameters worden gewijzigd voor een trigger of actie. Deze aanroep gebruikt dezelfde parameters als standaard HTTP-acties. 

De `unsubscribe` aanroep vindt automatisch plaats wanneer een bewerking de trigger of actie ongeldig maakt, bijvoorbeeld:

* De trigger verwijderen of uitschakelen. 
* De werkstroom verwijderen of uitschakelen. 
* Het abonnement verwijderen of uitschakelen. 

Om deze aanroepen `@listCallbackUrl()` te ondersteunen, retourneert de expressie een unieke 'callback-URL' voor de trigger of actie. Deze URL vertegenwoordigt een unieke id voor de eindpunten die gebruikmaken van de REST API van de service. De parameters voor deze functie zijn hetzelfde als de webhook trigger of actie.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Asynchrone duur wijzigen

Voor zowel triggers als acties u de duur voor het asynchrone patroon beperken tot een specifiek tijdsinterval door de `limit.timeout` eigenschap toe te voegen. Op die manier wordt de status van de actie gemarkeerd als `Cancelled` bij de `ActionTimedOut` code als de actie nog niet is voltooid wanneer het interval is verstreken. De `timeout` eigenschap maakt gebruik [van ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Configuratie-instellingen voor runtime

U het standaardruntimegedrag voor triggers `runtimeConfiguration` en acties wijzigen door deze eigenschappen toe te voegen aan de trigger- of actiedefinitie.

| Eigenschap | Type | Beschrijving | Trigger of actie | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Geheel getal | Wijzig de [*standaardlimiet*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) voor het aantal werkstroomexemplaren dat tegelijkertijd kan worden uitgevoerd (gelijktijdig of parallel). Als u deze waarde aanpast, u het aantal aanvragen beperken dat backendsystemen ontvangen. <p>De `runs` eigenschap `1` instellen op werken op `operationOptions` dezelfde `SingleInstance`manier als het instellen van de eigenschap op . U beide panden instellen, maar niet beide. <p>Zie [Voeg gelijktijdigheid van de trigger](#change-trigger-concurrency) of Trigger instances [achtereenvolgens in.](#sequential-trigger) | Alle triggers | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Geheel getal | Wijzig de [*standaardlimiet*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) voor het aantal werkstroomexemplaren dat moet wachten om uit te voeren wanneer uw logische app al de maximale gelijktijdige exemplaren uitvoert. <p>Zie [Wachttijdrunslimiet wijzigen](#change-waiting-runs)als u de standaardlimiet wilt wijzigen. | Alle triggers | 
| `runtimeConfiguration.concurrency.repetitions` | Geheel getal | Wijzig de [*standaardlimiet*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) voor het aantal 'voor elke' lusiteraties die tegelijkertijd kunnen worden uitgevoerd (gelijktijdig of parallel). <p>De `repetitions` eigenschap `1` instellen op werken op `operationOptions` dezelfde `SingleInstance`manier als het instellen van de eigenschap op . U beide panden instellen, maar niet beide. <p>Zie ['Voor elke' gelijktijdigheid wijzigen](#change-for-each-concurrency) of [Loop 'voor elke' lussen achtereenvolgens](#sequential-for-each)als u de standaardlimiet wilt wijzigen. | Actie: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Geheel getal | Voor specifieke acties die paginatie ondersteunen en hebben ingeschakeld, geeft deze waarde het *minimumaantal* resultaten aan dat moet worden opgehaald. <p>Zie [Bulkgegevens, items of resultaten opvragen met behulp van paginatie](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Actie: Gevarieerd |
| `runtimeConfiguration.secureData.properties` | Matrix | Bij veel triggers en acties verbergen deze instellingen invoer, uitvoer of beide uit de rungeschiedenis van de logische app. <p>Zie [Ingangen en uitvoer uit de uitvoeringsgeschiedenis verbergen](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view)voor meer informatie over het beveiligen van deze gegevens. | Meeste triggers en acties |
| `runtimeConfiguration.staticResult` | JSON-object | Voor acties die de [statische resultaatinstelling](../logic-apps/test-logic-apps-mock-data-static-results.md) ondersteunen `staticResult` en hebben ingeschakeld, heeft het object de volgende kenmerken: <p>- `name`, die verwijst naar de statische resultaatdefinitienaam van `staticResults` de huidige actie, die `definition` wordt weergegeven in het kenmerk in het kenmerk van de logische app-werkstroom. Zie [Statische resultaten - Schemaverwijzing voor werkstroomdefinitietaal voor](../logic-apps/logic-apps-workflow-definition-language.md#static-results)meer informatie . <p> - `staticResultOptions`, die aangeeft `Enabled` of statische resultaten al dan niet voor de huidige actie zijn. <p>Zie [Logische apps testen met nepgegevens testen door statische resultaten in te stellen](../logic-apps/test-logic-apps-mock-data-static-results.md) | Actie: Gevarieerd |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Bewerkingsopties

U het standaardgedrag voor triggers `operationOptions` en acties wijzigen met de eigenschap in trigger- of actiedefinitie.

| Bewerkingsoptie | Type | Beschrijving | Trigger of actie | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Tekenreeks | Voer http-gebaseerde acties synchroon uit, in plaats van asynchroon. <p><p>Zie Acties synchroon [uitvoeren](#asynchronous-patterns)als u deze optie wilt instellen. | Acties: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Reactie](#response-action) | 
| `OptimizedForHighThroughput` | Tekenreeks | Wijzig de [standaardlimiet](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) voor het aantal actie-uitvoeringen per 5 minuten tot de [maximale limiet.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) <p><p>Zie Uitvoeren in [de modus Hoge doorvoer](#run-high-throughput-mode)om deze optie in te stellen. | Alle acties | 
| `Sequential` | Tekenreeks | Voer "voor elke" lusiteraties één voor één uit, in plaats van allemaal tegelijkertijd parallel. <p>Deze optie werkt op dezelfde `runtimeConfiguration.concurrency.repetitions` manier `1`als het instellen van de eigenschap op . U beide panden instellen, maar niet beide. <p><p>Zie Looplussen voor elke lus [en loop se ren achtereenvolgens](#sequential-for-each)in om deze optie in te stellen.| Actie: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Tekenreeks | Voer de trigger voor elke logische app-instantie achtereenvolgens uit en wacht tot de eerder actieve uitvoering is voltooid voordat de volgende logische app-instantie wordt geactiveerd. <p><p>Deze optie werkt op dezelfde `runtimeConfiguration.concurrency.runs` manier `1`als het instellen van de eigenschap op . U beide panden instellen, maar niet beide. <p>Zie [Instanties opeenvolgend activeren](#sequential-trigger)als u deze optie wilt instellen. | Alle triggers | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Gelijktijdigheid van triggers wijzigen

Standaard worden logische app-werkstroominstanties op hetzelfde moment uitgevoerd (gelijktijdig of parallel). Dit gedrag betekent dat elke triggerinstantie wordt geactiveerd voordat de eerder actieve werkstroominstantie is uitgevoerd. Het aantal gelijktijdig lopende exemplaren heeft echter een [standaardlimiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wanneer het aantal gelijktijdig lopende werkstroomexemplaren deze limiet bereikt, moeten alle andere nieuwe exemplaren wachten met uitvoeren. Deze limiet helpt bij het bepalen van het aantal aanvragen dat backendsystemen ontvangen.

Wanneer u het gelijktijdigheidsbesturingselement van de trigger inschakelt, worden triggerexemplaren parallel uitgevoerd tot aan de [standaardlimiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze standaardgelijktijdigvalutalimiet wilt wijzigen, u de codeweergaveeditor of Logic Apps Designer `runtimeConfiguration.concurrency.runs` gebruiken omdat het wijzigen van de gelijktijdigheidsinstelling via de ontwerper de eigenschap in de onderliggende triggerdefinitie toevoegt of bijwerkt en vice versa. Deze eigenschap bepaalt het maximum aantal nieuwe werkstroomexemplaren dat parallel kan worden uitgevoerd.

Hier volgen enkele overwegingen voor wanneer u gelijktijdigheid op een trigger wilt inschakelen:

* Wanneer gelijktijdigheid is ingeschakeld, wordt de [SplitOn-limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) aanzienlijk verlaagd voor [debatchingarrays](#split-on-debatch). Als het aantal items deze limiet overschrijdt, wordt de spliton-mogelijkheid uitgeschakeld.

* U gelijktijdigheid niet uitschakelen nadat u het gelijktijdigheidscontrole hebt ingeschakeld.

* Wanneer gelijktijdigheid is ingeschakeld, wordt de [SplitOn-limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) aanzienlijk verlaagd voor [debatchingarrays](#split-on-debatch). Als het aantal items deze limiet overschrijdt, wordt de spliton-mogelijkheid uitgeschakeld.

* Wanneer gelijktijdigheid is ingeschakeld, kan een langlopende logische app-instantie ervoor zorgen dat nieuwe logische app-exemplaren een wachtstatus invoeren. Met deze status voorkomt u dat Azure Logic Apps nieuwe exemplaren maakt en zelfs wanneer het aantal gelijktijdige uitvoeringen kleiner is dan het opgegeven maximumaantal gelijktijdige uitvoeringen.

  * Als u deze status wilt onderbreken, annuleert u de vroegste exemplaren die *nog steeds worden uitgevoerd.*

    1. Selecteer **Overzicht**in het menu van uw logische app .

    1. Selecteer in de sectie **Geschiedenis uitvoeren** de vroegste instantie die nog steeds wordt uitgevoerd:

       ![Vroegste lopende instantie selecteren](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Als u alleen exemplaren wilt weergeven die nog worden uitgevoerd, opent u de lijst **Alles** en selecteert u **Uitvoeren**.

    1. Selecteer Uitvoeren **annuleren**onder **Logische app uitvoeren**.

       ![Vroegste lopende instantie zoeken](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Als u deze mogelijkheid wilt omzeilen, voegt u een time-out toe aan elke actie die deze runs zou kunnen tegenhouden. Zie [Asynchrone duur wijzigen](#asynchronous-limits)als u in de codeeditor werkt. Als u de ontwerper anders gebruikt, voert u de volgende stappen uit:

    1. Selecteer in uw logische app bij de actie waarbij u een time-out wilt toevoegen in de rechterbovenhoek de knop ellipsen (**... )** en selecteer **Vervolgens Instellingen**.

       ![Actie-instellingen openen](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Geef **onder Time-out**de time-outduur op in [de ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Time-outduur opgeven](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Als u uw logica-app achtereenvolgens wilt uitvoeren, stelt u de gelijktijdigheid van de trigger `1` in op de codeweergaveeditor of de ontwerper. Zorg ervoor dat u de eigenschap van `operationOptions` de `SingleInstance` trigger niet ook instelt in de codeweergaveeditor. Anders krijgt u een validatiefout. Zie [Instanties opeenvolgend activeren](#sequential-trigger)voor meer informatie.

#### <a name="edit-in-code-view"></a>Bewerken in de codeweergave 

Voeg in de onderliggende `runtimeConfiguration.concurrency.runs` triggerdefinitie de eigenschap toe, `1` `50`die een waarde kan hebben die varieert van .

Hier is een voorbeeld dat gelijktijdige runs beperkt tot 10 exemplaren:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Zie [Configuratie-instellingen voor Runtime voor](#runtime-config-options)meer informatie .

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer in de rechterbovenhoek van de trigger de knop ellipsen (**... )** en selecteer **Vervolgens Instellingen**.

1. Stel onder **gelijktijdigheidscontrole** **limiet** **op aan .** 

1. Sleep de schuifregelaar **Mate van Parallelisme** naar de gewenste waarde. Als u uw logische app achtereenvolgens wilt uitvoeren, sleept u de schuifregelaarwaarde naar **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>"voor elke" gelijktijdigheid wijzigen

Standaard worden 'voor elke' lusiteraties allemaal tegelijkertijd uitgevoerd (gelijktijdig of parallel). Dit gedrag betekent dat elke iteratie wordt gestart voordat de vorige iteratie is voltooid. Het aantal gelijktijdig lopende iteraties heeft echter een [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Wanneer het aantal gelijktijdig lopende iteraties deze limiet bereikt, moeten alle andere iteraties wachten om uit te voeren.

Als u de standaardlimiet wilt wijzigen, u de codeweergaveeditor of Logic Apps Designer `runtimeConfiguration.concurrency.repetitions` gebruiken, omdat het wijzigen van de gelijktijdigheidsinstelling via de ontwerper de eigenschap in de onderliggende actiedefinitie 'voor elke' toevoegt of bijwerkt en vice versa. Deze eigenschap regelt het maximum aantal iteraties dat parallel kan worden uitgevoerd.

> [!NOTE] 
> Als u de actie 'voor elke' instelt om achtereenvolgens uit te voeren met behulp `operationOptions` van `Sequential` de ontwerper of de codeweergaveeditor, stelt u de eigenschap van de actie niet in op in de codeweergaveeditor. Anders krijgt u een validatiefout. Zie ['Voor elke looploop selopen' achtereenvolgens uitvoeren](#sequential-for-each)voor meer informatie.

#### <a name="edit-in-code-view"></a>Bewerken in de codeweergave 

Voeg in de onderliggende definitie 'voor `runtimeConfiguration.concurrency.repetitions` elke' de eigenschap toe `1` of `50`bij, die een waarde kan hebben die varieert van en .

Hier is een voorbeeld dat gelijktijdige runs beperkt tot 10 iteraties:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Zie [Configuratie-instellingen voor Runtime voor](#runtime-config-options)meer informatie .

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer in de **actie Voor elke** actie in de rechterbovenhoek de knop ellipsen ( ...**)** en selecteer **Vervolgens Instellingen**.

1. Stel **concurrencycontrol** in op **Aan**. **Concurrency Control**

1. Sleep de schuifregelaar **Mate van Parallelisme** naar de gewenste waarde. Als u uw logische app achtereenvolgens wilt uitvoeren, sleept u de schuifregelaarwaarde naar **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Limiet voor wachtritten wijzigen

Standaard worden logische app-werkstroominstanties op hetzelfde moment uitgevoerd (gelijktijdig of parallel). Dit gedrag betekent dat elke triggerinstantie wordt geactiveerd voordat de eerder actieve werkstroominstantie is uitgevoerd. Het aantal gelijktijdig lopende exemplaren heeft echter een [standaardlimiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wanneer het aantal gelijktijdig lopende werkstroomexemplaren deze limiet bereikt, moeten alle andere nieuwe exemplaren wachten met uitvoeren.

Het aantal wachtruns heeft ook een [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Wanneer het aantal wachtruns deze limiet bereikt, accepteert de Logic Apps-engine geen nieuwe runs meer. Aanvraag en webhook triggers keren 429 fouten en terugkerende triggers beginnen polling pogingen over te slaan.

U niet alleen [de standaardlimiet voor triggergelijktijdigwissel wijzigen,](#change-trigger-concurrency)maar u ook de standaardlimiet voor wachtuitvoeringen wijzigen. Voeg in de onderliggende `runtimeConfiguration.concurrency.maximumWaitingRuns` triggerdefinitie de eigenschap toe, `1` `100`die een waarde kan hebben die varieert van .

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Zie [Configuratie-instellingen voor Runtime voor](#runtime-config-options)meer informatie .

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Instanties opeenvolgend activeren

Als u elke koppelingswerktaakinstantie voor logica-apps alleen wilt uitvoeren nadat de vorige instantie is uitgevoerd, stelt u de trigger in om achtereenvolgens uit te voeren. U de codeweergaveeditor of Logic Apps Designer gebruiken, omdat het `runtimeConfiguration.concurrency.runs` wijzigen van de gelijktijdigheidsinstelling via de ontwerper ook de eigenschap in de onderliggende triggerdefinitie toevoegt of bijwerkt en vice versa.

> [!NOTE] 
> Wanneer u een trigger instelt om achtereenvolgens uit te voeren met behulp van `operationOptions` de `Sequential` ontwerper of de codeweergaveeditor, stelt u de eigenschap van de trigger niet in op in de codeweergaveeditor. Anders krijgt u een validatiefout. 

#### <a name="edit-in-code-view"></a>Bewerken in de codeweergave

Stel in de triggerdefinitie een van deze eigenschappen in, maar niet beide. 

Stel `runtimeConfiguration.concurrency.runs` de `1`eigenschap in op :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*-or-*

Stel `operationOptions` de `SingleInstance`eigenschap in op :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Zie [Configuratie-instellingen voor runtime](#runtime-config-options) en [bewerkingsopties voor](#operation-options)meer informatie.

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer in de rechterbovenhoek van de trigger de knop ellipsen (**... )** en selecteer **Vervolgens Instellingen**.

1. Stel onder **gelijktijdigheidscontrole** **limiet** **op aan .** 

1. Sleep de schuifregelaar **Mate van Parallelisme** naar het getal `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Loops 'voor elke' loops opeenvolgend uitvoeren

Als u een lusiteratie voor elke lus alleen wilt uitvoeren nadat de vorige iteratie is uitgevoerd, stelt u de actie 'voor elke' in om opeenvolgend uit te voeren. U de codeweergaveeditor of Logic Apps Designer gebruiken, omdat het wijzigen `runtimeConfiguration.concurrency.repetitions` van de gelijktijdigheid van de actie via de ontwerper de eigenschap ook toevoegt of bijwerkt in de onderliggende actiedefinitie en vice versa.

> [!NOTE] 
> Wanneer u een actie 'voor elke' instelt om achtereenvolgens uit te voeren met behulp `operationOptions` van `Sequential` de ontwerper of codeweergaveeditor, stelt u de eigenschap van de actie niet in op in de codeweergaveeditor. Anders krijgt u een validatiefout. 

#### <a name="edit-in-code-view"></a>Bewerken in de codeweergave

Stel in de actiedefinitie een van deze eigenschappen in, maar niet beide. 

Stel `runtimeConfiguration.concurrency.repetitions` de `1`eigenschap in op :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*-or-*

Stel `operationOptions` de `Sequential`eigenschap in op :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Zie [Configuratie-instellingen voor runtime](#runtime-config-options) en [bewerkingsopties voor](#operation-options)meer informatie.

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer in de rechterbovenhoek **van Voor elke** actie de knop Ellipsen ( ...**)** en selecteer **Vervolgens Instellingen**.

1. Stel **concurrencycontrol** in op **Aan**. **Concurrency Control**

1. Sleep de schuifregelaar **Mate van Parallelisme** naar het getal `1`.

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Acties synchroon uitvoeren

Standaard volgen alle op HTTP gebaseerde acties het standaard asynchrone bewerkingspatroon. Dit patroon geeft aan dat wanneer een op HTTP gebaseerde actie een verzoek naar het opgegeven eindpunt verzendt, de externe server een antwoord '202 ACCEPTED' terugstuurt. Dit antwoord betekent dat de server het verzoek tot verwerking heeft geaccepteerd. De Logic Apps-engine blijft de URL controleren die is opgegeven door de locatiekop van het antwoord totdat de verwerking stopt, wat een niet-202-antwoord is.

Aanvragen hebben echter een time-outlimiet, dus voor langlopende acties u het `operationOptions` asynchrone gedrag uitschakelen door de eigenschap toe te voegen en in te `DisableAsyncPattern` stellen onder de invoer van de actie.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

Zie [Bewerkingsopties](#operation-options)voor meer informatie .

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Uitvoeren in de modus Hoge doorvoer

Voor één logische app-definitie heeft het aantal acties dat elke 5 minuten wordt uitgevoerd een [standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Als u deze limiet wilt verhogen `operationOptions` tot `OptimizedForHighThroughput`het [maximaal](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) mogelijke, stelt u de eigenschap in op . Met deze instelling wordt uw logische app in de modus 'hoge doorvoer' ingesteld.

> [!NOTE]
> De modus Hoge doorvoer is in preview. U een werkbelasting ook verdelen over meer dan één logische app als dat nodig is.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Triggers en acties verifiëren

HTTP- en HTTPS-eindpunten ondersteunen verschillende soorten verificatie. Op basis van de trigger of actie die u gebruikt om uitgaande oproepen of verzoeken te doen om toegang te krijgen tot deze eindpunten, u kiezen uit verschillende reeksen verificatietypen. Zie [Verificatie toevoegen aan uitgaande gesprekken voor](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)meer informatie .

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [taal voor werkstroomdefinitie](../logic-apps/logic-apps-workflow-definition-language.md)
