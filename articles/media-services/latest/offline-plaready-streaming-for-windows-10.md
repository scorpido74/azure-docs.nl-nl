---
title: Offline PlayReady-streaming configureren met Azure Media Services v3
description: In dit artikel ziet u hoe u uw Azure Media Services-account configureert voor het offline streamen van PlayReady voor Windows 10.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2019
ms.author: willzhan
ms.openlocfilehash: 151aadadb5674f7f144d42b1f9d5115501ed381d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887228"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offline PlayReady-streaming voor Windows 10 met Media Services v3

Azure Media Services ondersteunen offline downloaden/afspelen met DRM-beveiliging. Dit artikel heeft betrekking op offline ondersteuning van Azure Media Services voor Windows 10/PlayReady-clients. In de volgende artikelen lees je over de offline modus ondersteuning voor iOS/FairPlay en Android/Widevine-apparaten:

- [Offline FairPlay-streaming voor iOS](offline-fairplay-for-ios.md)
- [Offline Widevine Streaming voor Android](offline-widevine-for-android.md)

> [!NOTE]
> Offline DRM wordt alleen gefactureerd voor het indienen van één aanvraag voor een licentie wanneer u de inhoud downloadt. Eventuele fouten worden niet gefactureerd.

## <a name="overview"></a>Overzicht

Deze sectie geeft enige achtergrond op offline modus afspelen, vooral waarom:

* In sommige landen/regio's is de beschikbaarheid en/of bandbreedte op internet nog beperkt.Gebruikers kunnen ervoor kiezen om eerst te downloaden om inhoud in een hoge resolutie te kunnen bekijken voor een bevredigende kijkervaring. In dit geval, vaker, het probleem is niet de beschikbaarheid van het netwerk, maar het is beperkte bandbreedte van het netwerk. OTT/OVP-providers vragen om ondersteuning voor offline modus.
* Zoals bekendgemaakt op Netflix 2016 Q3 aandeelhoudersconferentie, het downloaden van inhoud is een "vaak gevraagde functie", en "we staan open voor het" zei Reed Hastings, Netflix CEO.
* Sommige aanbieders van inhoud kunnen de levering van DRM-licenties buiten de grens van een land/regio weigeren. Als een gebruiker naar het buitenland moet reizen en toch inhoud wil bekijken, is offline downloaden nodig.
 
De uitdaging waar we voor staan bij het implementeren van de offline modus is het volgende:

* MP4 wordt ondersteund door veel spelers, encoder tools, maar er is geen binding tussen MP4 container en DRM;
* Op de lange termijn is CFF met CENC de weg te gaan. Echter, vandaag de dag, de tools / speler ondersteuning ecosysteem is er nog niet. We hebben vandaag een oplossing nodig.
 
Het idee is: smooth streaming[(PIFF)](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)bestandsformaat met H264/AAC heeft een binding met PlayReady (AES-128 CTR). Individuele vloeiende streaming .ismv bestand (ervan uitgaande dat audio is muxed in video) is zelf een fMP4 en kan worden gebruikt voor het afspelen. Als een vloeiende streaming-inhoud door PlayReady-versleuteling gaat, wordt elk .ismv-bestand een PlayReady-beveiligde gefragmenteerde MP4. We kunnen kiezen voor een .ismv-bestand met de gewenste bitrate en het hernoemen als .mp4 om te downloaden.

Er zijn twee opties voor het hosten van de PlayReady beschermde MP4 voor progressieve download:

* Men kan deze MP4 in dezelfde container/ media service asset en gebruik maken van Azure Media Services streaming eindpunt voor progressieve download;
* Men kan SAS locator gebruiken voor progressieve download rechtstreeks van Azure Storage, het omzeilen van Azure Media Services.
 
U twee soorten PlayReady-licentielevering gebruiken:

* PlayReady-licentieleveringsservice in Azure Media Services;
* PlayReady-licentieservers worden overal gehost.

Hieronder vindt u twee sets testassets, waarvan de eerste PlayReady-licentielevering in AMS gebruikt, terwijl de tweede met mijn PlayReady-licentieserver wordt gehost op een Azure VM:

Asset #1:

* Url voor progressieve download:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS):[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Asset #2:

* Url voor progressieve download:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (on-premises):[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Voor het testen van afspeeltoepassingen gebruikten we een Universele Windows-toepassing op Windows 10. In [Windows 10 Universal monsters](https://github.com/Microsoft/Windows-universal-samples), is er een basisspeler monster genaamd Adaptive Streaming [Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Alles wat we moeten doen is om de code voor ons toe te voegen om gedownloade video te halen en te gebruiken als de bron, in plaats van adaptieve streaming bron. De wijzigingen zijn in knop klik gebeurtenis handler:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Offline modus afspelen van PlayReady beschermde fMP4](./media/offline-playready-for-windows/offline-playready1.jpg)

Aangezien de video onder PlayReady-beveiliging valt, kan de schermafbeelding de video niet opnemen.

Samengevat hebben we de offlinemodus bereikt op Azure Media Services:

* Contenttranscoding en PlayReady-versleuteling kunnen worden uitgevoerd in Azure Media Services of andere hulpprogramma's.
* Inhoud kan worden gehost in Azure Media Services of Azure Storage voor progressief downloaden.
* PlayReady-licentielevering kan afkomstig zijn van Azure Media Services of elders;
* De voorbereide vloeiende streaming content kan nog steeds worden gebruikt voor online streaming via DASH of glad met PlayReady als de DRM.

## <a name="next-steps"></a>Volgende stappen

[Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)
