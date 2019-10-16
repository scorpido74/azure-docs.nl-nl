---
title: Gebruikers interface-element van Azure TextBlock | Microsoft Docs
description: Hierin wordt het micro soft. common. TextBlock UI-element voor Azure Portal beschreven. Gebruiken om tekst toe te voegen aan de-interface.
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
ms.openlocfilehash: da2453889f04352dbabe182772312d70deec4464
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331615"
---
# <a name="microsoftcommontextblock-ui-element"></a>Micro soft. common. TextBlock UI-element
Een besturings element dat kan worden gebruikt om tekst toe te voegen aan de portal-interface.

## <a name="ui-sample"></a>UI-voor beeld
![Micro soft. common. TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

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
* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
