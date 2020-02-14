---
title: Streaming-eind punten (oorsprong)
titleSuffix: Azure Media Services
description: Meer informatie over streaming-eind punten (oorsprong), een dynamische verpakkings-en streaming-service die inhoud rechtstreeks levert aan een client speler of een Content Delivery Network (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/11/2020
ms.author: juliako
ms.openlocfilehash: 14fee047e1f62ae7f7d3484d89779e1512e4bab7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198714"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming-eind punten (oorsprong) in Azure Media Services

In Microsoft Azure Media Services vertegenwoordigt een [streaming-eind punt](https://docs.microsoft.com/rest/api/media/streamingendpoints) een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren met behulp van een van de algemene protocollen voor streaming media (HLS of Dash). Daarnaast biedt het **streaming-eind punt** dynamische code ring (just-in-time) voor toonaangevende DRMs.

Wanneer u een Media Services account maakt, wordt er een **standaard** streaming-eind punt voor u gemaakt met de status gestopt. U kunt het **standaard** streaming-eind punt niet verwijderen. Er kunnen meer streaming-eind punten worden gemaakt onder het account (Zie [quota's en beperkingen](limits-quotas-constraints.md)).

> [!NOTE]
> Als u Video's wilt streamen, moet u het **streaming-eind punt** starten van waaruit u de video wilt streamen.
>
> U wordt alleen gefactureerd wanneer uw streaming-eind punt de status actief heeft.

## <a name="naming-convention"></a>Naamgevings Conventie

De indeling van de hostnaam van de streaming-URL is: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, waarbij `servicename` = de naam van het streaming-eind punt of de naam van de live-gebeurtenis.

Wanneer u het standaard streaming-eind punt gebruikt, wordt `servicename` wegge laten, zodat de URL: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Beperkingen

* De naam van het streaming-eind punt heeft een maximale waarde van 24 tekens.
* De naam moet volgen op dit [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) -patroon: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typen

Er zijn twee typen **streaming-eind punten** : **Standard** (preview) en **Premium**. Het type wordt gedefinieerd door het aantal schaal eenheden (`scaleUnits`) dat u toewijst voor het streaming-eind punt.

In de tabel worden de typen beschreven:

|Type|Schaaleenheden|Beschrijving|
|--------|--------|--------|  
|**Standard**|0|Het standaard streaming-eind punt is een **standaard** type. Dit kan worden gewijzigd in het Premium-type door `scaleUnits`aan te passen.|
|**Premium**|>0|**Premium** Streaming-eind punten zijn geschikt voor geavanceerde workloads en bieden specifieke en schaal bare bandbreedte capaciteit. U gaat naar een **Premium** -type door `scaleUnits` (streaming-eenheden) aan te passen. `scaleUnits` bieden u speciale uitvoer capaciteit die kan worden aangeschaft in stappen van 200 Mbps. Wanneer u het **Premium** -type gebruikt, biedt elke ingeschakelde eenheid extra bandbreedte capaciteit voor de app. |

> [!NOTE]
> Voor klanten die inhoud willen leveren aan grote Internet doelgroepen, raden we u aan CDN op het streaming-eind punt in te scha kelen.

Zie [prijzen en sla](https://azure.microsoft.com/pricing/details/media-services/)voor informatie over sla.

## <a name="comparing-streaming-types"></a>Streaming-typen vergelijken

Functie|Standard|Premium
---|---|---
Doorvoer |Maxi maal 600 Mbps en kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.|200 Mbps per streaming-eenheid (SU). Kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.
CDN|Azure CDN, CDN van derden of geen CDN.|Azure CDN, CDN van derden of geen CDN.
Facturering is naar verhouding| Dagelijks|Dagelijks
Dynamische versleuteling|Ja|Ja
Dynamische verpakking|Ja|Ja
Schalen|Automatisch geschaald naar de beoogde door voer.|Extra SUs
IP-filtering/G20/aangepaste host <sup>1</sup>|Ja|Ja
Progressieve down load|Ja|Ja
Aanbevolen gebruik |Aanbevolen voor de meeste streaming-scenario's.|Professioneel gebruik.

<sup>1</sup> wordt alleen rechtstreeks op het streaming-eind punt gebruikt wanneer CDN niet is ingeschakeld op het eind punt.<br/>

## <a name="properties"></a>Eigenschappen

Deze sectie bevat informatie over een aantal eigenschappen van het streaming-eind punt. Zie [streaming-eind punt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)voor voor beelden van het maken van een nieuw streaming-eind punt en beschrijvingen van alle eigenschappen.

- `accessControl`: wordt gebruikt voor het configureren van de volgende beveiligings instellingen voor dit streaming-eind punt: Akamai-handtekening header verificatie sleutels en IP-adressen die verbinding mogen maken met dit eind punt. Deze eigenschap kan alleen worden ingesteld als `cdnEnabled` is ingesteld op false.

- `cdnEnabled`: geeft aan of de Azure CDN-integratie voor dit streaming-eind punt is ingeschakeld (standaard uitgeschakeld). Als u `cdnEnabled` instelt op True, worden de volgende configuraties uitgeschakeld: `customHostNames` en `accessControl`.

    Niet alle data centers bieden ondersteuning voor de integratie van Azure CDN. Voer de volgende stappen uit om te controleren of de Azure CDN integratie beschikbaar is voor uw Data Center:

  - Probeer de `cdnEnabled` in te stellen op True.
  - Het geretourneerde resultaat voor een `HTTP Error Code 412` (PreconditionFailed) controleren met een bericht van de eigenschap CdnEnabled van het streaming-eind punt kan niet worden ingesteld op True omdat de CDN-mogelijkheid niet beschikbaar is in de huidige regio.

    Als deze fout wordt weer gegeven, ondersteunt het data centrum het niet. Probeer een ander Data Center.

- `cdnProfile`: als `cdnEnabled` is ingesteld op waar, kunt u ook `cdnProfile` waarden door geven. `cdnProfile` is de naam van het CDN-profiel waar het CDN-eind punt wordt gemaakt. U kunt een bestaande cdnProfile opgeven of een nieuwe maken. Als waarde NULL is en `cdnEnabled` waar is, wordt de standaard waarde ' AzureMediaStreamingPlatformCdnProfile ' gebruikt. Als de gegeven `cdnProfile` al bestaat, wordt er een eind punt gemaakt. Als het profiel niet bestaat, wordt er automatisch een nieuw profiel gemaakt.
- `cdnProvider`: wanneer CDN is ingeschakeld, kunt u ook `cdnProvider` waarden door geven. `cdnProvider` bepaalt welke provider wordt gebruikt. Op dit moment worden drie waarden ondersteund: "StandardVerizon", "PremiumVerizon" en "StandardAkamai". Als er geen waarde wordt gegeven en `cdnEnabled` is ingesteld op True, wordt ' StandardVerizon ' gebruikt (dat is de standaard waarde).
- `crossSiteAccessPolicies`: wordt gebruikt om beleid voor meerdere sites op te geven voor verschillende clients. Zie voor meer informatie [beleid voor meerdere domein beleids regels](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) en [het beschikbaar maken van een service over domein grenzen](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). De instellingen zijn alleen van toepassing op Smooth Streaming.
- `customHostNames`: wordt gebruikt om een streaming-eind punt te configureren voor het accepteren van verkeer dat naar een aangepaste hostnaam wordt gestuurd. Deze eigenschap is geldig voor standaard-en Premium-streaming-eind punten en kan worden ingesteld als `cdnEnabled`: False.

    Het eigendom van de domein naam moet worden bevestigd door Media Services. Media Services controleert het eigendom van de domein naam door een `CName` record te vereisen met de Media Services-account-ID als onderdeel dat moet worden toegevoegd aan het domein dat wordt gebruikt. Een voor beeld: voor het gebruik van ' sports.contoso.com ' als een aangepaste hostnaam voor het streaming-eind punt moet een record voor `<accountId>.contoso.com` worden geconfigureerd om te verwijzen naar een van Media Services namen van verificatie-hostnamen. De naam van de verificatie-host bestaat uit verifydns.\<Media Services-DNS-zone >.

    Hieronder ziet u de verwachte DNS-zones die moeten worden gebruikt in de record controleren voor verschillende Azure-regio's.
  
  - Noord-Amerika, Europa, Singapore, Hong Kong SAR, Japan:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Bijvoorbeeld, een `CName` record waarmee "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" aan "verifydns.media.azure.net" wordt toegewezen, bewijst dat de Media Services-ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad het eigendom van het contoso.com domein heeft, waardoor elke naam onder contoso.com kan worden gebruikt als een aangepaste hostnaam voor een streaming-eind punt onder dat account. Als u de waarde voor de media service-ID wilt vinden, gaat u naar de [Azure Portal](https://portal.azure.com/) en selecteert u uw media service-account. De **account-id** wordt weer gegeven in de rechter bovenhoek van de pagina.

    Als er een poging wordt gedaan om een aangepaste hostnaam in te stellen zonder de juiste verificatie van de `CName` record, mislukt het DNS-antwoord en wordt het enige tijd in de cache opgeslagen. Zodra de juiste record is geplaatst, kan het enige tijd duren voordat het antwoord in de cache opnieuw is gevalideerd. Afhankelijk van de DNS-provider voor het aangepaste domein, duurt het enkele minuten tot een uur om de record opnieuw te valideren.

    Naast de `CName` die `<accountId>.<parent domain>` aan `verifydns.<mediaservices-dns-zone>`toewijst, moet u een andere `CName` maken die de naam van de aangepaste host (bijvoorbeeld `sports.contoso.com`) toewijst aan de hostnaam van het Media Services streaming-eind punt (bijvoorbeeld `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Streaming-eind punten die zich in hetzelfde Data Center bevinden, kunnen niet dezelfde aangepaste hostnaam delen.

    Op dit moment biedt Media Services geen ondersteuning voor SSL met aangepaste domeinen.

- `maxCacheAge`-overschrijft de standaard instelling voor de HTTP-cache van de Max-Age-header in het streaming-eind punt op media fragmenten en on-demand-manifesten. De waarde wordt ingesteld in seconden.
- `resourceState` -

    - Gestopt: de initiële status van een streaming-eind punt nadat deze is gemaakt
    - Starten: wordt overgezet naar de actieve status
    - Uitvoeren: kan inhoud streamen naar clients
    - Schalen: de schaal eenheden worden verg root of verkleind
    - Stoppen: wordt overgezet naar de status gestopt
    - Verwijderen: wordt verwijderd

- `scaleUnits`: bieden u speciale uitvoer capaciteit die kan worden aangeschaft in stappen van 200 Mbps. Als u wilt overstappen op een **Premium** -type, moet u `scaleUnits`aanpassen.

## <a name="working-with-cdn"></a>Werken met CDN

In de meeste gevallen moet CDN zijn ingeschakeld. Als u echter wilt anticiperen op het maximale gelijktijdigheid van een limiet van 500 viewers, is het raadzaam om CDN uit te scha kelen omdat CDN het beste met gelijktijdigheid kan worden geschaald.

### <a name="considerations"></a>Overwegingen

* Het streaming-eind punt `hostname` en de streaming-URL blijven hetzelfde, ongeacht of u CDN inschakelt.
* Als u de mogelijkheid wilt bieden om uw inhoud te testen met of zonder CDN, maakt u een ander streaming-eind punt dat niet is ingeschakeld voor CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Gedetailleerde uitleg over hoe caching werkt

Er is geen specifieke bandbreedte waarde bij het toevoegen van het CDN, omdat de hoeveelheid band breedte die nodig is voor een streaming-eind punt waarvoor CDN is ingeschakeld, varieert. Een partij is afhankelijk van het type inhoud, hoe populair het is, de bitrates en de protocollen. De CDN is alleen in de cache opgeslagen wat er wordt aangevraagd. Dit betekent dat populaire inhoud rechtstreeks vanuit het CDN wordt bediend, op voor waarde dat het video fragment in de cache wordt opgeslagen. Live-inhoud wordt waarschijnlijk in de cache opgeslagen, omdat er meestal veel mensen precies hetzelfde zijn. Inhoud op aanvraag kan een beetje trickier, omdat u een aantal inhoud zou kunnen hebben die populair is en niet. Als u miljoenen video-assets hebt waar geen van de elementen populair zijn (slechts één of twee kijkers per week), maar duizenden personen die alle verschillende Video's volgen, wordt het CDN veel minder effectief. Met deze cache missers verhoogt u de belasting van het streaming-eind punt.

U moet ook nadenken over de werking van adaptieve streaming. Elk afzonderlijk video fragment wordt in de cache opgeslagen als een eigen entiteit. Stel bijvoorbeeld dat de eerste keer dat een bepaalde video wordt bekeken. Als er in de Viewer slechts enkele seconden worden weer gegeven, worden alleen de video fragmenten die zijn gekoppeld aan wat de persoon die wordt bekeken in de cache in het CDN opgeslagen. Met adaptieve streaming hebt u doorgaans 5 tot 7 verschillende bitrates voor video. Als één persoon een bitrate bekijkt en een andere persoon een andere bitsnelheid bekijkt, worden ze afzonderlijk in het CDN opgeslagen. Zelfs als twee mensen dezelfde bitsnelheid volgen, kunnen ze worden gestreamd via verschillende protocollen. Elk protocol (HLS, MPEG-DASH, Smooth Streaming) wordt afzonderlijk in de cache opgeslagen. Elke bitsnelheid en elk protocol worden afzonderlijk in de cache opgeslagen en alleen de video fragmenten die zijn aangevraagd, worden in de cache opgeslagen.

### <a name="enable-azure-cdn-integration"></a>Integratie van Azure CDN inschakelen

Nadat een streaming-eind punt is ingericht met CDN ingeschakeld, is er een gedefinieerde wacht tijd op Media Services voordat de DNS-update wordt uitgevoerd om het streaming-eind punt toe te wijzen aan het CDN-eind punt.

Als u het CDN later wilt uitschakelen/inschakelen, moet het streaming-eind punt de status **gestopt** hebben. Het kan tot twee uur duren voordat de Azure CDN integratie is ingeschakeld en de wijzigingen voor alle CDN-Pop's actief zijn. U kunt echter het streaming-eind punt starten en streamen zonder onderbreking van het streaming-eind punt en zodra de integratie is voltooid, wordt de stroom vanuit het CDN geleverd. Tijdens de inrichtings periode bevinden uw streaming-eind punt zich in de **Start** status en kunt u gedegradeerde prestaties waarnemen.

Wanneer het standaard streaming-eind punt wordt gemaakt, wordt het standaard geconfigureerd met standaard Verizon. U kunt Premium Verizon-of Standard Akamai-providers configureren met behulp van REST Api's.

CDN-integratie is ingeschakeld in alle Azure-data centers, met uitzonde ring van China en federale overheids regio's.

Azure Media Services integratie met Azure CDN is geïmplementeerd op **Azure CDN van Verizon** voor Standard streaming-eind punten. Premium streaming-eind punten kunnen worden geconfigureerd met alle **Azure CDN prijs categorieën en providers**. 

> [!IMPORTANT]
> Zie het [CDN-overzicht](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN.

### <a name="determine-if-dns-change-was-made"></a>Bepalen of de DNS-wijziging is doorgevoerd

U kunt bepalen of de DNS-wijziging is doorgevoerd in een streaming-eind punt (het verkeer wordt omgeleid naar de Azure CDN) met behulp van https://www.digwebinterface.com. Als de resultaten azureedge.net domein namen in de resultaten heeft, wordt het verkeer nu naar het CDN gewijsd.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

[Overzicht van CDN](../../cdn/cdn-overview.md)

## <a name="next-steps"></a>Volgende stappen

Het voor beeld [in deze opslag plaats](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) laat zien hoe u het standaard streaming-eind punt met .net kunt starten.
