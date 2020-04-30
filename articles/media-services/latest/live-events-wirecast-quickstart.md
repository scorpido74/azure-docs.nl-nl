---
title: Een Azure Media Services live stream maken
description: Meer informatie over het maken van een Azure Media Services live stream met behulp van de portal en Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80984994"
---
# <a name="create-an-azure-media-services-live-stream"></a>Een Azure Media Services live stream maken

Deze Quick Start helpt u bij het maken van een Azure Media Services live stream met behulp van de Azure Portal en de Telestream-Wirecast. Hierbij wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure-Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

In deze Snelstartgids worden de volgende acties behandeld:

- Instellen van een on-premises Encoder met een gratis proef versie van Telestream Wirecast.
- Een live stream instellen.
- De uitvoer van live streams instellen.
- Een standaard streaming-eind punt wordt uitgevoerd.
- Gebruik Azure Media Player om de Live Stream en de uitvoer op aanvraag weer te geven.

Om alles te blijven gebruiken, wordt er een coderings voorinstelling gebruikt voor Azure Media Services in Wirecast, Pass-Through-Cloud codering en RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Een on-premises encoder instellen met behulp van Wirecast

1. Down load en installeer Wirecast voor uw besturings systeem op de [Telestream-website](https://www.telestream.net).
1. Start de toepassing en gebruik uw favoriete e-mail adres om het product te registreren. Laat de toepassing geopend.
1. Verifieer uw e-mail adres in de e-mail die u ontvangt. Vervolgens wordt de gratis proef versie van de toepassing gestart.
1. Aanbevolen: Bekijk de video-zelf studie in het scherm toepassing openen.

## <a name="set-up-an-azure-media-services-live-stream"></a>Een Azure Media Services Live Stream instellen

1. Ga naar het Azure Media Services-account in de portal en selecteer vervolgens **live streamen** in de **Media Services** -lijst.

   ![Live streaming-koppeling](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Selecteer **Live Event toevoegen** om een nieuwe gebeurtenis voor live streaming te maken.

   ![Pictogram live-gebeurtenis toevoegen](media/live-events-wirecast-quickstart/add-live-event.png)
1. Voer een naam in voor uw nieuwe gebeurtenis, zoals *TestLiveEvent*, in het vak **naam van live-gebeurtenis** .

   ![Vak live-evenement naam](media/live-events-wirecast-quickstart/live-event-name.png)
1. Voer een optionele beschrijving in van de gebeurtenis in het vak **Beschrijving** .
1. Selecteer de optie **Pass-Through-geen Cloud encoding** .

   ![Optie voor Cloud versleuteling](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP** .
1. Zorg ervoor dat de optie **Nee** is geselecteerd voor **starten van live gebeurtenis**, om te voor komen dat er wordt gefactureerd voor de live-gebeurtenis voordat deze klaar is. (Facturering begint wanneer de live gebeurtenis wordt gestart.)

   ![Live-gebeurtenis optie starten](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Selecteer de knop **beoordeling + maken** om de instellingen te controleren.
1. Selecteer de knop **maken** om de live-gebeurtenis te maken. Vervolgens keert u terug naar de aanbieding van Live-gebeurtenissen.
1. Selecteer de koppeling naar de live-gebeurtenis die u zojuist hebt gemaakt. U ziet dat uw gebeurtenis is gestopt.
1. Laat deze pagina in uw browser geopend. We worden later teruggebeld.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Een live stream instellen met behulp van Wirecast Studio

1. Selecteer in de Wirecast-toepassing **lege documenten maken** in het hoofd menu en selecteer vervolgens **door gaan**.

   ![Start scherm Wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Beweeg de muis aanwijzer over de eerste laag in het gebied **Wirecast lagen** .  Selecteer het pictogram **toevoegen** dat wordt weer gegeven en selecteer de video-invoer die u wilt streamen.

   ![Pictogram voor Wirecast toevoegen](media/live-events-wirecast-quickstart/add-icon.png)

   Het dialoog venster **Master laag 1** wordt geopend.
1. Selecteer **video-opname** in het menu en selecteer vervolgens de camera die u wilt gebruiken.

   ![Preview-gebied voor video-opname](media/live-events-wirecast-quickstart/video-shot-selection.png)

   De weer gave van de camera wordt weer gegeven in het gedeelte preview.
1. Beweeg de muis aanwijzer over de tweede laag in het gebied **Wirecast lagen** . Selecteer het pictogram **toevoegen** dat wordt weer gegeven en selecteer de audio-invoer die u wilt streamen. Het dialoog venster **Master laag 2** wordt geopend.
1. Selecteer **audio-opname** in het menu en selecteer vervolgens de audio-invoer die u wilt gebruiken.

   ![Invoer voor audio-opname](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Selecteer in het hoofd menu de optie **uitvoer instellingen**. Het dialoog venster **een uitvoer doel selecteren** wordt weer gegeven.
1. Selecteer **Azure Media Services** in de vervolg keuzelijst **doel** . Met de instelling voor uitvoer voor Azure Media Services worden de *meeste* uitvoer instellingen automatisch ingevuld.

   ![Wirecast uitvoer instellingen](media/live-events-wirecast-quickstart/azure-media-services.png)


In de volgende procedure gaat u terug naar Azure Media Services in uw browser om de invoer-URL te kopiëren en in te voeren in de uitvoer instellingen:

1. Selecteer op de pagina Azure Media Services van de portal **Start** om de gebeurtenis live stream te starten. (Facturering wordt nu gestart.)

   ![Pictogram starten](media/live-events-wirecast-quickstart/start.png)
2. Stel de **veilige/niet-beveiligde** wissel knop in op **niet beveiligd**. Met deze stap stelt u het protocol in op RTMP in plaats van RTMP.
3. Kopieer de URL naar het klem bord in het vak **invoer-URL** .

   ![Invoer-URL](media/live-events-wirecast-quickstart/input-url.png)
4. Ga naar de Wirecast-toepassing en plak de **invoer-URL** in het vak **adres** van de uitvoer instellingen.

   ![Invoer-URL voor Wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Selecteer **OK**.

## <a name="set-up-outputs"></a>Uitvoer instellen

Met dit onderdeel worden uw uitvoer ingesteld en kunt u een opname van uw Live Stream opslaan.  

> [!NOTE]
> Als u deze uitvoer wilt streamen, moet het streaming-eind punt actief zijn. Zie de sectie een [standaard streaming-eind punt uitvoeren](#run-the-default-streaming-endpoint) .

1. Selecteer de koppeling **uitvoer maken** onder de video-viewer voor **uitvoer** .
1. Als u wilt, kunt u de naam van de uitvoer in het vak **naam** bewerken in een duidelijker gebruiks vriendelijk, zodat u deze later eenvoudig kunt vinden.
   
   ![Vak uitvoer naam](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de vakken nu alleen behouden.
1. Selecteer **volgende** om een streaming-Locator toe te voegen.
1. Wijzig de naam van de Locator in iets meer gebruikers vriendelijk, indien gewenst.
   
   ![Vak voor de naam van de Locator](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Zorg ervoor dat de rest van dit scherm alleen voor Taan wordt gewijzigd.
1. Selecteer **Maken**.

## <a name="start-the-broadcast"></a>De uitzending starten

1. Selecteer in Wirecast **uitvoer** > **starten/stoppen met broadcasten** > **starten Azure Media Services: Azure Media Services** in het hoofd menu.

   ![Menu-items in de uitzending starten](media/live-events-wirecast-quickstart/start-broadcast.png)

   Wanneer de stroom is verzonden naar de live-gebeurtenis, wordt het **Live** -venster in Wirecast weer gegeven in de video speler op de pagina live event in azure Media Services.

1. Selecteer de knop **Go** onder het voorbeeld venster om de video en audio die u hebt geselecteerd voor de Wirecast-lagen te verzenden.

   ![De knop Wirecast go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Als er een fout optreedt, laadt u de speler opnieuw door de koppeling **opnieuw laden** in de speler te selecteren.

## <a name="run-the-default-streaming-endpoint"></a>Het standaard streaming-eind punt uitvoeren

1. Selecteer **streaming-eind punten** in de lijst met Media Services.

   ![Menu-item voor streaming-eind punten](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Als de standaard status van het streaming-eind punt is gestopt, selecteert u deze. Met deze stap gaat u naar de pagina voor dat eind punt.
1. Selecteer **Starten**.
   
   ![De knop Start voor het streaming-eind punt](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>De uitvoer uitzending afspelen met behulp van Azure Media Player

1. Kopieer de streaming-URL onder de video speler voor **uitvoer** .
1. Open in een webbrowser de Azure Media Player- [demo](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Plak de streaming-URL in het vak **URL** van Azure Media Player.
1. Selecteer de knop voor het bijwerken van de **speler** .
1. Selecteer het pictogram **afspelen** op de video om uw live stream te bekijken.

## <a name="stop-the-broadcast"></a>De uitzending stoppen

Wanneer u denkt dat u voldoende inhoud hebt gestreamd, stopt u de uitzending.

1. Selecteer in Wirecast de knop **broadcast** . Met deze stap wordt de uitzending van Wirecast gestopt.
1. Selecteer in de portal **stoppen**. Vervolgens wordt er een waarschuwing weer gegeven dat de live stream wordt gestopt, maar de uitvoer wordt nu een Asset op aanvraag.
1. Selecteer **stoppen** in het waarschuwings bericht. Azure Media Player wordt nu een fout weer gegeven, omdat de live stream niet meer beschikbaar is.

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
