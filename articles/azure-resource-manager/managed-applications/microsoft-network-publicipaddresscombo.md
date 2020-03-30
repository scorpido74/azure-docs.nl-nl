---
title: Gebruikersinterface-element PublicIpAddressCombo
description: Beschrijft het Microsoft.Network.PublicIpAddressCombo UI-element voor Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651915"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI-element

Een groep besturingselementen voor het selecteren van een nieuw of bestaand openbaar IP-adres.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Als de gebruiker 'Geen' selecteert voor een openbaar IP-adres, wordt het tekstvak voor het domeinnaamlabel verborgen.
- Als de gebruiker een bestaand openbaar IP-adres selecteert, wordt het tekstvak voor het domeinnaamlabel uitgeschakeld. De waarde is het domeinnaamlabel van het geselecteerde IP-adres.
- Het achtervoegsel van de domeinnaam (bijvoorbeeld westus.cloudapp.azure.com) wordt automatisch bijgewerkt op basis van de geselecteerde locatie.

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

Als de gebruiker geen openbaar IP-adres selecteert, retourneert het besturingselement de volgende uitvoer:

```json
{
  "newOrExistingOrNone": "none"
}
```

Als de gebruiker een nieuw of bestaand IP-adres selecteert, retourneert het besturingselement de volgende uitvoer:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Wanneer `options.hideNone` wordt opgegeven `newOrExistingOrNone` als **waar,** heeft alleen een waarde van **nieuw** of **bestaand**.
- Wanneer `options.hideDomainNameLabel` is opgegeven `domainNameLabel` als **waar,** is niet aangegeven.

## <a name="remarks"></a>Opmerkingen

- Als `constraints.required.domainNameLabel` deze is ingesteld op **true,** moet de gebruiker een domeinnaamlabel opgeven bij het maken van een nieuw openbaar IP-adres. Bestaande openbare IP-adressen zonder label zijn niet beschikbaar voor selectie.
- Als `options.hideNone` dit is ingesteld op **true,** wordt de optie om **Geen** voor het openbare IP-adres te selecteren verborgen. De standaardwaarde is **onwaar**.
- Als `options.hideDomainNameLabel` dit is ingesteld op **true,** wordt het tekstvak voor het label domeinnaam verborgen. De standaardwaarde is **onwaar**.
- Als `options.hideExisting` dit waar is, kan de gebruiker geen bestaand openbaar IP-adres kiezen. De standaardwaarde is **onwaar**.
- Voor `zone`zijn alleen openbare IP-adressen voor de opgegeven zone of zone veerkrachtige openbare IP-adressen beschikbaar.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
