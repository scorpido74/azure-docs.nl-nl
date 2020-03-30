---
title: Een Live stream van Azure Media Services maken met de portal en Wirecast
description: Meer informatie over het maken van een Live stream van Azure Media Service
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: e5bdd75ca61d53a64f003633d74e3d8f7992a98b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336439"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Een Livestream van Azure Media Services maken met de portal en Wirecast

In deze handleiding aan de slag wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Azure Media Services-account hebt gemaakt.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

In deze quickstart behandelen we:

- Het opzetten van een on-premises encoder met een gratis proefversie van Telestream Wirecast
- Een livestream opzetten
- Live stream-uitgangen instellen
- Een standaardeindpunt voor streaming uitvoeren
- De Azure Media Player gebruiken om de live stream en on-demand-uitvoer te bekijken

Om het simpel te houden, gebruiken we een coderingsvoorinstelling voor Azure Media Services in Wirecast, pass-through cloudcodering en RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Het opzetten van een on-premises encoder met Wirecast

1. Wirecast downloaden en installeren voor uw besturingssysteem ophttps://www.telestream.net
1. Start de applicatie en gebruik je favoriete e-mailadres om het product te registreren.  Houd de toepassing open.
1. U ontvangt een e-mail waarin u wordt gevraagd om uw e-mailadres te verifiëren, dan zal de toepassing de gratis proefperiode starten.
1. AANBEVOLEN: Bekijk de videotutorial in het scherm van de openingstoepassing.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Een livestream van Azure Media Services instellen

