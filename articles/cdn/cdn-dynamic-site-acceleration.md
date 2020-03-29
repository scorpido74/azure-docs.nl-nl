---
title: Dynamische siteversnelling via Azure CDN
description: Azure CDN ondersteunt dsa-optimalisatie (dynamic site acceleration) voor bestanden met dynamische inhoud.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 08e705d3c3623d4d02ccaea609eb0555aa1c8e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593922"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamische siteversnelling via Azure CDN

Met de explosie van sociale media, elektronische handel, en de hyper-gepersonaliseerde web, een snel toenemende percentage van de inhoud geserveerd aan eindgebruikers wordt gegenereerd in real time. Gebruikers verwachten een snelle, betrouwbare en gepersonaliseerde webervaring, onafhankelijk van hun browser, locatie, apparaat of netwerk. Echter, de innovaties die deze ervaringen zo boeiend ook trage pagina downloads en zet de kwaliteit van de ervaring van de consument in gevaar. 

De mogelijkheid voor het standaard-inhoudsleveringsnetwerk (CDN) omvat de mogelijkheid om bestanden dichter bij eindgebruikers in de cache te plaatsen om de levering van statische bestanden te versnellen. Met dynamische webtoepassingen is het echter niet mogelijk om inhoud op randlocaties te plaatsen omdat de server de inhoud genereert als reactie op gebruikersgedrag. Het versnellen van de levering van dergelijke inhoud is complexer dan traditionele randcaches en vereist een end-to-end oplossing die elk element fijn afstemt op het hele gegevenspad vanaf het begin tot de levering. Met Azure CDN dynamic site acceleration (DSA) optimalisatie worden de prestaties van webpagina's met dynamische inhoud meetbaar verbeterd.

