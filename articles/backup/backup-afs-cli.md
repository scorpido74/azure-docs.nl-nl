---
title: Back-ups maken van Azure-bestands shares met Azure CLI
description: Meer informatie over het gebruik van Azure CLI voor het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 10ae6d88e8a40ba4175e664ed338f0a02be7a7ca
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294485"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Back-ups maken van Azure-bestands shares met CLI

De Azure-opdracht regel interface (CLI) biedt een opdracht regel ervaring voor het beheer van Azure-resources. Het is een uitstekend hulp programma voor het maken van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt beschreven hoe u back-ups van Azure-bestands shares maakt met Azure CLI. U kunt deze stappen ook uitvoeren met [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) of in [Azure Portal](backup-afs.md).

Aan het einde van deze zelf studie leert u hoe u de onderstaande bewerkingen kunt uitvoeren met Azure CLI:

* Een Recovery Services-kluis maken
* Back-ups voor Azure-bestands shares inschakelen
* Een back-up op aanvraag activeren voor bestands shares

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, moet u Azure CLI versie 2.0.18 of hoger gebruiken. `run az --version`om de CLI-versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services kluis maken

Een Recovery service-kluis is een entiteit die u een geconsolideerde weer gave en beheer mogelijkheden biedt voor alle back-upitems. Wanneer de back-uptaak voor een beveiligde resource wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Voer de volgende stappen uit om een Recovery Services-kluis te maken:

1. Een kluis wordt in een resource groep geplaatst. Als u geen bestaande resource groep hebt, maakt u een nieuwe met [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . In deze zelf studie maken we de nieuwe resource groep *Azure files* in de regio VS-Oost.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Gebruik de [AZ backup-kluis Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet om de kluis te maken. Geef dezelfde locatie op als de kluis die voor de resource groep is gebruikt.

    In het volgende voor beeld wordt een Recovery Services-kluis gemaakt met de naam *azurefilesvault* in de regio VS-Oost.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Geef het type redundantie op dat moet worden gebruikt voor de kluis opslag. U kunt [lokaal redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) of [geografisch redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)gebruiken.

    In het volgende voor beeld wordt de optie voor opslag redundantie voor *azurefilesvault* ingesteld op **georedundante** gebruik van de back-up [AZ backup kluis backup-Properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Als u wilt controleren of de kluis is gemaakt, kunt u de cmdlet [AZ backup kluis show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) gebruiken om de details van uw kluis op te halen. In het volgende voor beeld worden de details weer gegeven van de *azurefilesvault* die u in de bovenstaande stappen hebt gemaakt.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    De uitvoer is vergelijkbaar met het volgende antwoord:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Back-ups voor Azure-bestands shares inschakelen

In deze sectie wordt ervan uitgegaan dat u al een Azure-bestands share hebt waarvoor u een back-up wilt configureren. Als u er nog geen hebt, maakt u een Azure-bestands share met behulp van de opdracht [AZ Storage share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Als u back-ups voor bestands shares wilt inschakelen, moet u een beveiligings beleid maken dat definieert wanneer een back-uptaak wordt uitgevoerd en hoe lange herstel punten worden opgeslagen. U kunt een back-upbeleid maken met behulp van de cmdlet [AZ backup policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

In het volgende voor beeld wordt de cmdlet [AZ Backup Protection Enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) gebruikt voor het inschakelen van back-ups voor de *Azure files* -bestands share in het *afsaccount* -opslag account met behulp van het back-upbeleid van *schema 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw **back-** upbewerking inschakelen. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Een back-up op aanvraag voor de bestands share activeren

Als u een back-up op aanvraag wilt activeren voor uw bestands share in plaats van te wachten totdat het back-upbeleid de taak op het geplande tijdstip uitvoert, gebruikt u de cmdlet [AZ Backup Protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) .

U moet de volgende para meters definiëren om een back-up op aanvraag te activeren:

* **--container naam** is de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](https://docs.microsoft.com/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--item-name** is de naam van de bestands share waarvoor u een back-up op aanvraag wilt activeren. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--behoud: totdat** de datum wordt opgegeven tot wanneer u het herstel punt wilt behouden. De waarde moet worden ingesteld in UTC-tijd notatie (dd-mm-jjjj).

In het volgende voor beeld wordt een back-up op aanvraag geactiveerd voor de *azuresfiles* -bestands share in het *afsaccount* -opslag account met retentie tot *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw back-upbewerking op aanvraag. Als u de status van een taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van Azure-bestands shares met CLI](restore-afs-cli.md)
* Meer informatie over het [beheren van Azure file share ackups met CLI](manage-afs-backup-cli.md)
