---
title: Een Azure Media Services live stream maken met de portal en Wirecast
description: Meer informatie over het maken van een Azure media service-Live Stream
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927624"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Een Azure Media Services live stream maken met de portal en Wirecast

In deze hand leiding wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Azure Media Services-account hebt gemaakt.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

In deze Snelstartgids worden de volgende acties behandeld:

- Een on-premises encoder instellen met een gratis proef versie van Telestream Wirecast
- Een live stream instellen
- Live stream-uitvoer instellen
- Een standaard streaming-eind punt uitvoeren
- De Azure Media Player gebruiken om de Live Stream en de uitvoer op aanvraag weer te geven

Om alles te blijven gebruiken, gaan we gebruikmaken van een coderings voorinstelling voor Azure Media Services in Wirecast, Pass-Through-Cloud codering en RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Een on-premises encoder instellen met Wirecast

1. Down load en installeer Wirecast voor uw besturings systeem op https://www.telestream.net
1. Start de toepassing en gebruik uw favoriete e-mail adres om het product te registreren.  Laat de toepassing geopend.
1. U ontvangt een e-mail bericht waarin u wordt gevraagd uw e-mail adres te verifiëren. vervolgens wordt de gratis proef versie van de toepassing gestart.
1. Aanbevolen: Bekijk de video-zelf studie in het scherm toepassing openen.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Een Azure Media Services Live Stream instellen

1. Nadat u naar het Azure Media Services-account in de portal hebt genavigeerd, selecteert u **live streamen** in de Media Services-lijst.
1. Klik op **live-gebeurtenis toevoegen** om een nieuwe gebeurtenis voor live streaming te maken.
1. Voer een naam in voor uw nieuwe gebeurtenis, zoals *TestLiveEvent* in het veld met de **naam** van de live-gebeurtenis.
1. Voer een optionele beschrijving in van de gebeurtenis in het veld **Beschrijving** .
1. Selecteer het keuze rondje **Pass-Through-geen Cloud encoding** .
1. Selecteer het keuze rondje **RTMP** . 
1. Zorg ervoor dat **er geen** keuze rondje is geselecteerd voor starten van live gebeurtenis, om te voor komen dat er wordt gefactureerd voor de live gebeurtenis voordat deze klaar is.  (Facturering begint zodra de live-gebeurtenis is gestart.)
1. Klik op de knop **beoordeling + maken** om de instellingen te controleren.
1. Klik op de knop **maken** om de live-gebeurtenis te maken. Vervolgens keert u terug naar de weer gave Live Event.
1. Klik op de **koppeling naar de live-gebeurtenis** die u zojuist hebt gemaakt. U ziet dat uw gebeurtenis is gestopt.
1. Laat deze pagina in uw browser geopend.  We zullen het later terugkomen.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Een live stream instellen met Wirecast Studio

1. Ervan uitgaande dat u nog steeds de toepassing Wirecast hebt geopend, selecteert u **lege documenten maken** in het hoofd menu en klikt u vervolgens op **door gaan**.
1. Beweeg de muis aanwijzer over de eerste laag in het gebied Wirecast lagen.  Klik op het pictogram **toevoegen** dat wordt weer gegeven en selecteer de video-invoer die u wilt streamen.  Het dialoog venster Master Layer 1 wordt geopend.
1. Selecteer **video-opname** in het menu en selecteer vervolgens de camera die u wilt gebruiken. De weer gave van de camera wordt weer gegeven in het gedeelte preview.
1. Beweeg de muis aanwijzer over de tweede laag in het gebied Wirecast lagen. Klik op het pictogram **toevoegen** dat wordt weer gegeven en selecteer de audio-invoer die u wilt streamen.  Het dialoog venster hoofddoel laag 2 wordt geopend.
1. Selecteer **audio-opname** in het menu en selecteer vervolgens de audio-invoer die u wilt gebruiken. 
1. Selecteer in het hoofd menu de optie **uitvoer instellingen**.  Het dialoog venster uitvoer wordt weer gegeven.
1. Selecteer **Azure Media Services** in de vervolg keuzelijst uitvoer.  Met de instelling voor uitvoer voor Azure Media Services worden de *meeste* uitvoer instellingen automatisch ingevuld.
1. In de volgende sectie gaat u terug naar Azure Media Services in uw browser om de *invoer-URL* te kopiëren en in te voeren in de uitvoer instellingen.

### <a name="copy-and-paste-the-input-url"></a>De invoer-URL kopiëren en plakken

1. Klik op de pagina Azure Media Services van de portal op **starten** om de gebeurtenis live stream te starten. (Facturering wordt nu gestart.)
2. Klik op de wissel knop **beveiligd/niet beveiligd** om deze in te stellen op **niet beveiligd**.  Hiermee wordt het protocol ingesteld op RTMP in plaats van RTMP.
3. Kopieer de **invoer-URL** naar het klem bord.
4. Ga naar de Wirecast-toepassing en plak de **invoer-URL** in het veld **adres** in de uitvoer instellingen.
5. Klik op **OK**.

## <a name="setting-up-outputs"></a>Uitvoer instellen

Met dit onderdeel worden uw uitvoer ingesteld en kunt u een opname van uw Live Stream opslaan.  

