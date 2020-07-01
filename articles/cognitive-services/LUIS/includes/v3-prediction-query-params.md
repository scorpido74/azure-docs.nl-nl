---
title: Para meters voor de V3 API-query teken reeks
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610779"
---
Para meters voor de V3 API-query reeks zijn:

|Query parameter|Naam van LUIS-Portal|Type|Versie|Standaard|Doel|
|--|--|--|--|--|--|
|`log`|Logboeken opslaan|booleaans|V2 & V3|false|Sla de query op in het logboek bestand. De standaard waarde is False.|
|`query`|-|tekenreeks|Alleen v3|Geen standaard-dit is vereist in de GET-aanvraag|**In v2**bevindt de utterance die moet worden voor speld in de `q` para meter. <br><br>**In v3**wordt de functionaliteit door gegeven in de `query` para meter.|
|`show-all-intents`|Scores voor alle intenties toevoegen|booleaans|Alleen v3|false|Alle intenties retour neren met de bijbehorende Score in het object voor **spellingen** . Intenties worden geretourneerd als objecten in een bovenliggend `intents` object. Zo kunt u programmatisch toegang krijgen zonder dat u de bedoeling in een matrix hoeft te vinden: `prediction.intents.give` . In v2 zijn deze geretourneerd in een matrix. |
|`verbose`|Meer details van entiteiten toevoegen|booleaans|V2 & V3|false|**In v2**zijn alle voorspelde intenten geretourneerd als deze zijn ingesteld op True. Als u alle voorspelde intenties nodig hebt, gebruikt u de V3-para meter van `show-all-intents` .<br><br>**In v3**biedt deze para meter alleen details van entiteits-meta gegevens van de voor spelling van de entiteit.  |
|`timezoneOffset`|-|tekenreeks|V2|-|De tijd zone die wordt toegepast op datetimeV2-entiteiten.|
|`datetimeReference`|-|tekenreeks|V3|-|De [tijd zone](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) die wordt toegepast op datetimeV2-entiteiten. Vervangt door `timezoneOffset` v2.|