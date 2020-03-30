---
title: Inhoud publiceren in de Azure-portal | Microsoft Documenten
description: In deze zelfstudie u uw inhoud publiceren in de Azure-portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 5f242018abfb15cea1b76cbcaad00942ec25d78d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015067"
---
# <a name="publish-content-in-the-azure-portal"></a>Inhoud publiceren in de Azure-portal  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Rest](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Overzicht
> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie gratis [proefversie van Azure voor](https://azure.microsoft.com/pricing/free-trial/)meer informatie. 
> 
> 

Als u aan uw gebruikers een URL wilt leveren die ze kunnen gebruiken om uw inhoud te streamen of te downloaden, moet u uw asset eerst publiceren door een locator te maken. Locators bieden toegang tot assetbestanden. Azure Media Services ondersteunt twee typen locators: 

* **Streaming-locators (OnDemandOrigin)**. Streaming-locators worden gebruikt voor adaptief streamen. Voorbeelden van adaptieve streaming zijn Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming en Dynamic Adaptive Streaming over HTTP (DASH, ook wel MPEG-DASH genoemd). Als u een streaming-locator wilt maken, moet uw asset een ISM-bestand bevatten. Bijvoorbeeld http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Progressieve locators (Shared Access Signature)**. Progressieve locators worden gebruikt voor het leveren van video via progressief downloaden.

Als u een HLS-streaming-URL wilt maken, wordt de URL toegevoegd *(format=m3u8-aapl):*

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Gebruik de volgende URL-indeling om een streaming-URL te maken voor het afspelen van Smooth Streaming-assets:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Als u een streaming-URL voor MPEG-DASH wilt maken, voegt u *(format=mpd-time-csf)* toe aan de URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Een Shared Access Signature-URL heeft de volgende indeling:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Zie het [inhoudsoverzicht voor](media-services-deliver-content-overview.md)meer informatie voor meer informatie.

> [!NOTE]
> Locators die vóór maart 2015 in Azure Portal zijn gemaakt, hebben een vervaldatum over twee jaar.  
> 
> 

Als u een vervaldatum van een locator wilt bijwerken, u een [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) of een [.NET API gebruiken.](https://go.microsoft.com/fwlink/?LinkID=533259) 

> [!NOTE]
> Wanneer u de vervaldatum van een Shared Access Signature-locator bijwerkt, wordt de URL gewijzigd.

### <a name="to-use-the-portal-to-publish-an-asset"></a>De portal gebruiken om een asset te publiceren
1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **-middelen**. Selecteer de asset die u wilt publiceren.
3. Selecteer de knop **Publiceren**.
4. Selecteer het type locator.
5. Selecteer **Toevoegen**.
   
    ![De video publiceren](./media/media-services-portal-vod-get-started/media-services-publish1.png)

De URL wordt toegevoegd aan de lijst met **gepubliceerde URL's**.

## <a name="play-content-in-the-portal"></a>Inhoud afspelen in de portal
U kunt uw video testen in een speler in Azure Portal.

Selecteer de video en selecteer vervolgens de knop **Afspelen**.

![De video afspelen in Azure Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Hierbij geldt het volgende:

* De video moet zijn gepubliceerd.
* Met de mediaspeler in Azure Portal wordt inhoud afgespeeld vanaf het standaardstreaming-eindpunt. Als u inhoud vanaf een ander streaming-eindpunt wilt afspelen, selecteert en kopieert u de URL en plak u deze in een andere speler. U kunt uw video bijvoorbeeld in de [Azure Media Player](https://aka.ms/azuremediaplayer) testen.
* Het streaming-eindpunt van waaruit u streamt, moet worden uitgevoerd.  

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

