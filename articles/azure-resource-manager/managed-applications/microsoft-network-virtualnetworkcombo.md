---
title: VirtualNetworkCombo-gebruikersinterface-element
description: Beschrijft het Microsoft.Network.VirtualNetworkCombo UI-element voor Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651967"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo-gebruikersinterface-element

Een groep besturingselementen voor het selecteren van een nieuw of bestaand virtueel netwerk.

## <a name="ui-sample"></a>UI-voorbeeld

Wanneer de gebruiker een nieuw virtueel netwerk kiest, kan de gebruiker de naam en het adresvoorvoegsel van elk subnet aanpassen. Het configureren van subnetten is optioneel.

![Microsoft.Network.VirtualNetworkCombo nieuw](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Wanneer de gebruiker een bestaand virtueel netwerk kiest, moet de gebruiker elk subnet dat de implementatiesjabloon nodig heeft, toewijzen aan een bestaand subnet. Het configureren van subnetten in dit geval is vereist.

![Microsoft.Network.VirtualNetworkCombo bestaande](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Opmerkingen

- Indien dit is opgegeven, wordt het `defaultValue.addressPrefixSize` eerste niet-overlappende adresvoorvoeging van grootte automatisch bepaald op basis van de bestaande virtuele netwerken in het abonnement van de gebruiker.
- De standaardwaarde `defaultValue.name` `defaultValue.addressPrefixSize` voor en is **null**.
- `constraints.minAddressPrefixSize`moeten worden gespecificeerd. Bestaande virtuele netwerken met een adresruimte die kleiner is dan de opgegeven waarde, zijn niet beschikbaar voor selectie.
- `subnets`moet worden gespecificeerd `constraints.minAddressPrefixSize` en moeten voor elk subnet worden gespecificeerd.
- Bij het maken van een nieuw virtueel netwerk wordt het adresvoorvoegsel van elk subnet `addressPrefixSize`automatisch berekend op basis van het adresvoorvoegsel van het virtuele netwerk en het respectievelijk .
- Wanneer u een bestaand virtueel netwerk gebruikt, `constraints.minAddressPrefixSize` zijn subnetten die kleiner zijn dan het desbetreffende niet beschikbaar voor selectie. Bovendien zijn subnetten die niet ten minste `minAddressCount` beschikbare adressen hebben, indien opgegeven, niet beschikbaar voor selectie. De standaardwaarde is **0**. Als u ervoor wilt zorgen dat de beschikbare `requireContiguousAddresses`adressen aaneengesloten zijn, geeft u het **gelden** voor . De standaardwaarde is **waar**.
- Het maken van subnetten in een bestaand virtueel netwerk wordt niet ondersteund.
- Als `options.hideExisting` **dit waar**is, kan de gebruiker geen bestaand virtueel netwerk kiezen. De standaardwaarde is **onwaar**.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
