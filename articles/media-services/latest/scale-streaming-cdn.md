---
title: Inhoud streamen met CDN-integratie
titleSuffix: Azure Media Services
description: Meer informatie over streaming content met CDN-integratie, evenals prefetching en Origin-Assist CDN-Prefetch.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128056"
---
# <a name="stream-content-with-cdn-integration"></a>Inhoud streamen met CDN-integratie

Azure Content Delivery Network (CDN) biedt ontwikkelaars een globale oplossing voor het snel leveren van inhoud met hoge bandbreedte door de inhoud op strategische, fysieke knooppunten in de hele wereld in de cache op te slaan.  

CDN caches inhoud gestreamd vanaf een Media Services [Streaming Endpoint (origin)](streaming-endpoint-concept.md) per codec, per streaming protocol, per bitrate, per containerformaat en per encryptie/DRM. Voor elke combinatie van codec-streaming protocol-container format-bitrate-encryptie, zal er een aparte CDN-cache.

De populaire inhoud wordt rechtstreeks vanuit de CDN-cache weergegeven, zolang het videofragment in de cache wordt opgeslagen. Live content wordt waarschijnlijk in de cache opgeslagen omdat veel mensen naar exact hetzelfde kijken. On-demand content kan een beetje lastiger zijn omdat je wat inhoud hebben die populair is en sommige niet. Als u miljoenen video-activa waar geen van hen zijn populair (slechts een of twee kijkers per week), maar je hebt duizenden mensen kijken naar alle verschillende video's, de CDN wordt veel minder effectief.

Je moet ook overwegen hoe adaptieve streaming werkt. Elk afzonderlijk videofragment wordt in de cache opgeslagen als zijn eigen entiteit. Stel je bijvoorbeeld de eerste keer voor dat een bepaalde video wordt bekeken. Als de kijker hier en daar slechts een paar seconden kijkt, worden alleen de videofragmenten die zijn gekoppeld aan wat de persoon heeft bekeken in de cache opgeslagen in CDN. Met adaptieve streaming heb je meestal 5 tot 7 verschillende bitrates video. Als een persoon kijkt naar een bitrate en een andere persoon kijkt naar een andere bitrate, dan zijn ze elk apart in de cache in het CDN. Zelfs als twee mensen kijken naar dezelfde bitrate, kunnen ze worden gestreamd via verschillende protocollen. Elk protocol (HLS, MPEG-DASH, Smooth Streaming) wordt apart opgeslagen. Dus elke bitrate en protocol worden apart in de cache opgeslagen en alleen de videofragmenten die zijn aangevraagd, worden in de cache opgeslagen.

Bij de beslissing om CDN in te schakelen op het [streaming-eindpunt van](streaming-endpoint-concept.md)Media Services, moet u rekening houden met het aantal verwachte kijkers. CDN helpt alleen als je veel kijkers verwacht voor je content. Als de maximale gelijktijdigheid van kijkers lager is dan 500, is het raadzaam om CDN uit te schakelen, omdat CDN het beste schaalt met gelijktijdigheid.

