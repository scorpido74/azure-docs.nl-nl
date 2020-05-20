---
title: Definitie van het app-schema
description: De LUIS-app wordt weer gegeven in de `.json` of `.lu` en bevat alle intenties, entiteiten, voor beelden van uitingen, functies en instellingen.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: dbbeb4eacbe8e07cf080b3a1527ca39c2b9a5991
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684359"
---
# <a name="app-schema-definition"></a>Definitie van het app-schema

De LUIS-app wordt weer gegeven in de `.json` of `.lu` en bevat alle intenties, entiteiten, voor beelden van uitingen, functies en instellingen.

## <a name="format"></a>Indeling

Wanneer u de app importeert en exporteert, kiest u `.json` of `.lu` .

|Indeling|Informatie|
|--|--|
|`.json`| Standaard indeling voor Program meren|
|`.lu`|Ondersteund door de [bot Builder-hulpprogram ma's](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)van bot Framework.|

## <a name="version-7x"></a>Versie 7. x

* Verplaatsen naar versie 7. x worden de entiteiten weer gegeven als geneste machine learning-entiteiten.
* Ondersteuning voor het ontwerpen van geneste machine-learning entiteiten met `enableNestedChildren` de eigenschap in de volgende ontwerp-api's:
    * [Label toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Batch label toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Labels controleren](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Eindpunt query's voor entiteiten Voorst Ellen](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Eindpunt query's voor intenties Voorst Ellen](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>Versie 6. x

* Als u wilt overstappen op versie 6. x, gebruikt u de nieuwe [machine learning-entiteit](reference-entity-machine-learned-entity.md) om uw entiteiten weer te geven.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Versie 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Volgende stappen

* Migreren naar de [v3-ontwerp-api's](luis-migration-authoring-entities.md)