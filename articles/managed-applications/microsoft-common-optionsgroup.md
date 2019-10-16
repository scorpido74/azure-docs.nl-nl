---
title: Gebruikers interface-element van Azure OptionsGroup | Microsoft Docs
description: Hierin wordt het micro soft. common. OptionsGroup UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers kiezen uit beschik bare opties bij het implementeren van een beheerde toepassing.
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
ms.openlocfilehash: 7bec506575f1c526e59487edb67349cdf862f1a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331676"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Micro soft. common. OptionsGroup UI-element
Een selectie besturings element met een rij beschik bare opties.

## <a name="ui-sample"></a>UI-voor beeld
![Micro soft. common. OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

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

## <a name="remarks"></a>Opmerkingen
- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde ervan is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label in `constraints.allowedValues` zijn. Als u niets opgeeft, wordt het eerste item in `constraints.allowedValues` standaard geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues` moet ten minste één item bevatten.

## <a name="sample-output"></a>Voorbeelduitvoer
```json
"two"
```

## <a name="next-steps"></a>Volgende stappen
* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
