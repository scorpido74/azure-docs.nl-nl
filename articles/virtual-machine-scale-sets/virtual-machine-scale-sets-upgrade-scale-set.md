---
title: Een azure-schaalset voor virtuele machines wijzigen
description: Meer informatie over het wijzigen en bijwerken van een Azure-configuratie van virtuele machines met de REST API's, Azure PowerShell en Azure CLI
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimckitt
ms.openlocfilehash: 66fd656b5175547641150a048e57c978dc06d291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476821"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset wijzigen

Gedurende de hele levenscyclus van uw toepassingen moet u mogelijk uw virtuele machineschaalset wijzigen of bijwerken. Deze updates kunnen onder meer bevatten hoe u de configuratie van de schaalset bijwerkt of de toepassingsconfiguratie wijzigt. In dit artikel wordt beschreven hoe u een bestaande schaalset wijzigt met de REST API's, Azure PowerShell of Azure CLI.

## <a name="fundamental-concepts"></a>Fundamentele concepten

### <a name="the-scale-set-model"></a>Het schaalmodel
Een schaalset heeft een "schaalsetmodel" dat de *gewenste* status van de schaalset als geheel vastlegt. Als u het model voor een schaalset wilt opvragen, u de 

- REST API met [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI met [az vmss show:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- U ook [resources.azure.com](https://resources.azure.com) of de taalspecifieke [Azure SDK's](https://azure.microsoft.com/downloads/)gebruiken.

De exacte presentatie van de uitvoer is afhankelijk van de opties die u aan de opdracht geeft. In het volgende voorbeeld wordt de gecondenseerde voorbeelduitvoer van de Azure CLI weergegeven:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Deze eigenschappen zijn van toepassing op de schaalset als geheel.


### <a name="the-scale-set-instance-view"></a>De schaalset-instantieweergave
Een schaalset heeft ook een 'schaalset-instantieweergave' die de huidige *runtime-status* van de schaalset als geheel vastlegt. Als u de instantieweergave voor een schaalset wilt opvragen, u het als:

- REST API met [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI met [az vmss get-instance-view:](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- U ook [resources.azure.com](https://resources.azure.com) of de taalspecifieke [Azure SDK's](https://azure.microsoft.com/downloads/) gebruiken

De exacte presentatie van de uitvoer is afhankelijk van de opties die u aan de opdracht geeft. In het volgende voorbeeld wordt de gecondenseerde voorbeelduitvoer van de Azure CLI weergegeven:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Deze eigenschappen geven een overzicht van de huidige runtime-status van de VM's in de schaalset, zoals de status van extensies die worden toegepast op de schaalset.


### <a name="the-scale-set-vm-model-view"></a>De vm-modelweergave voor schaalsets
Net als bij de manier waarop een schaalset een modelweergave heeft, heeft elke VM-instantie in de schaalset zijn eigen modelweergave. Als u de modelweergave voor een bepaalde VM-instantie in een schaalset wilt opvragen, u het volgende gebruiken:

- REST API met [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmssVm:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI met [az vmss show:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- U ook [resources.azure.com](https://resources.azure.com) of de [Azure SDKs](https://azure.microsoft.com/downloads/)gebruiken.

De exacte presentatie van de uitvoer is afhankelijk van de opties die u aan de opdracht geeft. In het volgende voorbeeld wordt de gecondenseerde voorbeelduitvoer van de Azure CLI weergegeven:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Deze eigenschappen beschrijven de configuratie van een VM-instantie binnen een schaalset, niet de configuratie van de schaalset als geheel. Het schaalsetmodel heeft `overprovision` bijvoorbeeld als eigenschap, terwijl het model voor een VM-exemplaar binnen een schaalset dat niet doet. Dit verschil is omdat overprovisioning een eigenschap is voor de schaaldie is ingesteld als geheel, niet afzonderlijke VM-exemplaren in de schaalset (zie [Ontwerpoverwegingen voor schaalsets voor](virtual-machine-scale-sets-design-overview.md#overprovisioning)meer informatie over overprovisioning).


### <a name="the-scale-set-vm-instance-view"></a>De weergave VM-instantie van de schaalset
Net als bij de manier waarop een schaalset een instantieweergave heeft, heeft elke VM-instantie in de schaalset zijn eigen instantieweergave. Als u de instantieweergave voor een bepaalde VM-instantie binnen een schaalset wilt opvragen, u het volgende gebruiken:

- REST API met [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmssVm:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI met [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- U ook [resources.azure.com](https://resources.azure.com) of de [Azure SDKs](https://azure.microsoft.com/downloads/) gebruiken

De exacte presentatie van de uitvoer is afhankelijk van de opties die u aan de opdracht geeft. In het volgende voorbeeld wordt de gecondenseerde voorbeelduitvoer van de Azure CLI weergegeven:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Deze eigenschappen beschrijven de huidige runtime-status van een VM-instantie binnen een schaalset, die alle extensies bevat die op de schaalset worden toegepast.


## <a name="how-to-update-global-scale-set-properties"></a>Eigenschappen van globale schaalset bijwerken
Als u een eigenschap met globale schaalset wilt bijwerken, moet u de eigenschap bijwerken in het schaalmodel. U deze update doen via:

- REST API met [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) als volgt:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- U een Resource Manager-sjabloon implementeren met de eigenschappen van de REST-API om globale schaalseteigenschappen bij te werken.

- Azure PowerShell met [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI met [az vmss-update:](/cli/azure/vmss)
    - Ga als lid van de eigenschap als u een eigenschap wijzigt:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Een object toevoegen aan een lijsteigenschap in een schaalset: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Ga als lid van het werk om een object uit een lijsteigenschap in een schaalset te verwijderen: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Als u de schaalset eerder `az vmss create` met de opdracht `az vmss create` hebt geïmplementeerd, u de opdracht opnieuw uitvoeren om de schaalset bij te werken. Zorg ervoor dat alle `az vmss create` eigenschappen in de opdracht hetzelfde zijn als voorheen, behalve de eigenschappen die u wilt wijzigen.

- U ook [resources.azure.com](https://resources.azure.com) of de [Azure SDKs](https://azure.microsoft.com/downloads/)gebruiken.

Zodra het schaalsetmodel is bijgewerkt, is de nieuwe configuratie van toepassing op nieuwe VM's die in de schaalset zijn gemaakt. De modellen voor de bestaande VM's in de schaalset moeten echter nog steeds up-to-date worden gebracht met het nieuwste algemene schaalmodel. In het model voor elke VM `latestModelApplied` is een booleaanse eigenschap genaamd die aangeeft of de`true` VM up-to-date is met het nieuwste algemene schaalsetmodel (dat betekent dat de VM up-to-date is met het nieuwste model).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Vm's up-to-date brengen met het nieuwste schaalmodel
Schaalsets hebben een "upgradebeleid" dat bepaalt hoe VM's up-to-date worden gebracht met het nieuwste schaalsetmodel. De drie modi voor het upgradebeleid zijn:

- **Automatisch** - In deze modus geeft de weegschaal geen garanties over de volgorde van de VM's die worden neergehaald. De schaalset kan alle VM's tegelijkertijd uitschakelen. 
- **Rolling** - In deze modus rolt de schaalset de update in batches uit met een optionele pauzetijd tussen batches.
- **Handleiding** - In deze modus gebeurt er niets met bestaande VM's wanneer u het schaalsetmodel bijwerkt.
 
Als u bestaande VM's wilt bijwerken, moet u een "handmatige upgrade" van elke bestaande VM uitvoeren. U deze handmatige upgrade uitvoeren met:

- REST API met [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) als volgt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell met [Update-AzVmssInstance:](/powershell/module/az.compute/update-azvmssinstance)
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI met [vmss-update-exemplaren van AZ](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- U ook de taalspecifieke [Azure SDK's](https://azure.microsoft.com/downloads/)gebruiken.

>[!NOTE]
> Service Fabric-clusters kunnen alleen *de automatische* modus gebruiken, maar de update wordt anders behandeld. Zie [Service Fabric-toepassingsupgrades](../service-fabric/service-fabric-application-upgrade.md)voor meer informatie.

Er is één type wijziging in globale schaalseteigenschappen die het upgradebeleid niet volgt. Wijzigingen in het besturingssysteem en het profiel van de gegevensschijf (zoals gebruikersnaam en wachtwoord voor beheerders) kunnen alleen worden gewijzigd in *API-versie 2017-12-01* of hoger. Deze wijzigingen zijn alleen van toepassing op VM's die zijn gemaakt na de wijziging in het schaalsetmodel. Om bestaande VM's up-to-date te brengen, moet u een "reimage" van elke bestaande VM doen. U dit reimage doen via:

- REST API met [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) als volgt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell met [Set-AzVmssVm:](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI met [az vmss reimage:](https://docs.microsoft.com/cli/azure/vmss)

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- U ook de taalspecifieke [Azure SDK's](https://azure.microsoft.com/downloads/)gebruiken.


## <a name="properties-with-restrictions-on-modification"></a>Eigenschappen met beperkingen op wijziging

### <a name="create-time-properties"></a>Eigenschappen voor maken-tijd
Sommige eigenschappen kunnen alleen worden ingesteld wanneer u de schaalset maakt. Deze eigenschappen omvatten:

- Beschikbaarheidszones
- Afbeeldingsverwijzinguitgever
- Afbeeldingsreferentieaanbieding
- Afbeeldingsku
- Accounttype Beheerde schijfopslag voor besturingssysteem

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Eigenschappen die alleen kunnen worden gewijzigd op basis van de huidige waarde
Sommige eigenschappen kunnen worden gewijzigd, met uitzonderingen afhankelijk van de huidige waarde. Deze eigenschappen omvatten:

- **singlePlacementGroup** - Als singlePlacementGroup waar is, kan deze worden gewijzigd in false. Als singlePlacementGroup echter vals is, kan deze **niet** worden gewijzigd in true.
- **subnet** - Het subnet van een schaalset kan worden gewijzigd zolang het oorspronkelijke subnet en het nieuwe subnet zich in hetzelfde virtuele netwerk bevinden.

### <a name="properties-that-require-deallocation-to-change"></a>Eigenschappen waarvoor deallocatie moet worden gewijzigd
Sommige eigenschappen kunnen alleen worden gewijzigd in bepaalde waarden als de VM's in de schaalset worden toegewezen. Deze eigenschappen omvatten:

- **SKU-naam**- Als de nieuwe VM SKU niet wordt ondersteund op de hardware waarop de schaalset momenteel is ingeschakeld, moet u de VM's in de schaalset detoewijzing en de toewijzing voordat u de SKU-naam wijzigt. Zie voor meer informatie [hoe u het formaat van een Azure VM wijzigt.](../virtual-machines/windows/resize-vm.md)


## <a name="vm-specific-updates"></a>VM-specifieke updates
Bepaalde wijzigingen kunnen worden toegepast op specifieke VM's in plaats van de eigenschappen van de globale schaalset. Momenteel is de enige VM-specifieke update die wordt ondersteund, het koppelen/loskoppelen van gegevensschijven aan/van VM's in de schaalset. Deze functie is beschikbaar als preview-versie. Zie de [preview-documentatie voor](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)meer informatie .


## <a name="scenarios"></a>Scenario's

### <a name="application-updates"></a>Toepassingsupdates
Als een toepassing wordt geïmplementeerd op een schaaldie is ingesteld via extensies, zorgt een update van de extensieconfiguratie ervoor dat de toepassing wordt bijgewerkt in overeenstemming met het upgradebeleid. Als u bijvoorbeeld een nieuwe versie van een script moet uitvoeren in een aangepaste scriptextensie, u de eigenschap *fileUris* bijwerken om naar het nieuwe script te wijzen. In sommige gevallen u een update forceren, ook al is de extensieconfiguratie ongewijzigd (u hebt het script bijvoorbeeld bijgewerkt zonder een wijziging in de URI van het script). In deze gevallen u de *forceUpdateTag* wijzigen om een update te forceren. Het Azure-platform interpreteert deze eigenschap niet. Als u de waarde wijzigt, is er geen effect op de manier waarop de extensie wordt uitgevoerd. Een verandering dwingt gewoon de uitbreiding te rerun. Zie de [REST API-documentatie voor extensies voor](/rest/api/compute/virtualmachineextensions/createorupdate)meer informatie over de *forceUpdateTag*. Houd er rekening mee dat de *forceUpdateTag* kan worden gebruikt met alle extensies, niet alleen de aangepaste scriptextensie.

Het is ook gebruikelijk dat toepassingen worden geïmplementeerd via een aangepaste afbeelding. Dit scenario wordt behandeld in de volgende sectie.

### <a name="os-updates"></a>OS-updates
Als u Azure-platformafbeeldingen gebruikt, u de afbeelding bijwerken door de *imageReference* te wijzigen (meer informatie, zie de [REST API-documentatie).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

>[!NOTE]
> Bij platformafbeeldingen is het gebruikelijk om "laatste" voor de referentieversie voor afbeeldingen op te geven. Wanneer u VM's maakt, uitschaalt en opnieuw beeldt, worden VM's gemaakt met de nieuwste beschikbare versie. Dit betekent echter **niet** dat de os-afbeelding na verloop van tijd automatisch wordt bijgewerkt wanneer nieuwe afbeeldingsversies worden uitgebracht. Een aparte functie is momenteel in preview die automatische OS-upgrades biedt. Zie de documentatie [Automatische OS-upgrades](virtual-machine-scale-sets-automatic-upgrade.md)voor meer informatie.

Als u aangepaste afbeeldingen gebruikt, u de afbeelding bijwerken door de *imageReference* ID bij te werken (meer informatie, zie de [REST API-documentatie).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="examples"></a>Voorbeelden

### <a name="update-the-os-image-for-your-scale-set"></a>De OS-afbeelding bijwerken voor uw schaalset
Mogelijk hebt u een schaalset waarop een oude versie van Ubuntu LTS 16.04 wordt uitgevoerd. U wilt updaten naar een nieuwere versie van Ubuntu LTS 16.04, zoals versie *16.04.201801090*. De eigenschap afbeeldingsreferentieversie maakt geen deel uit van een lijst, dus u deze eigenschappen rechtstreeks wijzigen met een van de volgende opdrachten:

- Azure PowerShell met [Update-AzVmss](/powershell/module/az.compute/update-azvmss) als volgt:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI met [az vmss-update:](/cli/azure/vmss)

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

U ook de afbeelding wijzigen die uw schaalset gebruikt. U bijvoorbeeld een aangepaste afbeelding bijwerken of wijzigen die wordt gebruikt door uw schaalset. U de afbeelding wijzigen die uw schaalset gebruikt door de eigenschap afbeeldingsreferentie-id bij te werken. De eigenschap image reference ID maakt geen deel uit van een lijst, dus u deze eigenschap rechtstreeks wijzigen met een van de volgende opdrachten:

- Azure PowerShell met [Update-AzVmss](/powershell/module/az.compute/update-azvmss) als volgt:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI met [az vmss-update:](/cli/azure/vmss)

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>De load balancer voor uw schaalset bijwerken
Stel dat u een schaalhebt ingesteld met een Azure Load Balancer en dat u de Azure Load Balancer wilt vervangen door een Azure Application Gateway. De eigenschappen load balancer en Application Gateway voor een schaalset maken deel uit van een lijst, zodat u de opdrachten gebruiken om lijstelementen te verwijderen of toe te voegen in plaats van de eigenschappen rechtstreeks te wijzigen:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Deze opdrachten gaan ervan uit dat er slechts één IP-configuratie en load balancer op de schaalset is. Als er meerdere zijn, moet u mogelijk een andere lijstindex dan *0*gebruiken.


## <a name="next-steps"></a>Volgende stappen
U ook algemene beheertaken uitvoeren op schaalsets met azure [CLI](virtual-machine-scale-sets-manage-cli.md) of [Azure PowerShell.](virtual-machine-scale-sets-manage-powershell.md)
