---
title: INTERFACE-element TextBox
description: Hierin wordt het gebruikers interface-element micro soft. common. TextBox beschreven voor Azure Portal. Gebruiken voor het toevoegen van niet-opgemaakte tekst.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474305"
---
# <a name="microsoftcommontextbox-ui-element"></a>Gebruikers interface-element van micro soft. common. TextBox

Een besturings element dat kan worden gebruikt voor het bewerken van niet-opgemaakte tekst.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Schema

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"contoso123"
```

## <a name="remarks"></a>Opmerkingen

- Als `constraints.required` is ingesteld op **waar**, moet het tekstvak een waarde hebben om te kunnen valideren. De standaardwaarde is **onwaar**.
- De `validations` eigenschap is een matrix waarin u voor waarden toevoegt voor het controleren van de waarde die is opgegeven in het tekstvak.
- De `regex` eigenschap is een reguliere java script-expressie patroon. Indien opgegeven, moet de waarde van het tekstvak overeenkomen met het patroon om te valideren. De standaard waarde is **Null**.
- De `isValid` eigenschap bevat een expressie die resulteert in waar of onwaar. Binnen de expressie definieert u de voor waarde die bepaalt of het tekstvak geldig is.
- De `message` eigenschap is een teken reeks die moet worden weer gegeven wanneer de validatie van de waarde van het tekstvak mislukt.
- Het is mogelijk om een waarde op te geven voor `regex` Wanneer `required` is ingesteld op **Onwaar**. In dit scenario is er geen waarde vereist om het tekstvak te valideren. Als er een is opgegeven, moet deze overeenkomen met het reguliere-expressie patroon.
- De `placeholder` eigenschap is Help-tekst die verdwijnt wanneer de gebruiker begint met het bewerken van. Als de `placeholder` en `defaultValue` beide zijn gedefinieerd, `defaultValue` krijgt de prioriteit en wordt weer gegeven.

## <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een tekstvak met het [micro soft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) -besturings element gebruikt om de beschik baarheid van een resource naam te controleren.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
