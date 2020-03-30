---
title: Live streamen met on-premises encoders die multibitrate streams maken - Azure | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u een kanaal instelt dat een multibitrate live stream ontvangt van een on-premises encoder.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f6366f162cb09898b694b14440718401c57c0adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251035"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Werken met kanalen die multi-bitrate live stream ontvangen van on-premises encoders

> [!NOTE]
> Vanaf 12 mei 2018 ondersteunen live kanalen niet langer het RTP/MPEG-2 transportstream ingest protocol. Migreer van RTP/MPEG-2 naar RTMP of gefragmenteerde MP4 -protocollen (Smooth Streaming).

## <a name="overview"></a>Overzicht
In Azure Media Services vertegenwoordigt een *kanaal* een pijplijn voor het verwerken van livestreaming-inhoud. Een kanaal ontvangt op twee manieren live-invoerstreams:

* Een on-premises live-encoder stuurt een multi-bitrate RTMP- of Smooth Streaming -stream (gefragmenteerde MP4) naar het kanaal dat niet is ingeschakeld om live codering met Media Services uit te voeren. De ingenomen streams passeren kanalen zonder verdere verwerking. Deze methode wordt *pass-through*genoemd. Een live encoder kan ook een single-bitrate stream sturen naar een kanaal dat niet is ingeschakeld voor live codering, maar dat raden we niet aan. Media Services levert de stream aan klanten die daarom vragen.

  > [!NOTE]
  > Het gebruik van een pass-through methode is de meest economische manier om live streaming te doen.


* Een on-premises live-encoder stuurt een stream met één bitrate naar het kanaal dat is ingeschakeld om live codering met Media Services uit te voeren in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). Het kanaal voert vervolgens live codering van de inkomende single-bitrate stream naar een multi-bitrate (adaptieve) videostream. Media Services levert de stream aan klanten die daarom vragen.

Vanaf de release mediaservices 2.10 u bij het maken van een kanaal opgeven hoe u wilt dat uw kanaal de invoerstream ontvangt. Je ook opgeven of je wilt dat het kanaal live wordt gecodeerd voor je stream. U hebt hiervoor twee opties:

* **Doorgeef:** Geef deze waarde op als u van plan bent een on-premises live-encoder te gebruiken die een stream met meerdere bitrate 's maakt (een pass-through stream) als uitvoer. In dit geval gaat de binnenkomende stroom door naar de uitvoer zonder codering. Dit is het gedrag van een kanaal voor de 2.10 release. Dit artikel geeft details over het werken met kanalen van dit type.
* **Live encoding**: Kies deze waarde als u van plan bent Media Services te gebruiken om uw single-bitrate live stream te coderen naar een stream met meerdere bitrates. Als u een live coderingskanaal in **een status actief laat,** worden factureringskosten in rekening gebracht. We raden je aan om je hardloopkanalen onmiddellijk te stoppen nadat je live-streaming evenement is voltooid om extra uurkosten te voorkomen. Media Services levert de stream aan klanten die daarom vragen.

> [!NOTE]
> In dit artikel worden kenmerken van kanalen besproken die niet zijn ingeschakeld om live codering uit te voeren. Zie [Live streaming met Azure Media Services om multibitrate streams te maken voor](media-services-manage-live-encoder-enabled-channels.md)informatie over het werken met kanalen die zijn ingeschakeld om live codering uit te voeren.
>
>Zie Aanbevolen op [lokalenencoders voor](media-services-recommended-encoders.md)informatie over aanbevolen on-premises encoders.

Het volgende diagram vertegenwoordigt een live-streaming workflow die een on-premises live encoder gebruikt om multi-bitrate RTMP of gefragmenteerde MP4 (Smooth Streaming) streams als uitvoer te hebben.

