---
title: Een Linux-VM kopiëren met behulp van Azure CLI
description: Meer informatie over het maken van een kopie van uw Azure Linux-VM met behulp van Azure CLI en Managed Disks.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: a6a8b766efdc781df1fea29da81dc48090875ad7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036573"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Een kopie van een virtuele Linux-machine maken met behulp van Azure CLI en Managed Disks

In dit artikel wordt beschreven hoe u een kopie maakt van uw virtuele Azure-machine (VM) waarop Linux wordt uitgevoerd met behulp van Azure CLI en het Azure Resource Manager-implementatie model. 

U kunt ook [een virtuele machine uploaden en maken op basis van een VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Vereisten

-   Installeer de [Azure CLI](/cli/azure/install-az-cli2).

-   Meld u aan bij een Azure-account met [AZ login](/cli/azure/reference-index#az-login).

-   Een Azure-VM die moet worden gebruikt als de bron voor uw kopie.

## <a name="stop-the-source-vm"></a>De bron-VM stoppen

Wijs de toewijzing van de bron-VM ongedaan met [AZ VM deallocate](/cli/azure/vm#az-vm-deallocate).
In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* in de resource groep *myResourceGroup*.

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>De bron-VM kopiëren

Als u een virtuele machine wilt kopiëren, maakt u een kopie van de onderliggende virtuele harde schijf. Met dit proces wordt een gespecialiseerde virtuele harde schijf (VHD) gemaakt als beheerde schijf met dezelfde configuratie en instellingen als de bron-VM.

Zie [Overzicht van Azure Managed Disks](../windows/managed-disks-overview.md) voor meer informatie over Azure Managed Disks. 

1.  Vermeld elke virtuele machine en de naam van de besturingssysteem schijf met [AZ VM List](/cli/azure/vm#az-vm-list). In het volgende voor beeld worden alle virtuele machines in de resource groep met de naam *myResourceGroup*weer gegeven:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    De uitvoer lijkt op die in het volgende voorbeeld:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Kopieer de schijf door een nieuwe beheerde schijf te maken en door [AZ Disk Create](/cli/azure/disk#az-disk-create)te gebruiken. In het volgende voor beeld wordt een schijf met de naam *myCopiedDisk* gemaakt op basis van de beheerde schijf met de naam *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Controleer de beheerde schijven nu in uw resource groep met behulp van [AZ Disk List](/cli/azure/disk#az-disk-list). In het volgende voor beeld ziet u de beheerde schijven in de resource groep met de naam *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Een virtueel netwerk instellen

Met de volgende optionele stappen maakt u een nieuw virtueel netwerk, subnet, openbaar IP-adres en virtuele netwerk interface kaart (NIC).

Als u een virtuele machine kopieert voor het oplossen van problemen of extra implementaties, wilt u mogelijk geen virtuele machine in een bestaand virtueel netwerk gebruiken.

Als u een virtuele netwerk infrastructuur voor de gekopieerde Vm's wilt maken, volgt u de volgende stappen. Als u geen virtueel netwerk wilt maken, gaat u door met [het maken van een virtuele machine](#create-a-vm).

1.  Maak het virtuele netwerk met [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *myVnet* en een subnet met de naam *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Maak een openbaar IP-adres met behulp van [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). In het volgende voor beeld wordt een openbaar IP-adres met de naam *myPublicIP* gemaakt met de DNS- *mypublicdns*. (Omdat de DNS-naam uniek moet zijn, geeft u een unieke naam op.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Maak de NIC met behulp van [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create).
    In het volgende voor beeld wordt een NIC gemaakt met de naam *myNic* die is gekoppeld aan het *mySubnet* -subnet:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een virtuele machine met behulp van [AZ VM Create](/cli/azure/vm#az-vm-create).

Geef als volgt de gekopieerde, beheerde schijf op die moet worden gebruikt als de besturingssysteem schijf (`--attach-os-disk`):

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure cli-opdrachten voor de Azure Resource Manager](../azure-cli-arm-commands.md)voor meer informatie over het gebruik van Azure CLI voor het beheren van uw nieuwe virtuele machine.
