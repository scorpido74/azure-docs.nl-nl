---
title: INTERFACE-element TextBox
description: Hierin wordt het gebruikers interface-element micro soft. common. TextBox beschreven voor Azure Portal. Gebruiken voor het toevoegen van niet-opgemaakte tekst.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652279"
---
# <a name="microsoftcommontextbox-ui-element"></a>Gebruikers interface-element van micro soft. common. TextBox

Een besturings element dat kan worden gebruikt voor het bewerken van niet-opgemaakte tekst.

## <a name="ui-sample"></a>UI-voor beeld

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

- Als `constraints.required` is ingesteld op **waar**, moet het tekstvak een waarde hebben om te kunnen valideren. De standaardwaarde is **onwaar**.
- `constraints.regex`is een reguliere java script-expressie patroon. Indien opgegeven, moet de waarde van het tekstvak overeenkomen met het patroon om te valideren. De standaard waarde is **Null**.
- `constraints.validationMessage`is een teken reeks die moet worden weer gegeven wanneer de validatie van de waarde van het tekstvak mislukt. Als dat niet is opgegeven, worden de ingebouwde validatie berichten van het tekstvak gebruikt. De standaard waarde is **Null**.
- Het is mogelijk om een waarde op te geven voor `constraints.regex` Wanneer `constraints.required` is ingesteld op **Onwaar**. In dit scenario is er geen waarde vereist om het tekstvak te valideren. Als er een is opgegeven, moet deze overeenkomen met het reguliere-expressie patroon.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