**Azure CDN van Akamai** en **Azure CDN van Verizon** bieden beide DSA-optimalisatie via het menu Geoptimaliseerd **voor** eindpunt tijdens het maken van eindpunten. Dynamische siteversnelling van Microsoft wordt aangeboden via [Azure Front Door Service.](https://docs.microsoft.com/azure/frontdoor/front-door-overview)

> [!Important]
> Voor **Azure CDN-profielen van Akamai-profielen** u de optimalisatie van een CDN-eindpunt wijzigen nadat het is gemaakt.
>   
> Voor profielen van **Azure CDN van Verizon** geldt dat u de optimalisatie van een CDN-eindpunt niet kunt wijzigen nadat deze is gemaakt.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>CDN-eindpuntconfiguratie om de levering van dynamische bestanden te versnellen

Als u een CDN-eindpunt wilt configureren om de levering van dynamische bestanden te optimaliseren, u de Azure-portal, de REST-API's of een van de client-SDK's gebruiken om hetzelfde programmatisch te doen. 

**Ga als lid van het CDN-eindpunt voor DSA-optimalisatie met behulp van de Azure-portal:**

1. Selecteer **Eindpunt**op de **profielpagina van CDN** .

   ![Een nieuw CDN-eindpunt toevoegen](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Het deelvenster **Een eindpunt toevoegen** wordt weergegeven.

2. Selecteer **onder Geoptimaliseerd voor**, Selecteer Dynamische **siteversnelling**.

    ![Een nieuw CDN-eindpunt maken met DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Voer **voor probepad**een geldig pad naar een bestand in.

    Probe pad is een functie die specifiek is voor DSA, en een geldig pad is vereist voor het maken. DSA gebruikt een klein *sondepadbestand* dat op de oorsprongsserver is geplaatst om netwerkrouteringsconfiguraties voor het CDN te optimaliseren. Voor het sondepadbestand u het voorbeeldbestand downloaden en uploaden naar uw site of een bestaand element op uw oorsprong gebruiken dat ongeveer 10 KB groot is.

4. Voer de andere vereiste eindpuntopties in (zie Voor meer informatie [een nieuw CDN-eindpunt maken)](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)en selecteer **Vervolgens Toevoegen**.

   Nadat het CDN-eindpunt is gemaakt, worden de DSA-optimalisaties voor alle bestanden die aan bepaalde criteria voldoen, van toepassing. 


**Een bestaand eindpunt voor DSA (alleen Azure CDN van Akamai-profielen) configureren:**

1. Selecteer op de **profielpagina van CDN** het eindpunt dat u wilt wijzigen.

2. Selecteer **Optimalisatie**in het linkerdeelvenster . 

   De pagina **Optimalisatie** wordt weergegeven.

3. Selecteer **onder Geoptimaliseerd voor**, Selecteer Dynamische **siteversnelling**en selecteer **Opslaan**.

> [!Note]
> DSA brengt extra kosten met zich mee. Zie [Content Delivery Network-prijzen voor](https://azure.microsoft.com/pricing/details/cdn/)meer informatie .

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimalisatie met Azure CDN

Dynamische siteversnelling op Azure CDN versnelt de levering van dynamische assets met behulp van de volgende technieken:

-   [Routeoptimalisatie](#route-optimization)
-   [TCP-optimalisaties](#tcp-optimizations)
-   [Objectprefetch (alleen Azure CDN van Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptieve beeldcompressie (alleen Azure CDN van Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Routeoptimalisatie

Routeoptimalisatie is belangrijk omdat het internet een dynamische plek is, waar verkeer en tijdelijke uitval voortdurend de netwerktopologie veranderen. Het Border Gateway Protocol (BGP) is het routeringsprotocol van het internet, maar er kunnen snellere routes zijn via intermediaire Point of Presence (PoP)-servers. 

Routeoptimalisatie kiest het meest optimale pad naar de oorsprong, zodat een site continu toegankelijk is en dynamische content wordt geleverd aan eindgebruikers via de snelste en meest betrouwbare route mogelijk. 

Het Akamai-netwerk maakt gebruik van technieken om realtime gegevens te verzamelen en verschillende paden door verschillende knooppunten in de Akamai-server te vergelijken, evenals de standaard BGP-route over het open internet om de snelste route tussen de oorsprong en de CDN-rand te bepalen. Deze technieken vermijden internet congestie punten en lange routes. 

Op dezelfde manier gebruikt het Verizon-netwerk een combinatie van Anycast DNS, pop-pop's met hoge capaciteit en statuscontroles om de beste gateways voor de beste routegegevens van de client naar de oorsprong te bepalen.
 
Hierdoor wordt volledig dynamische en transactionele inhoud sneller en betrouwbaarder aan eindgebruikers geleverd, zelfs als deze niet in cache kan worden opgeslagen. 

### <a name="tcp-optimizations"></a>TCP-optimalisaties

Transmission Control Protocol (TCP) is de standaard van de internetprotocolsuite die wordt gebruikt om informatie te leveren tussen toepassingen op een IP-netwerk.  Standaard zijn er verschillende heen-en-weeraanvragen nodig om een TCP-verbinding in te stellen, evenals limieten om netwerkcongestie te voorkomen, wat resulteert in inefficiënties op schaal. **Azure CDN van Akamai** pakt dit probleem op door te optimaliseren in drie gebieden: 

 - [Tcp langzame start elimineren](#eliminating-tcp-slow-start)
 - [Gebruik maken van permanente verbindingen](#leveraging-persistent-connections)
 - [TCP-pakketparameters afstemmen](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Tcp langzame start elimineren

TCP *slow start* is een algoritme van het TCP-protocol dat netwerkcongestie voorkomt door de hoeveelheid gegevens die via het netwerk worden verzonden te beperken. Het begint met kleine congestie venster maten tussen afzender en ontvanger totdat het maximum is bereikt of pakket verlies wordt gedetecteerd.

 Zowel **Azure CDN van Akamai** als **Azure CDN van Verizon-profielen** elimineren TCP trage start met de volgende drie stappen:

1. Status- en bandbreedtebewaking wordt gebruikt om de bandbreedte van verbindingen tussen edge PoP-servers te meten.
    
2. Statistieken worden gedeeld tussen edge PoP-servers, zodat elke server op de hoogte is van de netwerkomstandigheden en de serverstatus van de andere Pop's om hen heen.  
    
3. De CDN edge servers maken veronderstellingen over sommige transmissieparameters, zoals wat de optimale venstergrootte moet zijn bij het communiceren met andere CDN edge servers in de nabijheid. Deze stap betekent dat de initiële grootte van het congestievenster kan worden vergroot als de status van de verbinding tussen de CDN-randservers in staat is om hogere pakketgegevens over te dragen.  

#### <a name="leveraging-persistent-connections"></a>Gebruik maken van permanente verbindingen

Met behulp van een CDN maken minder unieke machines rechtstreeks verbinding met uw origine server in vergelijking met gebruikers die rechtstreeks verbinding maken met uw oorsprong. Azure CDN bundelt ook gebruikersverzoeken om minder verbindingen met de oorsprong tot stand te brengen.

Zoals eerder vermeld, zijn verschillende handshakeaanvragen vereist om een TCP-verbinding tot stand te brengen. Permanente verbindingen, die worden `Keep-Alive` geïmplementeerd door de HTTP-header, hergebruiken bestaande TCP-verbindingen voor meerdere HTTP-aanvragen om retourtijden te besparen en de levering te versnellen. 

**Azure CDN van Verizon** verzendt ook periodieke keep-alive pakketten via de TCP-verbinding om te voorkomen dat een open verbinding wordt gesloten.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-pakketparameters afstemmen

**Azure CDN van Akamai** stemt de parameters af die server-naar-serververbindingen regelen en vermindert de hoeveelheid lange-afstandsretouren die nodig zijn om inhoud op te halen die in de site is ingesloten met behulp van de volgende technieken:

- Het verhogen van de initiële congestie venster, zodat meer pakketten kunnen worden verzonden zonder te wachten op een bevestiging.
- Het verminderen van de eerste time-out voor het opnieuw verzenden, zodat een verlies wordt gedetecteerd en de doorgifte sneller plaatsvindt.
- Het verlagen van de minimale en maximale time-out voor het opnieuw verzenden om de wachttijd te verkorten voordat wordt aangenomen dat pakketten verloren zijn gegaan bij verzending.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objectprefetch (alleen Azure CDN van Akamai)

De meeste websites bestaan uit een HTML-pagina, die verwijst naar verschillende andere bronnen, zoals afbeeldingen en scripts. Wanneer een client een webpagina aanvraagt, downloadt en ontleden de browser het HTML-object meestal en doet het vervolgens aanvullende aanvragen voor gekoppelde elementen die nodig zijn om de pagina volledig te laden. 

*Prefetch* is een techniek om afbeeldingen en scripts op te halen die zijn ingesloten in de HTML-pagina terwijl de HTML aan de browser wordt weergegeven en voordat de browser deze objectaanvragen doet. 

Als de optie prefetch is ingeschakeld op het moment dat het CDN de HTML-basispagina naar de browser van de client bedient, ontleden de CDN het HTML-bestand en doet het extra aanvragen voor gekoppelde bronnen en slaat deze op in de cache. Wanneer de client de aanvragen voor de gekoppelde assets doet, beschikt de CDN-edgeserver al over de gevraagde objecten en kan deze onmiddellijk worden aangeboden zonder een retourvlucht naar de oorsprong. Deze optimalisatie komt zowel ten cachebare als niet-cachebare inhoud ten goede.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptieve beeldcompressie (alleen Azure CDN van Akamai)

Sommige apparaten, vooral mobiele apparaten, ervaren van tijd tot tijd lagere netwerksnelheden. In deze scenario's is het voordeliger voor de gebruiker om kleinere afbeeldingen op zijn webpagina sneller te ontvangen in plaats van lang te wachten op afbeeldingen met volledige resolutie.

Deze functie controleert automatisch de netwerkkwaliteit en maakt gebruik van standaard JPEG-compressiemethoden wanneer de netwerksnelheden trager zijn om de levertijd te verbeteren.

Adaptieve afbeeldingscompressie | Bestandsextensies  
--- | ---  
JPEG-compressie | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Caching

Bij DSA wordt caching standaard uitgeschakeld op het CDN, `Cache-Control` `Expires` zelfs wanneer de oorsprong of kopteksten in het antwoord bevat. DSA wordt meestal gebruikt voor dynamische assets die niet in de cache mogen worden opgeslagen omdat ze uniek zijn voor elke client. Caching kan dit gedrag doorbreken.

Als u een website met een mix van statische en dynamische activa hebt, is het het beste om een hybride benadering te kiezen om de beste prestaties te krijgen. 

Voor **Azure CDN Standard van Verizon** en Azure **CDN Standard van Akamai-profielen** u caching inschakelen voor specifieke DSA-eindpunten met [cachingregels.](cdn-caching-rules.md)

Ga als volgt te werk om toegang te krijgen tot regels voor caching:

1. Selecteer op de **profielpagina van CDN** onder instellingen de optie **Caching-regels**.  
    
    ![Knop Regels voor CDN-caching](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    De pagina **Regels voor caching** wordt geopend.

2. Maak een algemene of aangepaste cachingregel om caching voor uw DSA-eindpunt in te schakelen. 

Alleen voor **Azure CDN Premium van Verizon-profielen** schakelt u caching in voor specifieke DSA-eindpunten met behulp van de [rules engine](cdn-rules-engine.md). Alle regels die worden gemaakt, hebben alleen invloed op de eindpunten van je profiel die zijn geoptimaliseerd voor DSA. 

Om toegang te krijgen tot de regels motor:
    
1. Selecteer beheren op de profielpagina **van** **CDN** .  
    
    ![Knop CDN-profielbeheer](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Het CDN-beheerportaal wordt geopend.

2. Selecteer **ADN**in de CDN-beheerportal en selecteer **Vervolgens Rules Engine**. 

    ![Regels motor voor DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



U ook twee CDN-eindpunten gebruiken: één eindpunt dat is geoptimaliseerd met DSA om dynamische assets te leveren en een ander eindpunt dat is geoptimaliseerd met een statisch optimalisatietype, zoals algemene weblevering, aan de cachebare assets van de levering. Wijzig de URL's van uw webpagina om rechtstreeks te koppelen aan het item op het CDN-eindpunt dat u wilt gebruiken. 

Bijvoorbeeld: `mydynamic.azureedge.net/index.html` is een dynamische pagina en wordt geladen vanaf het DSA-eindpunt.De html-pagina verwijst naar meerdere statische elementen, zoals JavaScript-bibliotheken of afbeeldingen die `mystatic.azureedge.net/banner.jpg` zijn `mystatic.azureedge.net/scripts.js`geladen vanaf het statische CDN-eindpunt, zoals en . 



