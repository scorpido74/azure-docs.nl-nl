---
title: TextBox-element UI
description: Beschrijft het Gebruikersinterface-element Microsoft.Common.TextBox voor Azure-portal. Gebruiken voor het toevoegen van niet-opgemaakte tekst.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652279"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox-element UI

Een besturingselement dat kan worden gebruikt om niet-opgemaakte tekst te bewerken.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"my text value"
```

## <a name="remarks"></a>Opmerkingen

- Als `constraints.required` dit is ingesteld op **true,** moet het tekstvak een waarde hebben om te valideren. De standaardwaarde is **onwaar**.
- `constraints.regex`is een JavaScript-regelmatig expressiepatroon. Als dit is opgegeven, moet de waarde van het tekstvak overeenkomen met het patroon dat met succes moet worden gevalideerd. De standaardwaarde is **null**.
- `constraints.validationMessage`is een tekenreeks die moet worden weergegeven wanneer de waarde van het tekstvak de validatie mislukt. Als dit niet is opgegeven, worden de ingebouwde validatieberichten van het tekstvak gebruikt. De standaardwaarde is **null**.
- Het is mogelijk om een `constraints.regex` `constraints.required` waarde op te geven voor wanneer deze is ingesteld op **false.** In dit scenario is een waarde niet vereist om het tekstvak te valideren. Als er een is opgegeven, moet deze overeenkomen met het reguliere expressiepatroon.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
