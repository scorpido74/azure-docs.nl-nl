---
title: Streaming eindpunten (Origin)
titleSuffix: Azure Media Services
description: Meer informatie over Streaming Endpoints (Origin), een dynamische verpakkings- en streamingservice die inhoud rechtstreeks levert aan een clientplayer-app of aan een Content Delivery Network (CDN).
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
ms.openlocfilehash: 1a2a370ac92ea3edf925d97af6f5e721c79d0d2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529693"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming Endpoints (Origin) in Azure Media Services

In Microsoft Azure Media Services vertegenwoordigt een [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) een dynamische (just-in-time) verpakkings- en herkomstservice die uw live en on-demand content rechtstreeks aan een clientplayer-app kan leveren met behulp van een van de gebruikelijke streaming mediaprotocollen (HLS of DASH). Daarnaast biedt het **Streaming Endpoint** dynamische (just-in-time) encryptie aan toonaangevende DRM's. 

Wanneer u een Media Services-account maakt, wordt een **standaard** streamingeindpunt voor u gemaakt in een gestopte status. U het **standaard** eindpunt voor streaming niet verwijderen. Er kunnen meer streaming eindpunten worden gemaakt onder het account (zie [Quota en beperkingen).](limits-quotas-constraints.md)

> [!NOTE]
> Als je video's wilt streamen, moet je het **streamingeindpunt** starten van waaruit je de video wilt streamen.
>
> Er worden alleen kosten in rekening gebracht wanneer uw streamingeindpunt in de status van actief is.

Zorg ervoor dat u ook het [Dynamic-verpakkingsonderwerp bekijkt.](dynamic-packaging-overview.md) 

## <a name="naming-convention"></a>Naming

De hostnaamnotatie van de `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`streaming-URL is: , waar `servicename` = de naam van het streaming eindpunt of de naam van de live-gebeurtenis.

Bij het gebruik van `servicename` het standaard streaming eindpunt `{accountname}-{regionname}.streaming.azure.net`wordt weggelaten, zodat de URL is: .

### <a name="limitations"></a>Beperkingen

* De naam van het streaming eindpunt heeft een maximale waarde van 24 tekens.
* De naam moet dit [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) patroon volgen: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typen

Er zijn twee **typen streamingeindpunten:** **Standaard** (voorbeeld) en **Premium**. Het type wordt gedefinieerd door het`scaleUnits`aantal schaaleenheden ( ) dat u toewijst voor het streaming eindpunt.

In de tabel worden de typen beschreven:

|Type|Schaaleenheden|Beschrijving|
|--------|--------|--------|  
|**Standaard**|0|Het standaard streamingeindpunt is een **standaardtype:** het kan worden `scaleUnits`gewijzigd in het Premium-type door het aanpassen van .|
|**Premium**|>0|**Premium** Streaming Endpoints zijn geschikt voor geavanceerde workloads en bieden speciale en schaalbare bandbreedtecapaciteit. U gaat naar een **Premium-type** door aan te passen `scaleUnits` (streaming-eenheden). `scaleUnits`bieden u speciale uitgangscapaciteit die kan worden gekocht in stappen van 200 Mbps. Bij het gebruik van het **Premium-type** biedt elke ingeschakelde eenheid extra bandbreedtecapaciteit aan de app. |

> [!NOTE]
> Voor klanten die content willen leveren aan grote internetdoelgroepen, raden we u aan CDN in te schakelen op het Streaming Endpoint.

