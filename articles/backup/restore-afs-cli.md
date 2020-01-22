---
title: Azure-bestands shares herstellen met Azure CLI
description: Meer informatie over het gebruik van Azure CLI voor het herstellen van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294407"
---
# <a name="restore-azure-file-shares-with-cli"></a>Azure-bestands shares herstellen met CLI

De Azure-opdracht regel interface (CLI) biedt een opdracht regel ervaring voor het beheer van Azure-resources. Het is een uitstekend hulp programma voor het maken van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt uitgelegd hoe u een volledige bestands share herstelt of specifieke bestanden van een herstel punt dat door de [Azure backup](https://docs.microsoft.com/azure/backup/backup-overview) -service is gemaakt met behulp van Azure cli. U kunt deze stappen ook uitvoeren met [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) of in [Azure Portal](backup-afs.md).

Aan het einde van deze zelf studie leert u hoe u de onderstaande bewerkingen kunt uitvoeren met Azure CLI:

* Herstel punten weer geven voor een back-up van een Azure-bestands share
* Een volledige Azure-bestands share herstellen
* Afzonderlijke bestanden of mappen herstellen

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI versie 2.0.18 of hoger gebruiken. `run az --version`om de CLI-versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een Azure-bestands share hebt waarvan een back-up wordt gemaakt door de Azure Backup-service. Als u er nog geen hebt, raadpleegt u [back-ups van Azure-bestands shares met CLI](backup-afs-cli.md) voor het configureren van back-ups voor uw bestands share. Voor dit artikel worden de volgende resources gebruikt:

| Bestandsshare  | Opslagaccount | Regio | Details                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *Azure files*  | *afsaccount*      | US - oost | De oorspronkelijke bron heeft een back-up gemaakt met Azure Backup                 |
| *azurefiles1* | *afaccount1*      | US - oost | Doel bron die wordt gebruikt voor herstel naar een alternatieve locatie |

U kunt een vergelijk bare structuur voor uw bestands shares gebruiken om de verschillende typen herstel bewerkingen uit te proberen die in dit document worden uitgelegd.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Herstel punten voor de Azure-bestands share ophalen

Gebruik de cmdlet [AZ backup Recovery Point List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) om alle herstel punten voor de back-up van de bestands share weer te geven.

In het volgende voor beeld wordt de lijst met herstel punten voor *Azure files* -bestands share in het *afsaccount* -opslag account opgehaald.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

U kunt de bovenstaande cmdlet ook uitvoeren met behulp van de ' beschrijvende naam ' voor container en item door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

De resultatenset wordt een lijst met herstel punten met tijd en consistentie Details voor elk herstel punt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van het herstel punt dat kan worden gebruikt als waarde voor de para meter **--RP-name** in herstel bewerkingen.

## <a name="full-share-recovery-using-azure-cli"></a>Herstel van volledige shares met behulp van Azure CLI

U kunt deze terugzet optie gebruiken om de volledige bestands share te herstellen op de oorspronkelijke of een alternatieve locatie.

Definieer de volgende para meters voor het uitvoeren van herstel bewerkingen:

* **--container naam** is de naam van het opslag account dat als host fungeert voor de back-up van de oorspronkelijke bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--item-name** is de naam van de back-up van de oorspronkelijke bestands share die u wilt gebruiken voor de terugzet bewerking. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-full-share-to-the-original-location"></a>Volledige share naar de oorspronkelijke locatie herstellen

Wanneer u naar een oorspronkelijke locatie herstelt, hoeft u geen doel-gerelateerde para meters op te geven. Alleen **conflict oplossen** moet worden gegeven.

In het volgende voor beeld wordt met de herstel modus [AZ Backup Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) cmdlet ingesteld op *originallocation* om de *Azure files* -bestands share op de oorspronkelijke locatie te herstellen, met behulp van het Recovery Point 932883129628959823, dat we hebben verkregen bij het [ophalen van herstel punten voor de Azure-bestands share](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de bewerking ' herstellen '. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-full-share-to-an-alternate-location"></a>Volledige share naar een alternatieve locatie herstellen

U kunt deze optie gebruiken om een bestands share terug te zetten naar een andere locatie en de oorspronkelijke bestands share te bedenken. Geef de volgende para meters op voor herstel naar een alternatieve locatie:

* **--doel-Storage-account**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **--doel bestands share**: de bestands share binnen het doel-opslag account waarnaar de inhoud van de back-up wordt teruggezet.
* **--doelmap**: de map onder de bestands share waarnaar gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **--oplossen-conflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

In het volgende voor beeld wordt [AZ back Restore Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) with Restore Mode als *alternatelocation* om de *Azure files* -bestands share in het *afsaccount* -opslag account te herstellen naar de *azurefiles1* -bestands share in het *afaccount1* -opslag account.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de bewerking ' herstellen '. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Herstel op item niveau

U kunt deze terugzet optie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke of een alternatieve locatie te herstellen.

Definieer de volgende para meters voor het uitvoeren van herstel bewerkingen:

* **--container naam** is de naam van het opslag account dat als host fungeert voor de back-up van de oorspronkelijke bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--item-name** is de naam van de back-up van de oorspronkelijke bestands share die u wilt gebruiken voor de herstel bewerking. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Geef de volgende para meters op voor de items die u wilt herstellen:

* **SourceFilePath**: het absolute pad van het bestand dat in de bestands share moet worden hersteld, als een teken reeks. Dit pad is hetzelfde pad dat wordt gebruikt in het bestand [AZ Storage File down load](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) of [AZ opslag file show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) cli-opdrachten.
* **SourceFileType**: Kies of een map of een bestand is geselecteerd. De **map** of het **bestand**wordt geaccepteerd.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Afzonderlijke bestanden/mappen herstellen naar de oorspronkelijke locatie

Gebruik de herstellen [AZ Backup Restore-Azure files](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet with Restore Mode set to *originallocation* om specifieke bestanden/mappen op de oorspronkelijke locatie te herstellen.

In het volgende voor beeld wordt het bestand *' RestoreTest. txt '* op de oorspronkelijke locatie teruggezet: de *Azure files* file share.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de bewerking ' herstellen '. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Afzonderlijke bestanden/mappen naar een andere locatie herstellen

Als u specifieke bestanden/mappen naar een andere locatie wilt herstellen, gebruikt u de herstel modus [AZ Backup Restore-Azure files](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) waarvoor de terugzet methode is ingesteld op *alternatelocation* en geeft u de volgende streef parameters op:

* **--doel-Storage-account**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **--doel bestands share**: de bestands share binnen het doel-opslag account waarnaar de inhoud van de back-up wordt teruggezet.
* **--doelmap**: de map onder de bestands share waarnaar gegevens worden teruggezet. Als de gegevens waarvan een back-up is gemaakt, moeten worden hersteld naar een hoofdmap, geeft u de waarde van de doelmap op als een lege teken reeks.

In het volgende voor beeld wordt het *RestoreTest. txt* -bestand dat oorspronkelijk in de *Azure files* -bestands share voor komt, teruggezet naar een alternatieve locatie: de map *restoredata* in de *azurefiles1* -file share die wordt gehost in het *afaccount1* -opslag account.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de bewerking ' herstellen '. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van back-ups van Azure-bestands shares met Azure cli](manage-afs-backup-cli.md)
