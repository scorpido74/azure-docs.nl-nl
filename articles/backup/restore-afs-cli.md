---
title: Azure-bestandsshares herstellen met de Azure CLI
description: Meer informatie over het gebruik van de Azure CLI om back-upazure-bestandsshares te herstellen in de kluis Recovery Services
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931038"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure-bestandsshares herstellen met de Azure CLI

De Azure CLI biedt een opdrachtregelervaring voor het beheer van Azure-resources. Het is een geweldig hulpmiddel voor het bouwen van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt uitgelegd hoe u een volledige bestandsshare of specifieke bestanden herstellen vanaf een herstelpunt dat is gemaakt door [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) met behulp van de Azure CLI. U kunt deze stappen ook uitvoeren met [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) of in [Azure Portal](backup-afs.md).

Aan het einde van dit artikel leert u hoe u de volgende bewerkingen uitvoert met de Azure CLI:

* Herstelpunten weergeven voor een back-up azure-bestandsshare.
* Een volledig Azure-bestandsshare herstellen.
* Afzonderlijke bestanden of mappen herstellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI versie 2.0.18 of hoger gebruiken. Voer `az --version` uit om de CLI-versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een Azure-bestandsshare hebt waarvan een back-up wordt gemaakt door Azure Backup. Zie [Back-up van Azure-bestandsshares met de CLI](backup-afs-cli.md) om back-ups voor uw bestandsshare te configureren als u er geen hebt. Voor dit artikel gebruikt u de volgende bronnen:

| Bestandsshare  | Storage-account | Regio | Details                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurebestanden*  | *afsaccount*      | EastUS EastUS | Er wordt een back-up gemaakt van de oorspronkelijke bron met Azure Backup                 |
| *azurefiles1* | *afaccount1*      | EastUS EastUS | Doelbron die wordt gebruikt voor alternatieve locatieherstel |

U een vergelijkbare structuur voor uw bestandsshares gebruiken om de verschillende typen herstelberichten uit te proberen die in dit artikel worden uitgelegd.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Herstelpunten ophalen voor de Azure-bestandsshare

Gebruik de cmdlet van de [AZ-back-upherstelpunt](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) om alle herstelpunten voor de back-up fileshare weer te geven.

In het volgende voorbeeld wordt de lijst met herstelpunten opgehaald voor het *azurefiles-bestandsaandeel* in het account voor opslag van *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

U de vorige cmdlet ook uitvoeren met de vriendelijke naam voor de container en het item door de volgende twee extra parameters op te geven:

* **--back-up-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

De resultatenset is een lijst met herstelpunten met tijd- en consistentiedetails voor elk herstelpunt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van het herstelpunt dat kan worden gebruikt als waarde voor de parameter **--rp-name** in herstelbewerkingen.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Herstel van volledige delen met behulp van de Azure CLI

U deze hersteloptie gebruiken om de volledige bestandsshare in het origineel of een alternatieve locatie te herstellen.

Definieer de volgende parameters om herstelbewerkingen uit te voeren:

