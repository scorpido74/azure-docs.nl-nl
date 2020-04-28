---
title: PasswordBox UI-element
description: Hierin wordt het micro soft. common. PasswordBox UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers een geheime waarde opgeven bij het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652305"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Micro soft. common. PasswordBox UI-element

Een besturings element dat kan worden gebruikt om een wacht woord op te geven en te bevestigen.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Opmerkingen

- Dit element biedt geen ondersteuning `defaultValue` voor de eigenschap.
- Zie `constraints` [micro soft. common. TextBox](microsoft-common-textbox.md)voor meer informatie over de implementatie van.
- Als `options.hideConfirmation` is ingesteld op **True**, wordt het tweede tekstvak voor het bevestigen van het wacht woord van de gebruiker verborgen. De standaardwaarde is **onwaar**.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
