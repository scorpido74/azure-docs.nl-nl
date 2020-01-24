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
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705765"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Bestanden uploaden naar een Media Services v3-account met behulp van REST

In Media Services uploadt u uw digitale bestanden naar een BLOB-container die is gekoppeld aan een Asset. De [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) -entiteit kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten. Zodra de bestanden in de container van het activum zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming.

In dit artikel wordt beschreven hoe u een lokaal bestand kunt uploaden met behulp van REST.

## <a name="prerequisites"></a>Vereisten

Als u wilt de in dit onderwerp beschreven stappen hebt voltooid, hebt u naar:

- Bekijk het [concept over activa](assets-concept.md).
- [Postman configureren voor Azure Media Services REST API-aanroepen](media-rest-apis-with-postman.md).
    
    Zorg ervoor dat u de laatste stap in het onderwerp [Azure AD-Token ophalen](media-rest-apis-with-postman.md#get-azure-ad-token)volgt. 

## <a name="create-an-asset"></a>Een Asset maken

In deze sectie wordt beschreven hoe u een nieuwe Asset maakt.

1. Selecteer **activa** -> **een Asset te maken of bij te werken**.
2. Druk op **Verzenden**.

    ![Een Asset maken](./media/upload-files/postman-create-asset.png)

U ziet het **antwoord** met de informatie over nieuw gemaakte activa.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Een SAS-URL ophalen met de machtigingen lezen/schrijven 

In deze sectie wordt uitgelegd hoe u een SAS-URL kunt ophalen die is gegenereerd voor de gemaakte Asset. De SAS-URL is gemaakt met lees-en schrijf machtigingen en kan worden gebruikt voor het uploaden van digitale bestanden in de Asset-container.

1. Selecteer **activa** -> **de Asset-Url's weer te geven**.
2. Druk op **Verzenden**.

    ![Bestand uploaden](./media/upload-files/postman-create-sas-locator.png)

U ziet het **antwoord** met de informatie over de url's van de Asset. Kopieer de eerste URL en gebruik deze om uw bestand te uploaden.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Een bestand uploaden naar Blob Storage met behulp van de upload-URL

Gebruik de Azure Storage Api's of Sdk's (bijvoorbeeld de [opslag rest API](../../storage/common/storage-rest-api-auth.md) of de [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: een extern bestand coderen op basis van URL en de video-REST streamen](stream-files-tutorial-with-rest.md)
