---
title: Inhoud van Azure Storage naar Linux-containers verzenden
description: Meer informatie over het koppelen van een aangepaste netwerk share aan uw Linux-container in Azure App Service. Bestanden delen tussen apps, statische inhoud extern beheren en lokaal toegang krijgen, enzovoort.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297914"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Inhoud van Azure Storage in App Service in Linux verzenden

> [!NOTE]
> Dit artikel is van toepassing op Linux-containers. Zie [Azure files configureren in een Windows-container op app service](../configure-connect-to-azure-storage.md)voor meer informatie over het implementeren van aangepaste Windows-containers. Azure Storage in App Service op Linux is een **Preview** -functie. Deze functie wordt **niet ondersteund voor productie scenario's**.
>

In deze hand leiding wordt uitgelegd hoe u Azure Storage kunt koppelen aan App Service op Linux. Voor delen zijn onder andere beveiligde inhoud, draag baarheid van inhoud, permanente opslag, toegang tot meerdere apps en meerdere overdrachts methoden.

## <a name="prerequisites"></a>Vereisten

- [Azure cli](/cli/azure/install-azure-cli) (2.0.46 of hoger).
- Een bestaande [app service in de Linux-app](https://docs.microsoft.com/azure/app-service/containers/).
- Een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Een [Azure-bestands share en-map](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Beperkingen van Azure Storage met App Service

- Azure Storage met App Service is **in Preview** voor app service in Linux en Web App for containers. Het wordt **niet ondersteund** voor **productie scenario's**.
- Azure Storage met App Service ondersteunt koppelen **Azure files containers** (lezen/schrijven) en **Azure Blob-containers** (alleen-lezen)
- Azure Storage met App Service biedt **geen ondersteuning** voor het gebruik van de **opslag firewall** -configuratie vanwege beperkingen van de infra structuur.
- Met Azure Storage met App Service kunt u **Maxi maal vijf** koppel punten per app opgeven.
- Azure Storage die aan een app zijn gekoppeld, is niet toegankelijk via App Service FTP-en FTPs-eind punten. Gebruik [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage maakt **geen deel uit** van uw web-app en wordt afzonderlijk gefactureerd. Meer informatie over [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> App Service configuraties met Azure Blob Storage worden alleen in februari 2020 gelezen. [Meer informatie](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Uw app configureren met Azure Storage

Nadat u uw [Azure Storage-account, de bestands share en de map](#prerequisites)hebt gemaakt, kunt u uw app nu configureren met Azure Storage.

Als u een opslag account wilt koppelen aan een map in uw App Service-app, [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) gebruikt u de opdracht. Het opslag type kan AzureBlob of Azure files zijn. Azure files wordt in dit voor beeld gebruikt.


> [!CAUTION]
> De map die is opgegeven als koppel pad in uw web-app moet leeg zijn. Alle inhoud die in deze map is opgeslagen, wordt verwijderd wanneer een externe koppeling wordt toegevoegd. Als u bestanden migreert voor een bestaande app, maakt u een back-up van uw app en de bijbehorende inhoud voordat u begint.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

U moet dit doen voor alle andere directory's die u aan een opslag account wilt koppelen.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Azure Storage koppeling naar de web-app controleren

Zodra een opslag container is gekoppeld aan een web-app, kunt u dit controleren door de volgende opdracht uit te voeren:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Azure Storage gebruiken in docker-samen stellen

Azure Storage kunnen worden gekoppeld met apps met meerdere containers met behulp van de aangepaste ID. Voer uit [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)om de naam van de aangepaste ID weer te geven.

Wijs in uw *docker-Compose. yml* -bestand de `volumes` optie toe `custom-id`aan. Bijvoorbeeld:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Volgende stappen

- [Web-apps configureren in azure app service](../configure-common.md).

