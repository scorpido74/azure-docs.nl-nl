---
title: WachtwoordBox-gebruikersinterface-element
description: Beschrijft het Microsoft.Common.PasswordBox-gebruikersinterfaceelement voor Azure-portal. Stelt gebruikers in staat om een geheime waarde te bieden bij het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652305"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox-element UI

Een besturingselement dat kan worden gebruikt om een wachtwoord op te geven en te bevestigen.

## <a name="ui-sample"></a>UI-voorbeeld

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

- Dit element ondersteunt de `defaultValue` eigenschap niet.
- Zie Microsoft.Common.TextBox voor implementatiedetails van `constraints`, zie [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Als `options.hideConfirmation` dit is ingesteld op **true,** wordt het tweede tekstvak voor het bevestigen van het wachtwoord van de gebruiker verborgen. De standaardwaarde is **onwaar**.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
