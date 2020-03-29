---
title: Bestanden uploaden naar een V3-account van Azure Media Services met REST | Microsoft Documenten
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
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705765"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Bestanden uploaden naar een Media Services v3-account met REST

In Media Services uploadt u uw digitale bestanden naar een blobcontainer die is gekoppeld aan een actief. De [entiteit Asset](https://docs.microsoft.com/rest/api/media/operations/asset) kan video,audio, afbeeldingen, miniatuurverzamelingen, teksttracks en bestanden met gesloten bijschrift (en de metagegevens over deze bestanden) bevatten. Zodra de bestanden zijn geüpload in de container van het asset, wordt uw inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming.

In dit artikel ziet u hoe u een lokaal bestand uploadt met REST.

## <a name="prerequisites"></a>Vereisten

Als u de in dit onderwerp beschreven stappen wilt voltooien, moet u het volgende doen:

- [Asset-concept bekijken](assets-concept.md).
- [Postman configureren voor API-aanroepen van Azure Media Services REST](media-rest-apis-with-postman.md).
    
    Volg de laatste stap in het onderwerp [Azure AD-token opvragen](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Een asset maken

In deze sectie ziet u hoe u een nieuw actief maakt.

1. Selecteer **Assets** -> **Maak of werk een asset bij**.
2. Druk op **Verzenden**.

    ![Een asset maken](./media/upload-files/postman-create-asset.png)

U ziet het **antwoord** met de informatie over nieuw aangemaakt actief.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Een SAS-URL ophalen met lees-schrijfmachtigingen 

In deze sectie ziet u hoe u een SAS-URL krijgt die is gegenereerd voor het gemaakte item. De SAS-URL is gemaakt met lees-schrijfmachtigingen en kan worden gebruikt om digitale bestanden te uploaden naar de Asset-container.

1. Selecteer **assets** -> **Lijst de Asset URL's**.
2. Druk op **Verzenden**.

    ![Bestand uploaden](./media/upload-files/postman-create-sas-locator.png)

U ziet het **antwoord** met de informatie over de URL's van activa. Kopieer de eerste URL en gebruik deze om uw bestand te uploaden.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Een bestand uploaden naar blob-opslag met de URL van het uploaden

Gebruik de Azure Storage API's of SDK's (bijvoorbeeld de [Storage REST API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - REST](stream-files-tutorial-with-rest.md)
