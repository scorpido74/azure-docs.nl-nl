---
title: Virtuele harde schijven uitbreiden op een Linux-vm
description: Meer informatie over het uitbreiden van virtuele harde schijven op een Linux-vm met de Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1295c5276f0f342323acf8d86eaaf9f785af3e9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945191"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Virtuele harde schijven op een Linux-vm uitbreiden met de Azure CLI

In dit artikel wordt beschreven hoe beheerde schijven voor een Virtuele Linux-machine (VM) kunnen worden uitgebreid met de Azure CLI. U [gegevensschijven toevoegen](add-disk.md) om extra opslagruimte te bieden en u ook een bestaande gegevensschijf uitbreiden. De standaardgrootte van de virtuele harde schijf voor het besturingssysteem (OS) is doorgaans 30 GB op een Linux-vm in Azure. 

> [!WARNING]
> Zorg er altijd voor dat uw bestandssysteem in orde is, het type schijfpartitietabel ondersteunt de nieuwe grootte en zorg ervoor dat er een back-up van uw gegevens wordt gemaakt voordat u schijfgroottebewerkingen uitvoert. Zie [Back-ups van Linux VM's maken in Azure](tutorial-backup-vms.md)voor meer informatie. 

## <a name="expand-an-azure-managed-disk"></a>Een Azure-beheerde schijf uitbreiden
Zorg ervoor dat u de nieuwste [Azure CLI hebt](/cli/azure/install-az-cli2) geïnstalleerd en bent aangemeld bij een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index#az-login)

Dit artikel vereist een bestaande VM in Azure met ten minste één gegevensschijf die is gekoppeld en voorbereid. Zie Een virtuele machine maken en voorbereiden [met gegevensschijven](tutorial-manage-disks.md#create-and-attach-disks)als u nog geen VM hebt die u gebruiken.

Vervang in de volgende voorbeelden voorbeeldparameternamen zoals *myResourceGroup* en *myVM* door uw eigen waarden.

1. Bewerkingen op virtuele harde schijven kunnen niet worden uitgevoerd terwijl de VM wordt uitgevoerd. Deallocate uw VM met [az vm deallocate](/cli/azure/vm#az-vm-deallocate). In het volgende voorbeeld wordt de VM met de naam *myVM* toegewezen in de resourcegroep met de naam *myResourceGroup:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > De VM moet worden deallocated om de virtuele harde schijf uit te breiden. Als u `az vm stop` de VM stopt, worden de rekenbronnen niet vrijgegeven. Als u compute `az vm deallocate`resources wilt vrijgeven, gebruikt u .

1. Bekijk een lijst met beheerde schijven in een resourcegroep met [de lijst met AZ-schijven](/cli/azure/disk#az-disk-list). In het volgende voorbeeld wordt een lijst met beheerde schijven weergegeven in de resourcegroep met de naam *myResourceGroup:*

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Vouw de vereiste schijf uit met [de AZ-schijfupdate](/cli/azure/disk#az-disk-update). In het volgende voorbeeld wordt de beheerde schijf met de naam *myDataDisk* uitgebreid tot *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Wanneer u een beheerde schijf uitvouwt, wordt de bijgewerkte grootte naar boven afgerond op de dichtstbijzijnde beheerde schijfgrootte. Zie Overzicht van Azure Managed Disks - [Prijzen en facturering](../windows/managed-disks-overview.md)voor een tabel met de beschikbare beheerde schijfformaten en -lagen.

1. Start uw VM met [az vm start](/cli/azure/vm#az-vm-start). In het volgende voorbeeld wordt de VM met de naam *myVM* gestart in de resourcegroep *myResourceGroup:*

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Een schijfpartitie en bestandssysteem uitvouwen
Als u een uitgevouwen schijf wilt gebruiken, vouwt u de onderliggende partitie en het bestandssysteem uit.

1. SSH naar uw VM met de juiste referenties. U het openbare IP-adres van uw VM met [az vm show](/cli/azure/vm#az-vm-show)zien:

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Vouw het onderliggende partitie- en bestandssysteem uit.

    a. Als de schijf al is gemonteerd, u deze loskoppelen:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Met `parted` gebruiken om schijfgegevens weer te geven en het formaat van de partitie te wijzigen:

    ```bash
    sudo parted /dev/sdc
    ```

    Informatie weergeven over de `print`bestaande partitieindeling met . De uitvoer is vergelijkbaar met het volgende voorbeeld, waaruit blijkt dat de onderliggende schijf 215 GB is:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. De partitie `resizepart`uitvouwen met . Voer het partitienummer, *1*en een grootte voor de nieuwe partitie in:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Als u `quit`wilt afsluiten, voert u het in.

1. Controleer de partitieconsistentie met: `e2fsck`

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Wijzig het formaat `resize2fs`van het bestandssysteem met:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Monteer de partitie op de `/datadrive`gewenste locatie, zoals :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Als u wilt controleren of de `df -h`grootte van de gegevensschijf is gewijzigd, gebruikt u . In de volgende voorbeelduitvoer wordt weergegeven dat het gegevensstation */dev/sdc1* nu 200 GB bedraagt:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Volgende stappen
* Als u extra opslagruimte nodig hebt, u ook [gegevensschijven toevoegen aan een Linux-vm.](add-disk.md) 
* Zie Azure Disk Encryption [for Linux VM's voor](disk-encryption-overview.md)meer informatie over schijfversleuteling.
