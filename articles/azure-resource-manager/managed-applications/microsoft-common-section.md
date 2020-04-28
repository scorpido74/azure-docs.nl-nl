---
title: UI-element van sectie
description: Hierin wordt het gebruikers interface-element micro soft. common. Section voor Azure Portal beschreven. Gebruiken om elementen in de portal te groeperen voor het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652253"
---
# <a name="microsoftcommonsection-ui-element"></a>Gebruikers interface-element van micro soft. common. Section

Een besturings element dat een of meer elementen onder een kop groepeert.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Opmerkingen

- `elements`moet ten minste één element hebben en kan alle element typen behalve `Microsoft.Common.Section`bevatten.
- Dit element biedt geen ondersteuning `toolTip` voor de eigenschap.

## <a name="sample-output"></a>Voorbeelduitvoer
Als u de uitvoer waarden van elementen in `elements`wilt openen, gebruikt u de functies [()](create-uidefinition-functions.md#basics) of [stappen (](create-uidefinition-functions.md#steps) ) en punt notatie:

```json
steps('configuration').section1.text1
```

Elementen van het `Microsoft.Common.Section` type hebben geen uitvoer waarden zelf.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
