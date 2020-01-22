---
title: Back-ups van Azure-bestands shares beheren met CLI
description: Informatie over het gebruik van Azure CLI voor het beheren en controleren van back-ups van Azure-bestands shares van de Azure Backup-service.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294472"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Back-ups van Azure-bestands shares beheren met Azure CLI

De Azure-opdracht regel interface (CLI) biedt een opdracht regel ervaring voor het beheer van Azure-resources. Het is een uitstekend hulp programma voor het maken van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt uitgelegd hoe u de onderstaande taken uitvoert voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up wordt gemaakt door de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview). U kunt deze stappen ook uitvoeren met de [Azure Portal](https://portal.azure.com/).

* [Taken bewaken](#monitor-jobs)
* [Beleid wijzigen](#modify-policy)
* [De beveiliging van een bestands share stoppen](#stop-protection-on-a-file-share)
* [De beveiliging van een bestands share hervatten](#resume-protection-on-a-file-share)
* [Registratie van een opslag account ongedaan maken](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI versie 2.0.18 of hoger gebruiken. Voer `az --version` uit om de CLI-versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u al een Azure-bestands share hebt waarvan een back-up is gemaakt door de [Azure backup](https://docs.microsoft.com/azure/backup/backup-overview) service. Als u er nog geen hebt, raadpleegt u [back-ups van Azure-bestands shares met CLI](backup-afs-cli.md) voor het configureren van back-ups voor uw bestands shares. Voor dit artikel gebruiken we de volgende resources:

* **Resource groep**: *Azure files*
* **RecoveryServicesVault**: *azurefilesvault*
* **Opslag account**: *afsaccount*
* **Bestands share**: *Azure files*

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u back-up-of herstel bewerkingen uitvoert, maakt de back-upservice een taak voor tracering. Als u voltooide of momenteel actieve taken wilt controleren, gebruikt u de cmdlet [AZ Backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . Met CLI kunt u ook [een taak die momenteel wordt uitgevoerd, onderbreken](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) of [wachten totdat een taak is voltooid](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

In het volgende voor beeld wordt de status van back-uptaken voor de *azurefilesvault* Recovery Services-kluis weer gegeven:

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

U kunt een back-upbeleid wijzigen om de back-upfrequentie of het Bewaar bereik te wijzigen met behulp van [AZ backup item set-Policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Als u het beleid wilt wijzigen, definieert u de volgende para meters:

* **--container naam** is de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--naam** is de naam van de bestands share waarvoor u het beleid wilt wijzigen. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **---beleids naam** is de naam van het back-upbeleid dat u wilt instellen voor de bestands share. U kunt de [lijst AZ back-upbeleid](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) gebruiken om alle beleids regels voor uw kluis weer te geven.

In het volgende voor beeld wordt het *schedule2* -back-upbeleid ingesteld voor de *Azure files* -bestands share die aanwezig is in het *afsaccount* -opslag account.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

U kunt ook de bovenstaande opdracht met de ' beschrijvende namen ' voor de container en het item uitvoeren door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw bewerking beleid wijzigen. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>De beveiliging van een bestands share stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstel punten *verwijderen*
* Alle toekomstige back-uptaken stoppen, maar de herstel punten *behouden*

Er zijn mogelijk kosten verbonden aan het verlaten van de herstel punten in de opslag, omdat de onderliggende moment opnamen die zijn gemaakt door Azure Backup, behouden blijven. Het voor deel van het verlaten van de herstel punten is echter de optie om de bestands share later te herstellen, indien gewenst. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/storage/files)voor meer informatie over de kosten voor het verlaten van de herstel punten. Als u alle herstelpunten verwijdert, kunt u de bestandsshare niet terugzetten.

Als u de beveiliging voor de bestands share wilt stoppen, definieert u de volgende para meters:

* **--container naam** is de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--item-name** is de naam van de bestands share waarvoor u de beveiliging wilt stoppen. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Beveiliging stoppen en herstel punten behouden

Als u de beveiliging wilt stoppen tijdens het bewaren van gegevens, gebruikt u de cmdlet [AZ Backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

Het onderstaande voor beeld stopt de beveiliging van de *Azure files* -bestands share, maar behoudt alle herstel punten.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

U kunt ook de bovenstaande opdracht uitvoeren met behulp van de beschrijvende naam voor de container en het item door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de bewerking ' Stop beveiliging '. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Beveiliging stoppen zonder herstel punten te bewaren

Als u de beveiliging wilt stoppen zonder herstel punten te bewaren, gebruikt u de para meter [AZ Backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet with do a **Delete-Backup-Data** ingesteld op **True**.

In het volgende voor beeld wordt de beveiliging van de *Azure files* -bestands share gestopt zonder herstel punten te bewaren:

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

U kunt ook de bovenstaande opdracht uitvoeren met behulp van de beschrijvende naam voor de container en het item door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>De beveiliging van een bestands share hervatten

Als u de beveiliging van een Azure-bestands share en behouden herstel punten hebt gestopt, kunt u de beveiliging later hervatten. Als u de herstel punten niet behoudt, kunt u de beveiliging niet hervatten.

Als u de beveiliging voor de bestands share wilt hervatten, definieert u de volgende para meters:

* **--container naam** is de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--item-name** is de naam van de bestands share waarvoor u de beveiliging wilt hervatten. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **---beleids naam** is de naam van het back-upbeleid waarvoor u de beveiliging voor de bestands share wilt hervatten.

In het volgende voor beeld wordt de cmdlet [AZ Backup Protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) gebruikt om de beveiliging van de *Azure files* -bestands share te hervatten met behulp van het *schedule1* -back-upbeleid.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

U kunt ook de bovenstaande opdracht uitvoeren met behulp van de beschrijvende naam voor de container en het item door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de bewerking ' Resume Protection '. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Registratie van een opslag account ongedaan maken

Als u uw bestands shares in een bepaald opslag account wilt beveiligen met behulp van een andere Recovery Services-kluis, moet u eerst de [beveiliging stoppen voor alle bestands shares](#stop-protection-on-a-file-share) in dat opslag account. Hef de registratie van het account vervolgens op van de Recovery Services-kluis die momenteel wordt gebruikt voor beveiliging.

U moet een container naam opgeven om de registratie van het opslag account ongedaan te maken. Als u de **naam** of de **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .

In het volgende voor beeld wordt de registratie van de *afsaccount* -opslag account uit *azurefilesvault* ongedaan gemaakt met de cmdlet [AZ backup container unregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

U kunt de bovenstaande cmdlet ook uitvoeren met behulp van de beschrijvende naam voor de container door de volgende aanvullende para meter op te geven:

* **--back-upbeheer-type**: *opslag*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met back-up/herstel voor Azure-bestands shares oplossen](troubleshoot-azure-files.md) voor meer informatie
