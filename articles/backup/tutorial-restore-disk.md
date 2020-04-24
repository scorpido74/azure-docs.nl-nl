---
title: Zelf studie-een VM-schijf herstellen met Azure Backup
description: Leer hoe u een schijf kunt herstellen en een herstel-VM maken in Azure met Backup and Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 31e2645a4a627793f13c37c543d9e08240e06930
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113694"
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
> Het wordt zeer ten zeerste aanbevolen AZ CLI version 2.0.74 of hoger te gebruiken om alle voor delen van een snelle herstel bewerking te krijgen, inclusief het terugzetten van de beheerde schijf. Het is het beste als de gebruiker altijd de nieuwste versie gebruikt.

### <a name="managed-disk-restore"></a>Beheer van beheerde schijven

Als de virtuele machine waarvoor een back-up is gemaakt, beheerde schijven heeft en de bedoeling is om beheerde schijven te herstellen vanaf het herstel punt, geeft u eerst een Azure-opslag account op. Dit opslag account wordt gebruikt voor het opslaan van de VM-configuratie en de implementatie sjabloon die later kunnen worden gebruikt om de virtuele machine vanaf de herstelde schijven te implementeren. Vervolgens geeft u ook een doel resource groep op voor de beheerde schijven die moeten worden hersteld.

1. Gebruik [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) om een opslagaccount te maken. De naam van het opslagaccount mag alleen kleine letters bevatten, en moet globaal uniek zijn. Vervang *mystorageaccount* door uw eigen unieke naam:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Herstel de schijf vanaf uw herstelpunt met [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Vervang *mystorageaccount* door de naam van het opslagaccount dat u met de vorige opdracht hebt gemaakt. Vervang *myRecoveryPointName* door de naam van het herstel punt dat u hebt verkregen in de uitvoer van de vorige opdracht [AZ backup Recovery Point List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) . ***Geef ook de doel resource groep op waarop de beheerde schijven worden teruggezet***.

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
> Als de doel bron-groep niet is opgegeven, worden de beheerde schijven teruggezet als onbeheerde schijven naar het opgegeven opslag account. Dit heeft belang rijke gevolgen voor de herstel tijd sinds de tijd die nodig is om de schijven volledig te herstellen, is afhankelijk van het opgegeven opslag account. Klanten krijgen het voor deel van direct terugzetten alleen wanneer de doel-Resource-Group-para meter is opgegeven. Als de bedoeling is om beheerde schijven te herstellen als niet-beheerd, geeft u de para meter doel-resource-groep niet op en geeft u in plaats daarvan de para meter Restore-as-unmanaged-disk op zoals hieronder wordt weer gegeven. Deze para meter is beschikbaar vanaf AZ 3.4.0.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-as-unmanaged-disk
    ```
Hiermee worden beheerde schijven als niet-beheerde schijven teruggezet naar het opgegeven opslag account en wordt de herstel functionaliteit ' direct ' niet meer in beslag genomen. In toekomstige versies van CLI is het verplicht om de para meter doel-Resource-Group of ' herstellen-als onbeheerd-disk ' op te geven.

### <a name="unmanaged-disks-restore"></a>Onbeheerde schijven herstellen

Als de back-up van de virtuele machine niet-beheerde schijven bevat en als de bedoeling is om schijven te herstellen vanaf het herstel punt, geeft u eerst een Azure-opslag account op. Dit opslag account wordt gebruikt voor het opslaan van de VM-configuratie en de implementatie sjabloon die later kunnen worden gebruikt om de virtuele machine vanaf de herstelde schijven te implementeren. Standaard worden de niet-beheerde schijven teruggezet naar de oorspronkelijke opslag accounts. Als de gebruiker alle niet-beheerde schijven op één locatie wil herstellen, kan het opgegeven opslag account ook worden gebruikt als tijdelijke locatie voor die schijven.

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

Zoals hierboven vermeld, worden de niet-beheerde schijven teruggezet naar hun oorspronkelijke opslag account. Dit biedt de beste prestaties voor herstel. Maar als alle niet-beheerde schijven moeten worden hersteld naar een opgegeven opslag account, gebruikt u de relevante vlag zoals hieronder wordt weer gegeven.

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

Wanneer de *status* van de herstel taak rapporten *is voltooid*, zijn de benodigde gegevens (VM-configuratie en de implementatie sjabloon) hersteld naar het opslag account.

## <a name="create-a-vm-from-the-restored-disk"></a>Een VM maken op basis van de herstelde schijf

De laatste stap bestaat uit het maken van een virtuele machine op basis van de herstelde schijven. U kunt de implementatie sjabloon die is gedownload naar het opgegeven opslag account gebruiken om de virtuele machine te maken.

### <a name="fetch-the-job-details"></a>De taak Details ophalen

De resulterende taak details geven de sjabloon-URI die kan worden opgevraagd en geïmplementeerd. Gebruik de opdracht show command om meer informatie te krijgen over de geactiveerde herstelde taak.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

De uitvoer van deze query geeft alle details, maar we zijn alleen geïnteresseerd in de inhoud van het opslag account. We kunnen de [query mogelijkheid](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) van Azure CLI gebruiken om de relevante gegevens op te halen

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

### <a name="fetch-the-deployment-template"></a>De implementatie sjabloon ophalen

De sjabloon is niet direct toegankelijk omdat deze zich onder het opslag account van de klant bevindt en de opgegeven container. U hebt de volledige URL (samen met een tijdelijk SAS-token) nodig om toegang te krijgen tot deze sjabloon.

Extraheer eerst de URI van de sjabloon-Blob uit taak Details

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

De URI van de sjabloon-BLOB heeft de volgende indeling en extraheert de sjabloon naam

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

De naam van de sjabloon in het bovenstaande voor beeld is ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` dus en de naam van de container is```myVM-daa1931199fd4a22ae601f46d8812276```

Down load nu het SAS-token voor deze container en sjabloon, zoals [hier](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment) wordt beschreven

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

### <a name="deploy-the-template-to-create-the-vm"></a>De sjabloon implementeren om de virtuele machine te maken

Implementeer nu de sjabloon voor het maken van de virtuele machine, zoals [hier](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)wordt uitgelegd.

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
