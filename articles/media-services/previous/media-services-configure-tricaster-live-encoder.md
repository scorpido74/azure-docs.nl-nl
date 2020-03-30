---
title: Configureer de NewTek TriCaster-encoder om één bitrate live stream te verzenden | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u de Tricaster live-encoder configureert om één bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152496"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Gebruik de NewTek TriCaster encoder om een enkele bitrate live stream te verzenden  
> [!div class="op_single_selector"]
> * [Tricaster Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

In dit artikel ziet u hoe u de [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) live-encoder configureert om een enkele bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering. Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

In deze zelfstudie ziet u hoe u azure media services (AMS) beheert met het hulpprogramma Azure Media Services Explorer (AMSE). Deze tool wordt alleen uitgevoerd op Windows PC. Als u zich op Mac of Linux bevindt, gebruikt u de Azure-portal om [kanalen](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) en [programma's](media-services-portal-creating-live-encoder-enabled-channel.md)te maken.

Bij het gebruik van Tricaster voor het verzenden van een bijdrage feed naar AMS kanalen die zijn ingeschakeld voor live codering, kunnen er video / audio glitches in uw live evenement als u bepaalde functies van Tricaster, zoals snel snijden tussen feeds, of overschakelen naar / van leien. Het AMS-team werkt aan het oplossen van deze problemen, tot die tijd wordt het niet aanbevolen om deze functies te gebruiken.

> [!NOTE]
>  Overweeg om over te stappen op TLS 1.2, de vooraf uitgestelde TLS-versie.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Media Services-account maken](media-services-portal-create-account.md)
* Zorg ervoor dat er een streaming eindpunt wordt uitgevoerd. Zie [Streaming eindpunten beheren in een Media Services-account voor](media-services-portal-manage-streaming-endpoints.md) meer informatie
* Installeer de nieuwste versie van de [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) tool.
* Start de tool en maak verbinding met uw AMS-account.

## <a name="tips"></a>Tips

* Gebruik indien mogelijk een vaste internetverbinding.
* Als goede vuistregel voor het bepalen van de bandbreedtevereisten kunt u de streaming-bitrates verdubbelen. Hoewel dit geen verplichte vereiste is, helpt het de impact van netwerkcongestie te beperken.
* Sluit bij het gebruik van softwaregebaseerde encoders onnodige programma's af.

## <a name="create-a-channel"></a>Een kanaal maken

1. Navigeer in het gereedschap AMSE naar het tabblad **Live** en klik met de rechtermuisknop in het kanaalgebied. Selecteer **Kanaal maken...** van het menu.

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Geef een kanaalnaam op, het beschrijvingsveld is optioneel. Selecteer onder Kanaalinstellingen de optie **Standaard** voor de optie Live-codering, waarbij het invoerprotocol is ingesteld op **RTMP**. U alle andere instellingen laten zoals het is.

    Controleer of het **nieuwe kanaal nu** starten is geselecteerd.

3. Klik **op Kanaal maken**.

   ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Het kanaal kan wel 20 minuten duren om te starten.
>
>

Terwijl het kanaal wordt gestart, u [de encoder configureren.](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp)

> [!IMPORTANT]
> Facturering begint zodra Kanaal in een gereedstaat wordt. Zie de [staten van Channel](media-services-manage-live-encoder-enabled-channels.md#states)voor meer informatie.
>
>

## <a name="configure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>De NewTek TriCaster-encoder configureren

In deze zelfstudie worden de volgende uitvoerinstellingen gebruikt. De rest van deze sectie beschrijft configuratiestappen in meer detail.

**Video**:

* Codec: H.264
* Profiel: hoog (niveau 4.0)
* Bitrate: 5000 kbps
* Hoofdframe: 2 seconden (60 seconden)
* Framesnelheid: 30

**Audio**:

* Codec: AAC (LC)
* Bitsnelheid: 192 kbps
* Sample rate: 44,1 kHz

### <a name="configuration-steps"></a>Configuratiestappen

1. Maak een nieuw **NewTek TriCaster-project,** afhankelijk van welke video-invoerbron wordt gebruikt.
2. Zoek eenmaal binnen dat project de knop **Streamen** en klik op het tandwielpictogram ernaast om toegang te krijgen tot het menu van de streamconfiguratie.

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Zodra het menu is geopend, klikt u op **Nieuw** onder de kop Verbinding. Selecteer **Adobe Flash**wanneer u wordt gevraagd om het verbindingstype .

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klik op **OK**.
5. Een FMLE-profiel kan nu worden geïmporteerd door op de vervolgkeuzepijl onder **Streaming Profiel** te klikken en naar **Bladeren**te navigeren.

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navigeer naar de plaats waar het geconfigureerde FMLE-profiel is opgeslagen.
7. Selecteer deze en druk op **OK.**

    Zodra het profiel is geüpload, gaat u verder met de volgende stap.
8. Download de invoer-URL van het kanaal om deze toe te wijzen aan het Tricaster **RTMP-eindpunt.**

    Navigeer terug naar het AMSE-gereedschap en controleer de status van kanaalvoltooiing. Zodra de status is gewijzigd **van Starten** naar **Uitvoeren,** u de url van invoer ophalen.

    Wanneer het kanaal wordt uitgevoerd, klikt u met de rechtermuisknop op de kanaalnaam, navigeert u omlaag om de **url van invoer kopiëren naar het klembord te** plaatsen en selecteert u URL voor primaire **invoer**.  

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Plak deze informatie in het veld **Locatie** onder **Flash Server** binnen het Tricaster-project. Wijs ook een streamnaam toe in het veld **Stream-id.**

    Als streamgegevens zijn toegevoegd aan het FMLE-profiel, kan deze ook naar deze sectie worden geïmporteerd door op **Instellingen importeren**te klikken, naar het opgeslagen FMLE-profiel te navigeren en op **OK**te klikken. De relevante Flash Server-velden moeten worden gevuld met de informatie van FMLE.

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Klik als u klaar bent met **OK** onder aan het scherm. Wanneer video- en audio-ingangen in de Tricaster klaar zijn, begint u met streamen naar AMS door op de **knop Streamen** te klikken.

     ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Voordat u op **Streamen**klikt, **moet** u ervoor zorgen dat het kanaal klaar is.
> Zorg er ook voor dat u het Kanaal niet langer dan > 15 minuten in een gereedstaat verlaat zonder invoerbijdragefeed.
>
>

## <a name="test-playback"></a>Afspelen testen

Navigeer naar het amse-gereedschap en klik met de rechtermuisknop op het kanaal dat moet worden getest. Plaats in het menu de plaats **boven Afspelen van de voorvertoning** en selecteer met Azure Media **Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Als de stream in de speler wordt weergegeven, is de encoder goed geconfigureerd om verbinding te maken met AMS.

Als er een fout wordt ontvangen, moet het kanaal worden gereset en de instellingen van de encoder worden aangepast. Zie het artikel [over probleemoplossing](media-services-troubleshooting-live-streaming.md) voor richtlijnen.  

## <a name="create-a-program"></a>Een programma maken

1. Zodra het afspelen van het kanaal is bevestigd, maakt u een programma. Klik onder het tabblad **Live** in het gereedschap AMSE met de rechtermuisknop in het programmagebied en selecteer **Nieuw programma maken**.  

    ![tricaster tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Geef het programma een naam en pas indien nodig de lengte van het **archiefvenster** aan (die standaard vier uur is). U ook een opslaglocatie opgeven of als standaard laten staan.  
3. Schakel het **selectievakje Het programma nu starten in.**
4. Klik **op Programma maken**.  

    >[!NOTE]
    >Het maken van programma's kost minder tijd dan het maken van kanalen.
        
5. Zodra het programma wordt uitgevoerd, bevestigt u het afspelen door met de rechtermuisknop op het programma te klikken en naar **Het programma(s) af** te spelen en vervolgens te selecteren **met Azure Media Player**.  
6. Nadat deze is bevestigd, klikt u met de rechtermuisknop nogmaals op het programma en selecteert u **De URL voor uitvoer naar klembord kopiëren** (of deze informatie ophalen in de optie **Programma-informatie en -instellingen** in het menu).

De stream is nu klaar om te worden ingebed in een speler, of gedistribueerd naar een publiek voor live weergave.  

## <a name="troubleshooting"></a>Problemen oplossen

Zie het artikel [over probleemoplossing](media-services-troubleshooting-live-streaming.md) voor richtlijnen.

## <a name="next-step"></a>Volgende stap

Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
