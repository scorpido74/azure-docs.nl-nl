---
title: MultiStorageAccountCombo-gebruikersinterface-element
description: Beschrijft het Microsoft.Storage.MultiStorageAccountCombo UI-element voor Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651876"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo-gebruikersinterfaceelement

Een groep besturingselementen voor het maken van meerdere opslagaccounts met namen die beginnen met een gemeenschappelijk voorvoegsel.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Opmerkingen

- De waarde `defaultValue.prefix` voor wordt gekoppeld aan een of meer gehele getallen om de volgorde van opslagaccountnamen te genereren. Bijvoorbeeld, als `defaultValue.prefix` **is sa** `count` en is **2,** dan opslag account namen **sa1** en **sa2** worden gegenereerd. Gegenereerde opslagaccountnamen worden automatisch gevalideerd voor uniciteit.
- De namen van het opslagaccount `count`worden lexicografisch gegenereerd op basis van . Als u `count` bijvoorbeeld 10 is, eindigen de namen van het opslagaccount met getallen getallen (01, 02, 03).
- De standaardwaarde `defaultValue.prefix` voor is `defaultValue.type` **null**en is **Premium_LRS**.
- Elk type dat `constraints.allowedTypes` niet is opgegeven, wordt `constraints.excludedTypes` verborgen en elk type dat niet is opgegeven, wordt weergegeven. `constraints.allowedTypes`en `constraints.excludedTypes` zijn beide optioneel, maar kunnen niet tegelijkertijd worden gebruikt.
- Naast het genereren van namen `count` van opslagaccount, wordt gebruikt om de juiste multiplier voor het element in te stellen. Het ondersteunt een statische waarde, zoals **2,** of `[steps('step1').storageAccountCount]`een dynamische waarde van een ander element, zoals . De standaardwaarde is **1**.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
