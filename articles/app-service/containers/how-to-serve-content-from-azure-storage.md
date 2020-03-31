---
title: Inhoud van Azure Storage naar Linux-containers weergeven
description: Meer informatie over het koppelen van aangepaste netwerkdelen aan uw Linux-container in Azure App Service. Deel bestanden tussen apps, beheer statische inhoud op afstand en krijg lokaal toegang, enz.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297914"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Inhoud van Azure Storage weergeven in App-service op Linux

> [!NOTE]
> Dit artikel is van toepassing op Linux containers. Zie [Azure-bestanden configureren in een Windows Container on App Service](../configure-connect-to-azure-storage.md)als u wilt implementeren in aangepaste Windows-containers. Azure Storage in App Service op Linux is een **preview-functie.** Deze functie **wordt niet ondersteund voor productiescenario's**.
>

In deze handleiding ziet u hoe u Azure Storage koppelt aan App Service op Linux. Voordelen zijn beveiligde inhoud, beheerbaarheid van inhoud, permanente opslag, toegang tot meerdere apps en meerdere overdrachtsmethoden.

## <a name="prerequisites"></a>Vereisten

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 of hoger).
- Een bestaande [App Service op Linux app](https://docs.microsoft.com/azure/app-service/containers/).
- Een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Een [Azure-bestandsshare en -map](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Beperkingen van Azure Storage met App-service

- Azure Storage with App Service is **in preview** voor App Service op Linux en Web App for Containers. Het wordt **niet ondersteund** voor **productiescenario's.**
- Azure Storage with App Service ondersteunt het monteren van **Azure Files-containers** (Lezen / Schrijven) en **Azure Blob-containers** (Alleen lezen)
- Azure Storage with App Service **biedt geen ondersteuning voor** het gebruik van de configuratie Van de Storage **Firewall** vanwege infrastructuurbeperkingen.
- Met Azure Storage with App Service u **maximaal vijf** bevestigingspunten per app opgeven.
- Azure Storage die is gemonteerd op een app, is niet toegankelijk via FTP/FtPs-eindpunten van App Service. Azure [Storage Explorer gebruiken](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage is **niet inbegrepen** bij uw web-app en wordt afzonderlijk gefactureerd. Meer informatie over [azure storage-prijzen](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> App Service-configuraties met Azure Blob Storage worden pas in februari 2020 gelezen. [Meer informatie](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Uw app configureren met Azure Storage

Zodra u uw [Azure Storage-account, bestandsshare en map](#prerequisites)hebt gemaakt, u uw app nu configureren met Azure Storage.

Als u een opslagaccount wilt monteren op een [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) map in uw App Service-app, gebruikt u de opdracht. Opslagtype kan AzureBlob of AzureFiles zijn. AzureFiles wordt in dit voorbeeld gebruikt.


> [!CAUTION]
> De map die is opgegeven als het bevestigingspad in uw web-app moet leeg zijn. Alle inhoud die in deze map is opgeslagen, wordt verwijderd wanneer een externe houder wordt toegevoegd. Als u bestanden migreert voor een bestaande app, maakt u een back-up van uw app en de inhoud ervan voordat u begint.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

U moet dit doen voor alle andere mappen die u wilt worden gekoppeld aan een opslagaccount.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Koppeling naar Azure Storage naar de web-app verifiëren

Zodra een opslagcontainer is gekoppeld aan een web-app, u dit verifiëren door de volgende opdracht uit te voeren:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Azure Storage gebruiken in Docker Compose

Azure Storage kan worden gemonteerd met apps met meerdere containers met behulp van de aangepaste id. Voer de naam van de [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)aangepaste id uit om de aangepaste naam weer te geven.

Breng in uw *docker-compose.yml-bestand* de `volumes` optie in kaart aan `custom-id`. Bijvoorbeeld:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Volgende stappen

- [Web-apps configureren in Azure App Service](../configure-common.md).