In dit onderwerp wordt gesproken over het inschakelen van [CDN-integratie](#enable-azure-cdn-integration). Het verklaart ook prefetching (active caching) en het [Origin-Assist CDN-Prefetch](#origin-assist-cdn-prefetch) concept.

## <a name="considerations"></a>Overwegingen

* Het [streaming-eindpunt](streaming-endpoint-concept.md) `hostname` en de streaming-URL blijven hetzelfde, ongeacht of u CDN inschakelt of niet.
* Als u de mogelijkheid nodig hebt om uw inhoud met of zonder CDN te testen, maakt u een ander streamingeindpunt dat niet is ingeschakeld voor CDN.

## <a name="enable-azure-cdn-integration"></a>Azure CDN-integratie inschakelen

> [!IMPORTANT]
> U CDN niet inschakelen voor proefversies of Azure-accounts voor studenten.
>
> CDN-integratie is ingeschakeld in alle Azure-datacenters, behalve de regio's van de federale overheid en China.

Nadat een streaming-eindpunt is ingericht met CDN ingeschakeld, is er een bepaalde wachttijd op Media Services voordat DNS-update wordt uitgevoerd om het streaming-eindpunt in kaart te brengen naar CDN-eindpunt.

Als u het CDN later wilt uitschakelen/inschakelen, moet uw streaming-eindpunt in de **gestopte** status staan. Het kan tot twee uur duren voordat de Azure CDN-integratie is ingeschakeld en dat de wijzigingen actief zijn voor alle CDN-POP's. U echter uw streaming-eindpunt starten en streamen zonder onderbrekingen vanaf het streaming-eindpunt. Zodra de integratie is voltooid, wordt de stream geleverd vanuit het CDN. Tijdens de inrichtingsperiode bevindt uw streaming-eindpunt zich in de **beginstatus** en u de verminderde prestaties waarnemen.

Wanneer het standaardstreamingeindpunt wordt gemaakt, wordt het standaard geconfigureerd met Standaard Verizon. U Premium Verizon- of Standard Akamai-providers configureren met REST API's.

Azure Media Services-integratie met Azure CDN wordt geïmplementeerd op **Azure CDN van Verizon** voor standaard streaming eindpunten. Premium streaming-eindpunten kunnen worden geconfigureerd met alle **Azure CDN-prijsniveaus en -providers.**

> [!NOTE]
> Zie het [CDN-overzicht](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN.

## <a name="determine-if-a-dns-change-was-made"></a>Bepalen of er een DNS-wijziging is aangebracht

U met behulp van <https://www.digwebinterface.com>. Als u azureedge.net domeinnamen in de resultaten ziet, wordt het verkeer nu naar het CDN gericht.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

CDN caching is een reactief proces. Als CDN kan voorspellen wat het volgende object wordt aangevraagd, kan CDN het volgende object proactief opvragen en in de cache bewaren. Met dit proces u een cache-hit voor alle (of de meeste) van de objecten, die de prestaties verbetert.

Het concept van prefetching streeft ernaar om objecten te plaatsen aan de "rand van het internet" in afwachting dat deze zullen worden gevraagd door de speler op korte termijn, waardoor de tijd om dat object te leveren aan de speler.

Om dit doel te bereiken, moeten een streaming endpoint (origin) en CDN op een aantal manieren hand in hand werken:

- De oorsprong van Media Services moet de "intelligentie" (Origin-Assist) hebben om CDN te informeren over het volgende object dat vooraf moet worden opgehaald.
- CDN doet de prefetch en caching (CDN-prefetch deel). CDN moet ook de "intelligentie" om de oorsprong te informeren of het nu een prefetch of een regelmatige halen, omgaan met de 404 reacties, en een manier om eindeloze prefetch lus te voorkomen.

### <a name="benefits"></a>Voordelen

De voordelen van de *Origin-Assist CDN-Prefetch-functie* zijn:

- Prefetch verbetert de kwaliteit van het afspelen van video's door verwachte videosegmenten vooraf aan de rand te positioneren tijdens het afspelen, de latentie voor de kijker te verminderen en de downloadtijden van het videosegment te verbeteren. Dit resulteert in een snellere opstarttijd voor video's en lagere rebufferingsgebeurtenissen.
- Dit concept is van toepassing op het algemene CDN-oorsprongsscenario en is niet beperkt tot media.
- Akamai heeft deze functie toegevoegd aan [Akamai Cloud Embed (ACE).](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)

> [!NOTE]
> Deze functie is nog niet van toepassing op het Akamai CDN-eindpunt dat is geïntegreerd met het streamingeindpunt van Media Services. Het is echter beschikbaar voor Media Services-klanten die een reeds bestaand Akamai-contract hebben en aangepaste integratie vereisen tussen Akamai CDN en de oorsprong van Media Services.

### <a name="how-it-works"></a>Hoe werkt het?

CDN-ondersteuning `Origin-Assist CDN-Prefetch` voor de headers (voor zowel live als video on-demand streaming) is beschikbaar voor klanten die een direct contract hebben met Akamai CDN. De functie omvat de volgende HTTP-header uitwisselingen tussen Akamai CDN en de oorsprong van Media Services:

|HTTP-koptekst|Waarden|Afzender|Ontvanger|Doel|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (standaard) of 0 |CDN|Oorsprong|Als u wilt aangeven dat CDN vooraf is ingeschakeld.|
|`CDN-Origin-Assist-Prefetch-Path`| Voorbeeld: <br/>Fragmenten(video=14000000000,format=mpd-time-cmaf)|Oorsprong|CDN|Prefetch-pad naar CDN verstrekken.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (prefetch-aanvraag) of 0 (regulier verzoek)|CDN|Oorsprong|Het aangeven van het verzoek van CDN is een prefetch.|

Als u een deel van de header-uitwisseling in actie wilt zien, u de volgende stappen proberen:

1. Gebruik Postman of cURL om een verzoek uit te geven aan de oorsprong van mediaservices voor een audio- of videosegment of fragment. Zorg ervoor dat `CDN-Origin-Assist-Prefetch-Enabled: 1` u de koptekst toevoegt aan de aanvraag.
2. In het antwoord moet u `CDN-Origin-Assist-Prefetch-Path` de koptekst met een relatief pad als waarde zien.

### <a name="supported-streaming-protocols"></a>Ondersteunde streamingprotocollen

De `Origin-Assist CDN-Prefetch` functie ondersteunt de volgende streamingprotocollen voor live en on-demand streaming:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Vloeiende streaming

### <a name="faqs"></a>Veelgestelde vragen

* Wat gebeurt er als een prefetch-pad-URL ongeldig is, zodat CDN prefetch een 404 krijgt?

    CDN cachet slechts een 404-respons gedurende 10 seconden (of andere geconfigureerde waarde).

* Stel dat u een on-demand video hebt. Als CDN-prefetch is ingeschakeld, impliceert deze functie dan dat zodra een client het eerste videosegment aanvraagt, prefetch een lus start om alle volgende videosegmenten op dezelfde bitrate te prefetchen?

    Nee, CDN-prefetch wordt alleen gedaan na een door de klant geïnitieerde aanvraag/reactie. CDN-prefetch wordt nooit geactiveerd door een prefetch, om een prefetch-lus te voorkomen.

* Is de Origin-Assist CDN-Prefetch-functie altijd ingeschakeld? Hoe kan het worden in-/uitgeschakeld?

    Deze functie is standaard uitgeschakeld. Klanten moeten het inschakelen via Akamai API.

* Wat gebeurt er met Origin-Assist als het volgende segment of fragment nog niet beschikbaar is?

    In dit geval biedt `CDN-Origin-Assist-Prefetch-Path` de oorsprong van Media Services geen header en treedt CDN-prefetch niet op.

* Hoe `Origin-Assist CDN-Prefetch` werkt het met dynamische manifestfilters?

    Deze functie werkt onafhankelijk van manifestfilter. Wanneer het volgende fragment uit een filtervenster is, wordt de URL nog steeds gelokaliseerd door naar het raw-clientmanifest te kijken en vervolgens terug te keren als CDN-voorhaal-antwoordkop. Dus CDN krijgt de URL van een fragment dat is gefilterd uit DASH / HLS / Smooth manifest. De speler zal echter nooit een GET-verzoek doen aan CDN om dat fragment op te halen, omdat dat fragment niet is opgenomen in het DASH/HLS/Smooth-manifest dat door de speler wordt bewaard (de speler weet niet het bestaan van dat fragment).

* Kan DASH MPD/HLS playlist/Smooth manifest vooraf worden opgehaald?

    Nee, DASH MPD, HLS-hoofdafspeellijst, HLS-variantafspeellijst of vloeiende manifest-URL wordt niet toegevoegd aan de prefetch-header.

* Zijn prefetch-URL's relatief of absoluut?

    Hoewel Akamai CDN beide toestaat, biedt de oorsprong van Media Services alleen relatieve URL's voor prefetch-pad, omdat er geen duidelijk voordeel is in het gebruik van absolute URL's.

* Werkt deze functie met drm-beveiligde inhoud?

    Ja, aangezien deze functie op HTTP-niveau werkt, wordt het geen segment/fragment gedecodeerd of ontsnapt. Het maakt niet uit of de inhoud is versleuteld of niet.

* Werkt deze functie met Server Side Ad Insertion (SSAI)?
    
    Dit geldt voor originele/hoofdinhoud (de oorspronkelijke video-inhoud vóór het invoegen van advertenties) werkt, omdat SSAI de tijdstempel van de broninhoud van de oorsprong van Media Services niet wijzigt. Of deze functie werkt met advertentie-inhoud, hangt af van de vraag of de oorsprong van advertenties Origin-Assist ondersteunt. Als advertentie-inhoud bijvoorbeeld ook wordt gehost in Azure Media Services (dezelfde of afzonderlijke oorsprong), wordt de advertentie-inhoud ook vooraf opgehaald.

* Werkt deze functie met UHD/HEVC-inhoud?

    Ja.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* Controleer het document [Streaming Endpoint (Origin).](streaming-endpoint-concept.md)
* Het voorbeeld [in deze repository](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) laat zien hoe u het standaard streaming eindpunt start met .NET.
