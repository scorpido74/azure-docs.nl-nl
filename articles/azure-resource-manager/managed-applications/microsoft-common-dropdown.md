---
title: Vervolg keuzelijst GEBRUIKERSINTERFACE element
description: Hierin wordt het micro soft. common. vervolg keuzelijst gebruikers interface-element voor Azure Portal beschreven. Gebruiken om te selecteren uit beschik bare opties bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652383"
---
# <a name="microsoftcommondropdown-ui-element"></a>Micro soft. common. vervolg keuzelijst UI-element

Een selectie besturings element met een vervolg keuzelijst.

## <a name="ui-sample"></a>UI-voor beeld

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

- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label zijn in `constraints.allowedValues` . Als u niets opgeeft, wordt het eerste item in `constraints.allowedValues` geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues`moet ten minste één item bevatten.
- Als u een waarde wilt emuleren die niet vereist is, voegt u een item met een label en waarde van `""` (lege teken reeks) toe aan `constraints.allowedValues` .

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
