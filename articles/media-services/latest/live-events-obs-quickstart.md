---
title: Een livestream van Azure Media Services maken met OBS Studio
description: Meer informatie over het maken van een livestream van Azure Media Services met behulp van de portal en OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726611"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Een livestream van Azure Media Services maken met OBS

Met deze quickstart u een livestream van Azure Media Services maken met behulp van de Azure-portal en Open Broadcasting Studio (OBS). Er wordt van uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

In deze snelle start behandelen we:

- Het opzetten van een on-premises encoder met OBS.
- Het opzetten van een live stream.
- Het instellen van live stream-uitgangen.
- Het uitvoeren van een standaard streaming eindpunt.
- Azure Media Player gebruiken om de live stream en on-demand-uitvoer te bekijken.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure-portal.](https://portal.azure.com/) Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Een on-premises encoder opzetten met obs

1. Download en installeer OBS voor uw besturingssysteem op de [website van Open Broadcaster Software.](https://obsproject.com/)
1. Start de toepassing en houd deze open.

## <a name="run-the-default-streaming-endpoint"></a>Het standaardeindpunt voor streaming uitvoeren

1. Selecteer **Streaming-eindpunten** in de lijst Mediaservices.

   ![Menu-item Streaming Eindpunten](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Als de standaardstatus van streamingeindpunt is gestopt, selecteert u deze. Met deze stap gaat u naar de pagina voor dat eindpunt.
1. Selecteer **Starten**.

   ![Knop Start voor het streamingeindpunt](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Een livestream van Azure Media Services instellen

1. Ga naar het Azure Media Services-account binnen de portal en selecteer **Live streaming** in de lijst **Media Services.**

   ![Live streaming link](media/live-events-obs-quickstart/select-live-streaming.png)
1. Selecteer **Live-evenement toevoegen** om een nieuw live streaming-evenement te maken.

   ![Pictogram Live-gebeurtenis toevoegen](media/live-events-obs-quickstart/add-live-event.png)
1. Voer een naam in voor uw nieuwe evenement, zoals *TestLiveEvent,* in het vak **Naam van een Live-evenement.**

   ![Vak Naam van live-evenement](media/live-events-obs-quickstart/live-event-name.png)
1. Voer een optionele beschrijving van de gebeurtenis in het vak **Beschrijving** in.
1. Selecteer de **optie Pass-through – geen cloudcodering.**

   ![Optie voor cloudcodering](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP.**
1. Zorg ervoor dat de optie **Geen** is geselecteerd voor **Start live-evenement,** om te voorkomen dat er kosten in rekening wordt gebracht voor het live-evenement voordat het klaar is. (Facturering begint wanneer het live-evenement wordt gestart.)

   ![Optie Live-evenement starten](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecteer de knop **Controleren + maken** om de instellingen te controleren.
1. Selecteer de knop **Maken** om de live-gebeurtenis te maken. Je wordt vervolgens teruggezet naar de lijst met live-evenementen.
1. Selecteer de link naar het live-evenement dat u zojuist hebt gemaakt. Merk op dat uw evenement is gestopt.
1. Houd deze pagina open in uw browser. We komen er later op terug.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Een live stream opzetten met OBS Studio

OBS begint met een standaardscène, maar zonder invoer geselecteerd.

   ![OBS-standaardscherm](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Een videobron toevoegen

1. Klik in het deelvenster **Bronnen** op het pictogram **Toevoegen** om een nieuw bronapparaat te selecteren. Het menu **Bronnen** wordt geopend.

1. Selecteer **Apparaat voor het vastleggen van video in** het menu van het bronapparaat. Het menu **Bron maken/selecteren** wordt geopend.

   ![Menu OBS-bronnen met videoapparaat geselecteerd](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecteer de knop Bestaande keuzerondje **toevoegen** en klik op **OK**. Het menu **Eigenschappen voor videoapparaat** wordt geopend.

   ![OBS nieuwe videobronmenu met bestaande geselecteerde toevoegen](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Selecteer **in** de vervolgkeuzelijst Apparaat de video-invoer die u wilt gebruiken voor uw uitzending. Laat de rest van de instellingen voorlopig met rust en klik op **OK.** De invoerbron wordt toegevoegd aan het deelvenster **Bronnen** en de video-invoerweergave wordt weergegeven in het gebied **Voorbeeld.**

   ![OBS-camera-instellingen](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Een audiobron toevoegen

1. Klik in het deelvenster **Bronnen** op het pictogram **Toevoegen** om een nieuw bronapparaat te selecteren. Het menu Bronapparaat wordt geopend.

1. Selecteer **Audio-invoer vastleggen** in het menu van het bronapparaat. Het menu **Bron maken/selecteren** wordt geopend.

   ![Menu OBS-bronnen met audioapparaat geselecteerd](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecteer de knop Bestaande keuzerondje **toevoegen** en klik op **OK**. Het menu **Eigenschappen voor het vastleggen van audioinvoer** wordt geopend.

   ![OBS-audiobron met bestaande geselecteerde toevoegen ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Selecteer **in** de vervolgkeuzelijst Apparaat het audio-opnameapparaat dat u wilt gebruiken voor uw uitzending. Laat de rest van de instellingen voorlopig met rust en klik op OK. Het audio-opnameapparaat wordt toegevoegd aan het audiomixerpaneel.

   ![Vervolgkeuzelijst voor selectie van OBS-audioapparaten](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Streaming instellen in OBS

In de volgende procedure gaat u terug naar Azure Media Services in uw browser om de invoer-URL te kopiëren om in de uitvoerinstellingen te gaan:

1. Selecteer op de pagina Azure Media Services van de portal de optie **Start** om de live streamgebeurtenis te starten. (Facturering begint nu.)

   ![Pictogram Start](media/live-events-obs-quickstart/start.png)
1. Stel de **RTMP-schakelaar** in op **RTMPS**.
1. Kopieer de URL in het vak Url van **invoer** naar het klembord.

   ![Invoer-URL](media/live-events-obs-quickstart/input-url.png)

1. Overschakelen naar de OBS-toepassing.

1. Klik op de knop **Instellingen** in het deelvenster **Besturingselementen.** De instellingenopties worden geopend.

   ![Deelvenster OBS-besturingselementen met geselecteerde instellingen](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecteer **Streamen** in het menu **Instellingen.**

1. Selecteer alles weergeven in de vervolgkeuzelijst **Service** en selecteer **Vervolgens Aangepast...**.

1. Plak **in** het veld Server de RTMPS-URL die u naar het klembord hebt gekopieerd.

1. Voer iets in het veld **Stream-toets** in.  Het maakt niet echt uit wat het is, maar het moet een waarde hebben.

    ![OBS-streaminstellingen](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecteer **Uitvoer** in het menu **Instellingen.**

1. Voer *er 2* in het **intervalveld Hoofdframe** in. Dit stelt de lengte van het fragment in op 2 seconden. Voor een lagere latentie live levering, gebruik maken van de waarde van 1 seconde.

1. OPTIONEEL: Stel de **CPU-gebruiksinstelling in** op *zeer snel* als u een computer gebruikt die weinig verwerkingskracht heeft. Optioneel u de kbps instellen op iets lager als er ongewenste netwerkomstandigheden zijn.

   ![OBS-uitvoerinstellingen](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Laat de rest van de instellingen onbelegd en klik op **OK**.

### <a name="start-streaming"></a>Beginnen met streamen

1. Klik in het deelvenster **Besturingselementen** op **Streaming starten**.

    ![Knop OBS startstreaming](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Ga naar het evenementscherm Azure Media Services Live in uw browser en klik op de koppeling **Player opnieuw laden.** Je moet je stream nu zien in de Preview-speler.

## <a name="set-up-outputs"></a>Uitgangen instellen

Dit deel stelt uw uitvoer in en stelt u in staat om een opname van uw live stream op te slaan.  

> [!NOTE]
> Om deze uitvoer te kunnen streamen, moet het streaming-eindpunt worden uitgevoerd. Zie de latere [sectie Het standaardstreamingeindpunt uitvoeren.](#run-the-default-streaming-endpoint)

1. Selecteer de koppeling **Uitvoer maken** onder de video-viewer **Uitvoer.**
1. Als u wilt, bewerk de naam van de uitvoer in het vak **Naam** naar iets gebruiksvriendelijker, zodat het later gemakkelijk te vinden is.

   ![Uitvoernaam, vak](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de dozen voorlopig met rust.
1. Selecteer **Volgende** om een streaminglocator toe te voegen.
1. Verander de naam van de locator in iets gebruiksvriendelijker, als je wilt.

   ![Naamvak Locator](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Laat al het andere op dit scherm voor nu.
1. Selecteer **Maken**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>De uitvoeruitzending afspelen met Azure Media Player

1. Kopieer de streaming-URL onder de **videospeler Uitvoer.**
1. Open in een webbrowser de [demo van Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Plak de streaming-URL in het **VAK URL** van Azure Media Player.
1. Selecteer de knop **Player bijwerken.**
1. Selecteer het pictogram **Afspelen** in de video om je live stream te bekijken.

## <a name="stop-the-broadcast"></a>De uitzending stoppen

Als je denkt dat je genoeg content hebt gestreamd, stop je de uitzending.

1. Selecteer **Stoppen**in de portal .

1. Selecteer in OBS de knop **Streaming stoppen** in het deelvenster **Besturingselementen.** Deze stap stopt de uitzending van OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>De on-demand uitvoer afspelen met Azure Media Player

De uitvoer die u hebt gemaakt, is nu beschikbaar voor streaming op aanvraag, zolang uw streaming-eindpunt wordt uitgevoerd.

1. Ga naar de aanbieding MediaServices en selecteer **Activa**.
1. Zoek de gebeurtenisuitvoer die u eerder hebt gemaakt en selecteer de koppeling naar het item. De pagina met de uitvoer van activa wordt geopend.
1. Kopieer de streaming-URL onder de videospeler voor het item.
1. Ga terug naar Azure Media Player in de browser en plak de streaming-URL in het VAK URL.
1. Selecteer **Speler bijwerken**.
1. Selecteer het pictogram **Afspelen** in de video om het on-demand-item weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> Stop met de diensten! Nadat u de stappen in deze snelle start hebt voltooid, moet u het live-evenement en het streaming-eindpunt stoppen, of wordt er rekening gebracht voor de tijd dat ze blijven draaien. Als u het live-evenement wilt stoppen, [raadpleegt](#stop-the-broadcast) u de procedure voor de uitzending stoppen, stappen 2 en 3.

Ga als u het streaming-eindpunt stoppen:

1. Selecteer In de aanbieding MediaServices de optie **Streaming-eindpunten**.
2. Selecteer het standaard eindpunt voor streaming dat u eerder bent gestart. Met deze stap wordt de pagina van het eindpunt geopend.
3. Selecteer **Stoppen**.

> [!TIP]
> Als u de elementen niet van deze gebeurtenis wilt verwijderen, moet u ze verwijderen, zodat er geen kosten in rekening worden gebracht voor opslag.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Live-evenementen en live-uitgangen in Media Services](./live-events-outputs-concept.md)
