---
title: DropDown-gebruikersinterface-element
description: Beschrijft het Microsoft.Common.DropDown UI-element voor Azure-portal. Gebruik om uit beschikbare opties te kiezen bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652383"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI-element

Een selectiebesturingselement met een vervolgkeuzelijst.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Indien dit is opgegeven, moet de `constraints.allowedValues`standaardwaarde een label zijn dat aanwezig is in . Als dit niet is `constraints.allowedValues` opgegeven, is het eerste item in geselecteerd. De standaardwaarde is **null**.
- `constraints.allowedValues`moet ten minste één item hebben.
- Als u een waarde wilt emuleren die niet `""` vereist is, `constraints.allowedValues`voegt u een artikel met een label en de waarde van (lege tekenreeks) toe aan .

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
