---
title: Element Groottekiezer-gebruikersinterface
description: Beschrijft het Microsoft.Compute.SizeSelector UI-element voor Azure-portal. Gebruiken voor het selecteren van de grootte van een virtuele machine.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652045"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI-element

Een besturingselement voor het selecteren van een grootte voor een of meer virtuele machine-exemplaren.

## <a name="ui-sample"></a>UI-voorbeeld

De gebruiker ziet een kiezer met standaardwaarden uit de elementdefinitie.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Na het selecteren van het besturingselement ziet de gebruiker een uitgebreide weergave van de beschikbare formaten.

![Microsoft.Compute.SizeSelector uitgebreid](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Standard_D1"
```

## <a name="remarks"></a>Opmerkingen

- `recommendedSizes`moet ten minste één maat hebben. De eerste aanbevolen grootte wordt standaard gebruikt. De lijst met beschikbare formaten wordt niet gesorteerd op de aanbevolen status. De gebruiker kan die kolom selecteren om te sorteren op aanbevolen status.
- Als een aanbevolen grootte niet beschikbaar is op de geselecteerde locatie, wordt de grootte automatisch overgeslagen. In plaats daarvan wordt de volgende aanbevolen grootte gebruikt.
- `constraints.allowedSizes`en `constraints.excludedSizes` zijn beide optioneel, maar kunnen niet tegelijkertijd worden gebruikt. De lijst met beschikbare formaten kan worden bepaald door [lijst beschikbare virtuele machineformaten voor een abonnement aan](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)te roepen. Elke grootte die `constraints.allowedSizes` niet in de ruimte is `constraints.excludedSizes` opgegeven, is verborgen en elke grootte die niet is opgegeven, wordt weergegeven.
- `osPlatform`moet worden opgegeven, en kan **windows** of **Linux**. Het wordt gebruikt om de hardwarekosten van de virtuele machines te bepalen.
- `imageReference`wordt weggelaten voor afbeeldingen van de eerste partij, maar wordt geleverd voor afbeeldingen van derden. Het wordt gebruikt om de softwarekosten van de virtuele machines te bepalen.
- `count`wordt gebruikt om de juiste vermenigvuldiger voor het element in te stellen. Het ondersteunt een statische waarde, zoals **2,** of `[steps('step1').vmCount]`een dynamische waarde van een ander element, zoals . De standaardwaarde is **1**.
- Het `numAvailabilityZonesRequired` kan 1, 2 of 3 zijn.
- Standaard `hideDiskTypeFilter` is **dit vals.** Met het schijftypefilter kan de gebruiker alle schijftypen of alleen SSD's zien.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
