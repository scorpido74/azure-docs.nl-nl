---
title: Verificatie tokens door geven aan Azure Media Services | Microsoft Docs
description: Meer informatie over het verzenden van verificatie tokens van de client naar de Azure Media Services key delivery service
services: media-services
keywords: inhouds beveiliging, DRM, token verificatie
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 15d4cbc372f5d5ec0d323170189329152ed436e3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684938"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Meer informatie over hoe clients tokens door geven aan de Azure Media Services key delivery service
Klanten vragen vaak hoe een speler tokens kan door geven aan de Azure Media Services key delivery service voor verificatie, zodat de speler de sleutel kan verkrijgen. Media Services ondersteunt de SWT-indelingen (Simple Web token) en JSON Web Token (JWT). Verificatie van tokens wordt toegepast op elk type sleutel, ongeacht of u gebruikmaakt van een common Encryption-of Advanced Encryption Standard (AES)-envelop versleuteling in het systeem.

 Afhankelijk van de speler en het platform waarop u het doel hebt, kunt u het token door geven aan uw speler op de volgende manieren:

- Via de HTTP-autorisatie-header.
    > [!NOTE]
    > Het voor voegsel ' Bearer ' wordt verwacht volgens de OAuth 2,0-specificaties. Een voor beeld van een speler met de token configuratie wordt gehost op de pagina met de Azure Media Player- [demo](https://ampdemo.azureedge.net/). Als u de video bron wilt instellen, kiest u **AES (JWT-token)** of **AES (SWT-token)** . Het token wordt door gegeven via de autorisatie-header.

- Via de toevoeging van een URL-query parameter met "token = tokenvalue."  
    > [!NOTE]
    > Het voor voegsel ' Bearer ' wordt niet verwacht. Omdat het token via een URL wordt verzonden, moet u de token teken reeks beveiligen. Hier volgt een C# voorbeeld code die laat zien hoe u dit doet:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Via het veld CustomData.
Deze optie wordt alleen gebruikt voor de aanschaf van PlayReady-licenties, via het veld CustomData van de de verwervings Challenge van PlayReady-licenties. In dit geval moet het token zich in het XML-document bevinden, zoals hier wordt beschreven:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Plaats uw verificatie token in het element token.

- Via een alternatieve HTTP Live Streaming-afspeel lijst (HLS). Als u token verificatie wilt configureren voor AES en HLS afspelen op iOS/Safari, is er geen manier om rechtstreeks in het token te verzenden. Zie dit [blog bericht](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)voor meer informatie over het maken van een alternatieve afspeel lijst om dit scenario in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
