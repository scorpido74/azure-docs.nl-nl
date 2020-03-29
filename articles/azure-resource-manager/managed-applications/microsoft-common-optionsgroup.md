---
title: Gebruikersinterface-element OptionsGroup
description: Beschrijft het Microsoft.Common.OptionsGroup-element voor Azure-portal. Hiermee kunnen gebruikers kiezen uit beschikbare opties bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652344"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup-element

Een selectiebesturingselement met een rij beschikbare opties.

## <a name="ui-sample"></a>UI-voorbeeld

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

- Het label `constraints.allowedValues` voor is de weergavetekst voor een item en de waarde ervan is de uitvoerwaarde van het element wanneer deze is geselecteerd.
- Indien dit is opgegeven, moet de `constraints.allowedValues`standaardwaarde een label zijn dat aanwezig is in . Als dit niet is `constraints.allowedValues` opgegeven, is het eerste item standaard geselecteerd. De standaardwaarde is **null**.
- `constraints.allowedValues`moet ten minste één item hebben.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
