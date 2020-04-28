---
title: InfoBox UI-element
description: Hierin wordt het micro soft. common. InfoBox UI-element voor Azure Portal beschreven. Gebruiken om tekst of waarschuwingen toe te voegen bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652474"
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

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Opmerkingen

* Voor `icon`gebruikt u **geen**, **info**, **waarschuwing**of **fout**.
* De `uri` eigenschap is optioneel.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
