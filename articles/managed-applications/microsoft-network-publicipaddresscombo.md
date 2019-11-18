---
title: Gebruikers interface-element van Azure PublicIpAddressCombo | Microsoft Docs
description: Hierin wordt het element micro soft. Network. PublicIpAddressCombo UI voor Azure Portal beschreven.
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
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151500"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element

Een groep besturings elementen voor het selecteren van een nieuw of bestaand openbaar IP-adres.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Als de gebruiker geen selecteert voor het open bare IP-adres, wordt het tekstvak domeinnaam label verborgen.
- Als de gebruiker een bestaand openbaar IP-adres selecteert, wordt het tekstvak domeinnaam label uitgeschakeld. De waarde is het domein naam label van het geselecteerde IP-adres.
- Het achtervoegsel voor de domein naam (bijvoorbeeld westus.cloudapp.azure.com) wordt automatisch bijgewerkt op basis van de geselecteerde locatie.

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

Als de gebruiker geen openbaar IP-adres selecteert, retourneert het besturings element de volgende uitvoer:

```json
{
  "newOrExistingOrNone": "none"
}
```

Als de gebruiker een nieuw of bestaand IP-adres selecteert, retourneert het besturings element de volgende uitvoer:

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

- Als `options.hideNone` als **waar**is opgegeven, heeft `newOrExistingOrNone` alleen de waarde **Nieuw** of **bestaande**.
- Als `options.hideDomainNameLabel` als **waar**is opgegeven, wordt `domainNameLabel` niet-gedeclareerd.

## <a name="remarks"></a>Opmerkingen

- Als `constraints.required.domainNameLabel` is ingesteld op **waar**, moet de gebruiker een domein naam label opgeven bij het maken van een nieuw openbaar IP-adres. Bestaande open bare IP-adressen zonder label zijn niet beschikbaar voor selectie.
- Als `options.hideNone` is ingesteld op **True**, wordt de optie voor het selecteren van **geen** voor het open bare IP-adres verborgen. De standaardwaarde is **false**.
- Als `options.hideDomainNameLabel` is ingesteld op **True**, wordt het tekstvak voor het domein naam label verborgen. De standaardwaarde is **false**.
- Als `options.hideExisting` is ingesteld op True, kan de gebruiker geen bestaand openbaar IP-adres kiezen. De standaardwaarde is **false**.
- Voor `zone`zijn alleen de open bare IP-adressen voor de opgegeven zone of zone flexibele open bare IP-adressen beschikbaar.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
