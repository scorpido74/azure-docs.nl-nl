---
title: Configureer de Telestream Wirecast-coderingsprogramma voor het verzenden van een single-bitrate live stream | Microsoft Docs
description: 'In dit onderwerp laat zien hoe het configureren van het live coderingsprogramma Wirecast voor het verzenden van een single-bitrate stream aan AMS-kanalen die zijn ingeschakeld voor live codering. '
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
ms.openlocfilehash: 1d9d63aa6b3da1b8d8389722bd5af0eeed585d03
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134975"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Gebruik de Wirecast-coderingsprogramma voor het verzenden van een single-bitrate live stream 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [TriCaster](media-services-configure-tricaster-live-encoder.md)
>
>

In dit artikel wordt beschreven hoe u de Wirecast van de [Telestream](https://www.telestream.net/wirecast/overview.htm) configureert om een enkele bitrate stroom te verzenden naar AMS-kanalen die zijn ingeschakeld voor Live encoding. Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

In deze zelfstudie laat zien hoe Azure Media Services (AMS) beheren met Azure Media Services Explorer (AMSE)-hulpprogramma. Dit hulpprogramma wordt alleen uitgevoerd op Windows-PC. Als u gebruikmaakt van Mac of Linux, gebruikt u de Azure Portal om [kanalen](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) en [Program ma's](media-services-portal-creating-live-encoder-enabled-channel.md)te maken.

> [!NOTE]
> Encoders moeten TLS 1,2 ondersteunen bij het gebruik van RTMP-protocollen. Gebruik Wirecast-versie 13.0.2 of hoger als gevolg van de TLS 1,2-vereiste.

## <a name="prerequisites"></a>Vereisten
* [Een Azure Media Services-account maken](media-services-portal-create-account.md)
* Controleer of er is een Streaming-eindpunt is uitgevoerd. Zie [streaming-eind punten beheren in een Media Services-account](media-services-portal-manage-streaming-endpoints.md) voor meer informatie
* Installeer de meest recente versie van het hulp programma [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Het hulpprogramma voor starten en verbinding maken met uw AMS-account.

## <a name="tips"></a>Tips
* Gebruik indien mogelijk een internetverbinding ' hardwired '.
* Een goede vuistregel bij het bepalen van de bandbreedtevereisten voor, is het dubbele van de streaming bitsnelheden. Hoewel dit niet verplicht is, vermindert het de gevolgen van opstoppingen in het netwerk.
* Wanneer u coderingsprogramma's op basis van software, sluit u alle onnodige programma's.

## <a name="create-a-channel"></a>Een kanaal maken
1. In het hulp programma AMSE gaat u naar het tabblad **Live** en klikt u met de rechter muisknop binnen het kanaal gebied. Selecteer **kanaal maken...** in het menu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Geef de naam van een kanaal, het beschrijvingsveld is optioneel. Onder kanaal instellingen selecteert u **standaard** voor de optie Live encoding, waarbij het invoer protocol is ingesteld op **RTMP**. U kunt alle andere instellingen omdat laten.

    Zorg ervoor dat het **nieuwe kanaal nu starten** is geselecteerd.

3. Klik op **kanaal maken**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Het kanaal kan zo lang 20 minuten duren.
>
>

Terwijl het kanaal wordt gestart, kunt u [het coderings programma configureren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> De facturering begint zodra kanaal krijgt de status gereed. Zie [Staten van het kanaal](media-services-manage-live-encoder-enabled-channels.md#states)voor meer informatie.
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a>het Wirecast-coderings programma voor Telestream <a id="configure_wirecast_rtmp" />configureren
In deze zelfstudie worden de volgende uitvoerinstellingen gebruikt. De rest van deze sectie worden de configuratiestappen in meer detail beschreven.

**Video**:

* Codec: H.264
* Profiel: Hoog (niveau 4.0)
* Bitrate: 5000 kbps
* Sleutelframes: 2 seconden (60 seconden)
* Tarief frame: 30

**Audio**:

* Codec: AAC (LC)
* Bitrate: 192 kbps
* Samplefrequentie: 44,1 kHz

### <a name="configuration-steps"></a>Configuratiestappen
1. Open de Telestream Wirecast-toepassing op de computer die wordt gebruikt, en voor het streamen van RTMP instellen.
2. Configureer de uitvoer door te navigeren naar het tabblad **uitvoer** en **uitvoer instellingen te selecteren...** .

    Zorg ervoor dat de **uitvoer bestemming** is ingesteld op **RTMP-server**.
3. Klik op **OK**.
4. Stel op de pagina instellingen het **doel** veld in op **Azure Media Services**.

    Het coderings profiel is vooraf geselecteerd voor **Azure H. 264 720p 16:9 (1280x720)** . Als u deze instellingen wilt aanpassen, selecteert u het tandwiel pictogram rechts van de vervolg keuzelijst en kiest u vervolgens **nieuwe voor instelling**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Voorinstellingen van het coderingsprogramma configureren.

    Naam van de vooraf gedefinieerde instellingen en controleren op de volgende aanbevolen instellingen:

    **Hardware**

   * Coderingsprogramma: MainConcept H.264
   * Frames per seconde: 30
   * Gemiddelde bitsnelheid: 5000 kbits per seconde (kan worden aangepast op basis van de beperkingen op het netwerk)
   * Profiel: Main
   * Belangrijkste frame elke: 60 frames

     **Geluiden**

   * Doelbitsnelheid: 192 kbits/sec
   * Samplefrequentie: 44,100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Klik op **Opslaan**.

    Het veld Encoding heeft nu het zojuist gemaakte profiel beschikbaar voor selectie.

    Zorg ervoor dat het nieuwe profiel is geselecteerd.
7. Haal de invoer-URL van het kanaal op om deze toe te wijzen aan het Wirecast **RTMP-eind punt**.

    Ga terug naar het AMSE-hulpprogramma en de voltooiingsstatus kanaal controleren. Zodra de status is gewijzigd van **starten** in **wordt uitgevoerd**, kunt u de invoer-URL ophalen.

    Wanneer het kanaal actief is, klikt u met de rechter muisknop op de naam van het kanaal, gaat u omlaag om de **invoer-URL naar het klem bord te verplaatsen** en selecteert u vervolgens **primaire invoer-URL**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Plak in het venster **uitvoer instellingen** Wirecast deze informatie in het veld **adres** van de sectie uitvoer en wijs een stroom naam toe.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecteer **OK**.
2. Bevestig in het hoofd scherm van **Wirecast** de invoer bronnen voor video en audio zijn gereed en klik vervolgens op **stroom** in de linkerbovenhoek.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Voordat u op **Stream**klikt, **moet** u ervoor zorgen dat het kanaal gereed is.
> Zorg ervoor dat u niet laat het kanaal in een status gereed hebben zonder een invoer bijdrage feed voor langer dan 15 minuten >.
>
>

## <a name="test-playback"></a>Test afspelen

Navigeer naar het AMSE-hulpprogramma en met de rechtermuisknop op het kanaal moet worden getest. Klik in het menu met de muis aanwijzer op **het afspelen van de preview-versie** en selecteer **met Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Als de stroom in de speler wordt weergegeven, is klikt u vervolgens het coderingsprogramma correct is geconfigureerd om te verbinden met AMS.

Als er een fout is ontvangen, wordt het kanaal moet opnieuw worden ingesteld en encoder-instellingen aangepast. Raadpleeg het artikel over [probleem oplossing](media-services-troubleshooting-live-streaming.md) voor hulp.  

## <a name="create-a-program"></a>Een programma maken
1. Wanneer het kanaal afspelen hebt bevestigd, maakt u een programma. Klik onder het tabblad **Live** in het hulp programma AMSE met de rechter muisknop in het gebied Program en selecteer **nieuw programma maken**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Geef het programma een naam en wijzig indien nodig de **lengte van het archief venster** (de standaard waarde is vier uur). U kunt ook opgeven van een opslaglocatie bevinden of behoud de standaardwaarde.  
3. Schakel het selectie vakje **programma nu starten** in.
4. Klik op **programma maken**.  

   >[!NOTE]
   >Maken van een programma duurt minder lang dan het maken van kanalen.
       
5. Nadat het programma is uitgevoerd, kunt u het afspelen bevestigen door met de rechter muisknop op het programma te klikken en te navigeren om **de Program ma's** af te spelen en vervolgens te selecteren **met Azure Media Player**.  
6. Nadat deze is bevestigd, klikt u opnieuw met de rechter muisknop op het programma en selecteert **u de uitvoer-URL naar het klem bord kopiëren** (of deze gegevens ophalen uit de optie **informatie en instellingen** van het programma in het menu).

De stroom is nu klaar om te worden ingesloten in een speler of gedistribueerd naar een doelgroep voor het weergeven van live.  

## <a name="troubleshooting"></a>Problemen oplossen
Raadpleeg het artikel over [probleem oplossing](media-services-troubleshooting-live-streaming.md) voor hulp.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
