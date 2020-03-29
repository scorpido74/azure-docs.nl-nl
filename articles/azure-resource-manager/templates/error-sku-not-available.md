---
title: SKU niet beschikbare fouten
description: Beschrijft hoe u de fout van de SKU oplost die niet beschikbaar is bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942717"
---
# <a name="resolve-errors-for-sku-not-available"></a>Fouten voor een niet-beschikbare SKU oplossen

In dit artikel wordt beschreven hoe u de fout **SkuNotAvailable kunt** oplossen. Als u geen geschikte SKU vinden in die regio/zone of een alternatieve regio/zone die aan uw bedrijfsbehoeften voldoet, dient u een [SKU-aanvraag](https://aka.ms/skurestriction) in bij Azure Support.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert (meestal een virtuele machine), ontvangt u de volgende foutcode en foutbericht:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Oorzaak

U ontvangt deze fout wanneer de door u geselecteerde resource SKU (zoals vm-grootte) niet beschikbaar is voor de locatie die u hebt geselecteerd.

Als u een exemplaar azure spot VM of Spot-schaalset implementeert, is er geen capaciteit voor Azure Spot op deze locatie. Zie [Foutberichten herkennen voor](../../virtual-machines/error-codes-spot.md)meer informatie .

## <a name="solution-1---powershell"></a>Oplossing 1 - PowerShell

Als u wilt bepalen welke SKU's beschikbaar zijn in een regio/zone, gebruikt u de opdracht [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filter de resultaten op locatie. U moet de nieuwste versie van PowerShell voor deze opdracht hebben.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

De resultaten omvatten een lijst van SKU's voor de locatie en eventuele beperkingen voor die SKU. Een SKU kan worden vermeld `NotAvailableForSubscription`als .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Enkele aanvullende voorbeelden:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Het toevoegen van "fc" aan het eind geeft meer details.

## <a name="solution-2---azure-cli"></a>Oplossing 2 - Azure CLI

Als u wilt bepalen welke SKU's beschikbaar zijn in een regio, gebruikt u de `az vm list-skus` opdracht. Gebruik `--location` de parameter om de uitvoer te filteren op de locatie die u gebruikt. Gebruik `--size` de parameter om te zoeken met een gedeeltelijke groottenaam.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

De opdracht retourneert resultaten als:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Oplossing 3 - Azure-portal

Als u wilt bepalen welke SKU's beschikbaar zijn in een regio, gebruikt u de [portal](https://portal.azure.com). Meld u aan bij de portal en voeg een bron toe via de interface. Terwijl u de waarden instelt, ziet u de beschikbare SKU's voor die resource. U hoeft de implementatie niet te voltooien.

Begin bijvoorbeeld met het maken van een virtuele machine. Als u een andere beschikbare grootte wilt bekijken, selecteert u **Grootte wijzigen**.

![VM maken](./media/error-sku-not-available/create-vm.png)

U filteren en bladeren door de beschikbare maten.

![Beschikbare SKU's](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Oplossing 4 - RUST

Als u wilt bepalen welke SKU's beschikbaar zijn in een regio, gebruikt u de bewerking [ResourceSkus - List.](/rest/api/compute/resourceskus/list)

Het retourneert beschikbare SKU's en regio's in de volgende indeling:

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

