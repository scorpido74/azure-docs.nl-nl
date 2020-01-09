---
title: UI-element van sectie
description: Hierin wordt het gebruikers interface-element micro soft. common. Section voor Azure Portal beschreven. Gebruiken om elementen in de portal te groeperen voor het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
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

- `elements` moet ten minste één element hebben en kan alle element typen hebben, met uitzonde ring van `Microsoft.Common.Section`.
- Dit element biedt geen ondersteuning voor de eigenschap `toolTip`.

## <a name="sample-output"></a>Voorbeelduitvoer
Als u toegang wilt krijgen tot de uitvoer waarden van elementen in `elements`, gebruikt u de functies [basis ()](create-uidefinition-functions.md#basics) of [stappen (](create-uidefinition-functions.md#steps) ) en punt notatie:

```json
steps('configuration').section1.text1
```

Elementen van het type `Microsoft.Common.Section` geen uitvoer waarden zelf hebben.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
