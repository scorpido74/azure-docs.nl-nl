---
title: Een livestream van Azure Media Services maken
description: Meer informatie over het maken van een livestream van Azure Media Services met behulp van de portal en Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984994"
---
# <a name="create-an-azure-media-services-live-stream"></a>Een livestream van Azure Media Services maken

Met deze quickstart u een livestream van Azure Media Services maken met behulp van de Azure-portal en Telestream Wirecast. Er wordt van uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure-portal.](https://portal.azure.com/) Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

In deze snelle start behandelen we:

- Het opzetten van een on-premises encoder met een gratis proefperiode van Telestream Wirecast.
- Het opzetten van een live stream.
- Het instellen van live stream-uitgangen.
- Het uitvoeren van een standaard streaming eindpunt.
- Azure Media Player gebruiken om de live stream en on-demand-uitvoer te bekijken.

Om het simpel te houden, gebruiken we een coderingsvoorinstelling voor Azure Media Services in Wirecast, pass-through cloudcodering en RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Een on-premises encoder instellen met Wirecast

1. Wirecast downloaden en installeren voor uw besturingssysteem op de [Telestream website.](https://www.telestream.net)
1. Start de applicatie en gebruik je favoriete e-mailadres om het product te registreren. Houd de toepassing open.
1. Controleer in de e-mail die u ontvangt uw e-mailadres. Dan start de applicatie met de gratis proefperiode.
1. Aanbevolen: Bekijk de videotutorial in het scherm van de openingstoepassing.

## <a name="set-up-an-azure-media-services-live-stream"></a>Een livestream van Azure Media Services instellen

1. Ga naar het Azure Media Services-account binnen de portal en selecteer **Live streaming** in de lijst **Media Services.**

   ![Live streaming link](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Selecteer **Live-evenement toevoegen** om een nieuw live streaming-evenement te maken.

   ![Pictogram Live-gebeurtenis toevoegen](media/live-events-wirecast-quickstart/add-live-event.png)
1. Voer een naam in voor uw nieuwe evenement, zoals *TestLiveEvent,* in het vak **Naam van een Live-evenement.**

   ![Vak Naam van live-evenement](media/live-events-wirecast-quickstart/live-event-name.png)
1. Voer een optionele beschrijving van de gebeurtenis in het vak **Beschrijving** in.
1. Selecteer de **optie Pass-through – geen cloudcodering.**

   ![Optie voor cloudcodering](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP.**
1. Zorg ervoor dat de optie **Geen** is geselecteerd voor **Start live-evenement,** om te voorkomen dat er kosten in rekening wordt gebracht voor het live-evenement voordat het klaar is. (Facturering begint wanneer het live-evenement wordt gestart.)

   ![Optie Live-evenement starten](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Selecteer de knop **Controleren + maken** om de instellingen te controleren.
1. Selecteer de knop **Maken** om de live-gebeurtenis te maken. Je wordt vervolgens teruggezet naar de lijst met live-evenementen.
1. Selecteer de link naar het live-evenement dat u zojuist hebt gemaakt. Merk op dat uw evenement is gestopt.
1. Houd deze pagina open in uw browser. We komen er later op terug.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Een live stream opzetten met Wirecast Studio

1. Selecteer in de toepassing Wirecast **Leeg document maken** in het hoofdmenu en selecteer **Doorgaan**.

   ![Wirecast startscherm](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Zweef over de eerste laag in het gebied **wirecast-lagen.**  Selecteer het pictogram **Toevoegen** dat wordt weergegeven en selecteer de video-invoer die u wilt streamen.

   ![Pictogram Wirecast toevoegen](media/live-events-wirecast-quickstart/add-icon.png)

   Het dialoogvenster **Laag 1 master** wordt geopend.
1. Selecteer **Video-opname** in het menu en selecteer vervolgens de camera die u wilt gebruiken.

   ![Voorbeeldgebied voor video-opname](media/live-events-wirecast-quickstart/video-shot-selection.png)

   De weergave van de camera wordt weergegeven in het voorbeeldgebied.
1. Plaats de bovendetweede laag in het gebied **Wirecast-lagen.** Selecteer het pictogram **Toevoegen** dat wordt weergegeven en selecteer de audio-invoer die u wilt streamen. Het dialoogvenster **Laag 2 master** wordt geopend.
1. Selecteer **Audio-opname** in het menu en selecteer vervolgens de audio-invoer die u wilt gebruiken.

   ![Ingangen voor audio-opname](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Selecteer **Uitvoerinstellingen**in het hoofdmenu . Het dialoogvenster **Een uitvoerdoel selecteren** wordt weergegeven.
1. Selecteer **Azure Media Services** in de vervolgkeuzelijst **Bestemming.** De uitvoerinstelling voor Azure Media Services vult automatisch de *meeste* uitvoerinstellingen in.

   ![Scherm wirecast-uitvoerinstellingen](media/live-events-wirecast-quickstart/azure-media-services.png)


In de volgende procedure gaat u terug naar Azure Media Services in uw browser om de invoer-URL te kopiëren om in de uitvoerinstellingen te gaan:

1. Selecteer op de pagina Azure Media Services van de portal de optie **Start** om de live streamgebeurtenis te starten. (Facturering begint nu.)

   ![Pictogram Start](media/live-events-wirecast-quickstart/start.png)
2. Stel de **schakelschakelaar Veilig/Niet in** op **Niet veilig.** Met deze stap wordt het protocol ingesteld op RTMP in plaats van RTMPS.
3. Kopieer de URL in het vak Url van **invoer** naar het klembord.

   ![Invoer-URL](media/live-events-wirecast-quickstart/input-url.png)
4. Schakel over naar de Wirecast-toepassing en plak de **invoer-URL** in het vak **Adres** in de uitvoerinstellingen.

   ![URL van wirecastinvoer](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Selecteer **OK**.

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

## <a name="start-the-broadcast"></a>De uitzending starten

1. Selecteer in Wirecast De optie Start van **uitvoer** > **/ Stoppen** > met uitzenden**Start Azure Media Services: Azure Media Services** in het hoofdmenu.

   ![Menu-items voor uitzending starten](media/live-events-wirecast-quickstart/start-broadcast.png)

   Wanneer de stream naar het live-evenement is verzonden, wordt het venster **Live** in Wirecast weergegeven in de videospeler op de pagina met live gebeurtenissen in Azure Media Services.

1. Selecteer de knop **Ga** onder het voorbeeldvenster om te beginnen met het uitzenden van de video en audio die u hebt geselecteerd voor de Wirecast-lagen.

   ![Knop Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Als er een fout is, probeer dan de speler opnieuw te laden door de koppeling **Herladen speler** boven de speler te selecteren.

## <a name="run-the-default-streaming-endpoint"></a>Het standaardeindpunt voor streaming uitvoeren

1. Selecteer **Streaming-eindpunten** in de lijst Mediaservices.

   ![Menu-item Streaming Eindpunten](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Als de standaardstatus van streamingeindpunt is gestopt, selecteert u deze. Met deze stap gaat u naar de pagina voor dat eindpunt.
1. Selecteer **Starten**.
   
   ![Knop Start voor het streamingeindpunt](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>De uitvoeruitzending afspelen met Azure Media Player

1. Kopieer de streaming-URL onder de **videospeler Uitvoer.**
1. Open in een webbrowser de [demo van Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Plak de streaming-URL in het **VAK URL** van Azure Media Player.
1. Selecteer de knop **Player bijwerken.**
1. Selecteer het pictogram **Afspelen** in de video om je live stream te bekijken.

## <a name="stop-the-broadcast"></a>De uitzending stoppen

Als je denkt dat je genoeg content hebt gestreamd, stop je de uitzending.

1. Selecteer in Wirecast de knop **Broadcast.** Deze stap stopt de uitzending van Wirecast.
1. Selecteer **Stoppen**in de portal . U krijgt dan een waarschuwing dat de live stream zal stoppen, maar de uitvoer wordt nu een on-demand asset.
1. Selecteer **Stoppen** in het waarschuwingsbericht. Azure Media Player vertoont nu een fout, omdat de live stream niet meer beschikbaar is.

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
