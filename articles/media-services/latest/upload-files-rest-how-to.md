---
title: Bestanden uploaden naar een Azure Media Services v3-account met behulp van REST | Microsoft Docs
description: Meer informatie over het verkrijgen van media-inhoud in Media Services door het maken en uploaden van assets.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 57839c82af71f7cedfe1b3c77095210d273be4a1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091841"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Bestanden uploaden naar een Media Services v3-account met behulp van REST

In Media Services uploadt u uw digitale bestanden naar een BLOB-container die is gekoppeld aan een Asset. De [Asset](/rest/api/media/operations/asset) -entiteit kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten. Zodra de bestanden in de container van het activum zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming.

In dit artikel wordt beschreven hoe u een lokaal bestand kunt uploaden met behulp van REST.

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit onderwerp worden beschreven, moet u het volgende doen:

- Bekijk het [concept over activa](assets-concept.md).
- [Postman configureren voor Azure Media Services rest API-aanroepen](media-rest-apis-with-postman.md).
    
    Zorg ervoor dat u de laatste stap in het onderwerp [Azure AD-Token ophalen](media-rest-apis-with-postman.md#get-azure-ad-token)volgt. 

## <a name="create-an-asset"></a>Een asset maken

In deze sectie wordt beschreven hoe u een nieuwe Asset maakt.

1. **Activa**selecteren om  ->  **activa te maken of bij te werken**.
2. Druk op **Verzenden**.

    ![Een asset maken](./media/upload-files/postman-create-asset.png)

U ziet het **antwoord** met de informatie over nieuw gemaakte activa.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Een SAS-URL ophalen met de machtigingen lezen/schrijven 

In deze sectie wordt uitgelegd hoe u een SAS-URL kunt ophalen die is gegenereerd voor de gemaakte Asset. De SAS-URL is gemaakt met lees-en schrijf machtigingen en kan worden gebruikt voor het uploaden van digitale bestanden in de Asset-container.

1. **Activa**selecteren  ->  **lijst van de Asset-url's**.
2. Druk op **Verzenden**.

    ![Bestand uploaden](./media/upload-files/postman-create-sas-locator.png)

U ziet het **antwoord** met de informatie over de url's van de Asset. Kopieer de eerste URL en gebruik deze om uw bestand te uploaden.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Een bestand uploaden naar Blob Storage met behulp van de upload-URL

Gebruik de Azure Storage Api's of Sdk's (bijvoorbeeld de [opslag rest API](../../storage/common/storage-rest-api-auth.md) of de [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Extern bestand coderen op basis van URL en video streamen - REST](stream-files-tutorial-with-rest.md)
