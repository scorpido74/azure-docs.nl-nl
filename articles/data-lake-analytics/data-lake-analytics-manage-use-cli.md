---
title: Azure Data Lake Analytics beheren met Azure CLI
description: In dit artikel wordt beschreven hoe u de Azure CLI gebruiken voor het beheren van Data Lake Analytics-taken, gegevensbronnen, & gebruikers.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 69a48952ef273acb8cf7eb0ec5968e12b962b622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454360"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Azure Data Lake Analytics beheren met behulp van de Azure Command-line Interface (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Meer informatie over het beheren van Azure Data Lake Analytics-accounts, gegevensbronnen, gebruikers en taken met behulp van de Azure CLI. Als u beheeronderwerpen wilt bekijken met andere hulpmiddelen, klikt u op het tabblad hierboven selecteren.


**Vereisten**

Voordat u met deze zelfstudie begint, moet u de volgende bronnen hebben:

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Zie [Azure CLI installeren en configureren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Download en installeer de **pre-release** [Azure CLI-hulpprogramma’s](https://github.com/MicrosoftBigData/AzureDataLake/releases) om deze demo te voltooien.

* Verifieer met `az login` de opdracht en selecteer het abonnement dat u wilt gebruiken. Zie [Verbinding maken met een Azure-abonnement met Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over verificatie met een werk- of schoolaccount.

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Je hebt nu toegang tot de opdrachten Data Lake Analytics en Data Lake Store. Voer de volgende opdracht uit om de opdrachten Data Lake Store en Data Lake Analytics weer te geven:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Accounts beheren

Voordat u een Data Lake Analytics-vacatures uitvoert, moet u een Data Lake Analytics-account hebben. In tegenstelling tot Azure HDInsight betaalt u niet voor een Analytics-account wanneer er geen taak wordt uitgevoerd. U betaalt alleen voor de tijd dat het een taak uitvoert.  Zie [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)voor meer informatie.  

### <a name="create-accounts"></a>Accounts maken

Voer de volgende opdracht uit om een Data Lake-account te maken, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Accounts bijwerken

Met de volgende opdracht worden de eigenschappen van een bestaand Data Lake Analytics-account bijgewerkt

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Lijst van accounts

Gegevens Lake Analytics-accounts aanbieden binnen een specifieke resourcegroep

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Meer informatie over een account

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Een account verwijderen

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gegevensbronnen beheren

Data Lake Analytics ondersteunt momenteel de volgende twee gegevensbronnen:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Wanneer u een Analytics-account maakt, moet u een Azure Data Lake Storage-account aanwijzen als het standaardopslagaccount. Het standaard gegevensmeeropslagaccount wordt gebruikt om metagegevens en taakcontrolelogboeken op te slaan. Nadat u een Analytics-account hebt gemaakt, u extra Data Lake Storage-accounts en/of Azure Storage-account toevoegen. 

### <a name="find-the-default-data-lake-store-account"></a>Het standaard GegevensLake Store-account zoeken

U het standaard Data Lake Store-account weergeven dat wordt gebruikt door de `az dla account show` opdracht uit te voeren. Standaardaccountnaam wordt vermeld onder de eigenschap defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Extra Blob-opslagaccounts toevoegen

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Alleen klodderopslagkorte namen worden ondersteund. Gebruik geen FQDN, bijvoorbeeld "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Extra Data Lake Store-accounts toevoegen

Met de volgende opdracht wordt het opgegeven Data Lake Analytics-account bijgewerkt met een extra Data Lake Store-account:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Bestaande gegevensbron bijwerken

Ga als u een bestaande Blob-opslagaccountsleutel bij:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Gegevensbronnen weergeven:

Ga als volgt te werk om de Data Lake Store-accounts weer te geven:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Ga als een overzicht van het Blob-opslagaccount:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Gegevens lake Analytics lijst gegevensbron](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Gegevensbronnen verwijderen:
Een Data Lake Store-account verwijderen:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Ga als lid van de klodderopslagaccount:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Taken beheren
Je moet een Data Lake Analytics-account hebben voordat je een taak maken.  Zie [Data Lake Analytics-accounts beheren](#manage-accounts)voor meer informatie .

### <a name="list-jobs"></a>Taken weergeven

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Gegevens lake Analytics lijst gegevensbron](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Werkgegevens opvragen

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Vacatures indienen

> [!NOTE]
> De standaardprioriteit van een taak is 1000 en de standaardmate van parallellisme voor een taak is 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Taken annuleren
Gebruik de lijstopdracht om de taak-id te vinden en gebruik annuleren om de taak te annuleren.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pijplijnen en herhalingen

**Meer informatie over pijplijnen en herhalingen**

Gebruik de `az dla job pipeline`-opdrachten om de pijplijngegevens te zien voor eerder verzonden taken.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Gebruik de `az dla job recurrence`-opdrachten om de herhalingsgegevens te zien voor eerder verzonden taken.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Zie ook
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics beheren met Azure-portal](data-lake-analytics-manage-use-portal.md)
* [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

