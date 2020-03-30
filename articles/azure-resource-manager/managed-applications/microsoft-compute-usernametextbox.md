---
title: GebruikersnaamTextBox-element UI-element
description: Beschrijft het Microsoft.Compute.UserNameTextBox-gebruikersinterface-element voor Azure-portal. Hiermee kunnen gebruikers windows- of Linux-gebruikersnamen verstrekken.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651902"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox-element UI-element

Een tekstvakbesturingselement met ingebouwde validatie voor Windows- en Linux-gebruikersnamen.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Example name"
```

## <a name="remarks"></a>Opmerkingen

- Als `constraints.required` dit is ingesteld op **true,** moet het tekstvak een waarde hebben om te valideren. De standaardwaarde is **waar**.
- `osPlatform`moet worden opgegeven, en kan **windows** of **Linux**.
- `constraints.regex`is een JavaScript-regelmatig expressiepatroon. Als dit is opgegeven, moet de waarde van het tekstvak overeenkomen met het patroon dat met succes moet worden gevalideerd. De standaardwaarde is **null**.
- `constraints.validationMessage`is een tekenreeks die moet worden weergegeven wanneer de `constraints.regex`waarde van het tekstvak niet voldoet aan de validatie die is opgegeven door . Als dit niet is opgegeven, worden de ingebouwde validatieberichten van het tekstvak gebruikt. De standaardwaarde is **null**.
- Dit element heeft een ingebouwde validatie die `osPlatform`is gebaseerd op de opgegeven waarde voor . De ingebouwde validatie kan worden gebruikt samen met een aangepaste reguliere expressie. Als een `constraints.regex` waarde is opgegeven, worden zowel de ingebouwde als de aangepaste validaties geactiveerd.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
