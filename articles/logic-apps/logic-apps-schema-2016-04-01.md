---
title: Schema-updates juni-1-2016
description: Bijgewerkte schemaversie 2016-06-01 voor definities van logische apps in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792871"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schema-updates voor Azure Logic Apps - 1 juni 2016

De [bijgewerkte schema-](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) en API-versie voor Azure Logic Apps bevat belangrijke verbeteringen die logische apps betrouwbaarder en gebruiksvriendelijker maken:

* [Met scopes](#scopes) kun je acties groeperen of nesten als een verzameling acties.
* [Voorwaarden en lussen](#conditions-loops) zijn nu eersteklas acties.
* Nauwkeuriger bestellen voor het `runAfter` uitvoeren van acties met het pand, ter vervanging`dependsOn`

Als u uw logische apps wilt upgraden van het previewschema van 1 augustus 2015 naar het schema van 1 juni 2016, [raadpleegt u de upgradesectie](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Bereiken

Dit schema bevat scopes waarmee u acties groeperen of acties in elkaar nesten. Een voorwaarde kan bijvoorbeeld een andere voorwaarde bevatten. Meer informatie over [de syntaxis van het bereik](../logic-apps/logic-apps-loops-and-scopes.md)of dit voorbeeld van het basisbereik bekijken:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Voorwaarden en lussen veranderen

In eerdere schemaversies waren voorwaarden en lussen parameters die aan één actie waren gekoppeld. Dit schema tilt deze beperking op, zodat voorwaarden en lussen nu beschikbaar zijn als actietypen. Meer informatie over [lussen en scopes,](../logic-apps/logic-apps-loops-and-scopes.md) [voorwaarden](../logic-apps/logic-apps-control-flow-conditional-statement.md)of dit basisvoorbeeld waarin een voorwaardeactie wordt weergegeven:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>'runAfter' eigenschap

De `runAfter` eigenschap `dependsOn`vervangt, waardoor u meer precisie geeft wanneer u de taak voor uitvoeren opgeeft op basis van de status van eerdere acties. De `dependsOn` eigenschap gaf aan of "de actie is uitgevoerd en is geslaagd", op basis van de vraag of de vorige actie is geslaagd, mislukt of als overgeslagen - niet het aantal keren dat u de actie wilt uitvoeren. De `runAfter` eigenschap biedt flexibiliteit als een object dat alle actienamen opgeeft waarna het object wordt uitgevoerd. Deze eigenschap definieert ook een reeks statussen die acceptabel zijn als triggers. Als u bijvoorbeeld wilt dat een actie wordt uitgevoerd nadat actie A is `runAfter` geslaagd en ook nadat actie B is geslaagd of mislukt, stelt u de eigenschap in:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uw schema bijwerken

Als u wilt upgraden naar het [meest recente schema,](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)hoeft u slechts een paar stappen te nemen. Het upgradeproces omvat het uitvoeren van het upgradescript, opslaan als een nieuwe logische app en als u dat wilt, mogelijk het overschrijven van de vorige logische app.

1. Open uw logische app in de Azure-portal.

2. Ga naar **Overzicht**. Kies **Schema bijwerken**op de werkbalk logische app .
   
   ![Schema bijwerken kiezen][1]
   
   De bijgewerkte definitie wordt geretourneerd, die u indien nodig kopiëren en plakken in een resourcedefinitie. 

   > [!IMPORTANT]
   > *Zorg ervoor dat* u **Opslaan als** kiest, zodat alle verbindingsverwijzingen geldig blijven in de bijgewerkte logica-app.

3. Kies **opslaan als**op de werkbalk van het upgradeblad .

4. Voer de logische naam en status in. Als u uw bijgewerkte logica-app wilt implementeren, kiest u **Maken**.

5. Controleer of uw bijgewerkte logica-app werkt zoals verwacht.
   
   > [!NOTE]
   > Als u een handleiding of verzoektrigger gebruikt, wordt de URL van de terugbel-terugroep in uw nieuwe logische app gewijzigd. Test de nieuwe URL om ervoor te zorgen dat de end-to-end ervaring werkt. Als u eerdere URL's wilt behouden, u uw bestaande logische app klonen.

6. *Optioneel* Als u uw vorige logische app wilt overschrijven met de nieuwe schemaversie, kiest u op de werkbalk **Kloon**naast **Schema bijwerken**. Deze stap is alleen nodig als u dezelfde bron-id wilt behouden of de URL van de trigger van uw logische app wilt activeren.

## <a name="upgrade-tool-notes"></a>Notities met het hulpprogramma bijwerken

### <a name="mapping-conditions"></a>Voorwaarden in kaart brengen

In de geüpgradede definitie doet de tool de beste moeite om true- en false branch-acties samen te groeperen als een scope. In het bijzonder, `@equals(actions('a').status, 'Skipped')` de `else` ontwerper patroon van verschijnt als een actie. Als het gereedschap echter onherkenbare patronen detecteert, kan het gereedschap afzonderlijke voorwaarden creëren voor zowel de ware als de valse vertakking. U acties opnieuw toewijzen na een upgrade, indien nodig.

#### <a name="foreach-loop-with-condition"></a>"foreach" lus met voorwaarde

In het nieuwe schema u de filteractie gebruiken om het patroon te repliceren dat een **voor elke** lus gebruikt met één voorwaarde per item. De wijziging vindt echter automatisch plaats wanneer u een upgrade uitvoert. De voorwaarde wordt een filteractie die wordt weergegeven voorafgaand aan de **voor elke** lus, waarbij alleen een reeks items wordt geretourneerd die overeenkomen met de voorwaarde en die array wordt doorgeretourond aan **Voor elke** actie. Zie Bijvoorbeeld [Lussen en scopes.](../logic-apps/logic-apps-loops-and-scopes.md)

### <a name="resource-tags"></a>Resourcetags

Nadat u een upgrade hebt uitgevoerd, worden brontags verwijderd, dus u moet ze opnieuw instellen voor de bijgewerkte werkstroom.

## <a name="other-changes"></a>Andere wijzigingen

### <a name="renamed-manual-trigger-to-request-trigger"></a>Omgedoopt tot 'handmatige' trigger naar 'request' trigger

Het `manual` triggertype is afgeschaft en omgedoopt `request` tot `http`met tekst . Deze wijziging zorgt voor meer consistentie voor het soort patroon dat de trigger wordt gebruikt om te bouwen.

### <a name="new-filter-action"></a>Nieuwe 'filter'-actie

Als u een grote array wilt filteren tot `filter` een kleinere set items, accepteert het nieuwe type een array en een voorwaarde, evalueert het de voorwaarde voor elk item en retourneert het een array met items die aan de voorwaarde voldoen.

### <a name="restrictions-for-foreach-and-until-actions"></a>Beperkingen voor 'foreach' en 'until' acties

De `foreach` `until` en lus zijn beperkt tot een enkele actie.

### <a name="new-trackedproperties-for-actions"></a>Nieuwe 'trackedProperties' voor acties

Acties kunnen nu een `trackedProperties`extra eigenschap genaamd `runAfter` , `type` dat is broer of zus aan de en eigenschappen. Met dit object worden bepaalde actie-ingangen of uitvoer opgegeven die u wilt opnemen in de Azure Diagnostic-telemetrie, die wordt uitgestoten als onderdeel van een werkstroom. Bijvoorbeeld:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Werkstroomdefinities voor logische apps maken](../logic-apps/logic-apps-author-definitions.md)
* [Implementatie van logische apps automatiseren](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
