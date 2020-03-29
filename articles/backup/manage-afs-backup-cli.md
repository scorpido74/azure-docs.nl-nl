---
title: Back-ups van Azure-bestandsshare beheren met de Azure CLI
description: Meer informatie over het gebruik van azure CLI voor het beheren en bewaken van Azure-bestandsshares waareen back-up van azure backup wordt gemaakt.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934875"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Back-ups van Azure-bestandsshare beheren met de Azure CLI

De Azure CLI biedt een opdrachtregelervaring voor het beheer van Azure-resources. Het is een geweldig hulpmiddel voor het bouwen van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt uitgelegd hoe u taken uitvoert voor het beheren en bewaken van de Azure-bestandsshares waarvan een back-up wordt gemaakt door [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview) U deze stappen ook uitvoeren met de [Azure-portal.](https://portal.azure.com/) 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u de Azure CLI-versie 2.0.18 of hoger uitvoeren. Voer `az --version` uit om de CLI-versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat er al een Azure-bestandsshare is gemaakt waareen [back-up](https://docs.microsoft.com/azure/backup/backup-overview)van maakt door Azure Backup. Zie [Back-up van Azure-bestandsshares met de CLI](backup-afs-cli.md) om back-ups voor uw bestandsshares te configureren als u er geen hebt. Voor dit artikel gebruikt u de volgende bronnen:

* **Resourcegroep**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Opslagrekening**: *afsaccount*
* **Bestandsshare:** *azurefiles*

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u back-up- of herstelbewerkingen activeert, maakt de back-upservice een taak voor het bijhouden. Gebruik de cmdlet van de [AZ-back-uptakenlijst](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) om voltooide of momenteel lopende taken te controleren. Met de CLI u ook [een momenteel lopende taak opschorten](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) of wachten tot een taak is [voltooid.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

In het volgende voorbeeld wordt de status van back-uptaken voor de kluis *Azurefilesvault* Recovery Services weergegeven:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Beleid wijzigen

U een back-upbeleid wijzigen om de back-upfrequentie of het bewaarbereik te wijzigen met behulp van [het setbeleid voor AZ-back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Als u het beleid wilt wijzigen, definieert u de volgende parameters:

* **--containernaam:** de naam van het opslagaccount dat het bestandsaandeel host. Als u de **naam** of **de vriendelijke naam** van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--naam:** de naam van de bestandsshare waarvoor u het beleid wilt wijzigen. Als u de **naam** of **de vriendelijke naam** van uw back-upitem wilt ophalen, gebruikt u de opdracht lijst met [AZ-back-upitems.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--beleidsnaam:** de naam van het back-upbeleid dat u wilt instellen voor uw bestandsshare. U [de lijst met az-back-upbeleid](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) gebruiken om alle beleidsregels voor uw kluis weer te geven.

In het volgende voorbeeld wordt het back-upbeleid *schedule2* ingesteld voor het *azurefiles-bestandsaandeel* dat aanwezig is in het account voor opslag van *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

U de vorige opdracht ook uitvoeren door de vriendelijke namen voor de container en het item te gebruiken door de volgende twee extra parameters op te geven:

* **--back-up-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw wijzigingsbeleidsbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Stop alle toekomstige back-uptaken en *verwijder* alle herstelpunten.
* Stop alle toekomstige back-uptaken, maar laat de herstelpunten *achter.*

Er kunnen kosten verbonden zijn aan het verlaten van de herstelpunten in de opslag, omdat de onderliggende momentopnamen die zijn gemaakt door Azure Backup behouden blijven. Het voordeel van het verlaten van de herstelpunten is de optie om het bestandsaandeel later te herstellen, als u dat wilt. Zie de [prijsdetails](https://azure.microsoft.com/pricing/details/storage/files)voor informatie over de kosten van het verlaten van de herstelpunten. Als u ervoor kiest om alle herstelpunten te verwijderen, u de bestandsshare niet herstellen.

Als u de beveiliging van de bestandsshare wilt stoppen, definieert u de volgende parameters:

* **--containernaam:** de naam van het opslagaccount dat het bestandsaandeel host. Als u de **naam** of **de vriendelijke naam** van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-naam:** de naam van de bestandsshare waarvoor u de beveiliging wilt stoppen. Als u de **naam** of **de vriendelijke naam** van uw back-upitem wilt ophalen, gebruikt u de opdracht lijst met [AZ-back-upitems.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Stop de beveiliging en bewaar herstelpunten

Gebruik de [az-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) om de bescherming van gegevens te stoppen met het bewaren van gegevens.

In het volgende voorbeeld wordt de beveiliging voor het *azurefiles-bestand* niet meer beschermd, maar worden alle herstelpunten behouden.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

U de vorige opdracht ook uitvoeren door de vriendelijke naam voor de container en het item te gebruiken door de volgende twee extra parameters op te geven:

* **--back-up-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw stopbeveiligingsbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

### <a name="stop-protection-without-retaining-recovery-points"></a>Stop de beveiliging zonder herstelpunten te behouden

Gebruik de [az-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) om de beveiliging van de BACK-up te stoppen met de optie **delete-backup-data** die is ingesteld op **true**om de beveiliging te stoppen zonder herstelpunten te behouden.

In het volgende voorbeeld wordt de beveiliging voor het delen *van azurefiles-bestanden* gestopt zonder herstelpunten te behouden.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

U de vorige opdracht ook uitvoeren door de vriendelijke naam voor de container en het item te gebruiken door de volgende twee extra parameters op te geven:

* **--back-up-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare hervatten

Als u de beveiliging voor een Azure-bestandsshare hebt gestopt, maar herstelpunten hebt behouden, u de beveiliging later hervatten. Als u de herstelpunten niet behoudt, u de beveiliging niet hervatten.

Als u de beveiliging van het bestandsaandeel wilt hervatten, definieert u de volgende parameters:

* **--containernaam:** de naam van het opslagaccount dat het bestandsaandeel host. Als u de **naam** of **de vriendelijke naam** van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-naam:** de naam van de bestandsshare waarvoor u de beveiliging wilt hervatten. Als u de **naam** of **de vriendelijke naam** van uw back-upitem wilt ophalen, gebruikt u de opdracht lijst met [AZ-back-upitems.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--beleidsnaam:** de naam van het back-upbeleid waarvoor u de beveiliging voor de bestandsshare wilt hervatten.

In het volgende voorbeeld wordt de cmdlet voor de cv-cmdlet van [AZ-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) gebruikt om de beveiliging van het *azurefiles-bestand* te hervatten met behulp van het back-upbeleid *schema1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

U de vorige opdracht ook uitvoeren door de vriendelijke naam voor de container en het item te gebruiken door de volgende twee extra parameters op te geven:

* **--back-up-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw cv-beveiligingsbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

## <a name="unregister-a-storage-account"></a>Een opslagaccount uitschrijven

Als u uw bestandsshares in een bepaald opslagaccount wilt beveiligen met behulp van een andere vault van Recovery Services, stopt u eerst [de beveiliging van alle bestandsshares](#stop-protection-on-a-file-share) in die opslagaccount. Registreer het account vervolgens uit de kluis van de Herstelservices die momenteel voor bescherming wordt gebruikt.

U moet een containernaam opgeven om het opslagaccount uit te schrijven. Als u de **naam** of de **vriendelijke naam** van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

In het volgende voorbeeld wordt het *account van de afsaccountopslag* van *azurefilesvault* losgekoppeld met behulp van de cmdlet [voor az-back-upcontainer unregister.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

U de vorige cmdlet ook uitvoeren met de vriendelijke naam voor de container door de volgende extra parameter op te geven:

* **--back-up-management-type:** *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Volgende stappen

Zie [Problemen met azure-bestandsshares back-up oplossen](troubleshoot-azure-files.md)voor meer informatie.