* **--containernaam:** de naam van het opslagaccount dat de oorspronkelijke bestandsshare met back-up host. Als u de naam of de vriendelijke naam van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-naam:** de naam van het oorspronkelijke bestandsaandeel met een back-up dat u wilt gebruiken voor de herstelbewerking. Als u de naam of de vriendelijke naam van uw back-upitem wilt ophalen, gebruikt u de [opdracht lijst met AZ-back-upitems.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Een volledig deel herstellen naar de oorspronkelijke locatie

Wanneer u herstelt naar een oorspronkelijke locatie, hoeft u geen doelgerelateerde parameters op te geven. Alleen **conflict oplossen** moet worden verstrekt.

In het volgende voorbeeld wordt de cmdlet [az-back-backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) gebruikt, waarbij de herstelmodus is ingesteld op *de oorspronkelijke locatie* om het bestandsaandeel *azurefiles* op de oorspronkelijke locatie te herstellen. U gebruikt het herstelpunt 932883129628959823, dat u hebt verkregen in [herstelpunten ophalen voor het Azure-bestandsaandeel:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Een volledig aandeel naar een alternatieve locatie herstellen

U deze optie gebruiken om een bestandsshare te herstellen naar een alternatieve locatie en de oorspronkelijke bestandsshare te behouden zoals deze is. Geef de volgende parameters op voor alternatieve locatieherstel:

* **--target-storage-account:** het opslagaccount waarop de back-upinhoud wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **--target-file-share:** De bestandsshare binnen het doelopslagaccount waarop de back-upinhoud wordt hersteld.
* **--target-map:** de map onder de bestandsshare waaraan gegevens worden hersteld. Als de back-upinhoud moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap als een lege tekenreeks.
* **--resolve-conflict:** Instructie als er een conflict is met de herstelde gegevens. Accepteert **Overschrijven** of **Overslaan**.

In het volgende voorbeeld wordt [az backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) met herstelmodus gebruikt als *alternatieve locatie* om het *azurefiles-bestandsaandeel* in het account *van afsaccount* opslag te herstellen naar het *azurefiles1"-bestandsshare* in het *account1-opslagaccount.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

## <a name="item-level-recovery"></a>Herstel op itemniveau

U deze hersteloptie gebruiken om afzonderlijke bestanden of mappen in het origineel of een alternatieve locatie te herstellen.

Definieer de volgende parameters om herstelbewerkingen uit te voeren:

* **--containernaam:** de naam van het opslagaccount dat de oorspronkelijke bestandsshare met back-up host. Als u de naam of de vriendelijke naam van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-naam:** de naam van het oorspronkelijke bestandsaandeel met een back-up dat u wilt gebruiken voor de herstelbewerking. Als u de naam of de vriendelijke naam van uw back-upitem wilt ophalen, gebruikt u de [opdracht lijst met AZ-back-upitems.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Geef de volgende parameters op voor de items die u wilt herstellen:

* **SourceFilePath:** het absolute pad van het bestand, dat moet worden hersteld binnen de bestandsshare, als een tekenreeks. Dit pad is hetzelfde pad dat wordt gebruikt in de [az-opslagbestandsdownload-](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) of [AZ-opslagbestand met](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI-opdrachten.
* **SourceFileType:** kies of een map of een bestand is geselecteerd. Accepteert **directory** of **bestand**.
* **ResolveConflict:** Instructie als er een conflict is met de herstelde gegevens. Accepteert **Overschrijven** of **Overslaan**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Afzonderlijke bestanden of mappen naar de oorspronkelijke locatie herstellen

Gebruik de cmdlet [az-back-uprestore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) met herstelmodus ingesteld op *oorspronkelijke locatie* om specifieke bestanden of mappen naar hun oorspronkelijke locatie te herstellen.

In het volgende voorbeeld wordt het bestand *RestoreTest.txt* op de oorspronkelijke locatie hersteld: de bestandsshare *van AzureFiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Afzonderlijke bestanden of mappen naar een alternatieve locatie herstellen

Als u specifieke bestanden of mappen wilt herstellen naar een alternatieve locatie, gebruikt u de cmdlet [az-back-upherstel herstel-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) met herstelmodus ingesteld op *alternatieve locatie* en geeft u de volgende doelgerelateerde parameters op:

* **--target-storage-account:** het opslagaccount waarop de back-upinhoud wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **--target-file-share:** De bestandsshare binnen het doelopslagaccount waarop de back-upinhoud wordt hersteld.
* **--target-map:** de map onder de bestandsshare waaraan gegevens worden hersteld. Als de back-upinhoud moet worden hersteld naar een hoofdmap, geeft u de waarde van de doelmap als een lege tekenreeks.

In het volgende voorbeeld wordt het bestand *RestoreTest.txt* dat oorspronkelijk in het *azurefiles-bestand* is weergegeven, hersteld naar een alternatieve locatie: de map *restoredata* in het *azurefiles1-bestandsshare* dat wordt gehost in het *opslagaccount 1.File*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de [az-back-upfunctie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet om de status van de taak bij te houden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van back-ups van Azure-bestanden met Azure CLI](manage-afs-backup-cli.md).
