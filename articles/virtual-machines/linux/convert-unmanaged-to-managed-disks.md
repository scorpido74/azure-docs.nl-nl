---
title: Een Linux-vm converteren van onbeheerde schijven naar beheerde schijven
description: Een Linux-vm converteren van onbeheerde schijven naar beheerde schijven met Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969698"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Een Virtuele Linux-machine converteren van onbeheerde schijven naar beheerde schijven

Als u bestaande Virtuele Linux-machines (VM's) hebt die onbeheerde schijven gebruiken, u de VM's converteren naar [Azure Managed Disks.](../linux/managed-disks-overview.md) Hiermee wordt zowel de osschijf als eventuele gekoppelde gegevensschijven geconverteerd.

In dit artikel ziet u hoe u VM's converteert met behulp van de Azure CLI. Zie [Azure CLI](/cli/azure/install-azure-cli)installeren als u het moet installeren of upgraden. 

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [de veelgestelde vragen over migratie naar beheerde schijven](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De oorspronkelijke VHD's en het opslagaccount die vóór de conversie werden gebruikt door de VM worden niet verwijderd. Hiervoor worden nog altijd kosten in rekening gebracht. Als u wilt voorkomen dat er kosten worden doorberekend voor de artefacten, verwijdert u de oorspronkelijke VHD-blobs nadat u hebt gecontroleerd of de conversie is voltooid. Als u deze niet-gekoppelde schijven moet vinden om ze te verwijderen, raadpleegt u ons artikel [Ongekoppelde Azure-beheerde en onbeheerde schijven zoeken en verwijderen.](find-unattached-disks.md)

## <a name="convert-single-instance-vms"></a>VM's met één instantie converteren
In deze sectie wordt uitgelegd hoe u Azure VM's met één instantie converteert van niet-beheerde schijven naar beheerde schijven. (Als uw VM's zich in een beschikbaarheidsset bevinden, raadpleegt u de volgende sectie.) U dit proces gebruiken om de VM's om te zetten van premium (SSD) onbeheerde schijven naar premium beheerde schijven of van standaard (HDD) onbeheerde schijven naar standaard beheerde schijven.

1. Deallocate de VM met behulp van [az vm deallocate](/cli/azure/vm). In het volgende voorbeeld wordt `myVM` de VM `myResourceGroup`toegewezen die is vernoemd in de resourcegroep met de naam :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converteer de VM naar beheerde schijven met behulp van [az vm converteren](/cli/azure/vm). Met het volgende proces `myVM`wordt de vm met de naam , inclusief de osschijf en eventuele gegevensschijven, geconverteerd:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Start de VM na de conversie naar beheerde schijven met behulp van [az vm start](/cli/azure/vm). In het volgende voorbeeld `myVM` wordt de `myResourceGroup`vm gestart met de naam in de resourcegroep met de naam .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>VM's converteren in een beschikbaarheidsset

Als de VM's die u wilt converteren naar beheerde schijven zich in een beschikbaarheidsset bevinden, moet u eerst de beschikbaarheidsset converteren naar een beheerde beschikbaarheidsset.

Alle VM's in de beschikbaarheidsset moeten worden toegewezen voordat u de beschikbaarheidsset converteert. Plan om alle VM's om te zetten naar beheerde schijven nadat de beschikbaarheidsset zelf is geconverteerd naar een beheerde beschikbaarheidsset. Start vervolgens alle VM's en blijf normaal werken.

1. Vermeld alle VM's in een beschikbaarheiddie is ingesteld met behulp van [de lijst met beschikbaarheid van AZ-vm's](/cli/azure/vm/availability-set). In het volgende voorbeeld worden alle VM's weergegeven in de beschikbaarheidsset met de naam `myAvailabilitySet` `myResourceGroup`resourcegroep:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Deallocate alle VM's met behulp van [az vm deallocate](/cli/azure/vm). In het volgende voorbeeld wordt `myVM` de VM `myResourceGroup`toegewezen die is vernoemd in de resourcegroep met de naam :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converteer de beschikbaarheidsset met het converteren van [de beschikbaarheid van AZ-vm's](/cli/azure/vm/availability-set). In het volgende voorbeeld wordt `myAvailabilitySet` de beschikbaarheidsset geconverteerd met de naam `myResourceGroup`resourcegroep:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converteer alle VM's naar beheerde schijven met behulp van [az vm converteren](/cli/azure/vm). Met het volgende proces `myVM`wordt de vm met de naam , inclusief de osschijf en eventuele gegevensschijven, geconverteerd:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Start alle VM's na de conversie naar beheerde schijven met behulp van [az vm start](/cli/azure/vm). In het volgende voorbeeld `myVM` wordt de `myResourceGroup`vm gestart met de naam in de resourcegroep met de naam:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Converteren met de Azure-portal

U ook onbeheerde schijven converteren naar beheerde schijven met behulp van de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de VM in de lijst met VM's in de portal.
3. Selecteer **Schijven** in het menu in het blad voor de virtuele machine.
4. Selecteer Migreren **naar beheerde schijven**boven aan het **hoofdvan schijven** .
5. Als uw vm zich in een beschikbaarheidsset bevindt, wordt er een waarschuwing weergegeven op het blade **Migreren naar beheerde schijven** die u eerst moet converteren naar de beschikbaarheidsset. De waarschuwing moet een koppeling hebben waarop u klikken om de beschikbaarheidsset om te zetten. Zodra de beschikbaarheidsset is geconverteerd of als uw vm zich niet in een beschikbaarheidsset bevindt, klikt u op **Migreren** om het proces van het migreren van uw schijven naar beheerde schijven te starten.

De VM wordt gestopt en opnieuw gestart nadat de migratie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van Azure [Managed Disks](../windows/managed-disks-overview.md)voor meer informatie over opslagopties.
