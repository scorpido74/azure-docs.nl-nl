---
title: Media uploaden
titleSuffix: Azure Media Services
description: Meer informatie over het uploaden van media voor streaming of codering.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: a046a3caba9a0909d873356bda8d1fa6cf1f9860
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719254"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Media uploaden voor streaming of codering

In Media Services uploadt u uw digitale bestanden (media) naar een blob-container die is gekoppeld aan een asset. De entiteit [Asset](/rest/api/media/operations/asset) kan video, audio, afbeeldingen, verzamelingen miniaturen, tekstsporen en ondertitelingsbestanden (en de metagegevens over deze bestanden) bevatten. Zodra de bestanden in de container van de asset zijn geüpload, wordt de inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming.

Voordat u aan de slag gaat, moet u echter een paar waarden verzamelen of bedenken.

1. Het lokale bestandspad naar het bestand dat u wilt uploaden
1. De asset-id voor de asset (container)
1. De naam die u wilt gebruiken voor het geüploade bestand, inclusief de extensie
1. De naam van het opslagaccount dat u gebruikt
1. De toegangssleutel voor uw opslagaccount

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Wanneer u [een asset hebt gemaakt met behulp van Postman of een andere REST-methode en de SAS-URL voor de asset hebt opgehaald](how-to-create-asset.md?tabs=rest), gebruikt u de Azure Storage API's of SDK's (bijvoorbeeld de [Storage-REST API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)).

---
<!-- add these to the tabs when available -->
Zie de [Azure Storage-documentatie](https://docs.microsoft.com/azure/storage/blobs/) voor andere methoden om met blobs te werken in [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python) en [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Volgende stappen

> [Overzicht van Media Services](media-services-overview.md)
