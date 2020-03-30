---
title: Referentie van het werkdefinitietaalschema
description: Naslaggids voor het JSON-schema en de syntaxis voor de werkstroomdefinitietaal die werkstromen in Azure Logic Apps beschrijft
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283860"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Schema-naslaggids voor de taal voor werkstroomdefinitie in Azure Logic Apps

Wanneer u een logische app maakt in [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)heeft uw logische app een onderliggende werkstroomdefinitie die de werkelijke logica beschrijft die in uw logische app wordt uitgevoerd. Die werkstroomdefinitie maakt gebruik [van JSON](https://www.json.org/) en volgt een structuur die is gevalideerd door het schema Van de definitievan werkstroom. Deze verwijzing geeft een overzicht van deze structuur en hoe het schema kenmerken definieert in uw werkstroomdefinitie.

## <a name="workflow-definition-structure"></a>Werkstroomdefinitiestructuur

Een werkstroomdefinitie bevat altijd een trigger voor het instantiëren van uw logische app, plus een of meer acties die worden uitgevoerd nadat de trigger is geactiveerd.

Hier is de structuur op hoog niveau voor een werkstroomdefinitie:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Kenmerk | Vereist | Beschrijving |
|-----------|----------|-------------|
| `definition` | Ja | Het beginelement voor uw werkstroomdefinitie |
| `$schema` | Alleen wanneer extern wordt verwezen naar een werkstroomdefinitie | De locatie voor het JSON-schemabestand dat de versie van de werkstroomdefinitietaal beschrijft, die u hier vinden: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nee | De definities voor een of meer acties die moeten worden uitgevoerd tijdens de werkstroomruntijd. Zie [Triggers en acties](#triggers-actions)voor meer informatie. <p><p>Maximale acties: 250 |
| `contentVersion` | Nee | Het versienummer voor uw werkstroomdefinitie, die standaard '1.0.0.0' is. Als u wilt helpen bij het identificeren en bevestigen van de juiste definitie bij het implementeren van een werkstroom, geeft u een te gebruiken waarde op. |
| `outputs` | Nee | De definities voor de uitvoer om terug te keren van een werkstroom worden uitgevoerd. Zie [Uitvoer voor](#outputs)meer informatie . <p><p>Maximale uitgangen: 10 |
| `parameters` | Nee | De definities voor een of meer parameters die de waarden doorgeven die u wilt gebruiken tijdens de runtime van uw logische app. Zie [Parameters](#parameters)voor meer informatie . <p><p>Maximale parameters: 50 |
| `staticResults` | Nee | De definities voor een of meer statische resultaten die worden geretourneerd door acties als mock-uitgangen wanneer statische resultaten zijn ingeschakeld voor deze acties. In elke actiedefinitie `runtimeConfiguration.staticResult.name` verwijst het kenmerk `staticResults`naar de overeenkomstige definitie binnenin . Zie [Statische resultaten voor](#static-results)meer informatie . |
| `triggers` | Nee | De definities voor een of meer triggers die uw workflow instantiëren. U meer dan één trigger definiëren, maar alleen met de taal voor werkstroomdefinitie, niet visueel via de Logic Apps Designer. Zie [Triggers en acties](#triggers-actions)voor meer informatie. <p><p>Maximale triggers: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Triggers en acties

In een werkstroomdefinitie definiëren de `triggers` secties de `actions` aanroepen die tijdens de uitvoering van uw werkstroom plaatsvinden. Zie [Werkstroomtriggers en -acties](../logic-apps/logic-apps-workflow-actions-triggers.md)voor syntaxis en meer informatie over deze secties.

<a name="parameters"></a>

## <a name="parameters"></a>Parameters

De implementatielevenscyclus heeft meestal verschillende omgevingen voor ontwikkeling, test, fasering en productie. Wanneer u logische apps implementeert in verschillende omgevingen, wilt u waarschijnlijk verschillende waarden gebruiken, zoals verbindingstekenreeksen, op basis van uw implementatiebehoeften. Of u hebt waarden die u in uw logische app opnieuw wilt gebruiken zonder hardcodering of die vaak veranderen. In de sectie `parameters` van uw werkstroomdefinitie u parameters definiëren of bewerken voor de waarden die uw logische app gebruikt tijdens uitvoering. U moet deze parameters eerst definiëren voordat u elders in uw werkstroomdefinitie naar deze parameters verwijzen.

Hier is de algemene structuur voor een parameterdefinitie:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Kenmerk | Vereist | Type | Beschrijving |
|-----------|----------|------|-------------|
| <*parameternaam*> | Ja | Tekenreeks | De naam voor de parameter die u wilt definiëren |
| <*parametertype*> | Ja | int, float, string, bool, array, object, securestring, secureobject <p><p>**Opmerking:** Voor alle wachtwoorden, sleutels en `securestring` `secureobject` geheimen gebruikt `GET` u de typen of typen omdat de bewerking deze typen niet retourneert. Zie [Beveiligingsaanbevelingen voor actie en invoerparameters voor](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)meer informatie over het beveiligen van parameters. | Het type voor de parameter |
| <*standaardparameterwaarde*> | Ja | Hetzelfde als`type` | De standaardparameterwaarde die moet worden gebruikt als er geen waarde is opgegeven wanneer de werkstroom wordt geinstantt. Het `defaultValue` kenmerk is vereist zodat de Logic App Designer de parameter correct kan weergeven, maar u een lege waarde opgeven. |
| <*array-met-toegestane parameterwaarden*> | Nee | Matrix | Een array met waarden die de parameter kan accepteren |
| <*parameterbeschrijving*> | Nee | JSON-object | Alle andere parameterdetails, zoals een beschrijving voor de parameter |
||||

Maak vervolgens een [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md) voor uw werkstroomdefinitie, definieer sjabloonparameters die de gewenste waarden bij implementatie accepteren, vervang hardcoded waarden met verwijzingen naar sjabloon- of werkstroomdefinitieparameters naar gelang van het geval en sla de waarden op die moeten worden gebruikt bij implementatie in een afzonderlijk [parameterbestand.](../azure-resource-manager/templates/parameter-files.md) Op die manier u deze waarden gemakkelijker wijzigen via het parameterbestand zonder dat u uw logische app hoeft bij te werken en opnieuw te implementeren. Voor informatie die gevoelig is of moet worden beveiligd, zoals gebruikersnamen, wachtwoorden en geheimen, u deze waarden opslaan in Azure Key Vault en uw parameterbestand deze waarden uit uw sleutelkluis laten ophalen. Zie [Overzicht: Implementatie automatiseren voor logische apps met Azure Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)voor meer informatie en voorbeelden over het definiëren van parameters op de sjabloon- en werkstroomdefinitieniveaus.

<a name="static-results"></a>

## <a name="static-results"></a>Statische resultaten

Definieer `staticResults` in het kenmerk de `outputs` mock `status` van een actie en dat de actie wordt geretourneerd wanneer de statische resultaatinstelling van de actie is ingeschakeld. In de definitie van `runtimeConfiguration.staticResult.name` de actie verwijst het kenmerk naar `staticResults`de naam voor de statische resultaatdefinitie binnenin . Ontdek hoe u logische apps [testen met nepgegevens door statische resultaten in te stellen.](../logic-apps/test-logic-apps-mock-data-static-results.md)

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Kenmerk | Vereist | Type | Beschrijving |
|-----------|----------|------|-------------|
| <*statische-resultaat-definitie-naam*> | Ja | Tekenreeks | De naam voor een statische resultaatdefinitie die `runtimeConfiguration.staticResult` een actiedefinitie kan verwijzen via een object. Zie [Configuratie-instellingen voor Runtime voor](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)meer informatie . <p>U elke unieke naam gebruiken die u wilt. Standaard wordt deze unieke naam toegevoegd aan een getal, dat zo nodig wordt verhoogd. |
| <*uitvoer-attributen-en-waarden-geretourneerd*> | Ja | Varieert | De vereisten voor deze kenmerken variëren op basis van verschillende voorwaarden. Wanneer het `status` kenmerk `Succeeded`bijvoorbeeld `outputs` kenmerken en waarden bevat die door de actie als mock-uitvoer worden geretourneerd. Als `status` het `Failed`kenmerk `outputs` het `errors` kenmerk bevat, is het een `message` array met een of meer foutobjecten met foutgegevens. |
| <*koptekstwaarden*> | Nee | JSON | Koptekstwaarden die door de actie worden geretourneerd |
| <*statuscode geretourneerd*> | Ja | Tekenreeks | De statuscode die door de actie wordt geretourneerd |
| <*actiestatus*> | Ja | Tekenreeks | De status van de actie, bijvoorbeeld, `Succeeded` of`Failed` |
|||||

In deze HTTP-actiedefinitie worden `runtimeConfiguration.staticResult.name` bijvoorbeeld `HTTP0` de `staticResults` verwijzingen in het kenmerk waar de mock-uitvoer voor de actie zijn gedefinieerd, verwijzingen naar het kenmerk. Het `runtimeConfiguration.staticResult.staticResultOptions` kenmerk geeft aan dat `Enabled` de statische resultaatinstelling zich op de HTTP-actie bevindt.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

De HTTP-actie retourneert `HTTP0` de `staticResults`uitvoer in de definitie binnen . In dit voorbeeld is `OK`voor de statuscode de mock-uitvoer . Voor kopwaarden is `"Content-Type": "application/JSON"`de mock-uitvoer . Voor de status van de actie `Succeeded`is de mock-uitvoer .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Expressies

Met JSON u letterlijke waarden hebben die bestaan in het ontwerp, bijvoorbeeld:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

U ook waarden hebben die niet bestaan tot de looptijd. Als u deze waarden wilt weergeven, u *expressies*gebruiken die worden geëvalueerd tijdens de looptijd. Een expressie is een reeks die een of meer [functies,](#functions) [operatoren,](#operators)variabelen, expliciete waarden of constanten kan bevatten. In uw werkstroomdefinitie u een expressie overal in een JSON-tekenreekswaarde\@gebruiken door de expressie vooraf te bevestigen met het bord ( ). Bij het evalueren van een expressie die een JSON-waarde \@ vertegenwoordigt, wordt de expressie-expressieexpressie geëxtraheerd door het teken te verwijderen en resulteert deze altijd in een andere JSON-waarde.

Voor de eerder gedefinieerde `customerName` eigenschap u bijvoorbeeld de eigenschapswaarde opvragen met de functie [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) in een expressie en die waarde aan de `accountName` eigenschap toewijzen:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Met stringinterpolatie* u ook meerdere expressies \@ in tekenreeksen gebruiken{}die zijn verpakt door het teken en de krullende accolade (). Hier is de syntaxis:

```json
@{ "<expression1>", "<expression2>" }
```

Het resultaat is altijd een tekenreeks, waardoor `concat()` deze mogelijkheid vergelijkbaar is met de functie, bijvoorbeeld: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Als u een letterlijke tekenreeks \@ hebt die \@ begint met \@ het teken, moet u het teken met een ander teken als escape-teken voorzetten:\@\@

In deze voorbeelden ziet u hoe expressies worden geëvalueerd:

| JSON-waarde | Resultaat |
|------------|--------|
| "Sophia Owen" | Geef deze personages terug: 'Sophia Owen' |
| "Array[1]" | Deze tekens retourneren: 'array[1]' |
| "\@\@" | Retourneer deze tekens als een\@tekenreeks met één teken: ' |
| " \@" | Retourneer deze tekens als een \@tekenreeks met twee tekens: ' |
|||

Stel dat u voor deze voorbeelden 'myBirthMonth' definieert die gelijk is aan 'januari' en 'myAge' gelijk aan het getal 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

In deze voorbeelden ziet u hoe de volgende expressies worden geëvalueerd:

| JSON-expressie | Resultaat |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Deze tekenreeks retourneren: "Januari" |
| "\@{parameters('myBirthMonth')}" | Deze tekenreeks retourneren: "Januari" |
| "parameters('myAge')"\@ | Dit nummer retourneren: 42 |
| "\@{parameters('myAge')}" | Dit nummer retourneren als tekenreeks: "42" |
| "Mijn leeftijd \@is {parameters('myAge')}" | Geef deze tekenreeks terug: "Mijn leeftijd is 42" |
| "\@concat('Mijn leeftijd is', string(parameters('myAge')))" | Geef deze tekenreeks terug: "Mijn leeftijd is 42" |
| "Mijn leeftijd \@ \@is {parameters('myAge')}" | Retourneer deze tekenreeks, die de uitdrukking \@bevat: "Mijn leeftijd is {parameters('myAge')}' |
|||

Wanneer u visueel werkt in de Logic Apps Designer, u expressies maken via de opbouwfunctie Expressie, bijvoorbeeld:

![Logic Apps Designer >:](./media/logic-apps-workflow-definition-language/expression-builder.png)

Wanneer u klaar bent, wordt de expressie weergegeven voor de overeenkomstige eigenschap in uw werkstroomdefinitie, bijvoorbeeld de `searchQuery` eigenschap hier:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>Uitvoer

Definieer `outputs` in de sectie de gegevens die uw werkstroom kan retourneren wanneer deze is voltooid. Als u bijvoorbeeld een specifieke status of waarde van elke uitvoering wilt bijhouden, geeft u op dat de werkstroomuitvoer die gegevens retourneert.

> [!NOTE]
> Wanneer u reageert op binnenkomende aanvragen van de `outputs`REST-API van een service, gebruikt u geen . Gebruik in `Response` plaats daarvan het actietype. Zie [Werkstroomtriggers en -acties](../logic-apps/logic-apps-workflow-actions-triggers.md)voor meer informatie.

Hier is de algemene structuur voor een output definitie:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Kenmerk | Vereist | Type | Beschrijving |
|-----------|----------|------|-------------|
| <*sleutelnaam*> | Ja | Tekenreeks | De sleutelnaam voor de opbrengstretourwaarde |
| <*sleuteltype*> | Ja | int, float, string, securestring, bool, array, JSON object | Het type voor de opbrengstretourwaarde |
| <*sleutelwaarde*> | Ja | Hetzelfde als <*sleuteltype*> | De opbrengstretourwaarde |
|||||

Als u de uitvoer uit een werkstroom wilt uitvoeren, controleert u de rungeschiedenis en details van uw logische app in de Azure-portal of gebruikt u de [API Voor werkstroomREST.](https://docs.microsoft.com/rest/api/logic/workflows) U de uitvoer ook doorgeven aan externe systemen, bijvoorbeeld Power BI, zodat u dashboards maken.

<a name="operators"></a>

## <a name="operators"></a>Operators

In [expressies](#expressions) en [functies](#functions)voeren operators specifieke taken uit, zoals een eigenschap verwijzen of een waarde in een array.

| Operator | Taak |
|----------|------|
| ' | Als u een tekenreeks letterlijk wilt gebruiken als invoer of in expressies `'<myString>'`en functies, wikkelt u de tekenreeks alleen met enkele aanhalingstekens, bijvoorbeeld . Gebruik geen dubbele aanhalingstekens (""), die in strijd zijn met de JSON-opmaak rond een hele expressie. Bijvoorbeeld: <p>**Ja**: lengte('Hallo') </br>**Nee**: lengte("Hallo") <p>Wanneer u arrays of getallen passeert, hebt u geen inloopinterpunctie nodig. Bijvoorbeeld: <p>**Ja**: lengte([1, 2, 3]) </br>**No**: lengte("[1, 2, 3]") |
| [] | Als u wilt verwijzen naar een waarde op een specifieke positie (index) in een array, gebruikt u vierkante haakjes. Bijvoorbeeld om het tweede item in een array te krijgen: <p>`myArray[1]` |
| . | Als u een eigenschap in een object wilt verwijzen, gebruikt u de puntoperator. Bijvoorbeeld om de `name` eigenschap voor `customer` een JSON-object op te halen: <p>`"@parameters('customer').name"` |
| ? | Als u null-eigenschappen in een object wilt verwijzen zonder runtime-fout, gebruikt u de operator van het vraagteken. Als u bijvoorbeeld null-uitvoer van een trigger wilt verwerken, u deze expressie gebruiken: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Sommige expressies halen hun waarden uit runtime-acties die mogelijk nog niet bestaan wanneer de werkstroomdefinitie begint uit te voeren. Als u deze waarden wilt verwijzen of ermee wilt werken in expressies, u [*functies*](../logic-apps/workflow-definition-language-functions-reference.md) gebruiken die de taal voor werkstroomdefinitie biedt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over acties en triggers voor [taalvan werkstroomdefinitie](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Meer informatie over het programmatisch maken en beheren van logische apps met de [Workflow REST API](https://docs.microsoft.com/rest/api/logic/workflows)
