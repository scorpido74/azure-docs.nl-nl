---
title: Verificatietokens doorgeven aan Azure Media Services | Microsoft Documenten
description: Meer informatie over het verzenden van verificatietokens van de client naar de azure Media Services-service voor het leveren van sleutel
services: media-services
keywords: inhoudsbescherming, DRM, tokenverificatie
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684938"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Meer informatie over hoe clients tokens doorgeven aan de azure media services-service voor het leveren van sleutel
Klanten vragen vaak hoe een speler tokens kan doorgeven aan de key delivery service van Azure Media Services voor verificatie, zodat de speler de sleutel kan verkrijgen. Media Services ondersteunt de eenvoudige webtoken -indelingen (SWT) en JSON Web Token (JWT). Tokenverificatie wordt toegepast op elk type sleutel, ongeacht of u algemene versleuteling of AES-envelopversleuteling (Advanced Encryption Standard) in het systeem gebruikt.

 Afhankelijk van de speler en het platform dat je target, kun je het token op de volgende manieren doorgeven aan je speler:

- Via de header HTTP Authorization.
    > [!NOTE]
    > De "Drager" voorvoegsel wordt verwacht per de OAuth 2.0 specs. Een voorbeeldspeler met de tokenconfiguratie wordt gehost op de [demopagina](https://ampdemo.azureedge.net/)van Azure Media Player. Als u de videobron wilt instellen, kiest u **AES (JWT-token)** of **AES (SWT-token).** Het token wordt doorgegeven via de header Autorisatie.

- Via de toevoeging van een URL-queryparameter met 'token=tokenvalue'.  
    > [!NOTE]
    > Het voorvoegsel "Drager" wordt niet verwacht. Omdat het token via een URL wordt verzonden, moet u de tokenstring armor. Hier is een C# voorbeeldcode die laat zien hoe dit te doen:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Via het veld CustomData.
Deze optie wordt alleen gebruikt voor playready-licentieverwerving via het customdata-veld van de PlayReady License Acquisition Challenge. In dit geval moet het token zich in het XML-document begeven, zoals hier beschreven:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Plaats uw verificatietoken in het token-element.

- Via een alternatieve HTTP Live Streaming (HLS) afspeellijst. Als u tokenverificatie voor AES + HLS-weergave op iOS/Safari moet configureren, is er geen manier waarop u het token rechtstreeks verzenden. Zie dit [blogbericht](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)voor meer informatie over het afwisselen van de afspeellijst om dit scenario in te schakelen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
