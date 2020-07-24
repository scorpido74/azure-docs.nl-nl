---
title: Vervolg keuzelijst GEBRUIKERSINTERFACE element
description: Hierin wordt het micro soft. common. vervolg keuzelijst gebruikers interface-element voor Azure Portal beschreven. Gebruiken om te selecteren uit beschik bare opties bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004438"
---
# <a name="microsoftcommondropdown-ui-element"></a>Micro soft. common. vervolg keuzelijst UI-element

Een selectie besturings element met een vervolg keuzelijst. U kunt slechts één item of meerdere items selecteren. U kunt eventueel ook een beschrijving van de items toevoegen.

## <a name="ui-sample"></a>UI-voor beeld

Het element DropDown heeft andere opties die de weer gave in de portal bepalen.

Als er slechts één item is toegestaan voor selectie, wordt het besturings element weer gegeven als:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Micro soft. common. DropDown, enkelvoudige selectie":::

Wanneer beschrijvingen zijn opgenomen, wordt het besturings element weer gegeven als:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Micro soft. common. vervolg keuzelijst met één selectie met beschrijvingen":::

Als meervoudige selectie is ingeschakeld, voegt het besturings element de optie **alle selecteren** en selectie vakjes toe voor het selecteren van meer dan één item:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Micro soft. common. DropDown multi-select":::

Beschrijvingen kunnen worden opgenomen in meervoudige selectie ingeschakeld.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Micro soft. common. DropDown multi-select met beschrijvingen":::

Wanneer filteren is ingeschakeld, bevat het besturings element een tekstvak voor het toevoegen van de filter waarde.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Micro soft. common. DropDown multi-select met beschrijvingen":::

## <a name="schema"></a>Schema

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"two"
```

## <a name="remarks"></a>Opmerkingen

- Gebruiken `multiselect` om op te geven of gebruikers meer dan één item kunnen selecteren.
- Standaard `selectAll` is `true` Wanneer meervoudige selectie is ingeschakeld.
- Met deze `filter` eigenschap kunnen gebruikers zoeken in een lange lijst met opties.
- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label zijn in `constraints.allowedValues` . Als u niets opgeeft, wordt het eerste item in `constraints.allowedValues` geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues`moet ten minste één item bevatten.
- Als u een waarde wilt emuleren die niet vereist is, voegt u een item met een label en waarde van `""` (lege teken reeks) toe aan `constraints.allowedValues` .
- De `defaultDescription` eigenschap wordt gebruikt voor items die geen beschrijving hebben.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
