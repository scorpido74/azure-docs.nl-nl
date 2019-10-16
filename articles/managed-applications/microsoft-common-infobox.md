---
title: Gebruikers interface-element van Azure InfoBox | Microsoft Docs
description: Hierin wordt het micro soft. common. InfoBox UI-element voor Azure Portal beschreven. Gebruiken om tekst of waarschuwingen toe te voegen bij het implementeren van een beheerde toepassing.
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331693"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Micro soft. common. InfoBox UI-element
Een besturings element waarmee een informatie venster wordt toegevoegd. Het vak bevat belang rijke tekst of waarschuwingen waarmee gebruikers inzicht kunnen krijgen in de waarden die ze bieden. Het kan ook worden gekoppeld aan een URI voor meer informatie.

## <a name="ui-sample"></a>UI-voor beeld
![Micro soft. common. InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schema
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Opmerkingen

* Voor `icon`, gebruikt u **geen**, **info**, **waarschuwing**of **fout**.
* De eigenschap `uri` is optioneel.

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Volgende stappen
* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
