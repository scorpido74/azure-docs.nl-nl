---
title: Een Linux-vm kopiëren met Azure CLI
description: Meer informatie over het maken van een kopie van uw Azure Linux VM met Azure CLI en Managed Disks.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: ed8574133eafe751699e90ea8cae832ee649fb00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969587"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Een kopie van een Linux-vm maken met Azure CLI en Beheerde schijven

In dit artikel ziet u hoe u een kopie maakt van uw Virtuele Azure-machine (VM) met Linux met behulp van azure cli en het implementatiemodel azure resource manager. 

U ook [een VM uploaden en maken vanuit een VHD.](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="prerequisites"></a>Vereisten

-   Installeer de [Azure CLI](/cli/azure/install-az-cli2).

-   Meld u aan bij een Azure-account met [az-aanmelding](/cli/azure/reference-index#az-login).

-   Heb een Azure VM te gebruiken als de bron voor uw exemplaar.

## <a name="stop-the-source-vm"></a>De bron-VM stoppen

Deallocate de bron VM met behulp van [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
In het volgende voorbeeld wordt de VM met de naam *myVM* toegewezen aan de resourcegroep *myResourceGroup:*

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>De bron-VM kopiëren

Als u een vm wilt kopiëren, maakt u een kopie van de onderliggende virtuele harde schijf. Dit proces maakt een gespecialiseerde virtuele harde schijf (VHD) als een beheerde schijf die dezelfde configuratie en instellingen bevat als de bron-VM.

Zie [Overzicht van Azure Managed Disks](../windows/managed-disks-overview.md) voor meer informatie over Azure Managed Disks. 

1.  Vermeld elke VM en de naam van de OS-schijf met [de AZ VM-lijst](/cli/azure/vm#az-vm-list). In het volgende voorbeeld worden alle VM's in de resourcegroep met de naam *myResourceGroup weergegeven:*
    
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

1.  Kopieer de schijf door een nieuwe beheerde schijf te maken en met [az-schijf te maken.](/cli/azure/disk#az-disk-create) In het volgende voorbeeld wordt een schijf met de naam *myCopiedDisk* gemaakt van de beheerde schijf met de naam *myDisk:*

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Controleer de beheerde schijven nu in uw brongroep met behulp van [de az-schijflijst](/cli/azure/disk#az-disk-list). In het volgende voorbeeld worden de beheerde schijven in de resourcegroep genaamd *myResourceGroup weergegeven:*

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Een virtueel netwerk instellen

Met de volgende optionele stappen wordt een nieuw virtueel netwerk, subnet, openbaar IP-adres en virtuele netwerkinterfacekaart (NIC) gemaakt.

Als u een vm kopieert voor probleemoplossingsdoeleinden of extra implementaties, wilt u mogelijk geen VM gebruiken in een bestaand virtueel netwerk.

Als u een virtuele netwerkinfrastructuur voor uw gekopieerde VM's wilt maken, voert u de volgende stappen uit. Als u geen virtueel netwerk wilt maken, gaat u naar [Een virtuele virtuele netwerk maken.](#create-a-vm)

1.  Maak het virtuele netwerk met behulp van [az-netwerk vnet maken](/cli/azure/network/vnet#az-network-vnet-create). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVnet* en een subnet met de naam *mySubnet:*

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Maak een openbaar IP-adres met behulp van [az-netwerk public-ip maken](/cli/azure/network/public-ip#az-network-public-ip-create). In het volgende voorbeeld wordt een openbaar IP met de naam *myPublicIP* gemaakt met de DNS-naam van *mypublicdns*. (Omdat de DNS-naam uniek moet zijn, geeft u een unieke naam op.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Maak de NIC met behulp van [az-netwerk nic maken](/cli/azure/network/nic#az-network-nic-create).
    In het volgende voorbeeld wordt een NIC met de naam *myNic* aanmaken die is gekoppeld aan het *subnet van mySubnet:*

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een VM met behulp van [az vm maken](/cli/azure/vm#az-vm-create).

Geef de gekopieerde beheerde schijf op`--attach-os-disk`die als besturingssysteemschijf ( ) moet worden gebruikt:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van een [gedeelde afbeeldingsgalerie](shared-images.md) om VM-afbeeldingen te beheren.