1. Zodra u naar het Azure Media Services-account binnen de portal hebt genavigeerd, selecteert u **Live streaming** in de lijst MediaServices.<br/>
![Live streamingkoppeling selecteren](media/live-events-wirecast-quickstart/select-live-streaming.png)<br/>
1. Klik op **Live-evenement toevoegen** om een nieuw live streaming-evenement te maken.<br/>
![Pictogram Live-gebeurtenis toevoegen](media/live-events-wirecast-quickstart/add-live-event.png)<br/>
1. Voer een naam in voor uw nieuwe evenement, zoals *TestLiveEvent* in het veld **Naam van** het live-evenement.<br/>
![Tekstveld met de naam van een livegebeurtenis](media/live-events-wirecast-quickstart/live-event-name.png)<br/>
1. Voer een optionele beschrijving van de gebeurtenis in het veld **Beschrijving** in.
1. Selecteer de **doorgeefknop – geen radioknop voor het coderen van de cloud.**<br/>
![Knop keuzerondje voor cloudcodering](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecteer de **RTMP-keuzerondje.**
1. Zorg ervoor dat de knop **Geen** keuzerondje is geselecteerd voor Live-evenement starten, om te voorkomen dat er kosten in rekening wordt gebracht voor het live-evenement voordat het klaar is.  (Facturering begint zodra het live-evenement is gestart.) ![Knop Live event-keuzerondjes starten](media/live-events-wirecast-quickstart/start-live-event-no.png)<br/>
1. Klik **op De** knop Controleren + maken om de instellingen te controleren.
1. Klik **op de** knop Maken om de live-gebeurtenis te maken. Je wordt dan teruggestuurd naar de weergave van de live-evenementvermelding.
1. Klik op de **link naar het live evenement dat** je zojuist hebt gemaakt. Merk op dat uw evenement is gestopt.
1. Houd deze pagina open in uw browser.  We komen er later op terug.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Een live stream opzetten met Wirecast Studio

1. Ervan uitgaande dat de Wirecast-toepassing nog steeds is geopend, selecteert u **Leeg document maken** in het hoofdmenu en klikt u vervolgens op **Doorgaan**.
![Wirecast startscherm](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Zweef over de eerste laag in het gebied wirecast-lagen.  Klik **op** het pictogram Toevoegen dat wordt weergegeven en selecteer de video-invoer die u wilt streamen.  Het dialoogvenster Master Layer 1 wordt geopend.<br/>
![Pictogram Wirecast toevoegen](media/live-events-wirecast-quickstart/add-icon.png)
1. Selecteer **Video-opname** in het menu en selecteer vervolgens de camera die u wilt gebruiken. Als u een camera selecteert, wordt de weergave van de camera weergegeven in het gebied Voorbeeld.
![Wirecast video shot selectie scherm](media/live-events-wirecast-quickstart/video-shot-selection.png)
1. Plaats de bovendetweede laag in het gebied Wirecast-lagen. Klik **op** het pictogram Toevoegen dat wordt weergegeven en selecteer de audio-invoer die u wilt streamen.  Het dialoogvak Master Layer 2 wordt geopend.
1. Selecteer **Audio-opname** in het menu en selecteer vervolgens de audio-invoer die u wilt gebruiken.
![Wirecast audio shot selectie scherm](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Selecteer **Uitvoerinstellingen**in het hoofdmenu .  Het dialoogvenster Uitvoer wordt weergegeven.
1. Selecteer **Azure Media Services** in de vervolgkeuzelijst uitvoer.  Met de uitvoerinstelling voor Azure Media Services worden de *meeste* uitvoerinstellingen automatisch ingevuld.<br/>
![Scherm wirecast-uitvoerinstellingen](media/live-events-wirecast-quickstart/azure-media-services.png)
1. In de volgende sectie gaat u terug naar Azure Media Services in uw browser om de *invoer-URL* te kopiëren om in de uitvoerinstellingen in te voeren.

### <a name="copy-and-paste-the-input-url"></a>De invoer-URL kopiëren en plakken

1. Klik op de azure mediaservices-pagina van de portal op **Start** om de live streamgebeurtenis te starten. (Facturering begint nu.)<br/>
![Pictogram Start](media/live-events-wirecast-quickstart/start.png)
2. Klik op de **secure/Not secure** toggle om deze in te stellen op **Niet veilig.**  Hiermee wordt het protocol ingesteld op RTMP in plaats van RTMPS.
3. Kopieer de **URL van invoer** naar het klembord.
![Invoer-URL](media/live-events-wirecast-quickstart/input-url.png)
4. Schakel over naar de Wirecast-toepassing en plak de **invoer-URL** in het veld **Adres** in de uitvoerinstellingen.<br/>
![URL van wirecastinvoer](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Klik **op Oké.**

## <a name="setting-up-outputs"></a>Uitvoer instellen

Dit deel stelt uw uitvoer in en stelt u in staat om een opname van uw live stream op te slaan.  

> [!NOTE]
> Om deze uitvoer te streamen, moet het streaming-eindpunt worden uitgevoerd.  Zie Hieronder de sectie standaardstreamingeindpunt uitvoeren.

1. Klik op de koppeling **Uitvoer maken** onder de videoviewer Uitvoer.
1. Als u wilt, bewerk de naam van de uitvoer in het veld **Naam** naar iets gebruiksvriendelijker, zodat het later gemakkelijk te vinden is.
![Veld Uitvoernaam](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de velden voorlopig met rust.
1. Klik op **Volgende** streaminglocator toevoegen.
1. Verander de naam van de locator in iets gebruiksvriendelijker, indien gewenst.
![Naamvan de locator](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Laat al het andere op dit scherm voor nu.
1. Klik **op Maken**.

## <a name="starting-the-broadcast"></a>De uitzending starten

1. Selecteer in Wirecast **Uitvoer > Start / Stoppen met uitzenden > Start Azure Media Services: Azure Media Services** in het hoofdmenu.  Wanneer de stream naar het live-evenement is verzonden, wordt het venster Live in Wirecast weergegeven in de videospeler van het live evenement op de pagina met live gebeurtenissen in Azure Media Services.

   ![Menu-items voor uitzending starten](media/live-events-wirecast-quickstart/start-broadcast.png)

1. Klik op de knop **Ga** onder het voorbeeldvenster om te beginnen met het uitzenden van de video en audio die u hebt geselecteerd voor de Wirecast-lagen.

   ![Knop Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Als er een fout is, probeer dan de speler opnieuw te laden door op de link Opnieuw laden speler boven de speler te klikken.

## <a name="running-the-default-streaming-endpoint"></a>Het standaardeindpunt voor streaming uitvoeren

1. Controleer of je streamingeindpunt wordt uitgevoerd door **Streaming-eindpunten** te selecteren in de lijst Mediaservices. Je wordt naar de streaming endpoints pagina gebracht.<br/>
![Menu-item voor streamingeindpunt](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Als de standaardstatus van streamingeindpunt is gestopt, klikt u op het **standaard** eindpunt voor streaming. Dit brengt u naar de pagina voor dat eindpunt.
1. Klik op **Start**.  Hiermee wordt het streaming-eindpunt gestart.<br/>
![Menu-item voor streamingeindpunt](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-with-azure-media-player"></a>De uitvoeruitzending afspelen met Azure Media Player

1. Kopieer de **URL van streaming** onder de videospeler Uitvoer.
1. Open in een webbrowser de demo Azure Media Playerhttps://ampdemo.azureedge.net/azuremediaplayer.html
1. Plak de **URL van streaming** in het URL-veld van azure mediaplayer.
1. Klik op de knop **Speler bijwerken.**
1. Klik op het **afspeelpictogram** in de video om je live stream te bekijken.

## <a name="stopping-the-broadcast"></a>De uitzending stoppen

Als je denkt dat je genoeg content hebt gestreamd, stop je de uitzending.

1. Klik in Wirecast op de **uitzendknop.**  Dit zal de uitzending van Wirecast stoppen.
1. Klik in de portal op **Stoppen**. U krijgt een waarschuwing dat de live stream te stoppen, maar de output zal nu een on-demand asset.
1. Klik op **Stoppen** in het waarschuwingsbericht. De Azure Media Player toont nu ook een fout omdat de live stream niet meer beschikbaar is.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>De on-demand uitvoer afspelen met de Azure Media Player

De uitvoer die u hebt gemaakt, is nu beschikbaar voor streaming op aanvraag, zolang uw streaming-eindpunt wordt uitgevoerd.

1. Navigeer naar de aanbieding MediaServices en selecteer **Activa**.
1. Zoek de gebeurtenisuitvoer die u eerder hebt gemaakt en klik op de **koppeling naar het item**. De pagina met de uitvoer van activa wordt geopend.
1. Kopieer de **URL streaming** onder de videospeler voor het item.
1. Ga terug naar de Azure Media Player in de browser en plak de **URL streaming** in het URL-veld van azure mediaplayer.
1. Klik **op Speler bijwerken**.
1. Klik op het **afspeelpictogram** in de video om het on-demand-item te bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> Stop met de diensten! Zodra u de stappen in deze quickstart hebt voltooid, moet u het live-evenement en het streaming-eindpunt stoppen of blijft u in rekening worden gebracht voor de tijd dat ze blijven draaien. Als u het live-evenement wilt stoppen, raadpleegt u de uitzending stoppen, de stappen 2 en 3 hierboven.

### <a name="stopping-the-streaming-endpoint"></a>Het streaming-eindpunt stoppen

1. Selecteer In de aanbieding MediaServices de optie **Streaming-eindpunten**.
2. Klik op het **standaard** streaming eindpunt dat u eerder bent gestart. Hiermee wordt de pagina van het eindpunt geopend.
3. Klik op **Stoppen.**  Dit stopt het streaming eindpunt.

> [!TIP]
> Als u de elementen niet uit deze gebeurtenis wilt houden, moet u ze verwijderen om te voorkomen dat er kosten in rekening worden gebracht voor opslag.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Welk artikel is de volgende in volgorde](./live-events-outputs-concept.md)