Zie [Prijzen en SLA](https://azure.microsoft.com/pricing/details/media-services/)voor SLA-informatie .

## <a name="comparing-streaming-types"></a>Streamingtypen vergelijken

Functie|Standard|Premium
---|---|---
Doorvoer |Tot 600 Mbps en kan een veel hogere effectieve doorvoer bieden wanneer een CDN wordt gebruikt.|200 Mbps per streaming unit (SU). Kan zorgen voor een veel hogere effectieve doorvoer wanneer een CDN wordt gebruikt.
CDN|Azure CDN, CDN van derden of geen CDN.|Azure CDN, CDN van derden of geen CDN.
Facturering wordt naar rato berekend| Dagelijks|Dagelijks
Dynamische versleuteling|Ja|Ja
Dynamische verpakking|Ja|Ja
Schalen|Auto schaalt op naar de beoogde doorvoer.|Extra sus
IP-filtering/G20/Aangepaste host <sup>1</sup>|Ja|Ja
Progressieve download|Ja|Ja
Aanbevolen gebruik |Aanbevolen voor de overgrote meerderheid van de streaming scenario's.|Professioneel gebruik.

<sup>1</sup> Alleen rechtstreeks gebruikt op het streamingeindpunt wanneer het CDN niet is ingeschakeld op het eindpunt.<br/>

## <a name="streaming-endpoint-properties"></a>Eigenschappen van streamingeindpunt

In dit gedeelte vindt u meer informatie over enkele eigenschappen van het streamingeindpunt. Zie [Eindpunt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)streamen voor voorbeelden van het maken van een nieuw streamingeindpunt en beschrijvingen van alle eigenschappen.

- `accessControl`: Wordt gebruikt om de volgende beveiligingsinstellingen voor dit streamingeindpunt te configureren: Akamai Signature Header Authentication-sleutels en IP-adressen die verbinding kunnen maken met dit eindpunt. Deze eigenschap kan alleen `cdnEnabled` worden ingesteld wanneer deze is ingesteld op false.

- `cdnEnabled`: Geeft aan of de Azure CDN-integratie voor dit streaming-eindpunt is ingeschakeld (standaard uitgeschakeld). Als u `cdnEnabled` true instelt, worden de `customHostNames` volgende `accessControl`configuraties uitgeschakeld: en .

    Niet alle datacenters ondersteunen de Azure CDN-integratie. Ga als volgt te werk om te controleren of in uw datacenter de Azure CDN-integratie beschikbaar is:

  - Probeer de `cdnEnabled` ware in te stellen.
  - Controleer het geretourneerde `HTTP Error Code 412` resultaat op een (PreconditionFailed) met een bericht 'Eigenschap Streaming endpoint CdnEnabled kan niet worden ingesteld op true omdat cdn-mogelijkheden niet beschikbaar zijn in de huidige regio'.

    Als u deze fout krijgt, ondersteunt het datacenter deze niet. Probeer een ander datacenter.

- `cdnProfile`: `cdnEnabled` Wanneer is ingesteld op true, u ook waarden doorgeven. `cdnProfile` `cdnProfile`is de naam van het CDN-profiel waar het CDN-eindpunt wordt gemaakt. U een bestaand cdnProfiel verstrekken of een nieuwe cdn gebruiken. Als de waarde `cdnEnabled` NULL is en waar is, wordt de standaardwaarde 'AzureMediaStreamingPlatformCdnProfile' gebruikt. Als de `cdnProfile` geleverde al bestaat, wordt er een eindpunt onder gemaakt. Als het profiel niet bestaat, wordt er automatisch een nieuw profiel gemaakt.
- `cdnProvider`: Wanneer CDN is ingeschakeld, `cdnProvider` kunt u ook waarden doorgeven. `cdnProvider`bepaalt welke provider zal worden gebruikt. Momenteel worden drie waarden ondersteund: "StandardVerizon", "PremiumVerizon" en "StandardAkamai". Als er geen `cdnEnabled` waarde is opgegeven en waar is, wordt "StandardVerizon" gebruikt (dat is de standaardwaarde).
- `crossSiteAccessPolicies`: Wordt gebruikt om toegangsbeleid voor meerdere plaatsen voor verschillende clients op te geven. Zie [Specificatie van beleidsbestanden voor meerdere domeinen](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) en Een service beschikbaar maken over [domeingrenzen](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). De instellingen zijn alleen van toepassing op Smooth Streaming.
- `customHostNames`: Wordt gebruikt om een streamingeindpunt te configureren om verkeer te accepteren dat is gericht op een aangepaste hostnaam. Deze eigenschap is geldig voor standaard- en Premium `cdnEnabled`streaming eindpunten en kan worden ingesteld wanneer: false.

    Het eigendom van de domeinnaam moet worden bevestigd door Media Services. Media Services verifieert het eigendom `CName` van de domeinnaam door te eisen dat een record met de Media Services-account-id als onderdeel moet worden toegevoegd aan het domein dat in gebruik is. Als voorbeeld, als u wilt dat 'sports.contoso.com' wordt gebruikt als aangepaste `<accountId>.contoso.com` hostnaam voor het streaming-eindpunt, moet een record worden geconfigureerd om een van de hostnamen van Media Services te gebruiken. De naam van de verificatiehost bestaat uit verifydns. \<mediaservices-dns-zone>.

    Hieronder volgen de verwachte DNS-zones die worden gebruikt in de verificatierecord voor verschillende Azure-regio's.
  
  - Noord-Amerika, Europa, Singapore, Hong Kong SAR, Japan:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Een `CName` record dat '945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com' in kaart brengt met verifydns.media.azure.net, bewijst bijvoorbeeld dat de Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad eigenaar is van het contoso.com domein, waardoor elke naam onder contoso.com kan worden gebruikt als aangepaste hostnaam voor een streaming eindpunt onder dat account. Als u de waarde van de Media Service-id wilt vinden, gaat u naar de [Azure-portal](https://portal.azure.com/) en selecteert u uw Media Service-account. De **account-id** wordt rechtsboven op de pagina weergegeven.

    Als er wordt geprobeerd een aangepaste hostnaam in te `CName` stellen zonder een goede verificatie van de record, mislukt het DNS-antwoord en wordt deze vervolgens enige tijd in de cache opgeslagen. Zodra een goede record is op zijn plaats, kan het een tijdje duren totdat de in de cache opgeslagen reactie opnieuw is gevalideerd. Afhankelijk van de DNS-provider voor het aangepaste domein duurt het een paar minuten tot een uur om de record opnieuw te valideren.

    Naast de `CName` kaarten `<accountId>.<parent domain>` aan `verifydns.<mediaservices-dns-zone>`, moet `CName` u een andere maken die `sports.contoso.com`de aangepaste hostnaam (bijvoorbeeld ) in kaart `amstest-usea.streaming.media.azure.net`brengt aan de hostnaam van uw Media Services Streaming Endpoint (bijvoorbeeld).

    > [!NOTE]
    > Streaming eindpunten in hetzelfde datacenter kunnen niet dezelfde aangepaste hostnaam delen.

    Op dit moment ondersteunt Media Services geen SSL met aangepaste domeinen.

- `maxCacheAge`- Overschrijft de standaard http-cachecontrolheader van max-age die is ingesteld door het streaming-eindpunt op mediafragmenten en on-demandmanifesten. De waarde wordt in enkele seconden ingesteld.
- `resourceState` -

    - Gestopt: de beginstatus van een streamingeindpunt na het maken
    - Start: gaat over naar de loopstatus
    - Actief: is in staat om inhoud naar clients te streamen
    - Schalen: de schaaleenheden worden verhoogd of verlaagd
    - Stoppen: is de overgang naar de gestopte status
    - Verwijderen: wordt verwijderd

- `scaleUnits`: Geef u speciale uitgangscapaciteit die kan worden gekocht in stappen van 200 Mbps. Als u naar een **Premium-type** wilt gaan, past u . `scaleUnits`

## <a name="why-use-multiple-streaming-endpoints"></a>Waarom meerdere streaming endpoints gebruiken?

Eén streamingeindpunt kan zowel live- als on-demand video's streamen en de meeste klanten gebruiken slechts één streamingeindpunt. In dit gedeelte vindt u enkele voorbeelden van waarom u mogelijk meerdere streaming eindpunten moet gebruiken.

* Elke gereserveerde eenheid zorgt voor 200 Mbps bandbreedte. Als u meer dan 2.000 Mbps (2 Gbps) bandbreedte nodig hebt, u het tweede streaming eindpunt en de belastingsbalans gebruiken om u extra bandbreedte te geven.

    Cdn is echter de beste manier om scale-out te bereiken voor streaming content, maar als je zoveel content levert dat het CDN meer dan 2 Gbps haalt, kun je extra streaming endpoints (origins) toevoegen. In dit geval moet u inhouds-URL's uitdelen die in evenwicht zijn voor de twee streaming eindpunten. Deze aanpak geeft een betere caching dan proberen om aanvragen te sturen naar elke oorsprong willekeurig (bijvoorbeeld via een verkeersmanager). 
    
    > [!TIP]
    > Meestal als de CDN trekt meer dan 2 Gbps dan iets kan verkeerd worden geconfigureerd (bijvoorbeeld geen oorsprong afscherming).
    
* Load balancing verschillende CDN-providers. U bijvoorbeeld het standaardeindpunt voor streaming instellen om het Verizon CDN te gebruiken en een tweede maken om Akamai te gebruiken. Voeg vervolgens wat load balancing tussen de twee toe om multi-CDN balancing te bereiken. 

    Echter, klant vaak load balancing over meerdere CDN-providers met behulp van een enkele oorsprong.
* Gemengde inhoud streamen: Live en Video on Demand. 

    De toegangspatronen voor live en on-demand content zijn zeer verschillend. De live content heeft de neiging om veel vraag naar dezelfde inhoud in een keer te krijgen. De video on-demand content (long tail archive content bijvoorbeeld) heeft een laag gebruik op dezelfde inhoud. Dus caching werkt heel goed op de live content, maar niet zo goed op de lange staart inhoud.

    Overweeg een scenario waarin uw klanten voornamelijk live content bekijken, maar slechts af en toe on-demand content bekijken en het wordt geserveerd vanaf hetzelfde Streaming Endpoint. Het lage gebruik van on-demand content zou cacheruimte inbeslagnemen die beter zou worden opgeslagen voor de live content. In dit scenario raden we aan om de live content van het ene streaming eindpunt en de lange staartcontent van een ander streamingendpunt aan te bieden. Dit zal de prestaties van de inhoud van het live evenement verbeteren.
    
## <a name="scaling-streaming-with-cdn"></a>Streaming schalen met CDN

Zie de volgende artikelen:

- [CDN-overzicht](../../cdn/cdn-overview.md)
- [Streaming schalen met CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Stel vragen en ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

[Dynamische verpakking](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Streaming-eindpunten beheren](manage-streaming-endpoints-howto.md)