> [!NOTE]
> Als u deze uitvoer wilt streamen, moet het streaming-eind punt actief zijn.  Zie de sectie het standaard streaming-eind punt uitvoeren hieronder.

1. Klik op de koppeling **uitvoer maken** onder de video-viewer voor uitvoer.
1. Indien gewenst kunt u de naam van de uitvoer in het veld **naam** bewerken in een duidelijkere gebruikers vriendelijker, zodat u deze later eenvoudig kunt vinden.
1. Laat de rest van de velden nu alleen behouden.
1. Klik op **volgende** streaming-Locator toevoegen.
1. Wijzig de naam van de Locator in iets meer gebruikers vriendelijk, indien gewenst.
1. Zorg ervoor dat de rest van dit scherm alleen voor Taan wordt gewijzigd.
1. Klik op **Create**.

## <a name="starting-the-broadcast"></a>De uitzending starten

1. Selecteer in Wirecast de optie **uitvoer > starten/stoppen met uitzenden > start Azure Media Services: Azure Media Services** in het hoofd menu.  Wanneer de stroom naar de live-gebeurtenis is verzonden, wordt het Live-venster in Wirecast weer gegeven in de video speler live event op de pagina live event in Azure Media Services.

1. Klik op de knop **Go** onder het voorbeeld venster om de video en audio die u hebt geselecteerd voor de Wirecast-lagen te verzenden.

> [!TIP]
> Als er een fout optreedt, kunt u de speler opnieuw laden door te klikken op de koppeling opnieuw laden in de speler.

## <a name="running-the-default-streaming-endpoint"></a>Het standaard streaming-eind punt wordt uitgevoerd

1. Zorg ervoor dat het streaming-eind punt wordt uitgevoerd door **streaming-eind punten** te selecteren in de lijst Media Services. U gaat naar de pagina streaming-eind punten.
1. Als de standaard status van het streaming-eind punt is gestopt, klikt u op het **standaard** streaming-eind punt. Hiermee gaat u naar de pagina voor dat eind punt.
1. Klik op **Start**.  Hiermee wordt het streaming-eind punt gestart.

## <a name="play-the-output-broadcast-with-azure-media-player"></a>De uitvoer uitzending afspelen met Azure Media Player

1. Kopieer de **streaming-URL** onder de video speler voor uitvoer. 
1. Open in een webbrowser de demo Azure Media Player https://ampdemo.azureedge.net/azuremediaplayer.html
1. Plak de **streaming-URL** in het URL-veld van de Azure Media Player.
1. Klik op de knop **Update speler** .
1. Klik op het **afspeel** pictogram op de video om uw live stream te bekijken.

## <a name="stopping-the-broadcast"></a>De uitzending stoppen

Wanneer u denkt dat u voldoende inhoud hebt gestreamd, stopt u de uitzending.

1. Klik in Wirecast op de knop **broadcast** .  Hiermee wordt de uitzending van Wirecast gestopt.
1. Klik in de portal op **stoppen**. Er wordt een waarschuwing weer gegeven dat de Live Stream stopt, maar de uitvoer wordt nu een Asset op aanvraag.
1. Klik op **Stop** in het waarschuwings bericht. In het Azure Media Player wordt ook een fout weer gegeven, omdat de live stream niet meer beschikbaar is.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>De uitvoer op aanvraag afspelen met de Azure Media Player

De uitvoer die u hebt gemaakt, is nu beschikbaar voor streaming op aanvraag zolang het streaming-eind punt wordt uitgevoerd.

1. Ga naar de vermelding Media Services en selecteer **activa**.
1. Zoek de gebeurtenis uitvoer die u eerder hebt gemaakt en klik op de **koppeling naar de Asset**. De pagina Asset-uitvoer wordt geopend.
1. Kopieer de **streaming-URL** onder de video speler voor de Asset.
1. Ga terug naar de Azure Media Player in de browser en plak de **streaming-URL** in het URL-veld van de Azure Media Player.
1. Klik op **Update speler**.
1. Klik op het pictogram **afspelen** op de video om de Asset op aanvraag weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

  > [!IMPORTANT]
  > Stop de services. Wanneer u de stappen in deze Snelstartgids hebt voltooid, moet u ervoor zorgen dat u de live-gebeurtenis en het streaming-eind punt kunt stoppen of dat u wordt gefactureerd voor de tijd waarop ze actief blijven. Als u de live-gebeurtenis wilt stoppen, raadpleegt u de uitzending stoppen, stappen 2 en 3 hierboven.

### <a name="stopping-the-streaming-endpoint"></a>Het streaming-eind punt stoppen

1. Selecteer in de lijst Media Services **streaming-eind punten**.
2. Klik op het **standaard** streaming-eind punt dat u eerder hebt gestart. Hiermee opent u de pagina van het eind punt.
3. Klik op **stoppen**.  Hiermee wordt het streaming-eind punt gestopt.

>[!TIP]
>Als u de assets van deze gebeurtenis niet wilt blijven gebruiken, moet u deze verwijderen om te voor komen dat er wordt gefactureerd voor opslag.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Welk artikel bevindt zich hierna in de reeks](./live-events-outputs-concept.md)
