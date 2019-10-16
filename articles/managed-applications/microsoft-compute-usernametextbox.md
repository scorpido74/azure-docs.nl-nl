---
title: Gebruikers interface-element van Azure UserNameTextBox | Microsoft Docs
description: Hierin wordt het UI-element micro soft. compute. UserNameTextBox voor Azure Portal beschreven. Hiermee kunnen gebruikers Windows-of Linux-gebruikers namen opgeven.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 875c73c546fa52642959e2593d41f9af82c13797
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331579"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Micro soft. compute. UserNameTextBox UI-element
Een besturings element tekstvak met ingebouwde validatie voor Windows-en Linux-gebruikers namen.

## <a name="ui-sample"></a>UI-voor beeld
![Micro soft. compute. UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

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

## <a name="remarks"></a>Opmerkingen
- Als `constraints.required` is ingesteld op **waar**, moet het tekstvak een waarde hebben om te kunnen valideren. De standaard waarde is **True**.
- `osPlatform` moet worden opgegeven en kan **Windows** of **Linux**zijn.
- `constraints.regex` is een reguliere java script-expressie patroon. Indien opgegeven, moet de waarde van het tekstvak overeenkomen met het patroon om te valideren. De standaard waarde is **Null**.
- `constraints.validationMessage` is een teken reeks die moet worden weer gegeven wanneer de waarde van het tekstvak niet voldoet aan de validatie die is opgegeven door `constraints.regex`. Als dat niet is opgegeven, worden de ingebouwde validatie berichten van het tekstvak gebruikt. De standaard waarde is **Null**.
- Dit element heeft ingebouwde validatie die is gebaseerd op de waarde die is opgegeven voor `osPlatform`. De ingebouwde validatie kan worden gebruikt in combi natie met een aangepaste reguliere expressie. Als er een waarde voor `constraints.regex` is opgegeven, worden de ingebouwde en aangepaste validaties geactiveerd.

## <a name="sample-output"></a>Voorbeelduitvoer
```json
"Example name"
```

## <a name="next-steps"></a>Volgende stappen
* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
