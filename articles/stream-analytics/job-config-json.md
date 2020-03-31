---
title: Azure Stream Analytics JobConfig.json
description: In dit artikel worden de ondersteunde velden weergegeven voor het Azure Stream Analytics JobConfig.json-bestand dat wordt gebruikt om taken te maken in Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617955"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.json

De volgende velden worden ondersteund in het *bestand JobConfig.json* dat wordt gebruikt om [een Azure Stream Analytics-taak](quick-create-vs-code.md)te maken met behulp van Visual Studio Code .

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Name|Type|Vereist|Waarde|
|----|----|--------|-----|
|DataLocale|tekenreeks|Nee|De gegevensland van de taak streamanalytics. Waarde moet de naam van een ondersteunde. Standaard 'en-US' als er geen opgegeven.|
|Uitvoerfoutbeleid|tekenreeks|Nee|Geeft het beleid aan dat moet worden toegepast op gebeurtenissen die tot de uitvoer komen en niet naar de externe opslag kunnen worden geschreven omdat deze niet zijn vervormd (ontbrekende kolomwaarden, kolomwaarden van verkeerd type of grootte). - Stoppen of vallen|
|EventslatearrivalmaxDelayinSeconds|geheel getal|Nee|De maximale toelaatbare vertraging in seconden waar gebeurtenissen te laat aankomen zou kunnen worden opgenomen. Het ondersteunde bereik is -1 tot 1814399 (20.23:59:59 dagen) en -1 wordt gebruikt om wachttijd voor onbepaalde tijd aan te geven. Als de eigenschap afwezig is, wordt het geïnterpreteerd om een waarde van -1 te hebben.|
|EventsoutofordermaxDelayinSeconds|geheel getal|Nee|De maximaal toelaatbare vertraging in seconden waar out-of-order gebeurtenissen kunnen worden aangepast om weer in orde te zijn.|
|EventsOutOfOrderPolicy|tekenreeks|Nee|Geeft het beleid aan dat moet worden toegepast op gebeurtenissen die niet in orde komen in de invoergebeurtenisstream. - Aanpassen of neerzetten|
|StreamingEenheden|geheel getal|Ja|Hiermee geeft u het aantal streaming-eenheden op dat de streamingtaak gebruikt.|
|Compatibiliteitsniveau|tekenreeks|Nee|Hiermee bepaalt u bepaalde runtime-gedragingen van de streamingtaak. - Aanvaardbare waarden zijn "1.0", "1.1", "1.2"|
|UseSystemAssignedIdentity|booleaans|Nee|Stel de true in om deze taak in staat te stellen te communiceren met andere Azure-services als zichzelf met behulp van een Beheerde Azure Active Directory Identity.|
|GlobalStorage.AccountNaam|tekenreeks|Nee|Het wereldwijde opslagaccount wordt gebruikt voor het opslaan van inhoud die betrekking heeft op uw streamanalytics-taak, zoals SQL-referentiegegevensmomentopnamen.|
|GlobalStorage.AccountSleutel|tekenreeks|Nee|Overeenkomstige sleutel voor een wereldwijd opslagaccount.|
|GegevensBronCredentialDomain|tekenreeks|Nee|Gereserveerde eigenschap voor lokale opslag van referenties.|
|ScriptType|tekenreeks|Ja|Gereserveerde eigenschap om het type van dit bronbestand aan te geven. Aanvaardbare waarde is "JobConfig" voor JobConfig.json.|
|Tags|JSON-sleutelwaardeparen|Nee|Tags zijn naam-/waardeparen waarmee u resources categoriseren en geconsolideerde facturering weergeven door dezelfde tag toe te passen op meerdere bronnen en resourcegroepen. Tagnamen zijn hoofdlettergevoelig en tagwaarden zijn hoofdlettergevoelig.|

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Stream Analytics-taak maken in Visual Studio Code](quick-create-vs-code.md)
* [Query's van TestStream Analytics lokaal met voorbeeldgegevens met Behulp van Visual Studio Code](visual-studio-code-local-run.md)
* [Query's van Test Stream Analytics lokaal tegen live stream-invoer met behulp van Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[Een Azure Stream Analytics-taak implementeren met behulp van CI/CD npm-pakket](setup-cicd-vs-code.md)