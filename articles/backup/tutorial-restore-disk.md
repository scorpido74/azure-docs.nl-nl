---
title: Zelf studie-een VM-schijf herstellen met Azure Backup
description: Leer hoe u een schijf kunt herstellen en een herstel-VM maken in Azure met Backup and Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 9b2048d8683ba2dde00a874445eb936cfb775cf1
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171745"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Een schijf herstellen en een herstelde VM maken in Azure

Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of afzonderlijke bestanden herstellen. In dit artikel wordt uitgelegd hoe u een volledige VM herstelt met behulp van CLI. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Herstelpunten in een lijst opnemen en selecteren
> * Een schijf herstellen vanaf een herstelpunt
> * Een VM maken op basis van de herstelde schijf

Zie [Back up and restore Azure VMs with PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) (Back-ups maken en Azure-VM’s herstellen met PowerShell) voor meer informatie over het gebruik van PowerShell om een schijf te herstellen en een herstelde VM te maken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.18 of nieuwer uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie vereist een Linux-VM die met Azure Backup is beschermd. Om het per ongeluk verwijderen van een VM en het herstelproces te simuleren, maakt u een VM op basis van een schijf in een herstelpunt. Zie [Een back-up van een virtuele machine maken in Azure met de CLI](quick-backup-vm-cli.md) als u een Linux-VM nodig hebt die is beschermd met Azure Backup.

## <a name="backup-overview"></a>Overzicht van Backup

Wanneer Azure een back-up begint, maakt de back-upextensie op de VM een momentopname van een bepaald tijdstip. De back-upextensie wordt geïnstalleerd op de VM wanneer de eerste back-up wordt aangevraagd. Azure Backup kan ook een momentopname van de onderliggende opslag maken als de VM niet wordt uitgevoerd ten tijde van de back-up.

Standaard maakt Azure Backup een back-up die consistent is met een bestandssysteem. Nadat Azure Backup de momentopname heeft gemaakt, worden de gegevens overgedragen naar de Recovery Services-kluis. Voor maximale efficiëntie identificeert Azure Backup welke gegevensblokken sinds de vorige back-up zijn gewijzigd. Alleen deze worden vervolgens overgedragen.

Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt er een herstelpunt gemaakt.

## <a name="list-available-recovery-points"></a>Lijst met beschikbare herstelpunten maken

Voor het herstellen van een schijf selecteert u een herstelpunt als bron voor de te herstellen gegevens. Aangezien het standaardbeleid elke dag een herstelpunt maakt en gedurende 30 dagen bewaart, kunt u een reeks herstelpunten behouden waarmee u een bepaald tijdstip kunt kiezen om te herstellen.

Gebruik [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) om een lijst met beschikbare herstelpunten te zien. De **naam** van het herstelpunt wordt gebruikt om schijven te herstellen. In deze zelfstudie willen we het meest recente beschikbare herstelpunt gebruiken. Met de parameter `--query [0].name` selecteert u als volgt de naam van het meest recente herstelpunt:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Een VM-schijf herstellen

Om uw schijf te herstellen vanaf het herstelpunt, maakt u eerst een Azure opslagaccount. Dit opslagaccount wordt gebruikt voor het opslaan van de herstelde schijf. In latere stappen wordt de herstelde schijf gebruikt voor het maken van een VM.

1. Gebruik [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) om een opslagaccount te maken. De naam van het opslagaccount mag alleen kleine letters bevatten, en moet globaal uniek zijn. Vervang *mystorageaccount* door uw eigen unieke naam:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Herstel de schijf vanaf uw herstelpunt met [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Vervang *mystorageaccount* door de naam van het opslagaccount dat u met de vorige opdracht hebt gemaakt. Vervang *myRecoveryPointName* door de naam van het herstelpunt dat u hebt verkregen in de uitvoer van de vorige opdracht [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

## <a name="monitor-the-restore-job"></a>De hersteltaak bewaken

U kunt de status van hersteltaken controleren met [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De uitvoer is vergelijkbaar met het volgende voorbeeld, waarin u kunt zien dat de hersteltaak wordt uitgevoerd (*InProgress*):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Wanneer de *Status* van de hersteltaak *Voltooid* is, is de schijf hersteld naar het opslagaccount.

## <a name="convert-the-restored-disk-to-a-managed-disk"></a>De herstelde schijf converteren naar een beheerde schijf

Met de hersteltaak wordt een niet-beheerde schijf gemaakt. Om een VM ta maken op basis van de schijf, moet u deze eerst converteren naar een beheerde schijf.

1. Haal de verbindingsgegevens voor uw opslagaccount op met [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string). Vervang *mystorageaccount* door de naam van uw opslagaccount, als volgt:

    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Uw onbeheerde schijf wordt beveiligd in het opslagaccount. Met de volgende opdrachten wordt informatie over uw onbeheerde schijf opgehaald en wordt een variabele met de naam *uri* gemaakt, die wordt gebruikt in de volgende stap, wanneer u de beheerde schijf maakt.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Nu kunt u [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create) gebruiken om van uw herstelde schijf een beheerde schijf te maken. De variabele *uri* uit de vorige stap wordt gebruikt als bron voor de beheerde schijf.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Nu u een beheerde schijf van uw herstelde schijf hebt, kunt u de onbeheerde schijf en het opslagaccount opschonen met [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete). Vervang *mystorageaccount* door de naam van uw opslagaccount, als volgt:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```

## <a name="create-a-vm-from-the-restored-disk"></a>Een VM maken op basis van de herstelde schijf

De laatste stap is het maken van een VM van de beheerde schijf.

1. Gebruik [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) om als volgt een VM te maken van uw beheerde schijf:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Om te bevestigen dat uw VM van uw herstelde schijf is gemaakt, maakt u als volgt een lijst van de VM's in uw resourcegroep met [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list):

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een schijf van een herstelpunt hersteld en vervolgens een VM van de schijf gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> * Herstelpunten in een lijst opnemen en selecteren
> * Een schijf herstellen vanaf een herstelpunt
> * Een VM maken op basis van de herstelde schijf

Ga naar de volgende zelfstudie voor meer informatie over het herstellen van afzonderlijke bestanden van een herstelpunt.

> [!div class="nextstepaction"]
> [Bestanden herstellen naar een virtuele machine in Azure](tutorial-restore-files.md)
