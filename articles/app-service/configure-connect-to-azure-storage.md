---
title: Aangepaste opslag toevoegen (Windows-container)
description: Meer informatie over het koppelen van een aangepaste netwerk share in een aangepaste Windows-container in Azure App Service. Bestanden delen tussen apps, statische inhoud extern beheren en lokaal toegang krijgen, enzovoort.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: ad70bbe36369c03225079d1194043e6ceb109c6f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671011"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Azure Files configureren in een Windows-container op App Service

> [!NOTE]
> Dit artikel is van toepassing op aangepaste Windows-containers. Zie [inhoud van Azure Storage bezorgen](./containers/how-to-serve-content-from-azure-storage.md)om te implementeren in app service op _Linux_.
>

Deze hand leiding laat zien hoe u toegang krijgt tot Azure Storage in Windows-containers. Alleen [Azure files shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) en [Premium-bestands shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) worden ondersteund. U gebruikt Azure Files-shares in deze procedure. Voor delen zijn onder andere beveiligde inhoud, portabiliteit van inhoud, toegang tot meerdere apps en meerdere overdrachts methoden.

## <a name="prerequisites"></a>Vereisten

- [Azure cli](/cli/azure/install-azure-cli) (2.0.46 of hoger).
- [Een bestaande Windows-container-app in Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Een Azure-bestands share maken](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Bestanden uploaden naar Azure-bestands share](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files is niet-standaard opslag en wordt afzonderlijk gefactureerd, niet opgenomen in de web-app. Het gebruik van de firewall configuratie wordt niet ondersteund vanwege beperkingen van de infra structuur.
>

## <a name="link-storage-to-your-web-app-preview"></a>Opslag koppelen aan uw web-app (preview)

 Als u een Azure Files share wilt koppelen aan een map in uw App Service-app, gebruikt u de [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) opdracht. Het opslag type moet Azure files zijn.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

U moet dit doen voor andere directory's die u wilt koppelen aan een Azure Files share.

## <a name="verify"></a>Verifiëren

Zodra een Azure Files share is gekoppeld aan een web-app, kunt u dit controleren door de volgende opdracht uit te voeren:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Volgende stappen

- [Een ASP.net-app migreren naar Azure app service met behulp van een Windows-container (preview)](app-service-web-tutorial-windows-containers-custom-fonts.md).
