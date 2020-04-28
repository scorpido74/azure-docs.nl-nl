---
title: OptionsGroup UI-element
description: Hierin wordt het micro soft. common. OptionsGroup UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers kiezen uit beschik bare opties bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652344"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Micro soft. common. OptionsGroup UI-element

Een selectie besturings element met een rij beschik bare opties.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"two"
```

## <a name="remarks"></a>Opmerkingen

- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label zijn in `constraints.allowedValues`. Als u niets opgeeft, wordt het eerste `constraints.allowedValues` item in standaard geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues`moet ten minste één item bevatten.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
