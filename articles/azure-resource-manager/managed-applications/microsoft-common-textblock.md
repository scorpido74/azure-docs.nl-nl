---
title: TextBlock-element
description: Beschrijft het Microsoft.Common.TextBlock-gebruikersinterfaceelement voor Azure-portal. Tekst toevoegen aan de interface.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652214"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock-element

Een besturingselement dat kan worden gebruikt om tekst toe te voegen aan de portalinterface.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
