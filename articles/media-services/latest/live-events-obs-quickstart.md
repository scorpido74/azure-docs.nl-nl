---
title: Een Azure Media Services live stream maken met IB Studio
description: Meer informatie over het maken van een Azure Media Services live stream met behulp van de portal en IB Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726611"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Een Azure Media Services live stream maken met IB

Deze Quick Start helpt u bij het maken van een Azure Media Services live stream met behulp van de Azure Portal en open Broadcasting Studio (IB). Hierbij wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

In deze Snelstartgids worden de volgende acties behandeld:

- Instellen van een on-premises Encoder met IB.
- Een live stream instellen.
- De uitvoer van live streams instellen.
- Een standaard streaming-eind punt wordt uitgevoerd.
- Gebruik Azure Media Player om de Live Stream en de uitvoer op aanvraag weer te geven.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure-Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Een on-premises encoder instellen met behulp van IB

1. Down load en installeer IB voor uw besturings systeem op de [website van open Broadcaster software](https://obsproject.com/).
1. Start de toepassing en blijf deze geopend.

## <a name="run-the-default-streaming-endpoint"></a>Het standaard streaming-eind punt uitvoeren

1. Selecteer **streaming-eind punten** in de lijst met Media Services.

   ![Menu-item voor streaming-eind punten](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Als de standaard status van het streaming-eind punt is gestopt, selecteert u deze. Met deze stap gaat u naar de pagina voor dat eind punt.
1. Selecteer **Starten**.

   ![De knop Start voor het streaming-eind punt](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Een Azure Media Services Live Stream instellen

1. Ga naar het Azure Media Services-account in de portal en selecteer vervolgens **live streamen** in de **Media Services** -lijst.

   ![Live streaming-koppeling](media/live-events-obs-quickstart/select-live-streaming.png)
1. Selecteer **Live Event toevoegen** om een nieuwe gebeurtenis voor live streaming te maken.

   ![Pictogram live-gebeurtenis toevoegen](media/live-events-obs-quickstart/add-live-event.png)
1. Voer een naam in voor uw nieuwe gebeurtenis, zoals *TestLiveEvent*, in het vak **naam van live-gebeurtenis** .

   ![Vak live-evenement naam](media/live-events-obs-quickstart/live-event-name.png)
1. Voer een optionele beschrijving in van de gebeurtenis in het vak **Beschrijving** .
1. Selecteer de optie **Pass-Through-geen Cloud encoding** .

   ![Optie voor Cloud versleuteling](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP** .
1. Zorg ervoor dat de optie **Nee** is geselecteerd voor **starten van live gebeurtenis**, om te voor komen dat er wordt gefactureerd voor de live-gebeurtenis voordat deze klaar is. (Facturering begint wanneer de live gebeurtenis wordt gestart.)

   ![Live-gebeurtenis optie starten](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecteer de knop **beoordeling + maken** om de instellingen te controleren.
1. Selecteer de knop **maken** om de live-gebeurtenis te maken. Vervolgens keert u terug naar de aanbieding van Live-gebeurtenissen.
1. Selecteer de koppeling naar de live-gebeurtenis die u zojuist hebt gemaakt. U ziet dat uw gebeurtenis is gestopt.
1. Laat deze pagina in uw browser geopend. We worden later teruggebeld.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Een live stream instellen met behulp van IB Studio

IB begint met een standaard scène, maar er is geen invoer geselecteerd.

   ![Standaard scherm van IB](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Een video bron toevoegen

1. Klik in het deel venster **bronnen** op het pictogram **toevoegen** om een nieuw bron apparaat te selecteren. Het menu **bronnen** wordt geopend.

1. Selecteer **apparaat voor video-opname** in het menu van het bron apparaat. Het menu **bron maken/selecteren** wordt geopend.

   ![Menu bronnen IB met geselecteerd video apparaat](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecteer het keuze rondje **bestaande toevoegen** en klik vervolgens op **OK**. Het menu met **Eigenschappen voor video apparaat** wordt geopend.

   ![IB nieuw video bron menu met de optie bestaande toevoegen](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Selecteer in de vervolg keuzelijst voor **apparaten** de video-invoer die u voor uw uitzending wilt gebruiken. Laat de overige instellingen voor Taan ongewijzigd en klik op **OK**. De invoer bron wordt toegevoegd aan het deel venster **bronnen** en de weer gave video-invoer wordt weer gegeven in het gedeelte **Preview** .

   ![Camera-instellingen voor IB](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Een audio bron toevoegen

1. Klik in het deel venster **bronnen** op het pictogram **toevoegen** om een nieuw bron apparaat te selecteren. Het menu van het bron apparaat wordt geopend.

1. Selecteer **Audio-invoer vastleggen** in het menu van het bron apparaat. Het menu **bron maken/selecteren** wordt geopend.

   ![Menu bronnen IB met geselecteerd audio apparaat](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecteer het keuze rondje **bestaande toevoegen** en klik vervolgens op **OK**. Het menu **Eigenschappen voor audio-invoer vastleggen** wordt geopend.

   ![IB-audio bron met bestaande toevoegen geselecteerd ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Selecteer in de vervolg keuzelijst **apparaat** het Audio Capture-apparaat dat u wilt gebruiken voor uw uitzending. Laat de overige instellingen voor Taan ongewijzigd en klik op OK. Het Audio Capture-apparaat wordt toegevoegd aan het audio mixer paneel.

   ![Vervolg keuzelijst voor selectie van IB-audio apparaten](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Streaming in IB instellen

In de volgende procedure gaat u terug naar Azure Media Services in uw browser om de invoer-URL te kopiëren en in te voeren in de uitvoer instellingen:

1. Selecteer op de pagina Azure Media Services van de portal **Start** om de gebeurtenis live stream te starten. (Facturering wordt nu gestart.)

   ![Pictogram starten](media/live-events-obs-quickstart/start.png)
1. Stel de **RTMP** -wissel knop in op **RTMP**.
1. Kopieer de URL naar het klem bord in het vak **invoer-URL** .

   ![Invoer-URL](media/live-events-obs-quickstart/input-url.png)

1. Schakel over naar de IB-toepassing.

1. Klik op de knop **instellingen** in het deel venster **besturings elementen** . De instellingen opties worden geopend.

   ![Deel venster IB-besturings elementen met geselecteerde instellingen](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecteer **Stream** in het menu **instellingen** .

1. Selecteer in de vervolg keuzelijst **service** de optie alles weer geven en selecteer vervolgens **aangepast...**.

1. Plak in het veld **Server** de RTMP-URL die u hebt gekopieerd naar het klem bord.

1. Voer een waarde in het veld **stroom sleutel** in.  Het is niet belang rijk wat het is, maar het moet wel een waarde hebben.

    ![Instellingen voor IB-stream](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecteer **uitvoer** in het menu **instellingen** .

1. Voer *2* in het veld **interval van keyframe** in. Hiermee stelt u de lengte van het fragment in op 2 seconden. Voor Live levering met een lagere latentie gebruikt u de waarde 1 seconde.

1. Optioneel: Stel de **voor instelling voor CPU-gebruik** in op *veryfast* als u een computer gebruikt die weinig verwerkings kracht heeft. Desgewenst kunt u de kbps instellen op iets lager als er sprake is van ongewenste netwerk omstandigheden.

   ![IB uitvoer instellingen](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Laat de overige instellingen ongewijzigd en klik op **OK**.

### <a name="start-streaming"></a>Streamen starten

1. Klik in het deel venster **beheer** op **streaming starten**.

    ![Knop streaming IB starten](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Ga in uw browser naar het scherm Azure Media Services Live Event en klik op de koppeling voor het **opnieuw laden** van de speler. U ziet nu de stroom in de preview-versie van de speler.

## <a name="set-up-outputs"></a>Uitvoer instellen

Met dit onderdeel worden uw uitvoer ingesteld en kunt u een opname van uw Live Stream opslaan.  

> [!NOTE]
> Als u deze uitvoer wilt streamen, moet het streaming-eind punt actief zijn. Zie de sectie een [standaard streaming-eind punt uitvoeren](#run-the-default-streaming-endpoint) .

1. Selecteer de koppeling **uitvoer maken** onder de video-viewer voor **uitvoer** .
1. Als u wilt, kunt u de naam van de uitvoer in het vak **naam** bewerken naar een gebruiks vriendelijker, zodat u deze later eenvoudig kunt vinden.

   ![Vak uitvoer naam](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de vakken nu alleen behouden.
1. Selecteer **volgende** om een streaming-Locator toe te voegen.
1. Wijzig de naam van de Locator in iets meer gebruikers vriendelijk, indien gewenst.

   ![Vak voor de naam van de Locator](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Zorg ervoor dat de rest van dit scherm alleen voor Taan wordt gewijzigd.
1. Selecteer **Maken**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>De uitvoer uitzending afspelen met behulp van Azure Media Player

1. Kopieer de streaming-URL onder de video speler voor **uitvoer** .
1. Open in een webbrowser de Azure Media Player- [demo](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Plak de streaming-URL in het vak **URL** van Azure Media Player.
1. Selecteer de knop voor het bijwerken van de **speler** .
1. Selecteer het pictogram **afspelen** op de video om uw live stream te bekijken.

## <a name="stop-the-broadcast"></a>De uitzending stoppen

Wanneer u denkt dat u voldoende inhoud hebt gestreamd, stopt u de uitzending.

1. Selecteer in de portal **stoppen**.

1. Selecteer in IB de knop **streaming stoppen** in het deel venster **besturings elementen** . Met deze stap wordt de uitzending van IB gestopt.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>De uitvoer op aanvraag afspelen met behulp van Azure Media Player

De uitvoer die u hebt gemaakt, is nu beschikbaar voor streaming op aanvraag zolang het streaming-eind punt wordt uitgevoerd.

1. Ga naar de vermelding Media Services en selecteer **activa**.
1. Zoek de gebeurtenis uitvoer die u eerder hebt gemaakt en selecteer de koppeling naar de Asset. De pagina Asset-uitvoer wordt geopend.
1. Kopieer de streaming-URL onder de video speler voor de Asset.
1. Ga terug naar Azure Media Player in de browser en plak de streaming-URL in het vak URL.
1. Selecteer **Update speler**.
1. Selecteer het pictogram **afspelen** op de video om de Asset op aanvraag weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> Stop de services. Nadat u de stappen in deze Quick Start hebt voltooid, moet u ervoor zorgen dat u de live gebeurtenis en het streaming-eind punt stopt, of u wordt gefactureerd voor de tijd waarop ze actief blijven. Als u de live-gebeurtenis wilt stoppen, raadpleegt u de procedure voor [het stoppen van de uitzending](#stop-the-broadcast) , stappen 2 en 3.

Het streaming-eind punt stoppen:

1. Selecteer in de lijst Media Services **streaming-eind punten**.
2. Selecteer het standaard streaming-eind punt dat u eerder hebt gestart. Met deze stap wordt de pagina van het eind punt geopend.
3. Selecteer **stoppen**.

> [!TIP]
> Als u de assets van deze gebeurtenis niet wilt blijven gebruiken, moet u deze verwijderen, zodat u niet in rekening wordt gebracht voor opslag.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Live-evenementen en live-uitvoer in Media Services](./live-events-outputs-concept.md)
