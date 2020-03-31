---
title: Back-ups maken van Azure-bestandsshares met Azure CLI
description: Meer informatie over het gebruik van Azure CLI om back-ups te maken van Azure-bestandsshares in de Vault voor Herstelservices
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844038"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Back-ups maken van Azure-bestandsshares met CLI

De Azure command-line interface (CLI) biedt een command-line ervaring voor het beheren van Azure-resources. Het is een geweldig hulpmiddel voor het bouwen van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt beschreven hoe u een back-up maakt van Azure-bestandsshares met Azure CLI. U kunt deze stappen ook uitvoeren met [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) of in [Azure Portal](backup-afs.md).

Aan het einde van deze zelfstudie leert u hoe u onderstaande bewerkingen uitvoeren met Azure CLI:

* Een Recovery Services-kluis maken
* Back-up voor Azure-bestandsshares inschakelen
* Een on-demand back-up activeren voor bestandsshares

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI versie 2.0.18 of hoger gebruiken. Om de CLI-versie te vinden, `run az --version`. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Een Vault voor Herstelservices maken

Een kluis van een herstelservice is een entiteit die u een geconsolideerde weergave- en beheermogelijkheid biedt voor alle back-upitems. Wanneer de back-uptaak voor een beveiligde resource wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Volg de volgende stappen om een kluis met herstelservices te maken:

1. Een kluis wordt in een resourcegroep geplaatst. Als u geen bestaande resourcegroep hebt, maakt u een nieuwe groep met [de AZ-groep](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . In deze zelfstudie maken we de nieuwe *azurefiles* voor resourcesgroepen in de regio Oost-VS.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Gebruik de [az back-up kluis maken](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet om de kluis te maken. Geef dezelfde locatie op voor de kluis als voor de resourcegroep.

    In het volgende voorbeeld wordt een kluis met de naam azurefilesvault in de regio Oost-VS met de naam *azurefilesvault* gesneubezeid.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Geef het type redundantie op dat moet worden gebruikt voor de kluisopslag. U [lokaal redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) of [georedundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)gebruiken.

    In het volgende voorbeeld wordt de opslagredundantieoptie voor *azurefilesvault* ingesteld op **Georedundant** met behulp van de setcmdlet [voor back-up-eigenschappen van AZ Backup](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) Vault.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Om te controleren of de kluis succesvol is gemaakt, u de cmdlet van de [AZ-back-upkluis gebruiken](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) om details van uw kluis te krijgen. In het volgende voorbeeld worden de details weergegeven van de *azurefilesvault* die we in de bovenstaande stappen hebben gemaakt.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    De uitvoer zal vergelijkbaar zijn met het volgende antwoord:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Back-up voor Azure-bestandsshares inschakelen

In deze sectie wordt ervan uitgegaan dat u al een Azure-bestandsshare hebt waarvoor u een back-up wilt configureren. Als u er geen hebt, maakt u een Azure-bestandsshare met de opdracht [voor het maken van az-opslagdelen.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Als u back-ups voor bestandsshares wilt inschakelen, moet u een beveiligingsbeleid maken dat bepaalt wanneer een back-uptaak wordt uitgevoerd en hoe lang herstelpunten worden opgeslagen. U een back-upbeleid maken met behulp van het [az-back-upbeleid om](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet te maken.

In het volgende voorbeeld wordt de cmdlet voor [az-back-upbeveiliging](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) ingeschakeld om back-ups in te schakelen voor de *azurefiles-bestandsshare* in het account voor opslag van *afsaccount* met behulp van het back-upbeleid *schema 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor de **ingeschakelde back-upbewerking.** Als u de status van de taak wilt bijhouden, gebruikt u de cmdlet van de [AZ-back-upfunctie.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Een on-demand back-up activeren voor bestandsshare

Als u een on-demand back-up voor uw bestandsshare wilt activeren in plaats van te wachten tot het back-upbeleid de taak op de geplande tijd uitvoert, gebruikt u de cmdlet voor [back-upvan az-back-upbeveiliging.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

U moet de volgende parameters definiëren om een on-demand back-up te activeren:

* **--containernaam** is de naam van het opslagaccount dat het bestandsaandeel host. Als u de **naam** of **de vriendelijke naam** van uw container wilt ophalen, gebruikt u de opdracht [az-back-upcontainerlijst.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name** is de naam van het bestandsshare waarvoor u een on-demand back-up wilt activeren. Als u de **naam** of **de vriendelijke naam** van uw back-upitem wilt ophalen, gebruikt u de opdracht lijst met [AZ-back-upitems.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--behouden-totdat** de datum is opgegeven totdat u het herstelpunt wilt behouden. De waarde moet worden ingesteld in utc-tijdformaat (dd-mm-yyyy).

In het volgende voorbeeld wordt een on-demand back-up geactiveerd voor de *fileshare azuresfiles* in het account van de opslag van *afsaccount* met retentie tot *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Het kenmerk **Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw back-upop aanvraag. Als u de status van een taak wilt bijhouden, gebruikt u de cmdlet van de [AZ-back-upfunctie.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van Azure-bestandsshares met CLI](restore-afs-cli.md)
* Meer informatie over het [beheren van ACKUPS voor Azure-bestanden met CLI](manage-afs-backup-cli.md)
