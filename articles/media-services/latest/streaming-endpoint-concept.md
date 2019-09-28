---
title: Streaming-eind punten (oorsprong) in Azure Media Services | Microsoft Docs
description: In Azure Media Services vertegenwoordigt een streaming-eind punt (oorsprong) een dynamische verpakkings-en streaming-service waarmee inhoud rechtstreeks kan worden geleverd aan een client speler of een Content Delivery Network (CDN) voor verdere distributie.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd1dc7b55060e8262b300022f5ffd1b4da5f7922
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350346"
---
# <a name="streaming-endpoints"></a>Streaming-eindpunten 

In Microsoft Azure Media Services vertegenwoordigt een [streaming-eind punt](https://docs.microsoft.com/rest/api/media/streamingendpoints) een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren, met behulp van een van de algemene streaming-media protocollen (HLS of DASH). Daarnaast biedt het **streaming-eind punt** dynamische (just-in-time) versleuteling voor de toonaangevende DRMs.

Wanneer u een Media Services account maakt, wordt er een **standaard** streaming-eind punt voor u gemaakt met de status gestopt. U kunt het **standaard** streaming-eind punt niet verwijderen. Er kunnen extra streaming-eind punten worden gemaakt onder het account (Zie [quota's en beperkingen](limits-quotas-constraints.md)). 

> [!NOTE]
> Als u Video's wilt streamen, moet u het **streaming-eind punt** starten van waaruit u de video wilt streamen. 
>  
> U wordt alleen gefactureerd wanneer uw streaming-eind punt de status actief heeft.

## <a name="naming-convention"></a>Naamgevings Conventie

De indeling van de hostnaam van de streaming-URL is: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, waarbij `servicename` = de naam van het streaming-eind punt of de naam van de live-gebeurtenis. 

Wanneer het standaard streaming-eind punt wordt gebruikt, wordt `servicename` wegge laten, zodat de URL: `{accountname}-{regionname}.streaming.azure.net`. 

### <a name="limitations"></a>Beperkingen

* De naam van het streaming-eind punt heeft een maximale waarde van 24 tekens.
* De naam moet volgen op dit [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) -patroon: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typen  

Er zijn twee typen **streaming-eindpunten**: **Standaard** (preview) en **Premium**. Het type wordt gedefinieerd door het aantal schaal eenheden (`scaleUnits`) dat u toewijst voor het streaming-eind punt. 

In de tabel worden de typen beschreven:  

|type|Schaaleenheden|Description|
|--------|--------|--------|  
|**Standard**|0|Het standaard streaming-eind punt is een **standaard** type, dat kan worden gewijzigd in het Premium `scaleUnits`-type door de aanpassing.|
|**Premium**|>0|**Premium** Streaming-eind punten zijn geschikt voor geavanceerde workloads, met speciale en schaal bare bandbreedte capaciteit. U gaat naar een **Premium** -type door `scaleUnits` aanpassing (streaming units). `scaleUnits`Geef een specifieke uitvoerige capaciteit die kan worden aangeschaft in stappen van 200 Mbps. Bij gebruik van het **Premium**-type biedt elke ingeschakelde eenheid extra bandbreedte voor de toepassing. |

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

<sup>1</sup> alleen rechtstreeks op het streaming-eind punt gebruikt wanneer CDN niet is ingeschakeld op het eind punt.<br/>

## <a name="properties"></a>properties 

Deze sectie bevat informatie over een aantal eigenschappen van het streaming-eind punt. Zie [streaming-eind punt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)voor voor beelden van het maken van een nieuw streaming-eind punt en beschrijvingen van alle eigenschappen. 

- `accessControl`-Wordt gebruikt voor het configureren van de volgende beveiligings instellingen voor dit streaming-eind punt: Akamai en IP-adressen die verbinding mogen maken met dit eind punt, worden door de handtekening header geverifieerd.<br />Deze eigenschap kan alleen worden ingesteld als `cdnEnabled` deze is ingesteld op false.
- `cdnEnabled`-Geeft aan of de Azure CDN integratie voor dit streaming-eind punt is ingeschakeld (standaard uitgeschakeld). Als u instelt `cdnEnabled` op True, worden de volgende configuraties uitgeschakeld: `customHostNames` en `accessControl`.
  
    Niet alle data centers bieden ondersteuning voor de integratie van Azure CDN. Ga als volgt te werk om te controleren of uw Data Center de Azure CDN-integratie beschikbaar heeft:
 
  - Probeer de `cdnEnabled` waarde in te stellen op True.
  - Het geretourneerde resultaat voor een `HTTP Error Code 412` (PreconditionFailed) controleren met een bericht van de eigenschap ' streaming-eind punt CdnEnabled kan niet worden ingesteld op True omdat de CDN-mogelijkheid niet beschikbaar is in de huidige regio. ' 

    Als deze fout wordt weer gegeven, wordt het niet door het Data Center ondersteund. U moet een ander Data Center proberen.
- `cdnProfile`-Wanneer `cdnEnabled` is ingesteld op waar, kunt u ook waarden `cdnProfile` door geven. `cdnProfile`is de naam van het CDN-profiel waar het CDN-eind punt wordt gemaakt. U kunt een bestaande cdnProfile opgeven of een nieuwe maken. Als de waarde Null is `cdnEnabled` en waar is, wordt de standaard waarde ' AzureMediaStreamingPlatformCdnProfile ' gebruikt. Als de gegeven `cdnProfile` al bestaat, wordt er een eind punt gemaakt. Als het profiel niet bestaat, wordt er automatisch een nieuw profiel gemaakt.
- `cdnProvider`-Wanneer CDN is ingeschakeld, kunt u ook waarden `cdnProvider` door geven. `cdnProvider`Hiermee wordt bepaald welke provider wordt gebruikt. Op dit moment worden drie waarden ondersteund: "StandardVerizon", "PremiumVerizon" en "StandardAkamai". Als er geen waarde wordt gegeven `cdnEnabled` en waar is, wordt "StandardVerizon" gebruikt (dat is de standaard waarde).
- `crossSiteAccessPolicies`-Wordt gebruikt om beleid voor meerdere sites op te geven voor verschillende clients. Zie voor meer informatie [beleid voor meerdere domein beleids regels](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) en [het beschikbaar maken van een service over domein grenzen](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>De instellingen zijn alleen van toepassing op Smooth Streaming.
- `customHostNames`: Wordt gebruikt voor het configureren van een streaming-eind punt voor het accepteren van verkeer dat wordt doorgestuurd naar een aangepaste hostnaam.  Deze eigenschap is geldig voor standaard-en Premium-streaming-eind punten en kan `cdnEnabled`worden ingesteld als: False.
    
    Het eigendom van de domein naam moet worden bevestigd door Media Services. Media Services controleert het eigendom van de domein naam door een `CName` record te vereisen met de Media Services-account-id als onderdeel dat moet worden toegevoegd aan het domein dat in gebruik is. Een voor beeld: voor ' Sports.contoso.com ' die moet worden gebruikt als aangepaste hostnaam voor het streaming-eind punt, moet een `<accountId>.contoso.com` record voor worden geconfigureerd om te verwijzen naar een van Media Services-namen van verificatie-hosts. De naam van de verificatie-host bestaat uit verifydns. \<Media Services-DNS-zone >. 

    Hieronder ziet u de verwachte DNS-zones die moeten worden gebruikt in de record controleren voor verschillende Azure-regio's.
  
  - Noord-Amerika, Europa, Singapore, Hong Kong SAR, Japan:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - China
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Bijvoorbeeld, een `CName` record waarbij "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" aan "verifydns.media.Azure.net" wordt toegewezen, bewijst dat de Media Services id 945a4c4e-28ea-45cd-8ccb-a519f6b700ad het eigendom van het contoso.com domein heeft, waardoor het inschakelen van elke naam onder contoso.com moet worden gebruikt als een aangepaste hostnaam voor een streaming-eind punt onder dat account. Als u de waarde voor de media service-ID wilt vinden, gaat u naar de [Azure Portal](https://portal.azure.com/) en selecteert u uw media service-account. De **account-id** wordt weer gegeven in de rechter bovenhoek van de pagina.
        
    Als er een poging wordt gedaan om een aangepaste hostnaam in te stellen zonder de juiste verificatie `CName` van de record, mislukt het DNS-antwoord en wordt het enige tijd in de cache opgeslagen. Zodra de juiste record is geplaatst, kan het enige tijd duren voordat het antwoord in de cache opnieuw is gevalideerd. Afhankelijk van de DNS-provider voor het aangepaste domein, kan het enkele minuten tot een uur duren om de record opnieuw te valideren.
        
    `CName` Naast de `sports.contoso.com`toewijzing `<accountId>.<parent domain>` aan `verifydns.<mediaservices-dns-zone>`, moet u een andere `CName` maken die de naam van de aangepaste host (bijvoorbeeld) toewijst aan de hostnaam van uw Media Services-streaming-eind punt (bijvoorbeeld `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Streaming-eind punten die zich in hetzelfde Data Center bevinden, kunnen niet dezelfde aangepaste hostnaam delen.

    Op dit moment biedt Media Services geen ondersteuning voor SSL met aangepaste domeinen. 
    
- `maxCacheAge`-Overschrijft de standaard instelling voor de HTTP-cache van de maximale leeftijds waarde voor het streamen van het streaming-eind punt op media fragmenten en on-demand manifesten. De waarde wordt ingesteld in seconden.
- `resourceState` -

    - Gestopt: de initiële status van een streaming-eind punt na het maken
    - Starten: er wordt overgeschakeld naar de actieve status
    - Uitvoeren-kan inhoud streamen naar clients
    - Schalen: de schaal eenheden worden verg root of verkleind
    - Stoppen: wordt overgezet naar de status gestopt
    - Verwijderen-wordt verwijderd
    
- `scaleUnits`-Biedt u specifieke uitvoerige capaciteit die kan worden aangeschaft in stappen van 200 Mbps. Als u naar een **Premium** -type moet gaan, past `scaleUnits`u aan.

## <a name="working-with-cdn"></a>Werken met CDN

In de meeste gevallen moet CDN zijn ingeschakeld. Als u echter een maximale gelijktijdigheid verwacht van minder dan 500 kijkers, dan wordt het aanbevolen CDN uit te schakelen, omdat CDN het beste schaalt met gelijktijdigheid.

### <a name="considerations"></a>Overwegingen

* Het streaming-eind punt `hostname` en de streaming-URL blijven hetzelfde, ongeacht of u CDN inschakelt.
* Als u de mogelijkheid wilt bieden om uw inhoud te testen met of zonder CDN, kunt u een ander streaming-eind punt maken dat niet is ingeschakeld voor CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Gedetailleerde uitleg over hoe caching werkt

Er is geen specifieke bandbreedte waarde bij het toevoegen van het CDN, omdat de hoeveelheid band breedte die nodig is voor een streaming-eind punt waarvoor CDN is ingeschakeld, varieert. Een partij is afhankelijk van het type inhoud, hoe populair het is, hoe vertrouwt en de protocollen. De CDN is alleen in de cache opslaan wat er wordt aangevraagd. Dit betekent dat populaire inhoud rechtstreeks vanuit het CDN wordt bediend, op voor waarde dat het video fragment in de cache wordt opgeslagen. Live-inhoud wordt waarschijnlijk in de cache opgeslagen, omdat er meestal veel mensen precies hetzelfde zijn. Inhoud op aanvraag kan een beetje trickier zijn, omdat u inhoud zou kunnen hebben die populair zijn en andere niet. Als u miljoenen video-assets hebt waar geen van uw apparaten populair zijn (slechts 1 of 2 kijkers per week), maar duizenden mensen die alle verschillende Video's volgen, wordt het CDN veel minder effectief. Met deze cache missers verhoogt u de belasting van het streaming-eind punt.
 
U moet ook nadenken over de werking van adaptieve streaming. Elk afzonderlijk video fragment wordt in de cache opgeslagen als een eigen entiteit. Als bijvoorbeeld de eerste keer dat een bepaalde video wordt weer gegeven, de persoon overs laat dat er slechts een paar seconden wordt weer gegeven, hoeft u alleen de video fragmenten te zien die zijn gekoppeld aan wat de gevolgde persoon in het CDN in de cache plaatst. Met adaptieve streaming hebt u doorgaans 5 tot 7 verschillende bitrates voor video. Als één persoon een bitrate bekijkt en een andere persoon een andere bitsnelheid bekijkt, worden ze afzonderlijk in het CDN opgeslagen. Zelfs als twee mensen dezelfde bitsnelheid volgen, kunnen ze via verschillende protocollen worden gestreamd. Elk protocol (HLS, MPEG-DASH, Smooth Streaming) wordt afzonderlijk in de cache opgeslagen. Elke bitsnelheid en elk protocol worden afzonderlijk in de cache opgeslagen en alleen de video fragmenten die zijn aangevraagd, worden in de cache opgeslagen.

### <a name="enable-azure-cdn-integration"></a>Integratie van Azure CDN inschakelen

Nadat een streaming-eind punt is ingericht met CDN ingeschakeld, is er een ingestelde wacht tijd op Media Services voordat de DNS-update wordt uitgevoerd om het streaming-eind punt toe te wijzen aan het CDN-eind punt.

Als u het CDN later wilt uitschakelen/inschakelen, moet het streaming-eind punt de status **gestopt** hebben. Het kan tot twee uur duren voordat de Azure CDN integratie is ingeschakeld en de wijzigingen voor alle CDN-Pop's actief zijn. Uw streaming-eind punt kan echter wel worden gestart zonder onderbreking van het streaming-eind punt en zodra de integratie is voltooid, wordt de stroom vanuit het CDN geleverd. Tijdens de inrichtings periode krijgt het streaming-eind punt de status **begin** en kan de prestaties afnemen.

Wanneer het standaard streaming-eind punt wordt gemaakt, wordt het standaard geconfigureerd met standaard Verizon. U kunt Premium Verizon-of Standard Akamai-providers configureren met behulp van REST Api's. 

CDN-integratie is ingeschakeld in alle Azure-data centers, met uitzonde ring van China en federale overheids regio's.

> [!IMPORTANT]
> Azure Media Services integratie met Azure CDN is geïmplementeerd op **Azure CDN van Verizon** voor Standard streaming-eind punten. Premium streaming-eind punten kunnen worden geconfigureerd met alle **Azure CDN prijs categorieën en providers**. Zie [overzicht van CDN](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN-functies.

### <a name="determine-if-dns-change-has-been-made"></a>Vaststellen of de DNS-wijziging is doorgevoerd

U kunt bepalen of de DNS-wijziging is doorgevoerd in een streaming-eind punt (het verkeer wordt omgeleid naar de https://www.digwebinterface.com Azure CDN) met behulp van. Als de resultaten azureedge.net domein namen in de resultaten heeft, wordt het verkeer nu naar het CDN gewijsd.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

Het voor beeld [in deze opslag plaats](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) laat zien hoe u het standaard streaming-eind punt met .net kunt starten.