![Live-werkstroom][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Gemeenschappelijk scenario voor live streaming
In de volgende stappen worden taken beschreven die betrokken zijn bij het maken van veelvoorkomende toepassingen voor live streaming.

1. Sluit een videocamera aan op een computer. Start en configureer een on-premises live encoder met een multibitrate RTMP of gefragmenteerde MP4 -stream (Smooth Streaming) als uitvoer. Zie [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](https://go.microsoft.com/fwlink/?LinkId=532824) voor meer informatie.

    Je deze stap ook uitvoeren nadat je je kanaal hebt gemaakt.
2. Een kanaal maken en starten.

3. Haal de URL van het kanaal op.

    De live encoder gebruikt de inname-URL om de stream naar het kanaal te sturen.
4. De URL van de kanaalpreview ophalen.

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.
5. Maak een programma.

    Wanneer u de Azure-portal gebruikt, maakt het maken van een programma ook een asset.

    Wanneer u de .NET SDK of REST gebruikt, moet u een asset maken en opgeven om dit item te gebruiken bij het maken van een programma.
6. Publiceer het item dat aan het programma is gekoppeld.   

    >[!NOTE]
    >Wanneer uw Azure Media Services-account wordt gemaakt, wordt een **standaard** eindpunt voor streaming toegevoegd aan uw account in de **status Gestopt.** Het streaming-eindpunt van waar u inhoud wilt streamen, moet de status **Wordt uitgevoerd** hebben.

7. Start het programma wanneer u klaar bent om te beginnen met streamen en archiveren.

8. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.

9. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.

10. Verwijder het programma (en verwijder het item optioneel).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Beschrijving van een kanaal en de bijbehorende componenten
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Kanaalinvoer (inname) configuraties
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Inname streaming protocol
Media Services ondersteunt het opnemen van live feeds door multi-bitrate gefragmenteerde MP4 en multi-bitrate RTMP te gebruiken als streaming protocollen. Wanneer het RTMP-streamingprotocol is geselecteerd, worden twee inname (invoer)eindpunten voor het kanaal gemaakt:

* **Primaire URL**: Hiermee geeft u de volledig gekwalificeerde URL op van het primaire RTMP-innamepunt van het kanaal.
* **Secundaire URL** (optioneel): hiermee geeft u de volledig gekwalificeerde URL op van het secundaire RTMP-innamepunt van het kanaal.

Gebruik de secundaire URL als u de duurzaamheid en fouttolerantie van uw innamestream wilt verbeteren (evenals encoderfailover en fouttolerantie), vooral voor de volgende scenario's:

- Eén encoder dubbelpushen naar zowel primaire als secundaire URL's:

    Het belangrijkste doel van dit scenario is om meer veerkracht te bieden aan netwerkschommelingen en kriebels. Sommige RTMP-encoders kunnen de verbinding tussen het netwerk en niet goed verbreken. Wanneer er een netwerkverbinding plaatsvindt, kan een encoder stoppen met coderen en vervolgens de gebufferde gegevens niet verzenden wanneer er opnieuw verbinding wordt gemaakt. Dit veroorzaakt discontinuïteiten en gegevensverlies. Netwerkverversingen kunnen plaatsvinden als gevolg van een slecht netwerk of onderhoud aan de Azure-kant. Primaire/secundaire URL's verminderen de netwerkproblemen en bieden een gecontroleerd upgradeproces. Elke keer dat een geplande netwerkverbinding plaatsvindt, beheert Media Services de primaire en secundaire verbinding en zorgt voor een vertraagde verbinding tussen de twee. Encoders hebben dan de tijd om gegevens te blijven verzenden en opnieuw verbinding te maken. De volgorde van de loskoppelingen kan willekeurig zijn, maar er zal altijd een vertraging zijn tussen primaire/secundaire of secundaire/primaire URL's. In dit scenario is de encoder nog steeds het enige punt van falen.

- Meerdere encoders, waarbij elke encoder naar een speciaal punt duwt:

    Dit scenario biedt zowel encoder als inname redundantie. In dit scenario wordt encoder1 naar de primaire URL gepusht en wordt encoder2 naar de secundaire URL gepusht. Wanneer een encoder uitvalt, kan de andere encoder gegevens blijven verzenden. Gegevensredundantie kan worden gehandhaafd omdat Media Services de primaire en secundaire URL's niet tegelijkertijd loskoppelt. In dit scenario wordt ervan uitgegaan dat encoders tijdgesynchroniseerd zijn en exact dezelfde gegevens bevatten.  

- Meerdere encoders dubbelpushen naar zowel primaire als secundaire URL's:

    In dit scenario pushen beide encoders gegevens naar zowel primaire als secundaire URL's. Dit biedt de beste betrouwbaarheid en fouttolerantie, evenals gegevensredundantie. Dit scenario kan zowel encoderfouten als ontkoppeling verdragen, zelfs als één encoder niet meer werkt. Het gaat ervan uit dat encoders zijn tijd gesynchroniseerd en bieden precies dezelfde gegevens.  

Zie [Azure Media Services RTMP Support en Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824)voor informatie over RTMP live encoders.

#### <a name="ingest-urls-endpoints"></a>Inname van URL's (eindpunten)
Een kanaal biedt een invoereindpunt (inname VAN URL) dat u opgeeft in de live encoder, zodat de encoder streams naar uw kanalen kan pushen.   

U de inname URL's krijgen wanneer u het kanaal maakt. Om deze URL's te krijgen, hoeft het kanaal niet in de **status Actief** te zijn. Wanneer u klaar bent om gegevens naar het kanaal te duwen, moet het kanaal in de **status Actief** zijn. Nadat het kanaal gegevens begint in te nemen, u een voorbeeld van uw stream bekijken via de url van het voorbeeld.

Je hebt een optie om een gefragmenteerde MP4 -livestream (Smooth Streaming) in te nemen via een SSL-verbinding. Als u SSL wilt innemen, moet u de inname van DE URL bijwerken naar HTTPS. Op dit moment u RTMP niet via SSL innemen.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Hoofdframeinterval
Wanneer u een on-premises live-encoder gebruikt om multibitratestream te genereren, geeft het hoofdframeinterval de duur aan van de groep afbeeldingen (GOP) zoals die door die externe encoder wordt gebruikt. Nadat het kanaal deze binnenkomende stream heeft ontvangen, u uw live stream leveren aan toepassingen voor het afspelen van klanten in een van de volgende indelingen: Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH) en HTTP Live Streaming (HLS). Wanneer u live streaming doet, wordt HLS altijd dynamisch verpakt. Media Services berekent standaard automatisch de hls-segmentverpakkingsverhouding (fragmenten per segment) op basis van het hoofdframeinterval dat wordt ontvangen van de live-encoder.

In de volgende tabel ziet u hoe de duur van het segment wordt berekend:

| Hoofdframeinterval | HLS-segmentverpakkingsverhouding (FragmentsPerSegment) | Voorbeeld |
| --- | --- | --- |
| Minder dan of gelijk aan 3 seconden |3:1 |Als KeyFrameInterval (of GOP) 2 seconden is, is de standaard hls-segmentverpakkingsverhouding 3 op 1. Hierdoor ontstaat een HLS-segment van 6 seconden. |
| 3 tot 5 seconden |2:1 |Als KeyFrameInterval (of GOP) 4 seconden is, is de standaard hls-segmentverpakkingsverhouding 2 op 1. Hierdoor ontstaat een HLS-segment van 8 seconden. |
| Meer dan 5 seconden |1:1 |Als KeyFrameInterval (of GOP) 6 seconden is, is de standaard hls-segmentverpakkingsverhouding 1 op 1. Hierdoor ontstaat een HLS-segment van 6 seconden. |

U de verhouding fragmenten per segment wijzigen door de uitvoer van het kanaal te configureren en FragmentsPerSegment in te stellen op ChannelOutputHls.

U ook de intervalwaarde van het hoofdframe wijzigen door de eigenschap KeyFrameInterval in te stellen op ChannelInput. Als u KeyFrameInterval expliciet instelt, wordt de HLS-segmentverpakkingsverhouding FragmentsPerSegment berekend via de eerder beschreven regels.  

Als u zowel KeyFrameInterval als FragmentsPerSegment expliciet instelt, gebruikt Media Services de waarden die u instelt.

#### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U de IP-adressen definiëren die video op dit kanaal mogen publiceren. Een toegestaan IP-adres kan als een van de volgende voorwaarden worden opgegeven:

* Eén IP-adres (bijvoorbeeld 10.0.0.1)
* Een IP-bereik dat een IP-adres en een CIDR-subnetmasker gebruikt (bijvoorbeeld 10.0.0.1/22)
* Een IP-bereik dat een IP-adres en een gestippeld subnetmasker achter de komma gebruikt (bijvoorbeeld 10,0,0,1(255.255.252.0))

Als er geen IP-adressen zijn opgegeven en er geen regeldefinitie is, is er geen IP-adres toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.

### <a name="channel-preview"></a>Kanaalvoorbeeld
#### <a name="preview-urls"></a>Url's bekijken
Kanalen bieden een voorbeeldeindpunt (preview-URL) dat u gebruikt om uw stream te bekijken en te valideren voordat u verder wordt verwerkt en geleverd.

U de url van de preview ophalen wanneer u het kanaal maakt. Om de URL te krijgen, hoeft het kanaal niet in de **status Actief** te zijn. Nadat het kanaal gegevens begint in te nemen, kun je een voorbeeld van je stream bekijken.

Momenteel kan de previewstream alleen worden geleverd in een gefragmenteerde MP4-indeling (Smooth Streaming), ongeacht het opgegeven invoertype. U de [smooth streaming health monitor-speler](https://playready.directtaps.net/smoothstreaming/) gebruiken om de vloeiende stream te testen. U ook een speler die wordt gehost in de Azure-portal gebruiken om uw stream te bekijken.

#### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U de IP-adressen definiëren die verbinding mogen maken met het voorbeeldeindpunt. Als er geen IP-adressen zijn opgegeven, is een IP-adres toegestaan. Een toegestaan IP-adres kan als een van de volgende voorwaarden worden opgegeven:

* Eén IP-adres (bijvoorbeeld 10.0.0.1)
* Een IP-bereik dat een IP-adres en een CIDR-subnetmasker gebruikt (bijvoorbeeld 10.0.0.1/22)
* Een IP-bereik dat een IP-adres en een gestippeld subnetmasker achter de komma gebruikt (bijvoorbeeld 10,0,0,1(255.255.252.0))

### <a name="channel-output"></a>Kanaaluitvoer
Zie de intervalsectie [Hoofdframe](#keyframe_interval) voor informatie over kanaaluitvoer.

### <a name="channel-managed-programs"></a>Door kanalen beheerde programma's
Een kanaal is gekoppeld aan programma's die u gebruiken om het publiceren en opslaan van segmenten in een live stream te beheren. Kanalen beheren programma's. De kanaal- en programmarelatie is vergelijkbaar met traditionele media, waar een kanaal een constante stroom van inhoud heeft en een programma is ondergebracht bij een bepaalde getimede gebeurtenis op dat kanaal.

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. Archiefvenster lengte bepaalt ook het maximale aantal tijd dat klanten terug kunnen zoeken in de tijd van de huidige live positie. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma is gekoppeld aan een asset die de gestreamde inhoud opslaat. Een asset wordt toegewezen aan een blokblobcontainer in het Azure-opslagaccount en de bestanden in het asset worden opgeslagen als blobs in die container. Als u het programma wilt publiceren zodat uw klanten de stream kunnen bekijken, moet u een OnDemand-locator voor het bijbehorende actief maken. U deze locator gebruiken om een streaming URL op te bouwen die u aan uw klanten verstrekken.

Een kanaal ondersteunt maximaal drie gelijktijdig lopende programma's, zodat u meerdere archieven van dezelfde binnenkomende stream maken. U verschillende onderdelen van een evenement naar behoefte publiceren en archiveren. Stel je bijvoorbeeld voor dat je zakelijke vereiste is om 6 uur van een programma te archiveren, maar alleen de laatste 10 minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Eén programma is ingesteld op het archiveren van zes uur van het evenement, maar het programma wordt niet gepubliceerd. Het andere programma is ingesteld op archiveren voor 10 minuten, en dit programma wordt gepubliceerd.

Gebruik bestaande programma's niet voor nieuwe gebeurtenissen. Maak in plaats daarvan een nieuw programma voor elke gebeurtenis. Start het programma wanneer u klaar bent om te beginnen met streamen en archiveren. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.

Als u gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u het programma en verwijdert u het bijbehorende item. Een item kan niet worden verwijderd als een programma het gebruikt. Het programma moet eerst worden verwijderd.

Zelfs nadat u het programma hebt gestopt en verwijderd, kunnen gebruikers uw gearchiveerde inhoud op aanvraag streamen als video, totdat u het item verwijdert. Als u de gearchiveerde inhoud wilt behouden, maar deze niet beschikbaar hebt voor streaming, verwijdert u de streaminglocator.

## <a name="channel-states-and-billing"></a><a id="states"></a>Kanaalstaten en facturering
Mogelijke waarden voor de huidige status van een kanaal zijn:

* **Gestopt**: Dit is de beginstatus van het kanaal na de creatie ervan. In deze status kunnen de kanaaleigenschappen worden bijgewerkt, maar is streaming niet toegestaan.
* **Vanaf**: Het kanaal wordt gestart. Tijdens deze status is geen updates of streaming toegestaan. Als er een fout optreedt, keert het kanaal terug naar de **status Gestopt.**
* **Running**: Het kanaal kan live streams verwerken.
* **Stoppen**: Het kanaal wordt gestopt. Tijdens deze status is geen updates of streaming toegestaan.
* **Verwijderen**: Het kanaal wordt verwijderd. Tijdens deze status is geen updates of streaming toegestaan.

In de volgende tabel ziet u hoe kanaalstatussen worden toegewezen aan de factureringsmodus.

| Kanaalstaat | Portal UI-indicatoren | Gefactureerd? |
| --- | --- | --- |
| **Beginnen** |**Beginnen** |Geen (tijdelijke status) |
| **Wordt uitgevoerd** |**Klaar** (geen lopende programma's)<p><p>of<p>**Streaming** (ten minste één lopend programma) |Ja |
| **Stoppen** |**Stoppen** |Geen (tijdelijke status) |
| **Gestopt** |**Gestopt** |Nee |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Ondertiteling en invoeging van advertenties
In de volgende tabel worden ondersteunde standaarden voor ondertiteling en het invoegen van advertenties weergegeven.

| Standard | Opmerkingen |
| --- | --- |
| CEA-708 en M.E.m.) |CEA-708 en EIA-608 zijn ondertitelingsstandaarden voor de Verenigde Staten en Canada.<p><p>Momenteel wordt ondertiteling alleen ondersteund als deze wordt uitgevoerd in de gecodeerde invoerstream. U moet een live media-encoder gebruiken die 608 of 708 bijschriften kan invoegen in de gecodeerde stream die naar Media Services wordt verzonden. Media Services levert de inhoud met ingevoegde bijschriften aan uw kijkers. |
| TTML binnen .ismt (Smooth Streaming teksttracks) |Met een dynamische verpakking van Media Services kunnen uw klanten inhoud streamen in een van de volgende indelingen: DASH, HLS of Smooth Streaming. Als u echter gefragmenteerde MP4 (Smooth Streaming) inneemt met bijschriften in .ismt (Smooth Streaming text tracks), u de stream alleen leveren aan Smooth Streaming-clients. |
| SCTE-35 |SCTE-35 is een digitaal signaleringssysteem dat wordt gebruikt om reclame in te brengen. Downstream ontvangers gebruiken het signaal om reclame splice in de stroom voor de toegewezen tijd. SCTE-35 moet worden verzonden als een spaarzame track in de invoerstroom.<p><p>Momenteel is de enige ondersteunde invoerstream-indeling die advertentiesignalen draagt gefragmenteerd MP4 (Smooth Streaming). De enige ondersteunde uitvoerindeling is ook Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Overwegingen
Wanneer u een on-premises live-encoder gebruikt om een stream met meerdere bitrates naar een kanaal te verzenden, zijn de volgende beperkingen van toepassing:

* Zorg ervoor dat u voldoende gratis internetverbinding hebt om gegevens naar de innamepunten te verzenden.
* Het gebruik van een secundaire inname-URL vereist extra bandbreedte.
* De inkomende multi-bitrate stream kan maximaal 10 videokwaliteitsniveaus (lagen) en maximaal 5 audiotracks hebben.
* De hoogste gemiddelde bitrate voor een van de videokwaliteitsniveaus moet lager zijn dan 10 Mbps.
* Het totaal van de gemiddelde bitrates voor alle video- en audiostreams moet lager zijn dan 25 Mbps.
* U het invoerprotocol niet wijzigen terwijl het kanaal of de bijbehorende programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* Je één bitrate inje kanaal opnemen. Maar omdat het kanaal de stream niet verwerkt, ontvangen de clienttoepassingen ook een enkele bitrate stream. (We raden deze optie niet aan.)

Hier volgen andere overwegingen met betrekking tot het werken met kanalen en gerelateerde componenten:

* Elke keer dat u de live encoder opnieuw configureert, roept u de **methode Opnieuw instellen** op het kanaal aan. Voordat u het kanaal reset, moet u het programma stoppen. Nadat u het kanaal hebt gereset, start u het programma opnieuw op.

  > [!NOTE]
  > Wanneer u het programma opnieuw start, moet u het koppelen aan een nieuw item en een nieuwe locator maken. 
  
* Een kanaal kan alleen worden gestopt **Running** als het actief is en alle programma's op het kanaal zijn gestopt.
* Standaard u slechts vijf kanalen toevoegen aan uw Media Services-account. Zie [Quota en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie .
* Je wordt alleen gefactureerd als je kanaal de **status Actief** is. Zie de sectie [Kanaalstaten en facturering](media-services-live-streaming-with-onprem-encoders.md#states) voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Aanbevolen op lokalen encoders](media-services-recommended-encoders.md)

[Azure Media Services gefragmenteerde MP4-levens inname specificatie](../media-services-fmp4-live-ingest-overview.md)

[Overzicht van Azure Media Services en veelvoorkomende scenario's](media-services-overview.md)

[Concepten van Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
