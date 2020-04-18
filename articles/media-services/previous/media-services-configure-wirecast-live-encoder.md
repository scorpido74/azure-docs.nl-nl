---
title: De Telestream Wirecast-encoder configureren om één bitrate live stream te verzenden | Microsoft Documenten
description: 'In dit onderwerp wordt uitgelegd hoe u de Wirecast live-encoder configureert om één bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 8e3705aaecb0760513f0605aece89b7ffc0044a8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641646"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Gebruik de Wirecast-encoder om een enkele bitrate live stream te verzenden 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

In dit artikel ziet u hoe u de Live-encoder [van Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) configureert om één bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering. Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

In deze zelfstudie ziet u hoe u azure media services (AMS) beheert met het hulpprogramma Azure Media Services Explorer (AMSE). Deze tool wordt alleen uitgevoerd op Windows PC. Als u zich op Mac of Linux bevindt, gebruikt u de Azure-portal om [kanalen](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) en [programma's](media-services-portal-creating-live-encoder-enabled-channel.md)te maken.

> [!NOTE]
> Encoders moeten TLS 1.2 ondersteunen bij het gebruik van RTMPS-protocollen. Gebruik de Wirecast versie 13.0.2 of hoger vanwege de TLS 1.2 vereiste.

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

    ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Geef een kanaalnaam op, het beschrijvingsveld is optioneel. Selecteer onder Kanaalinstellingen de optie **Standaard** voor de optie Live-codering, waarbij het invoerprotocol is ingesteld op **RTMP**. U alle andere instellingen laten zoals het is.

    Controleer of het **nieuwe kanaal nu** starten is geselecteerd.

3. Klik **op Kanaal maken**.

   ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Het kanaal kan wel 20 minuten duren om te starten.
>
>

Terwijl het kanaal wordt gestart, u [de encoder configureren.](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)

> [!IMPORTANT]
> Facturering begint zodra Kanaal in een gereedstaat wordt. Zie de [staten van Channel](media-services-manage-live-encoder-enabled-channels.md#states)voor meer informatie.
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />De Telestream Wirecast-encoder configureren
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
1. Open de Telestream Wirecast-toepassing op de machine die wordt gebruikt en stel het in voor RTMP-streaming.
2. Configureer de uitvoer door naar het tabblad **Uitvoer** te navigeren en **Uitvoerinstellingen te selecteren...**.

    Controleer of de **uitvoerbestemming** is ingesteld op **RTMP-server**.
3. Klik op **OK**.
4. Stel op de pagina Instellingen het veld **Doel** in als **Azure Media Services**.

    Het coderingsprofiel is vooraf geselecteerd in **Azure H.264 720p 16:9 (1280x720).** Als u deze instellingen wilt aanpassen, selecteert u het tandwielpictogram rechts van de vervolgkeuzelijst en kiest u **Nieuwe voorinstelling**.

    ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configureren encodervoorinstellingen.

    Geef de voorinstelling een naam en controleer op de volgende aanbevolen instellingen:

    **Video**

   * Encoder: MainConcept H.264
   * Frames per seconde: 30
   * Gemiddelde bitsnelheid: 5000 kbits/sec (Kan worden aangepast op basis van netwerkbeperkingen)
   * Profiel: Hoofd
   * Hoofdframe elke: 60 frames

     **Audio**

   * Streefbittarief: 192 kbits/sec
   * Sample rate: 44.100 kHz

     ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Klik op **Opslaan**.

    Het veld Codering heeft nu het nieuw gemaakte profiel beschikbaar voor selectie.

    Controleer of het nieuwe profiel is geselecteerd.
7. Download de invoer-URL van het kanaal om deze toe te wijzen aan het Wirecast **RTMP-eindpunt.**

    Navigeer terug naar het AMSE-gereedschap en controleer de status van kanaalvoltooiing. Zodra de status is gewijzigd **van Starten** naar **Uitvoeren,** u de url van invoer ophalen.

    Wanneer het kanaal wordt uitgevoerd, klikt u met de rechtermuisknop op de kanaalnaam, navigeert u omlaag om de url **van invoer kopiëren naar klembord te** plaatsen en selecteert u URL voor primaire **invoer**.  

    ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Plak deze informatie in het venster **Wirecast-uitvoerinstellingen** in het veld **Adres** van de uitvoersectie en wijs een streamnaam toe.

    ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecteer **OK**.
2. Bevestig op het **hoofdscherm van Wirecast** dat invoerbronnen voor video en audio klaar zijn en druk vervolgens op **Stream** in de linkerbovenhoek.

    ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Voordat u op **Streamen**klikt, **moet** u ervoor zorgen dat het kanaal klaar is.
> Zorg er ook voor dat u het Kanaal niet langer dan > 15 minuten in een gereedstaat verlaat zonder invoerbijdragefeed.
>
>

## <a name="test-playback"></a>Afspelen testen

Navigeer naar het amse-gereedschap en klik met de rechtermuisknop op het kanaal dat moet worden getest. Plaats in het menu de plaats **boven Afspelen van de voorvertoning** en selecteer met Azure Media **Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Als de stream in de speler wordt weergegeven, is de encoder goed geconfigureerd om verbinding te maken met AMS.

Als er een fout wordt ontvangen, moet het kanaal worden gereset en de instellingen van de encoder worden aangepast. Zie het artikel [over probleemoplossing](media-services-troubleshooting-live-streaming.md) voor richtlijnen.  

## <a name="create-a-program"></a>Een programma maken
1. Zodra het afspelen van het kanaal is bevestigd, maakt u een programma. Klik onder het tabblad **Live** in het gereedschap AMSE met de rechtermuisknop in het programmagebied en selecteer **Nieuw programma maken**.  

    ![draadgietafsing](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
