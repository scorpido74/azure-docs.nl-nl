---
title: Zelfstudie - Een VM-schijf herstellen met Azure Backup
description: Leer hoe u een schijf kunt herstellen en een herstel-VM maken in Azure met Backup and Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238732"
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
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Een VM-schijf herstellen

> [!IMPORTANT]
> Het is zeer sterk aanbevolen om Az CLI versie 2.0.74 of later te gebruiken om alle voordelen van een snelle herstel inclusief beheerde schijf te herstellen. Het is het beste als de gebruiker altijd de nieuwste versie gebruikt.

### <a name="managed-disk-restore"></a>Beheerde schijfherstel

Als de back-upVM schijven heeft beheerd en als het de bedoeling is beheerde schijven vanaf het herstelpunt te herstellen, geeft u eerst een Azure-opslagaccount op. Dit opslagaccount wordt gebruikt om de VM-configuratie en de implementatiesjabloon op te slaan die later kunnen worden gebruikt om de VM vanaf de herstelde schijven te implementeren. Vervolgens biedt u ook een doelgroep voor de beheerde schijven die moeten worden hersteld.

1. Gebruik [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) om een opslagaccount te maken. De naam van het opslagaccount mag alleen kleine letters bevatten, en moet globaal uniek zijn. Vervang *mystorageaccount* door uw eigen unieke naam:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Herstel de schijf vanaf uw herstelpunt met [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Vervang *mystorageaccount* door de naam van het opslagaccount dat u met de vorige opdracht hebt gemaakt. Vervang *myRecoveryPointName* door de naam van het herstelpunt dat u hebt verkregen in de uitvoer van de vorige [opdracht voor az-back-upherstelpunten.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) ***Geef ook de doelgroep aan waarnaar de beheerde schijven worden hersteld in***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Als er geen doelbrongroep wordt verstrekt, worden de beheerde schijven hersteld als onbeheerde schijven naar het opgegeven opslagaccount. Dit zal aanzienlijke gevolgen hebben voor het herstel tijd sinds de tijd die nodig is om de schijven te herstellen volledig afhankelijk is van de gegeven opslag account.

### <a name="unmanaged-disks-restore"></a>Onbeheerde schijven herstellen

Als de back-upvm onbeheerde schijven heeft en als het de bedoeling is schijven te herstellen vanaf het herstelpunt, geeft u eerst een Azure-opslagaccount op. Dit opslagaccount wordt gebruikt om de VM-configuratie en de implementatiesjabloon op te slaan die later kunnen worden gebruikt om de VM vanaf de herstelde schijven te implementeren. Standaard worden de niet-beheerde schijven hersteld naar hun oorspronkelijke opslagaccounts. Als de gebruiker alle onbeheerde schijven naar één plaats wil herstellen, kan het opgegeven opslagaccount ook worden gebruikt als faseringslocatie voor die schijven.

In latere stappen wordt de herstelde schijf gebruikt voor het maken van een VM.

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

Zoals hierboven vermeld, worden de niet-beheerde schijven hersteld naar hun oorspronkelijke opslagaccount. Dit zorgt voor de beste herstelprestaties. Maar als alle onbeheerde schijven moeten worden hersteld naar een gegeven opslagaccount, gebruik dan de relevante vlag zoals hieronder weergegeven.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

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

Wanneer de *status* van de hersteltaakrapporten *Voltooid*is, is de benodigde informatie (VM-configuratie en de implementatiesjabloon) hersteld naar het opslagaccount.

## <a name="create-a-vm-from-the-restored-disk"></a>Een VM maken op basis van de herstelde schijf

De laatste stap is het maken van een VM van de herstelde schijven. U de implementatiesjabloon die naar het opgegeven opslagaccount is gedownload, gebruiken om de VM te maken.

### <a name="fetch-the-job-details"></a>De taakgegevens ophalen

De resulterende taakgegevens geven de sjabloon URI die kan worden opgevraagd en geïmplementeerd. Gebruik de opdracht Taakshow om meer details te krijgen voor de geactiveerde herstelde taak.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

De uitvoer van deze query geeft alle details, maar we zijn alleen geïnteresseerd in de inhoud van het opslagaccount. We kunnen de [querycapaciteit](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) van Azure CLI gebruiken om de relevante gegevens op te halen

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>De implementatiesjabloon ophalen

De sjabloon is niet direct toegankelijk omdat deze onder het opslagaccount van een klant en de opgegeven container valt. We hebben de volledige URL (samen met een tijdelijk SAS-token) nodig om toegang te krijgen tot deze sjabloon.

Haal eerst de sjabloonblob Uri uit taakgegevens

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

De sjabloonblob Uri is van deze indeling en haalt de naam van de sjabloon uit

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Dus, de sjabloon naam van ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` het bovenstaande voorbeeld zal zijn en de container naam is```myVM-daa1931199fd4a22ae601f46d8812276```

Nu krijgt u het SAS-token voor deze container en sjabloon zoals [hier](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment) beschreven

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>De sjabloon implementeren om de VM te maken

Implementeer nu de sjabloon om de VM te maken zoals [hier](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)uitgelegd.

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Om te bevestigen dat uw VM van uw herstelde schijf is gemaakt, maakt u als volgt een lijst van de VM's in uw resourcegroep met [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list):

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
