---
title: Een gezoneerde Linux-vm maken met de Azure CLI
description: Een Linux-vm maken in een beschikbaarheidszone met de Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: e229bb7af02255c0714c559b841afac9a66a7c7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535609"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Een Virtuele Linux-machine maken in een beschikbaarheidszone met de Azure CLI

In dit artikel wordt de Azure CLI gebruikt om een Linux-vm te maken in een Azure-beschikbaarheidszone. Een [beschikbaarheidszone](../../availability-zones/az-overview.md) is een fysiek afgescheiden zone in een Azure-regio. Gebruik beschikbaarheidszones om uw apps en gegevens te beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter.

Als u een beschikbaarheidszone wilt gebruiken, maakt u een virtuele machine in een [ondersteunde Azure-regio](../../availability-zones/az-overview.md#services-support-by-region).

Zorg ervoor dat u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) hebt geïnstalleerd en ingelogd bent op een Azure-account met [az-aanmelding.](/cli/azure/reference-index)


## <a name="check-vm-sku-availability"></a>Beschikbaarheid van VM-SKU controleren
De beschikbaarheid van VM-grootten, of SKU's, kan verschillen per regio en zone. Als hulp bij de planning voor het gebruik van de beschikbaarheidszones kunt u een overzicht maken van de beschikbare VM-SKU's per Azure-regio en zone. Hiermee zorgt u ervoor dat u een correcte VM-grootte kiest en de gewenste tolerantie in zones verkrijgt. Voor meer informatie over de verschillende VM-typen en -grootten raadpleegt u het [Overzicht van de VM-grootten](sizes.md).

U de beschikbare VM SKU's bekijken met de opdracht [AZ VM List-Skus.](/cli/azure/vm) Met het volgende voorbeeld wordt een lijst weergegeven van de beschikbare VM-SKU's in de regio *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

De uitvoer is vergelijkbaar met het volgende verkorte voorbeeld, waarin de beschikbaarheidszones worden weergegeven waarin elke VM-grootte beschikbaar is:

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group).  

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt. In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroupVM* gemaakt in de regio *eastus2.* East US 2 is een van de Azure-regio's die beschikbaarheidszones ondersteunt.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

De resourcegroep wordt opgegeven bij het maken of wijzigen van een VM, die in dit artikel kan worden gezien.

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm). 

Wanneer u een virtuele machine maakt, zijn er diverse opties beschikbaar zoals besturingssysteeminstallatiekopie, schijfgrootte en beheerdersreferenties. In dit voorbeeld wordt een virtuele machine gemaakt met de naam *myVM* waarop Ubuntu-server wordt uitgevoerd. De VM wordt gemaakt in beschikbaarheidszone *1*. Standaard wordt de VM gemaakt in de *Standard_DS1_v2* grootte.

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Het maken van de virtuele machine kan een paar minuten duren. Wanneer de virtuele machine is gemaakt, biedt de Azure CLI informatie over de virtuele machine. Let op `zones` de waarde, die de beschikbaarheidszone aangeeft waarin de VM wordt uitgevoerd. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Zone voor beheerde schijf en IP-adres bevestigen

Wanneer de VM wordt geïmplementeerd in een beschikbaarheidszone, wordt een beheerde schijf voor de vm gemaakt in dezelfde beschikbaarheidszone. Standaard wordt er ook een openbaar IP-adres gemaakt in die zone. In de volgende voorbeelden vindt u informatie over deze bronnen.

Als u wilt controleren of de beheerde schijf van de VM zich in de beschikbaarheidszone bevindt, gebruikt u de opdracht [AZ VM Show](/cli/azure/vm) om de schijf-id terug te sturen. In dit voorbeeld wordt de schijf-id opgeslagen in een variabele die in een latere stap wordt gebruikt. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Nu u informatie krijgen over de beheerde schijf:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

In de uitvoer ziet u dat de beheerde schijf zich in dezelfde beschikbaarheidszone bevindt als de virtuele machine:

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Gebruik de opdracht [az vm list-ip-adressen](/cli/azure/vm) om de naam van de openbare IP-adresbron in *myVM*terug te geven. In dit voorbeeld wordt de naam opgeslagen in een variabele die in een latere stap wordt gebruikt.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Nu u informatie krijgen over het IP-adres:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Uit de uitvoer blijkt dat het IP-adres zich in dezelfde beschikbaarheidszone bevindt als de VM:

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een VM maakt in een beschikbaarheidszone. Meer informatie over [de beschikbaarheid](availability.md) voor Azure VM's.




