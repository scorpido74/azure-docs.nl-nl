---
title: Releasenotities voor Azure Media Services | Microsoft Documenten
description: In dit artikel wordt gesproken over de v2-releasenotities van Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: a55ef4f814c0d13a6efbeeb8cd9a5565d2869432
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183292"
---
# <a name="azure-media-services-release-notes"></a>Notities vrijgeven van Azure Media Services

Deze releasenotes voor Azure Media Services geven een overzicht van wijzigingen ten opzichte van eerdere releases en bekende problemen.

> [!NOTE]
> Er worden geen nieuwe functies meer aan Media Services v2 toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

We willen van onze klanten horen, zodat we ons kunnen richten op het oplossen van problemen die u aangaan. Als u een probleem wilt melden of vragen wilt stellen, dient u een bericht in het [MSDN-forum]van Azure Media Services . 

## <a name="known-issues"></a><a id="issues"/>Bekende problemen
### <a name="media-services-general-issues"></a><a id="general_issues"/>Algemene problemen mediaservices

| Probleem | Beschrijving |
| --- | --- |
| Verschillende veelvoorkomende HTTP-headers worden niet geleverd in de REST API. |Als u Media Services-toepassingen ontwikkelt met behulp van de REST API, vindt u dat sommige veelvoorkomende HTTP-koptekstvelden (waaronder CLIENT-REQUEST-ID, REQUEST-ID en RETURN-CLIENT-REQUEST-ID) niet worden ondersteund. De headers worden toegevoegd in een toekomstige update. |
| Procentuele codering is niet toegestaan. |Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`bouwen van URL's voor de streaming-inhoud (bijvoorbeeld). Om deze reden is procentcodering niet toegestaan. De waarde van de eigenschap Naam kan geen van de volgende tekens hebben [die het percentage-encoding-gereserveerd](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)eeraan: !*'();:@&=+$,/?%#[]". Ook kan er slechts een "." voor de bestandsnaam extensie. |
| De ListBlobs-methode die deel uitmaakt van de Azure Storage SDK-versie 3.x, mislukt. |Media Services genereert SAS URL's op basis van de versie [2012-02-12.](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) Als u de Storage SDK wilt gebruiken om blobs in een blobcontainer weer te geven, gebruikt u de methode [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) die deel uitmaakt van de Storage SDK-versie 2.x. |
| Het beperkingsmechanisme voor MediaServices beperkt het resourcegebruik voor toepassingen die overmatige verzoeken indienen bij de service. De service kan de HTTP-statuscode 'Service niet beschikbaar' 503 retourneren. |Zie voor meer informatie de beschrijving van de 503 HTTP-statuscode in [foutcodes van Media Services](media-services-encoding-error-codes.md). |
| Wanneer u entiteiten opvraagt, wordt een limiet van 1.000 entiteiten tegelijk geretourneerd omdat de openbare REST-versie 2 queryresultaten beperkt tot 1.000 resultaten. |Gebruik Overslaan en nemen (.NET)/top (REST) zoals beschreven in [dit .NET-voorbeeld](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) en [dit voorbeeld van de REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Sommige clients kunnen een probleem met een herhalingstag tegenkomen in het Smooth Streaming-manifest. |Zie voor meer informatie [deze sectie.](media-services-deliver-content-overview.md#known-issues) |
| Media Services .NET SDK-objecten kunnen niet worden geserialiseerd en werken daarom niet met Azure Cache voor Redis. |Als u het SDK AssetCollection-object probeert te serialiseren om het toe te voegen aan Azure Cache voor Redis, wordt er een uitzondering gemaakt. |
|De REST API reageert met een foutmelding met de tekst "Het filter kan niet worden geopend door deze versie van REST Api" wanneer u een filter op asset- of accountniveau probeert te krijgen.|Het filter is gemaakt of gewijzigd met een nieuwere API-versie dan wordt gebruikt om te proberen het filter te krijgen. Dit kan gebeuren als twee API-versies worden gebruikt door code of tools die door de klant worden gebruikt.  De beste oplossing hier is om de code of tools te upgraden om de nieuwere of de twee API-versies te gebruiken.|

## <a name="rest-api-version-history"></a><a id="rest_version_history"/>REST API-versiegeschiedenis
Zie de [API-verwijzing]naar de API voor Azure Media Services REST voor informatie over de versiegeschiedenis van de Media Services REST API.

## <a name="february-2020"></a>Februari 2020

Sommige analysemediaprocessors worden buiten gebruik gesteld. Zie voor de pensioendatums het onderwerp [oudere onderdelen.](legacy-components.md)

## <a name="september-2019"></a>September 2019

### <a name="deprecation-of-media-processors"></a>Afschaffing van mediaverwerkers

We kondigen afschaffing van *Azure Media Indexer* en *Azure Media Indexer 2 Preview*aan. [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze oudere mediaprocessors.

Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md)

Zie ook [Migreren van Azure Media Indexer en Azure Media Indexer 2 naar Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augustus 2019

### <a name="deprecation-of-media-processors"></a>Afschaffing van mediaverwerkers

We kondigen de afschaffing aan van de *Media-processors van Windows Azure Media Encoder* (WAME) en Azure Media *Encoder* (AME). Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md)

Zie [WAME migreren naar Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) en [Migreren naar Media Encoder Standard voor](https://go.microsoft.com/fwlink/?LinkId=2101335)meer informatie.

## <a name="march-2019"></a>Maart 2019

De functie Media Hyperlapse Preview van Azure Media Services is afgeschaft.

## <a name="december-2018"></a>December 2018

De Media Hyperlapse Preview-functie van Azure Media Services wordt binnenkort buiten gebruik gesteld. Vanaf 19 december 2018 zal Media Services geen wijzigingen of verbeteringen meer aanbrengen in Media Hyperlapse. Op 29 maart 2019 is het niet meer beschikbaar.

## <a name="october-2018"></a>Oktober 2018

### <a name="cmaf-support"></a>CMAF-ondersteuning

CMAF- en 'cbcs'-encryptieondersteuning voor Apple HLS (iOS 11+) en MPEG-DASH-spelers die CMAF ondersteunen.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatuursprites

U mediaservices nu gebruiken om Web VTT-miniatuursprites te genereren met behulp van onze v2-API's. Zie [Een miniatuursprite genereren](generate-thumbnail-sprite.md)voor meer informatie.

## <a name="july-2018"></a>Juli 2018

Met de laatste servicerelease zijn er kleine opmaakwijzigingen in de foutberichten die door de service worden geretourneerd wanneer een taak mislukt, met betrekking tot de manier waarop deze wordt opgesplitst in twee of meer regels.

## <a name="may-2018"></a>Mei 2018 

Vanaf 12 mei 2018 ondersteunen live kanalen niet langer het RTP/MPEG-2 transportstream ingest protocol. Migreer van RTP/MPEG-2 naar RTMP of gefragmenteerde MP4 -protocollen (Smooth Streaming).

## <a name="october-2017-release"></a>Release van oktober 2017
> [!IMPORTANT] 
> Media Services is de ondersteuning voor Azure Access Control Service-verificatiesleutels aan het afkeuren. Op 22 juni 2018 u niet langer verifiëren met de Back-end mediaservices via code met behulp van Access Control Service-sleutels. U moet uw code bijwerken om Azure Active Directory (Azure AD) te gebruiken per [verificatie op basis van Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) Let op waarschuwingen over deze wijziging in de Azure-portal.

### <a name="updates-for-october-2017"></a>Updates voor oktober 2017
#### <a name="sdks"></a>SDK's
* De .NET SDK is bijgewerkt om Azure AD-verificatie te ondersteunen. Ondersteuning voor Access Control Service-verificatie is verwijderd uit de nieuwste .NET SDK op Nuget.org om een snellere migratie naar Azure AD aan te moedigen. 
* De JAVA SDK is bijgewerkt om Azure AD-verificatie te ondersteunen. Ondersteuning voor Azure AD-verificatie is toegevoegd aan de Java SDK. Zie [Aan de slag met de Java-client SDK voor Azure Media Services voor](media-services-java-how-to-use.md) informatie over het gebruik van de Java SDK met Media Services.

#### <a name="file-based-encoding"></a>Op bestanden gebaseerde codering
* U nu de Premium Encoder gebruiken om uw inhoud te coderen op de H.265 high-efficiency video coding (HEVC) video codec. Er is geen prijseffect als u H.265 verkiest boven andere codecs, zoals H.264. Zie Voorwaarden voor [onlineservices](https://azure.microsoft.com/support/legal/)voor informatie over HEVC-octrooilicenties.
* Voor bronvideo die is gecodeerd met de H.265 (HEVC) videocodec, zoals video die is vastgelegd met iOS11 of GoPro Hero 6, u nu de Premium Encoder of de Standard Encoder gebruiken om die video's te coderen. Zie [Voorwaarden voor onlineservices](https://azure.microsoft.com/support/legal/)voor informatie over octrooilicenties.
* Voor inhoud die audiotracks in meerdere talen bevat, moeten de taalwaarden correct worden gelabeld volgens de bijbehorende bestandsindelingsspecificatie (bijvoorbeeld ISO MP4). Vervolgens u de Standaard Encoder gebruiken om de inhoud te coderen voor streaming. De resulterende streaming locator geeft een overzicht van de beschikbare audiotalen.
* De Standard Encoder ondersteunt nu twee nieuwe audio-only systeem presets, "AAC Audio" en "AAC Goede Kwaliteit Audio." Beide produceren stereo geavanceerde audio codering (OC) output, bij bit snelheden van 128 Kbps en 192 Kbps, respectievelijk.
* De Premium Encoder ondersteunt nu QuickTime/MOV-bestandsindelingen als invoer. De videocodec moet een van de [Apple ProRes-typen zijn die in dit GitHub-artikel worden vermeld.](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats) De audio moet OC of pulse code modulatie (PCM) zijn. De Premium Encoder ondersteunt bijvoorbeeld geen DVC/DVCPro-video verpakt in QuickTime/MOV-bestanden als invoer. De Standard Encoder ondersteunt deze videocodecs.
* De volgende bug fixes werden gemaakt in encoders:

    * U nu taken indienen met behulp van een invoeritem. Nadat deze taken zijn voltooid, u het item wijzigen (bijvoorbeeld bestanden toevoegen, verwijderen of de naam van bestanden binnen het item wijzigen) en extra taken indienen.
    * De kwaliteit van JPEG-miniaturen geproduceerd door de Standard Encoder is verbeterd.
    * De Standard Encoder verwerkt invoermetadata en miniatuurgeneratie beter in video's met zeer korte duur.
    * Verbeteringen aan de H.264-decoder die in de Standaard Encoder wordt gebruikt, elimineren bepaalde zeldzame artefacten. 

#### <a name="media-analytics"></a>Media Analytics
Algemene beschikbaarheid van de Azure Media Redactor: deze mediaprocessor voert anonymisatie uit door de gezichten van geselecteerde personen te vervagen en is ideaal voor gebruik in scenario's voor openbare veiligheid en nieuwsmedia. 

Voor een overzicht over deze nieuwe processor, zie [deze blogpost](https://azure.microsoft.com/blog/azure-media-redactor/). Zie [Gezichten redact met Azure Media Analytics](media-services-face-redaction.md)voor informatie over documentatie en instellingen.



## <a name="june-2017-release"></a>Release juni 2017

Media Services ondersteunt nu [Azure AD-gebaseerde verificatie.](media-services-use-aad-auth-to-access-ams-api.md)

> [!IMPORTANT]
> Momenteel ondersteunt Media Services het verificatiemodel van access control service. De autorisatie van de Toegangscontroleservice wordt afgeschaft op 1 juni 2018. We raden u aan om zo snel mogelijk naar het Azure Active Directory-verificatiemodel te migreren.

## <a name="march-2017-release"></a>Release maart 2017

U nu de standaardencoder gebruiken om [een bitrateladder automatisch](media-services-autogen-bitrate-ladder-with-mes.md) te genereren door de voorinstellingstekenreeks 'Adaptive Streaming' op te geven wanneer u een coderingstaak maakt. Als u een video wilt coderen voor streaming met Media Services, gebruikt u de voorinstelling 'Adaptieve streaming'. Als u een coderingsvoorinstelling voor uw specifieke scenario wilt aanpassen, u beginnen met [deze voorinstellingen.](media-services-mes-presets-overview.md)

U nu Media Encoder Standard of Media Encoder Premium Workflow gebruiken om [een coderingstaak te maken die fMP4-segmenten genereert.](media-services-generate-fmp4-chunks.md) 

## <a name="february-2017-release"></a>Release februari 2017

Vanaf 1 april 2017 wordt elke taakrecord in uw account ouder dan 90 dagen automatisch verwijderd, samen met de bijbehorende taakrecords. Verwijdering vindt plaats, zelfs als het totale aantal records lager is dan het maximumquotum. Als u de taak-/taakgegevens wilt archiveren, u de code gebruiken die is beschreven in [Assets en gerelateerde entiteiten beheren met de Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Release januari 2017

In Media Services vertegenwoordigt een streamingeindpunt een streamingservice die inhoud rechtstreeks kan leveren aan een clientplayertoepassing of aan een content delivery network (CDN) voor verdere distributie. Media Services biedt ook naadloze integratie van azure Content Delivery Network. De uitgaande stream van een StreamingEndpoint-service kan een live stream, een video op aanvraag of een progressieve download van uw asset in uw Media Services-account zijn. Elk Media Services-account bevat een standaard streaming-eindpunt. Onder het account kunnen extra streaming eindpunten worden gemaakt. 

Er zijn twee versies van streaming endpoints, 1.0 en 2.0. Vanaf 10 januari 2017 bevatten nieuw aangemaakte Media Services-accounts het standaardstreamingeindpunt van versie 2.0. Extra streaming eindpunten die je aan dit account toevoegt zijn ook versie 2.0. Deze wijziging heeft geen invloed op bestaande accounts. Bestaande streaming eindpunten zijn versie 1.0 en kunnen worden geüpgraded naar versie 2.0. Er zijn gedrags-, facturerings- en functiewijzigingen met deze wijziging. Zie [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Overzicht van streaming-eindpunten) voor meer informatie.

Vanaf de 2.15-versie heeft Media Services de volgende eigenschappen toegevoegd aan de entiteit streaming eindpunt:

* CdnProvider 
* CdnProfiel
* FreeTrialEndTime 
* StreamingEndpointVersie 

Zie [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)voor meer informatie over deze eigenschappen. 

## <a name="december-2016-release"></a>Release december 2016

 U Media Services nu gebruiken om toegang te krijgen tot telemetrie-/metrische gegevens voor zijn services. U de huidige versie van Media Services gebruiken om telemetriegegevens te verzamelen voor live kanaal-, streaming-eindpunten en archiefentiteiten. Zie [Telemetrie mediaservices](media-services-telemetry-overview.md)voor meer informatie.

## <a name="july-2016-release"></a><a id="july_changes16"/>Release juli 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Updates van het manifestbestand (*. ISM) gegenereerd door coderingstaken
Wanneer een coderingstaak wordt ingediend bij Media Encoder Standard of Media Encoder Premium, genereert de coderingstaak een [streamingmanifestbestand](media-services-deliver-content-overview.md) (*.ism) in het uitvoeritem. Met de laatste servicerelease is de syntaxis van dit streaming manifestbestand bijgewerkt.

> [!NOTE]
> De syntaxis van het streamingmanifest (.ism) bestand is gereserveerd voor intern gebruik. Het is onderhevig aan verandering in toekomstige releases. Wijzig of manipuleer de inhoud van dit bestand niet.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Een nieuw klantenmanifest (*. ISMC)-bestand wordt gegenereerd in het uitvoerelement wanneer een coderingstaak een of meer MP4-bestanden uitvoert
Te beginnen met de nieuwste servicerelease, na het voltooien van een coderingstaak die een of meer MP4-bestanden genereert, bevat het uitvoeritem ook een streaming clientmanifest (*.ismc) bestand. Het .ismc-bestand helpt de prestaties van dynamische streaming te verbeteren. 

> [!NOTE]
> De syntaxis van het clientmanifest (.ismc)-bestand is gereserveerd voor intern gebruik. Het is onderhevig aan verandering in toekomstige releases. Wijzig of manipuleer de inhoud van dit bestand niet.
> 
> 

Voor meer informatie, zie [deze blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Bekende problemen
Sommige clients kunnen een probleem met een herhalingstag tegenkomen in het Smooth Streaming-manifest. Zie voor meer informatie [deze sectie.](media-services-deliver-content-overview.md#known-issues)

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Release april 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services introduceerde Media Analytics voor krachtige video-intelligentie. Zie Media [Services Analytics-overzicht](media-services-analytics-overview.md)voor meer informatie.

### <a name="apple-fairplay-preview"></a>Apple FairPlay (preview)
U mediaservices nu gebruiken om uw HLS-content (Live Streaming) dynamisch te versleutelen met Apple FairPlay. U ook de mediaservices service voor licentieverlening gebruiken om FairPlay-licenties aan klanten te leveren. Zie 'Azure Media Services gebruiken om uw HLS-inhoud te streamen die is beveiligd met Apple FairPlay'.

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Release februari 2016
De nieuwste versie van de Media Services SDK voor .NET (3.5.3) bevat een Google Widevine-gerelateerde bug fix. Het was onmogelijk om AssetDeliveryPolicy te hergebruiken voor meerdere activa versleuteld met Widevine. Als onderdeel van deze bugfix is de volgende eigenschap toegevoegd aan de SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Release januari 2016
Codering gereserveerde eenheden werden omgedoopt tot verwarring met encoder namen te verminderen.

De gereserveerde eenheden Basic, Standard en Premium werden omgedoopt tot gereserveerde eenheden S1, S2 en S3. Klanten die gereserveerde standaardcoderingseenheden gebruiken, zien S1 vandaag als het label in de Azure-portal (en in de factuur). Klanten die Standard en Premium gebruiken, zien respectievelijk de labels S2 en S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Release december 2015

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder afschaffing aankondiging

 Media Encoder zal worden afgeschaft vanaf ongeveer 12 maanden na de release van Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure-SDK voor PHP
Het Azure SDK-team heeft een nieuwe versie van het [Azure SDK voor PHP-pakket](https://github.com/Azure/azure-sdk-for-php) gepubliceerd met updates en nieuwe functies voor Media Services. In het bijzonder ondersteunt de Media Services SDK voor PHP nu de nieuwste [functies voor contentbescherming.](media-services-content-protection-overview.md) Deze functies zijn dynamische versleuteling met AES en DRM (PlayReady en Widevine) met en zonder tokenbeperkingen. Het ondersteunt ook schalen [codering eenheden](media-services-dotnet-encoding-units.md).

Zie voor meer informatie:

* Met de volgende [codevoorbeelden](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) u snel aan de slag:
  * **vodworkflow_aes.php**: Dit PHP-bestand laat zien hoe u AES-128 dynamische encryptie en de key delivery service gebruikt. Het is gebaseerd op de .NET-steekproef uitgelegd in [Gebruik AES-128 dynamische encryptie en de key delivery service](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Dit PHP-bestand laat zien hoe je PlayReady dynamische encryptie en de licentiedelivery service gebruiken. Het is gebaseerd op het .NET-voorbeeld dat wordt uitgelegd in [PlayReady gebruiken en/of Widevine dynamische algemene versleuteling.](media-services-protect-with-playready-widevine.md)
  * **scale_encoding_units.php**: Dit PHP-bestand laat zien hoe u gereserveerde eenheden schalen.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Release november 2015
 Media Services biedt nu de Widevine-licentieleveringsservice in de cloud. Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Zie ook [deze tutorial](media-services-protect-with-playready-widevine.md) en de [GitHub repository.](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 

Widevine-licentieservices van Media Services zijn in preview. Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Release oktober 2015
Media Services is nu live in de volgende datacenters: Brazilië Zuid, India West, India Zuid, en India Central. U nu de Azure-portal gebruiken om [Media Service-accounts](media-services-portal-create-account.md) te maken en verschillende taken uit te voeren die zijn beschreven op de webpagina van Media [Services-documentatie.](https://azure.microsoft.com/documentation/services/media-services/) Live Encoding is niet ingeschakeld in deze datacenters. Verder zijn niet alle soorten codering gereserveerde eenheden beschikbaar in deze datacenters.

* Brazilië Zuid: Alleen standaard en basic codering gereserveerde eenheden zijn beschikbaar.
* India West, India South en India Central: Alleen Basic codering gereserveerde eenheden zijn beschikbaar.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Release september 2015
Media Services biedt nu de mogelijkheid om zowel video on demand als live streams te beschermen met Widevine modulaire DRM-technologie. U de volgende partners voor bezorgdiensten gebruiken om u te helpen Widevine-licenties te leveren:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
U kunt de [.NET SDK van Media Services](https://www.nuget.org/packages/windowsazure.mediaservices/) (vanaf versie 3.5.1) of de REST API gebruiken om AssetDeliveryConfiguration voor het gebruik van Widevine te configureren. 
* Media Services heeft ondersteuning toegevoegd voor Apple ProRes-video's. U nu uw QuickTime-bronvideobestanden uploaden die Apple ProRes of andere codecs gebruiken. Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* U nu Media Encoder Standard gebruiken om subclipping en live archiefextractie uit te brengen. Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* De volgende filterupdates zijn uitgevoerd: 
  
  * U nu het Apple HLS-formaat gebruiken met een filter met alleen audio. U deze update gebruiken om een audio-only track te verwijderen door (audio-only=false) op te geven in de URL.
  * Wanneer u filters voor uw assets definieert, u nu meerdere (maximaal drie) filters in één URL combineren.
    
    Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services ondersteunt nu I-frames in HLS-versie 4. I-frame ondersteuning optimaliseert fast-forward en terugspoelen operaties. Standaard bevatten alle HLS-versie 4-uitgangen de I-frame-afspeellijst (EXT-X-I-FRAME-STREAM-INF).
Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Release augustus 2015
* De Media Services SDK voor de Java-versie 0.8.0 release en nieuwe monsters zijn nu beschikbaar. Zie voor meer informatie:
    
* De Azure Media Player is bijgewerkt met ondersteuning voor multi-audiostream. Voor meer informatie, zie [deze blogpost](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Release juli 2015
* De algemene beschikbaarheid van Media Encoder Standard werd aangekondigd. Voor meer informatie, zie [deze blogpost](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard maakt gebruik van voorinstellingen, zoals beschreven in [deze sectie](https://go.microsoft.com/fwlink/?LinkId=618336). Wanneer u een voorinstelling voor 4K-codeert, krijgt u het type Premium gereserveerde eenheid. Zie [Codering schalen voor](media-services-scale-media-processing-overview.md)meer informatie.
* Live real-time bijschriften werden gebruikt met Media Services en de Media Player. Voor meer informatie, zie [deze blogpost](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.4.0.0. De volgende updates werden gemaakt: 

* Ondersteuning werd geïmplementeerd voor live archief. U geen actief downloaden dat een live archief bevat.
* Ondersteuning is geïmplementeerd voor dynamische filters.
* Functionaliteit is geïmplementeerd zodat gebruikers een opslagcontainer kunnen bewaren terwijl ze een asset verwijderen.
* Bug fixes werden gemaakt met betrekking tot het opnieuw proberen beleid in kanalen.
* Media Encoder Premium Workflow is ingeschakeld.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Release juni 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.3.0.0. De volgende updates werden gemaakt: 

* Er is ondersteuning toegevoegd voor de Detectiespec van OpenId Connect.
* Er is ondersteuning toegevoegd voor het overschrijven van sleutels aan de kant van de identiteitsprovider.

Als u een identiteitsprovider gebruikt die een Detectiedocument van OpenID Connect blootlegt (zoals Azure AD, Google en Salesforce doen), u Media Services instrueren om ondertekeningssleutels te verkrijgen voor validatie van JSON Web Tokens (JWTs) van de detectiespec OpenID Connect. 

Zie [Json-websleutels gebruiken van de detectiespec OpenID Connect om te werken met JWT-verificatie in Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)voor meer informatie.

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Release mei 2015
De volgende nieuwe functies werden aangekondigd:

* [Een voorbeeld van live codering met Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamisch manifest](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Release april 2015
### <a name="general-media-services-updates"></a>Updates van General Media Services
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) werd aangekondigd.
* Vanaf de Media Services REST 2.10 worden kanalen die zijn geconfigureerd om een RTMP (Real-Time Messaging Protocol) in te nemen, gemaakt met primaire en secundaire inname-URL's. Zie [Kanaalinnameconfiguraties voor](media-services-live-streaming-with-onprem-encoders.md#channel_input)meer informatie.
* Azure Media Indexer is bijgewerkt.
* Ondersteuning voor de Spaanse taal werd toegevoegd.
* Er is een nieuwe configuratie voor de XML-indeling toegevoegd.

Voor meer informatie, zie [deze blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.2.0.0. De volgende updates werden gemaakt:

* Wijziging doorbreken: TokenRestrictionTemplate.Issuer en TokenRestrictionTemplate.Audience zijn gewijzigd in een tekenreekstype.
* Er zijn updates gemaakt met betrekking tot het maken van aangepaste beleid voor het opnieuw proberen.
* Bug fixes werden gemaakt met betrekking tot het uploaden en downloaden van bestanden.
* De klasse MediaServicesCredentials accepteert nu primaire en secundaire eindpunten voor toegangscontrole om te verifiëren.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Release maart 2015
### <a name="general-media-services-updates"></a>Updates van General Media Services
* Media Services biedt nu content delivery network integratie. Om de integratie te ondersteunen, is de eigenschap CdnEnabled toegevoegd aan StreamingEndpoint. CdnEnabled kan worden gebruikt met REST API's vanaf versie 2.9. Zie [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)voor meer informatie. CdnEnabled kan worden gebruikt met de .NET SDK vanaf versie 3.1.0.2. Zie [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)voor meer informatie.
* De Media Encoder Premium Workflow werd aangekondigd. Zie [Premium-codering introduceren in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)voor meer informatie.

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Februari 2015 release
### <a name="general-media-services-updates"></a>Updates van General Media Services
De Media Services REST API is nu versie 2.9. Vanaf deze versie u de integratie van het Content Delivery Network inschakelen met streaming eindpunten. Zie [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx)voor meer informatie.

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Release januari 2015
### <a name="general-media-services-updates"></a>Updates van General Media Services
De algemene beschikbaarheid van contentbescherming met dynamische encryptie werd aangekondigd. Zie [Media Services verbetert de streamingbeveiliging met de algemene beschikbaarheid van DRM-technologie](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)voor meer informatie.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.1.0.1.

In deze versie is de standaardmicrosoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate constructor als verouderd gemarkeerd. De nieuwe constructor neemt TokenType als argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Release december 2014
### <a name="general-media-services-updates"></a>Updates van General Media Services
* Sommige updates en nieuwe functies zijn toegevoegd aan de Media Indexer. Zie releasenotes van [Azure Media Indexer versie 1.1.6.7 voor](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)meer informatie.
* Er is een nieuwe REST API toegevoegd die u gebruiken om gereserveerde eenheden bij te werken. Zie [EncodingReservedUnitType with REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)voor meer informatie.
* CORS-ondersteuning is toegevoegd voor de belangrijkste bezorgservice.
* Er zijn prestatieverbeteringen aangebracht in het opvragen van autorisatiebeleidsopties.
* In het Datacenter van China is de url voor [de levering](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) van sleutels nu per klant (net als in andere datacenters).
* Hls automatische doelduur is toegevoegd. Bij live streaming wordt HLS altijd dynamisch verpakt. Media Services berekent standaard automatisch de hls-segmentverpakkingsverhouding (FragmentsPerSegment) op basis van het hoofdframeinterval (KeyFrameInterval). Deze methode wordt ook wel aangeduid als een groep foto's (GOP) die wordt ontvangen van de levende encoder. Zie [Werken met mediaservices live streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx)voor meer informatie.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) is nu versie 3.1.0.0. De volgende updates werden gemaakt:

* De .NET SDK-afhankelijkheid is geüpgraded naar het .NET 4.5 Framework.
* Er is een nieuwe API toegevoegd die u gebruiken om gereserveerde eenheden bij te werken. Zie [Gereserveerd eenheidstype bijwerken en gereserveerde eenheden bijwerken met .NET](media-services-dotnet-encoding-units.md).
* JWT-ondersteuning voor tokenverificatie is toegevoegd. Zie [JWT-tokenverificatie in Media Services en dynamische versleuteling voor](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)meer informatie.
* Relatieve verschuivingen voor BeginDatum en Vervaldatum in de PlayReady-licentiesjabloon zijn toegevoegd.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Release november 2014
* U Media Services nu gebruiken om live FMP4-inhoud (Smooth Streaming) in te nemen via een SSL-verbinding. Als u SSL wilt innemen, moet u de inname van DE URL bijwerken naar HTTPS. Op dit moment ondersteunt Media Services geen SSL met aangepaste domeinen. Zie Werken met Live [Streaming van Azure Media Services](https://msdn.microsoft.com/library/azure/dn783466.aspx)voor meer informatie over live streaming.
* Momenteel u geen RTMP-livestream opnemen via een SSL-verbinding.
* U alleen via SSL streamen als het streamingeindpunt van waaruit u uw inhoud levert, is gemaakt na 10 september 2014. Als uw streaming-URL's zijn gebaseerd op de streamingeindpunten die na 10 september 2014 zijn gemaakt, bevat de URL 'streaming.mediaservices.windows.net' (de nieuwe indeling). Streaming-URL's die 'origin.mediaservices.windows.net' (de oude indeling) bevatten, ondersteunen geen SSL. Als uw URL in de oude indeling is en u via SSL wilt streamen, [maakt u een nieuw streamingeindpunt.](media-services-portal-manage-streaming-endpoints.md) Als u uw inhoud wilt streamen via SSL, gebruikt u URL's op basis van het nieuwe streamingeindpunt.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
De Media Services SDK voor .NET-extensies is nu versie 2.0.0.3.

De Media Services SDK voor .NET is nu versie 3.0.0.8. De volgende updates werden gemaakt:

* Refactoring werd geïmplementeerd in de beleidsklassen opnieuw proberen.
* Er is een tekenreeks voor gebruikersagents toegevoegd aan HTTP-aanvraagkoppen.
* Er is een nuget-herstelbuildstap toegevoegd.
* Scenariotests werden vastgesteld om x509-cert uit repository te gebruiken.
* Validatie-instellingen zijn toegevoegd voor wanneer het kanaal en de streaming-update worden bijgewerkt.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nieuwe GitHub-repository om Voorbeelden van Media Services te hosten
Voorbeelden staan in de [Media Services-voorbeelden GitHub-repository.](https://github.com/Azure/Azure-Media-Services-Samples)

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Release september 2014
De Media Services REST metadata is nu versie 2.7. Zie de [API-referentie voor Media Services REST voor](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)meer informatie over de nieuwste REST-updates.

De Media Services SDK voor .NET is nu versie 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Wijzigingen doorbreken
* Origin is omgedoopt tot [StreamingEndpoint].
* Er is een wijziging aangebracht in het standaardgedrag wanneer u de Azure-portal gebruikt om MP4-bestanden te coderen en vervolgens te publiceren.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nieuwe functies/scenario's die deel uitmaken van de algemene beschikbaarheidsrelease
* De Media Indexer media processor werd geïntroduceerd. Zie [Mediabestanden indexeren met de Media-indexer](https://msdn.microsoft.com/library/azure/dn783455.aspx)voor meer informatie.
* U de entiteit [StreamingEndpoint] gebruiken om aangepaste namen voor domein (host) toe te voegen.
  
    Als u een aangepaste domeinnaam wilt gebruiken als het streamingeindpunt voor Media Services, voegt u aangepaste hostnamen toe aan uw streaming-eindpunt. Gebruik de REST-API's van Media Services of de .NET SDK om aangepaste hostnamen toe te voegen.
  
    De volgende overwegingen zijn van toepassing:
  
  * U moet eigenaar zijn van de aangepaste domeinnaam.
  * Het eigendom van de domeinnaam moet worden gevalideerd door Media Services. Als u het domein wilt valideren, maakt u een CName waarmee het mediaservicesaccountid wordt toegewezen om DNS mediaservices-dns-zone te verifiëren.
  * U moet een andere CName maken die de aangepaste hostnaam (bijvoorbeeld sports.contoso.com) aan de hostnaam van uw Media Services StreamingEndpoint (bijvoorbeeld amstest.streaming.mediaservices.windows.net) toewijst.

    Zie de eigenschap CustomHostNames in het artikel [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) voor meer informatie.

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nieuwe functies/scenario's die deel uitmaken van de openbare preview-release
* Voorbeeld van live streaming. Zie [Werken met mediaservices live streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx)voor meer informatie.
* Belangrijke bezorgservice. Zie [AES-128 dynamische versleuteling en de key delivery service gebruiken](https://msdn.microsoft.com/library/azure/dn783457.aspx)voor meer informatie.
* AES dynamische encryptie. Zie [AES-128 dynamische versleuteling en de key delivery service gebruiken](https://msdn.microsoft.com/library/azure/dn783457.aspx)voor meer informatie.
* PlayReady-licentieleveringsservice. 
* PlayReady dynamische encryptie. 
* Media Services PlayReady-licentiesjabloon. Zie het overzicht van de [Media Services PlayReady-licentiesjabloon]voor meer informatie.
* Opslagversleutelde assets streamen. Zie [Inhoud met opslagbeheer streamen voor](https://msdn.microsoft.com/library/azure/dn783451.aspx)meer informatie .

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Release augustus 2014
Wanneer u een asset codeert, wordt een uitvoerelement geproduceerd wanneer de coderingstaak is voltooid. Tot deze release produceerde de Media Services Encoder metadata over output assets. Te beginnen met deze release produceert de encoder ook metadata over input assets. Zie [Metagegevens invoeren] en [metagegevens van uitvoer]voor meer informatie.

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Release juli 2014
De volgende bugfixes zijn gemaakt voor de Azure Media Services Packager en Encryptor:

* Wanneer een live archiefasset naar HLS wordt verzonden, wordt alleen audio afgespeeld: dit probleem is opgelost en nu kunnen zowel audio als video worden afgespeeld.
* Wanneer een asset is verpakt in HLS en AES 128-bits envelopversleuteling, spelen de verpakte streams niet af op Android-apparaten: deze bug is opgelost en de verpakte stream speelt terug op Android-apparaten die HLS ondersteunen.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Release mei 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Updates van General Media Services
U nu [dynamische verpakkingen] gebruiken om HLS versie 3 te streamen. Als u HLS-versie 3 wilt streamen, voegt u de volgende indeling toe aan het pad van de oorsprongszoeker: * .ism/manifest(format=m3u8-aapl-v3). Voor meer informatie, zie [dit forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamische verpakking ondersteunt nu ook het leveren van HLS (versie 3 en versie 4) versleuteld met PlayReady op basis van Smooth Streaming statisch versleuteld met PlayReady. Zie Smooth Streaming met [PlayReady beveiligen voor](https://msdn.microsoft.com/library/azure/dn189154.aspx)informatie over het versleutelen van vloeiend streamen met PlayReady.

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.0.0.5. De volgende updates werden gemaakt:

* Snelheid en veerkracht zijn beter wanneer u mediaassets uploadt en downloadt.
* Er zijn verbeteringen aangebracht in de logica voor nieuwe technieken en het hanteren van tijdelijke uitzonderingen: 
  
  * Tijdelijke foutdetectie en logica voor het opnieuw proberen zijn verbeterd voor uitzonderingen die worden veroorzaakt wanneer u bestanden opvraagt, opslaat en bestanden uploadt of downloadt. 
  * Wanneer u webuitzonderingen krijgt (bijvoorbeeld tijdens een tokenaanvraag voor Access Control Service), mislukken fatale fouten nu sneller.

Zie [Logica opnieuw proberen in de Media Services SDK voor .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Januari/februari 2014 releases
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 en 3.0.0.3
De wijzigingen in 3.0.0.1 en 3.0.0.2 omvatten:

* Problemen met betrekking tot het gebruik van LINQ-query's met OrderBy-instructies zijn opgelost.
* Testoplossingen in [GitHub] werden opgesplitst in op eenheden gebaseerde tests en scenariotests.

Zie de [releases Media Services .NET SDK 3.0.0.1 en 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html)voor meer informatie over de wijzigingen.

De volgende wijzigingen zijn aangebracht in versie 3.0.0.3:

* Azure-opslagafhankelijkheden zijn geüpgraded naar versie 3.0.3.0.
* Een probleem met achterwaartse compatibiliteit is opgelost voor 3.0. *.* Releases.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Release december 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> De 3.0.x.x-releases zijn niet achterwaarts compatibel met 2.4.x.x-releases.
> 
> 

De nieuwste versie van de Media Services SDK is nu 3.0.0.0. U het nieuwste pakket downloaden van NuGet of de bits van [GitHub]krijgen.

Vanaf de Media Services SDK-versie 3.0.0.0 u de [Azure AD Access Control Service-tokens](https://msdn.microsoft.com/library/hh147631.aspx) opnieuw gebruiken. Zie voor meer informatie de sectie 'Access Control Service-tokens voor hergebruik' in [Verbinding maken met Media Services met de Media Services SDK voor .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-extensies 2.0.0.0
 De Media Services .NET SDK-extensies zijn een reeks uitbreidingsmethoden en helperfuncties die uw code vereenvoudigen en gemakkelijker te ontwikkelen met Media Services. U de nieuwste bits van [Media Services .NET SDK-extensies](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)krijgen.

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Release november 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Media Services .NET SDK wijzigingen
Vanaf deze versie verwerkt de Media Services SDK voor .NET tijdelijke foutfouten die kunnen optreden wanneer er aanwordt verwezen naar de API-laag Media Services REST.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Release augustus 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Media Services PowerShell-cmdlets die zijn opgenomen in Azure SDK-hulpprogramma's
De volgende Media Services PowerShell-cmdlets zijn nu opgenomen in [Azure SDK-hulpprogramma's:](https://github.com/Azure/azure-sdk-tools)

* Get-AzureMediaServices 

    Bijvoorbeeld: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Bijvoorbeeld: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Bijvoorbeeld: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Bijvoorbeeld: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Release juni 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Wijzigingen in Media Services
De volgende wijzigingen in deze sectie zijn updates die zijn opgenomen in de releases van Media Services van juni 2013:

* Mogelijkheid om meerdere opslagaccounts te koppelen aan een Media Service-account. 
    * StorageAccount
    * Asset.StorageAccountName en Asset.StorageAccount
* Mogelijkheid om Job.Priority bij te werken. 
* Meldingsgerelateerde entiteiten en eigenschappen: 
    * FunctiemeldingAbonnement
    * NotificationEndPoint
    * Taak
* Asset.Uri (Asset.Uri) 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Media Services .NET SDK wijzigingen
De volgende wijzigingen zijn opgenomen in de Media Services SDK-releases van juni 2013. De nieuwste Media Services SDK is beschikbaar op GitHub.

* Vanaf versie 2.3.0.0 ondersteunt de Media Services SDK het koppelen van meerdere opslagaccounts aan een Media Services-account. De volgende API's ondersteunen deze functie:
  
    * IStorageAccount-type
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts, eigenschap
    * StorageAccount, eigenschap
    * StorageAccountName, eigenschap
  
      Zie [Media Services-assets beheren voor meerdere opslagaccounts](https://msdn.microsoft.com/library/azure/dn271889.aspx)voor meer informatie .
* Api's die verband houden met meldingen. Vanaf versie 2.2.0.0 u luisteren naar azure queue-opslagmeldingen. Zie [Meldingen van de taakvan Media Services verwerken](https://msdn.microsoft.com/library/azure/dn261241.aspx)voor meer informatie .
  
    * Microsoft.WindowsAzure.MediaServices.Client.iJob.JobNotificationSubscriptions, eigenschap
    * Microsoft.WindowsAzure.MediaServices.Type Client.INotificationEndPoint
    * Microsoft.WindowsAzure.MediaServices.client.iJobNotificationSubscription type
    * Microsoft.WindowsAzure.MediaServices.client.NotificationEndPointCollection type
    * Microsoft.WindowsAzure.MediaServices.client.NotificationEndPointType type
* Afhankelijkheid van de opslagclient SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Afhankelijkheid van OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Release december 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Media Services .NET SDK wijzigingen
* IntelliSense: Ontbrekende IntelliSense documentatie is toegevoegd voor vele soorten.
* Microsoft.Practices.TransientFaultHandling.Core: Er is een probleem opgelost waarbij de SDK nog steeds afhankelijk was van een oude versie van deze assemblage. De SDK verwijst nu naar versie 5.1.1209.1 van deze assemblage.

Oplossingen voor problemen die zijn gevonden in de SDK van november 2012:

* IAsset.Locators.Count: Deze telling wordt nu correct gerapporteerd op nieuwe IAsset-interfaces nadat alle locators zijn verwijderd.
* IAssetFile.ContentFileSize: Deze waarde is nu correct ingesteld na een upload door IAssetFile.Upload(bestandspad).
* IAssetFile.ContentFileSize: deze eigenschap kan nu worden ingesteld wanneer u een assetbestand maakt. Het werd voorheen alleen gelezen.
* IAssetFile.Upload(bestandspad): er is een probleem opgelost waarbij deze synchrone uploadmethode de volgende fout gaf toen meerdere bestanden naar het item werden geüpload. De fout was "Server kan de aanvraag niet verifiëren. Zorg ervoor dat de waarde van de autorisatiekop correct is gevormd, inclusief de handtekening."
* IAssetFile.UploadAsync: Er is een probleem opgelost dat het gelijktijdig uploaden van bestanden beperkte tot vijf bestanden.
* IAssetFile.UploadProgressChanged: Deze gebeurtenis wordt nu geleverd door de SDK.
* IAssetFile.DownloadAsync(tekenreeks, BlobTransferClient, ILocator, CancellationToken): Deze methode overbelasting is nu voorzien.
* IAssetFile.DownloadAsync: Er is een probleem opgelost dat het gelijktijdig downloaden van bestanden beperkte tot vijf bestanden.
* IAssetFile.Delete(): er is een probleem opgelost waarbij het verwijderen van oproepen een uitzondering kan maken als er geen bestand is geüpload voor het IAssetFile.
* Taken: Er is een probleem opgelost waarbij het koppelen van een taak 'MP4 naar vloeiende streams' met een 'PlayReady-beveiligingstaak' met behulp van een taaksjabloon helemaal geen taken heeft gemaakt.
* EncryptionUtils.GetCertificateFromStore(): Deze methode gooit niet langer een null-referentieuitzondering vanwege een fout bij het vinden van het certificaat op basis van certificaatconfiguratieproblemen.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Release november 2012
De wijzigingen in deze sectie waren updates opgenomen in de NOVEMBER 2012 (versie 2.0.0.0) SDK. Voor deze wijzigingen kan worden geschreven voor een code die is geschreven voor de SDK-release van juni 2012 die moet worden gewijzigd of herschreven.

* Assets
  
    * IAsset.Create(assetName) is de *enige* functie voor het maken van activa. IAsset.Create uploadt geen bestanden meer als onderdeel van de methodeaanroep. Gebruik IAssetFile voor het uploaden.
    * De iAsset.Publish-methode en de opsomwaarde AssetState.Publish zijn verwijderd uit de Services SDK. Elke code die afhankelijk is van deze waarde moet worden herschreven.
* Fileinfo
  
    * Deze klasse is verwijderd en vervangen door IAssetFile.
  
* IAssetFiles
  
    * IAssetFile vervangt FileInfo en heeft een ander gedrag. Om het te gebruiken, instantiate het iAssetFiles object, gevolgd door een bestand uploaden met behulp van de Media Services SDK of de Storage SDK. De volgende IAssetFile.Upload-overbelasting kan worden gebruikt:
  
        * IAssetFile.Upload(filePath): deze synchrone methode blokkeert de thread en we raden deze alleen aan wanneer u één bestand uploadt.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Deze asynchrone methode is het voorkeursuploadmechanisme. 
    
            Bekende bug: Als je het annuleringstoken gebruikt, wordt de upload geannuleerd. De taken kunnen veel annuleringsstatussen hebben. U moet uitzonderingen goed vangen en verwerken.
* Locators
  
    * De oorsprong-specifieke versies werden verwijderd. De SAS-specifieke context. Locators.CreateSasLocator (asset, accessPolicy) wordt gemarkeerd afgeschaft of verwijderd door algemene beschikbaarheid. Zie de sectie 'Locators' onder 'Nieuwe functionaliteit' voor bijgewerkt gedrag.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Preview release van juni 2012
De volgende functionaliteit was nieuw in de release van de SDK in november:

* Entiteiten verwijderen
  
    * IAsset-, IAssetFile-, ILocator-, IAccessPolicy- en IContentKey-objecten worden nu op objectniveau verwijderd, dat wil zeggen IObject.Delete(), in plaats van dat er een verwijdering in de verzameling vereist, dat wil zeggen cloudMediaContext.ObjCollection.Delete(objInstance).
* Locators
  
    * Locators moeten nu worden gemaakt met behulp van de createlocator-methode. Ze moeten de enumwaarden LocatorType.SAS of LocatorType.OnDemandOrigin gebruiken als argument voor het specifieke type locator dat u wilt maken.
    * Er zijn nieuwe eigenschappen toegevoegd aan locators om het gemakkelijker te maken om bruikbare URI's voor uw inhoud te verkrijgen. Dit herontwerp van locators biedt meer flexibiliteit voor toekomstige uitbreidbaarheid van derden en verhoogt het gebruiksgemak voor mediaclienttoepassingen.
* Ondersteuning voor asynchrone methoden
  
    * Asynchrone ondersteuning is toegevoegd aan alle methoden.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[MSDN-forum azure mediaservices]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[API-verwijzing naar Azure Media Services REST]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Invoermetagegevens]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Uitvoermetagegevens]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEindpunt]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Overzicht van sjablonen voor Media Services PlayReady-licentie]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamische verpakking]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Logica opnieuw proberen in de Media Services SDK voor .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

