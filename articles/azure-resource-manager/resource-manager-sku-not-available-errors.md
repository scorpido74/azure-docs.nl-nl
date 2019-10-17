---
title: Fouten in azure SKU niet beschikbaar | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen met de SKU niet beschik bare fout bij het implementeren van resources met Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: fca028412052a9a1520e1178f5d182a9987a9a85
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390219"
---
# <a name="resolve-errors-for-sku-not-available"></a>Fouten oplossen voor SKU is niet beschikbaar

In dit artikel wordt beschreven hoe u de **SkuNotAvailable** -fout kunt oplossen. Als u geen geschikte SKU in die regio kunt vinden of een alternatieve regio die voldoet aan de behoeften van uw bedrijf, dient u een [SKU-aanvraag](https://aka.ms/skurestriction) in bij Azure-ondersteuning.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert (meestal een virtuele machine), wordt de volgende fout code en fout bericht weer gegeven:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Oorzaak

Deze fout wordt weer gegeven wanneer de resource-SKU die u hebt geselecteerd (zoals VM-grootte) niet beschikbaar is voor de locatie die u hebt geselecteerd.

## <a name="solution-1---powershell"></a>Oplossing 1-Power shell

Gebruik de opdracht [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) om te bepalen welke sku's beschikbaar zijn in een regio. De resultaten filteren op locatie. U moet de meest recente versie van Power shell voor deze opdracht hebben.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

De resultaten omvatten een lijst met Sku's voor de locatie en eventuele beperkingen voor die SKU. U ziet dat een SKU mogelijk wordt vermeld als `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Oplossing 2-Azure CLI

Gebruik de `az vm list-skus` opdracht om te bepalen welke Sku's beschikbaar zijn in een regio. Gebruik de para meter `--location` om de uitvoer te filteren op de locatie die u gebruikt. Gebruik de para meter `--size` om te zoeken op een gedeeltelijke grootte naam.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

De opdracht retourneert resultaten als:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Oplossing 3-Azure Portal

Gebruik de [Portal](https://portal.azure.com)om te bepalen welke sku's beschikbaar zijn in een regio. Meld u aan bij de portal en voeg een resource toe via de-interface. Wanneer u de waarden instelt, ziet u de beschik bare Sku's voor die bron. U hoeft de implementatie niet te volt ooien.

U kunt bijvoorbeeld het proces voor het maken van een virtuele machine starten. Als u andere beschik bare grootte wilt zien, selecteert u **grootte wijzigen**.

![VM maken](./media/resource-manager-sku-not-available-errors/create-vm.png)

U kunt filteren en door de beschik bare grootten bladeren.

![Beschik bare Sku's](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Oplossing 4-REST

Als u wilt bepalen welke Sku's beschikbaar zijn in een regio, gebruikt u de bewerking [resource-sku's-List](/rest/api/compute/resourceskus/list) .

Het retourneert beschik bare Sku's en regio's in de volgende indeling:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

