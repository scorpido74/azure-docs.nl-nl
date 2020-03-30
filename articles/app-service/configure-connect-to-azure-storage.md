---
title: Aangepaste opslag toevoegen (Windows-container)
description: Meer informatie over het koppelen van aangepaste netwerkshare in een aangepaste Windows-container in Azure App Service. Deel bestanden tussen apps, beheer statische inhoud op afstand en krijg lokaal toegang, enz.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120681"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Azure-bestanden configureren in een Windows Container op App-service

> [!NOTE]
> Dit artikel is van toepassing op aangepaste Windows-containers. Zie [Inhoud uit Azure Storage weergeven](./containers/how-to-serve-content-from-azure-storage.md)voor implementatie naar App Service op _Linux._
>

In deze handleiding ziet u hoe u toegang krijgt tot Azure Storage in Windows Containers. Alleen [Azure Files Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) en Premium Files [Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) worden ondersteund. U gebruikt Azure Files Shares in deze how-to. Voordelen zijn beveiligde inhoud, overdraagbaarheid van inhoud, toegang tot meerdere apps en meerdere overdrachtsmethoden.

## <a name="prerequisites"></a>Vereisten

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 of hoger).
- [Een bestaande Windows Container-app in Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure-bestandsshare maken](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Bestanden uploaden naar Azure File-share](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files wordt niet-standaard opgeslagen en afzonderlijk gefactureerd, niet inbegrepen bij de web-app. Het ondersteunt geen firewall-configuratie vanwege beperkingen in de infrastructuur.
>

## <a name="limitations"></a>Beperkingen

- Azure Storage in Windows-containers is **in preview** en wordt **niet ondersteund** voor **productiescenario's.**
- Azure Storage in Windows-containers ondersteunt alleen het monteren van **Azure Files-containers** (Lezen / schrijven).
- Azure Storage in Windows-containers wordt momenteel **niet ondersteund** voor het toevoegen van uw eigen codescenario's in Windows App Service-abonnementen.
- Azure Storage in **Windows-containers biedt geen ondersteuning voor** het gebruik van de configuratie Van de **Opslagfirewall** vanwege infrastructuurbeperkingen.
- Met Azure Storage in Windows-containers u **maximaal vijf** bevestigingspunten per app opgeven.
- Azure Storage die is gemonteerd op een app, is niet toegankelijk via FTP/FtPs-eindpunten van App Service. Azure [Storage Explorer gebruiken](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage wordt onafhankelijk van elkaar gefactureerd en **niet opgenomen** in uw web-app. Meer informatie over [azure storage-prijzen](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Opslag koppelen aan uw web-app (voorbeeld)

 Als u een Azure Files Share wilt monteren op [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) een map in uw App Service-app, gebruikt u de opdracht. Opslagtype moet AzureFiles zijn.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

U moet dit doen voor andere mappen die u wilt koppelen aan een Azure-bestandenaandeel.

## <a name="verify"></a>Verifiëren

Zodra een Azure Files-share is gekoppeld aan een web-app, u dit verifiëren door de volgende opdracht uit te voeren:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Volgende stappen

- [Migreer een ASP.NET-app naar Azure App Service met een Windows-container (Preview).](app-service-web-tutorial-windows-containers-custom-fonts.md)
