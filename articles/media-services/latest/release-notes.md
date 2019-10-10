---
title: Opmerkingen bij de release van Azure Media Services v3 | Microsoft Docs
description: Om up-to-date te blijven met de meest recente ontwikkelingen, biedt dit artikel u de meest recente updates op Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/07/2019
ms.author: juliako
ms.openlocfilehash: 34d68974366eb17704b4ea1291bd1d29a0eea95a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168622"
---
# <a name="azure-media-services-v3-release-notes"></a>Release opmerkingen bij Azure Media Services v3

Dit artikel bevat informatie over het volgende om up-to-date te blijven met de meest recente ontwikkelingen:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="known-issues"></a>Bekende problemen

> [!NOTE]
> U kunt momenteel geen gebruik maken van de Azure-portal om v3-resources te beheren. Gebruik de [rest API](https://aka.ms/ams-v3-rest-sdk), CLI of een van de ondersteunde sdk's.

Zie voor meer informatie [migratie richtlijnen voor het overstappen van Media Services versie 2 tot v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Live lineaire code ring van Live Events

Media Services v3 kondigt de preview van 24 uur x 365 dagen aan live lineaire code ring van Live Events aan.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Afschaffing van media processors

Er wordt een afschaffing van *Azure media indexer* en *Azure media indexer 2 Preview*aangekondigd. De processor van [Azure media indexer](../previous/media-services-index-content.md) media wordt op 1 oktober 2020 buiten gebruik gesteld. De [Azure media indexer 2 Preview](../previous/media-services-process-content-with-indexer2.md) -media processors worden buiten gebruik gesteld op 1 januari van 2020. [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze verouderde media processors.

Zie [Migrate from Azure media indexer en Azure media indexer 2 to Azure Media Services video indexer](../previous/migrate-indexer-v1-v2.md)voor meer informatie.

## <a name="august-2019"></a>2019 augustus

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Zuid-Afrika regionale paar is geopend voor Media Services 

Media Services is nu beschikbaar in Zuid-Afrika-noord en Zuid-Afrika-west regio's.

Zie [Clouds en regio's waarin Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Afschaffing van media processors

Er wordt een afschaffing van de media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) aangekondigd. deze worden buiten gebruik gesteld op 30 november 2019.

Zie [WAME migreren naar Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) en [aam migreren naar Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335)voor meer informatie.
 
## <a name="july-2019"></a>2019 juli

### <a name="content-protection"></a>Content Protection

Wanneer inhoud die wordt beveiligd met token beperking wordt gestreamd, moeten eind gebruikers een token verkrijgen dat wordt verzonden als onderdeel van de aanvraag voor de sleutel levering. Met de functie voor het voor *komen van tokens* kunnen Media Services klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of licentie aan te vragen. Zie [token replay voor komen](content-protection-overview.md#token-replay-prevention)voor meer informatie.

Deze functie is momenteel beschikbaar in VS Centraal en VS-West-centraal.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Video subknipsel

U kunt nu een video knippen of subfragmenten wanneer u deze codeert met behulp van een [taak](https://docs.microsoft.com/rest/api/media/jobs). 

Deze functionaliteit werkt met elke [trans formatie](https://docs.microsoft.com/rest/api/media/transforms) die is gebouwd met behulp van de [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -voor instellingen of de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) -voor waarden. 

Zie voor beelden:

* [Een video met .NET knippen](subclip-video-dotnet-howto.md)
* [Een video met REST samenknippen](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Mei 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor ondersteuning voor Media Services Diagnostische logboeken en metrische gegevens

U kunt nu Azure Monitor gebruiken om emmited weer te geven op Media Services.

* Gebruik de diagnostische logboeken van Azure Monitor om aanvragen te bewaken die worden verzonden door het Media Services key delivery-eind punt. 
* Bewaak de metrische gegevens die worden verzonden door Media Services [streaming-eind punten](streaming-endpoint-concept.md).   

Zie [Media Services metrische gegevens en Diagnostische logboeken bewaken](media-services-metrics-diagnostic-logs.md)voor meer informatie.

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Ondersteuning voor meerdere audio-tracks in dynamische pakketten 

Bij het streamen van assets met meerdere audio sporen met meerdere codecs en talen, ondersteunt [dynamische verpakking](dynamic-packaging-overview.md) nu meerdere audio sporen voor de HLS-uitvoer (versie 4 of hoger).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Het regionale paar van Korea is geopend voor Media Services 

Media Services is nu beschikbaar in Korea-centraal en Korea-zuid regio's. 

Zie [Clouds en regio's waarin Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie.

### <a name="performance-improvements"></a>Prestatie verbeteringen

Er zijn updates toegevoegd die Media Services prestatie verbeteringen bevatten.

* De maximale bestands grootte die wordt ondersteund voor de verwerking, is bijgewerkt. Zie, [quota's en beperkingen](limits-quotas-constraints.md).
* De [versleutelings snelheid wordt verbeterd](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Nieuwe voor instellingen

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) is toegevoegd aan de ingebouwde analyse-voor instellingen.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) is toegevoegd aan de ingebouwde coderings definities. Zie voor meer informatie [code ring met inhoud](cae-experimental.md). 

## <a name="march-2019"></a>2019 maart

Dynamische verpakking ondersteunt nu Dolby Atmos. Zie [Audio codecs die worden ondersteund door dynamische pakketten](dynamic-packaging-overview.md#audio-codecs)voor meer informatie.

U kunt nu een lijst met Asset-of account filters opgeven die van toepassing zijn op uw streaming-Locator. Zie [filters koppelen aan streaming-Locator](filters-concept.md#associating-filters-with-streaming-locator)voor meer informatie.

## <a name="february-2019"></a>Februari 2019

Media Services V3 wordt nu ondersteund in azure National Clouds. Niet alle functies zijn nog in alle Clouds beschikbaar. Zie [Clouds en regio's waarin Azure Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie.

De gebeurtenis [micro soft. media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) is toegevoegd aan de Azure Event grid-schema's voor Media Services.

## <a name="january-2019"></a>Januari 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard-en MPI-bestanden 

Bij het coderen met Media Encoder Standard voor het maken van MP4-bestand (en), wordt er een nieuw. mpi-bestand gegenereerd en toegevoegd aan de uitvoer Asset. Dit MPI-bestand is bedoeld om de prestaties te verbeteren voor [dynamische pakket](dynamic-packaging-overview.md) -en streaming-scenario's.

U moet het MPI-bestand niet wijzigen of verwijderen of afhankelijk van de aanwezigheid (of niet) van een dergelijk bestand afnemen in uw service.

## <a name="december-2018"></a>December 2018

Updates van de GA-versie van de V3 API zijn onder andere:
       
* De **PresentationTimeRange** -eigenschappen zijn niet meer vereist voor **Asset filters** en **account filters**. 
* De query opties $top en $skip voor **taken** en **trans formaties** zijn verwijderd en $OrderBy is toegevoegd. Als onderdeel van het toevoegen van de nieuwe ordenings functionaliteit werd ontdekt dat de opties voor $top en $skip per ongeluk werden weer gegeven, zelfs als ze niet zijn geïmplementeerd.
* De Enumeration-uitbreid baarheid is opnieuw ingeschakeld. Deze functie is ingeschakeld in de Preview-versies van de SDK en is per ongeluk uitgeschakeld in de GA-versie.
* Er zijn twee vooraf gedefinieerde stroomsgewijze beleids regels gewijzigd. **SecureStreaming** is nu **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** is nu **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

De CLI 2,0-module is nu beschikbaar voor [Azure Media Services v3 ga](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v-2.0.50.

### <a name="new-commands"></a>Nieuwe opdrachten

- [AZ AMS account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [AZ AMS account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [AZ AMS Asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [AZ AMS Asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ AMS content-Key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [AZ AMS job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ AMS Live-Event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [AZ AMS live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ AMS streaming-Endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ AMS streaming-Locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [AZ AMS account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) -Hiermee kunt u gereserveerde media-eenheden beheren. Zie [gereserveerde media-eenheden schalen](media-reserved-units-cli-how-to.md)voor meer informatie.

### <a name="new-features-and-breaking-changes"></a>Nieuwe functies en belang rijke wijzigingen

#### <a name="asset-commands"></a>Asset-opdrachten

- ```--storage-account``` en ```--container``` argumenten zijn toegevoegd.
- Standaard waarden voor verloop tijd (nu + 23h) en machtigingen (lezen) in ```az ams asset get-sas-url```-opdracht toegevoegd.

#### <a name="job-commands"></a>Opdracht opdrachten

- ```--correlation-data``` en ```--label``` argumenten toegevoegd
- de naam van ```--output-asset-names``` is gewijzigd in ```--output-assets```. Nu accepteert het een lijst met door spaties gescheiden activa in de indeling ' assets = label '. Activa zonder label kunnen als volgt worden verzonden: ' assets = '.

#### <a name="streaming-locator-commands"></a>Opdrachten voor streaming-Locator

- ```az ams streaming locator``` basis opdracht is vervangen door ```az ams streaming-locator```.
- ```--streaming-locator-id``` en ```--alternative-media-id support``` argumenten zijn toegevoegd.
- het argument ```--content-keys argument``` is bijgewerkt.
- de naam van ```--content-policy-name``` is gewijzigd in ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Streaming-beleids opdrachten

- ```az ams streaming policy``` basis opdracht is vervangen door ```az ams streaming-policy```.
- Ondersteuning voor versleutelings parameters in ```az ams streaming-policy create``` toegevoegd.

#### <a name="transform-commands"></a>Opdrachten transformeren

- ```--preset-names``` argument vervangen door ```--preset```. Nu kunt u slechts één uitvoer/vooraf ingestelde waarde per keer instellen (als u meer wilt toevoegen om ```az ams transform output add``` uit te voeren). U kunt ook aangepaste StandardEncoderPreset instellen door het pad naar uw aangepaste JSON door te geven.
- ```az ams transform output remove``` kan worden uitgevoerd door de uitvoer index door te geven die moet worden verwijderd.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumenten toegevoegd aan ```az ams transform create```-en ```az ams transform output add```-opdrachten.

## <a name="october-2018---ga"></a>Oktober 2018-GA

In deze sectie worden Azure Media Services updates (AMS) van oktober beschreven.

### <a name="rest-v3-ga-release"></a>REST v3 GA release

De [rest v3 ga-release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) bevat meer Api's voor Live, account/Asset level-manifest filters en DRM-ondersteuning.

#### <a name="azure-resource-management"></a>Azure-resource beheer 

Ondersteuning voor Azure Resource Management maakt Unified management en Operations API mogelijk (nu alles op één plek).

Vanaf deze release kunt u Resource Manager-sjablonen gebruiken om live-gebeurtenissen te maken.

#### <a name="improvement-of-asset-operations"></a>Verbetering van activa bewerkingen 

De volgende verbeteringen zijn geïntroduceerd:

- Opname van HTTP (s) Url's of Azure Blob Storage SAS-Url's.
- Geef de naam van de container voor assets op. 
- Eenvoudigere uitvoer ondersteuning voor het maken van aangepaste werk stromen met Azure Functions.

#### <a name="new-transform-object"></a>Nieuw Transform-object

Het nieuwe object **transform** vereenvoudigt het coderings model. Met het nieuwe object kunt u eenvoudig sjabloon en voor instellingen voor het maken en delen van coderings bronnen beheren. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory-verificatie en RBAC

Met Azure AD-verificatie en op rollen gebaseerde Access Control (RBAC) kunt u beveiligde trans formaties, LiveEvents, beleids regels voor inhouds sleutels of activa op rol of gebruikers in azure AD inschakelen.

#### <a name="client-sdks"></a>Client-SDK 's  

Talen die worden ondersteund in Media Services V3: .NET core, Java, node. js, Ruby, type script, python en go.

#### <a name="live-encoding-updates"></a>Live encoding-updates

De volgende Live encoding-updates worden geïntroduceerd:

- Nieuwe modus voor de lage latentie voor Live (10 seconden end-to-end).
- Verbeterde RTMP-ondersteuning (verbeterde stabiliteit en meer ondersteuning voor broncode encoders).
- RTMP beveiligde opname.

    Wanneer u een live gebeurtenis maakt, krijgt u nu vier opname-Url's. De 4 opname-Url's zijn bijna identiek, hebben hetzelfde streaming token (AppId), alleen het poort nummer onderdeel is anders. Twee van de Url's zijn primaire en back-ups voor RTMP. 
- ondersteuning voor trans codering met 24 uur. 
- Verbeterde ondersteuning voor AD-Signa lering in RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Verbeterde ondersteuning voor Event Grid

U kunt de volgende Event Grid verbeteringen voor de ondersteuning bekijken:

- Azure Event Grid integratie voor eenvoudiger ontwikkeling met Logic Apps en Azure Functions. 
- Abonneer u op gebeurtenissen op code ring, Live kanalen en meer.

### <a name="cmaf-support"></a>CMAF-ondersteuning

Ondersteuning voor CMAF en ' cbcs-versleuteling voor Apple HLS (iOS 11 +) en MPEG-DASH-spelers die CMAF ondersteunen.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA release is in augustus aangekondigd. Zie [Wat is video indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)voor nieuwe informatie over momenteel ondersteunde functies. 

### <a name="plans-for-changes"></a>Plannen voor wijzigingen

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
De Azure CLI 2,0-module die bewerkingen bevat voor alle functies (waaronder Live, beleids regels voor inhouds sleutels, accounts/Asset-filters, streaming-beleid) is binnenkort beschikbaar. 

### <a name="known-issues"></a>Bekende problemen

De volgende problemen zijn alleen van invloed op klanten die de preview-API voor activa of AccountFilters hebben gebruikt.

Als u activa of account filters hebt gemaakt tussen 09/28 en 10/12 met Media Services v3 CLI of Api's, moet u alle asset-en AccountFilters verwijderen en opnieuw maken vanwege een versie conflict. 

## <a name="may-2018---preview"></a>Mei 2018-preview

### <a name="net-sdk"></a>.NET SDK

De volgende functies zijn aanwezig in de .NET SDK:

* **Trans formaties** en **taken** voor het coderen of analyseren van media-inhoud. Zie voor voor beelden [stroom bestanden](stream-files-tutorial-with-api.md) en [analyseren](analyze-videos-tutorial-with-api.md).
* **Streaming-Locators** voor het publiceren en streamen van inhoud op apparaten van eind gebruikers
* **Stroomsgewijze beleids regels** en **beleids regels voor inhouds sleutels** voor het configureren van key delivery and Content Protection (DRM) bij het leveren van inhoud.
* **Live Events** en **Live outputs** voor het configureren van de opname en het archiveren van inhoud voor live streamen.
* **Activa** voor het opslaan en publiceren van media-inhoud in azure Storage. 
* **Streaming-eind punten** voor het configureren en schalen van dynamische pakketten, versleuteling en streaming voor zowel live als on-demand media-inhoud.

### <a name="known-issues"></a>Bekende problemen

* Bij het verzenden van een taak kunt u opgeven dat u uw bron video wilt opnemen met behulp van HTTPS-Url's, SAS-Url's of paden naar bestanden die zich in Azure Blob Storage bevinden. Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](media-services-overview.md)
- [Release opmerkingen bij Media Services v2](../previous/media-services-release-notes.md)
