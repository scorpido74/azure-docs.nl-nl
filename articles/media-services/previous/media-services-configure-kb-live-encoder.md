---
title: De Haivision KB-encoder configureren om één bitrate live stream naar Azure te verzenden | Microsoft Documenten
description: In dit onderwerp ziet u hoe u de Haivision KB live-encoder configureert om één bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: afc0fcb6751a08b41010fa569c67a9827e0abec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131934"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Gebruik de Haivision KB live encoder om een enkele bitrate live stream te sturen  
> [!div class="op_single_selector"]
> * [Haivision Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

In dit onderwerp ziet u hoe u de [Havision KB live-encoderencoder](https://www.haivision.com/products/kb-series/) configureert om een enkele bitratestream naar AMS-kanalen te verzenden die zijn ingeschakeld voor live codering. Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

In deze zelfstudie ziet u hoe u azure media services (AMS) beheert met het hulpprogramma Azure Media Services Explorer (AMSE). Deze tool wordt alleen uitgevoerd op Windows PC. Als u zich op Mac of Linux bevindt, gebruikt u de Azure-portal om [kanalen](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) en [programma's](media-services-portal-creating-live-encoder-enabled-channel.md)te maken.

## <a name="prerequisites"></a>Vereisten
*   Toegang tot een Haivision KB-encoder, met SW v5.01 of hoger.
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
[Haivision Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Geef een kanaalnaam op, het beschrijvingsveld is optioneel. Selecteer onder Kanaalinstellingen de optie **Standaard** voor de optie Live-codering, waarbij het invoerprotocol is ingesteld op **RTMP**. U alle andere instellingen laten zoals het is. Controleer of het **nieuwe kanaal nu** starten is geselecteerd.
3. Klik **op Kanaal maken**.
[Haivision Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Het kanaal kan wel 20 minuten duren om te starten.

## <a name="configure-the-haivision-kb-encoder"></a>De Haivision KB-encoder configureren
In deze zelfstudie worden de volgende uitvoerinstellingen gebruikt. De rest van deze sectie beschrijft configuratiestappen in meer detail.

Video:
-   Codec: H.264
-   Profiel: hoog (niveau 4.0)
-   Bitrate: 5000 kbps
-   Hoofdframe: 2 seconden (60 frames)
-   Framesnelheid: 30

Audio:
-   Codec: AAC (LC)
-   Bitsnelheid: 192 kbps
-   Sample rate: 44,1 kHz

## <a name="configuration-steps"></a>Configuratiestappen
1.  Log in op de Haivision KB gebruikersinterface.
2.  Klik op de **menuknop** in het kanaalcontrolecentrum en selecteer **Kanaal toevoegen**  
    ![Screenshot 2017-08-14 om 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ de **kanaalnaam** in het veld Naam en klik op volgende.  
    ![Screenshot 2017-08-14 om 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selecteer de **vervolgkeuzebron kanaalinvoer** in de vervolgkeuzelijst **Invoerbron** en klik op volgende.
    ![Screenshot 2017-08-14 om 9.20.44 uur](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Kies **h264-720-AAC-192** in de **vervolgkeuzelijst Encoder Template** en klik vervolgens.
    ![Screenshot 2017-08-14 om 9.23.15 uur](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Kies **RTMP** in de vervolgkeuzelijst **Nieuwe uitvoer** selecteren en klik op volgende.  
    ![Screenshot 2017-08-14 om 9.27.51 uur](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Vul in het venster **Kanaaluitvoer** de Azure-streamgegevens in. Plak de **RTMP-koppeling** vanaf de oorspronkelijke kanaalinstelling in het **gebied Server.** Typ in het gebied **uitvoernaam** de naam van het kanaal. Gebruik in het gebied Sjabloon Stroomnaam de sjabloon RTMPStreamName_%video_bitrate% om de stream een naam te geven.
    ![Screenshot 2017-08-14 om 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klik op volgende en klik vervolgens op Gereed.
9.  Klik op de **afspeelknop** om het encoderkanaal te starten.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Afspelen testen
Navigeer naar het amse-gereedschap en klik met de rechtermuisknop op het kanaal dat moet worden getest. Plaats in het menu de plaats boven Afspelen van de voorvertoning en selecteer met Azure Media Player.

Als de stream in de speler wordt weergegeven, is de encoder goed geconfigureerd om verbinding te maken met AMS.

Als er een fout wordt ontvangen, moet het kanaal worden gereset en de instellingen van de encoder worden aangepast. Zie het artikel over probleemoplossing voor richtlijnen.

## <a name="create-a-program"></a>Een programma maken
1.  Zodra het afspelen van het kanaal is bevestigd, maakt u een programma. Klik onder het tabblad Live in het gereedschap AMSE met de rechtermuisknop in het programmagebied en selecteer Nieuw programma maken.
[Haivision Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Geef het programma een naam en pas indien nodig de lengte van het archiefvenster aan (die standaard vier uur is). U ook een opslaglocatie opgeven of als standaard laten staan.
2.  Schakel het selectievakje Het programma nu starten in.
3.  Klik op Programma maken.
4.  Zodra het programma wordt uitgevoerd, bevestigt u het afspelen door met de rechtermuisknop op het programma te klikken en te navigeren naar Afspelen van het programma(en) en vervolgens te selecteren met Azure Media Player.
5.  Nadat deze is bevestigd, klikt u met de rechtermuisknop nogmaals op het programma en selecteert u De URL voor uitvoer naar klembord kopiëren (of deze informatie ophalen in de optie Programma-informatie en -instellingen in het menu).

De stream is nu klaar om te worden ingebed in een speler, of gedistribueerd naar een publiek voor live weergave.

> [!NOTE]
> Het maken van programma's kost minder tijd dan het maken van kanalen.
