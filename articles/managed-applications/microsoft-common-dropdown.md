---
title: Gebruikers interface-element van Azure vervolg keuzelijst | Microsoft Docs
description: Hierin wordt het micro soft. common. vervolg keuzelijst gebruikers interface-element voor Azure Portal beschreven. Gebruiken om te selecteren uit beschik bare opties bij het implementeren van een beheerde toepassing.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e1db91362d17d9100199f0b30119f8024f00a903
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151337"
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

- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde ervan is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label in `constraints.allowedValues`zijn. Als u niets opgeeft, wordt het eerste item in `constraints.allowedValues` geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues` moet ten minste één item bevatten.
- Als u een waarde wilt emuleren die niet vereist is, voegt u een item met een label en waarde van `""` (lege teken reeks) toe aan `constraints.allowedValues`.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
