---
title: Optimalisatie van mediastreaming met Azure CDN
description: Streaming mediabestanden optimaliseren voor vloeiende bezorging
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
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: c6ed546735058e330368151adb0df7323f943050
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593659"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimalisatie van mediastreaming met Azure CDN 
 
Het gebruik van high-definition video neemt toe op het internet, wat problemen oplevert voor een efficiënte levering van grote bestanden. Klanten verwachten een vlotte weergave van video-on-demand of live video-assets op verschillende netwerken en klanten over de hele wereld. Een snel en efficiënt leveringsmechanisme voor mediastreamingbestanden is essentieel om een soepele en plezierige consumentenervaring te garanderen.  

Live streaming media is vooral moeilijk te leveren vanwege de grote maten en het aantal gelijktijdige kijkers. Lange vertragingen zorgen ervoor dat gebruikers vertrekken. Omdat live streams niet van tevoren in de cache kunnen worden opgeslagen en grote latencies niet acceptabel zijn voor kijkers, moeten videofragmenten tijdig worden geleverd. 

De aanvraagpatronen van streaming bieden ook een aantal nieuwe uitdagingen. Wanneer een populaire live stream of een nieuwe serie wordt uitgebracht voor video on demand, kunnen duizenden tot miljoenen kijkers tegelijkertijd de stream aanvragen. In dit geval is smart request consolidation essentieel om de oorsprongsservers niet te overweldigen wanneer de assets nog niet in de cache zijn opgeslagen.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimalisaties voor mediastreaming voor Azure CDN van Microsoft

**Azure CDN Standard van** Microsoft-eindpunten leveren streaming media-assets rechtstreeks met behulp van het algemene optimalisatietype voor weblevering. 

Mediastreamingoptimalisatie voor **Azure CDN Standard van Microsoft** is effectief voor live of video-on-demand streamingmedia die afzonderlijke mediafragmenten gebruiken voor levering. Dit proces verschilt van een enkele grote asset overgedragen via progressieve download of met behulp van byte-range verzoeken. Zie Optimalisatie van het downloaden van [bestanden met Azure CDN voor](cdn-large-file-optimization.md)informatie over die stijl van medialevering.

De algemene medialevering of video-on-demand medialeveringsoptimalisatietypen maken gebruik van Azure Content Delivery Network (CDN) met back-endoptimalisaties om mediaassets sneller te leveren. Ze gebruiken ook configuraties voor media-assets op basis van aanbevolen procedures die in de loop van de tijd zijn geleerd.

### <a name="partial-cache-sharing"></a>Delen van gedeeltelijke cache
Met het delen van gedeeltelijke caches kan het CDN gedeeltelijk in de cache opgeslagen inhoud weergeven aan nieuwe aanvragen. Als het eerste verzoek aan het CDN bijvoorbeeld resulteert in een cachemiss, wordt de aanvraag naar de oorsprong verzonden. Hoewel deze onvolledige inhoud in de CDN-cache wordt geladen, kunnen andere aanvragen voor het CDN beginnen met het verkrijgen van deze gegevens. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimalisaties voor mediastreaming voor Azure CDN van Verizon

**Azure CDN Standard van Verizon** en **Azure CDN Premium van** Verizon-eindpunten leveren streaming media-assets rechtstreeks met behulp van het algemene optimalisatietype voor weblevering. Een paar functies op het CDN helpen direct bij het standaard leveren van mediaassets.

### <a name="partial-cache-sharing"></a>Delen van gedeeltelijke cache

Met het delen van gedeeltelijke caches kan het CDN gedeeltelijk in de cache opgeslagen inhoud weergeven aan nieuwe aanvragen. Als het eerste verzoek aan het CDN bijvoorbeeld resulteert in een cachemiss, wordt de aanvraag naar de oorsprong verzonden. Hoewel deze onvolledige inhoud in de CDN-cache wordt geladen, kunnen andere aanvragen voor het CDN beginnen met het verkrijgen van deze gegevens. 

### <a name="cache-fill-wait-time"></a>Wachttijd cachevullen

 De wachttijd voor het invullen van de cache dwingt de edge-server om volgende aanvragen voor dezelfde bron vast te houden totdat HTTP-antwoordkoppen van de oorsprongsserver aankomen. Als HTTP-antwoordkoppen van de oorsprong binnenkomen voordat de timer verloopt, worden alle aanvragen die in de wacht zijn gezet uit de groeiende cache weergegeven. Tegelijkertijd wordt de cache gevuld door gegevens van de oorsprong. Standaard is de wachttijd voor het invullen van de cache ingesteld op 3000 milliseconden. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimalisaties voor mediastreaming voor Azure CDN van Akamai
 
**Azure CDN Standard van Akamai** biedt een functie die streaming media-assets efficiënt levert aan gebruikers over de hele wereld op schaal. De functie vermindert latencies omdat het de belasting op de origin-servers vermindert. Deze functie is beschikbaar met de standaard Akamai-prijscategorie. 

Mediastreamingoptimalisatie voor **Azure CDN Standard van Akamai** is effectief voor live of video-on-demand streamingmedia die afzonderlijke mediafragmenten gebruiken voor levering. Dit proces verschilt van een enkele grote asset overgedragen via progressieve download of met behulp van byte-range verzoeken. Zie [Grote bestandsoptimalisatie](cdn-large-file-optimization.md)voor informatie over die stijl van medialevering.

De algemene medialevering of video-on-demand mediaweergaveoptimalisatietypen gebruiken een CDN met back-endoptimalisaties om mediaassets sneller te leveren. Ze gebruiken ook configuraties voor media-assets op basis van aanbevolen procedures die in de loop van de tijd zijn geleerd.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Een Akamai CDN-eindpunt configureren om mediastreaming te optimaliseren
 
U het eindpunt van uw cdn-eindpunt (Content Delivery Network) configureren om de levering voor grote bestanden via de Azure-portal te optimaliseren. U hiervoor ook de REST API's of een van de Client SDK's gebruiken. In de volgende stappen wordt het proces weergegeven via de Azure-portal voor een **Azure CDN-standaard vanuit Akamai-profiel:**

1. Als u een nieuw eindpunt wilt toevoegen, selecteert u Op een Akamai **CDN-profielpagina** **Eindpunt**.
  
    ![Nieuw eindpunt](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Selecteer **in** de vervolgkeuzelijst Geoptimaliseerd voor **mediastreaming voor** video-on-demand-elementen. Als u een combinatie van live en video-on-demand streaming doet, selecteert u **Algemene mediastreaming**.

    ![Streaming geselecteerd](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Nadat u het eindpunt hebt gemaakt, wordt de optimalisatie voor alle bestanden die aan bepaalde criteria voldoen, van toepassing. In de volgende sectie wordt dit proces beschreven. 

### <a name="caching"></a>Caching

Als **Azure CDN Standard van Akamai** detecteert dat het element een streamingmanifest of -fragment is, worden verschillende cache-vervaldatums gebruikt dan algemene webbezorging. (Zie de volledige lijst in de volgende tabel.) Zoals altijd worden cache-control of Expires headers verzonden vanaf de oorsprong worden geëerd. Als het actief geen media-element is, worden de vervaldatums opgeslagen voor algemene weblevering.

De korte negatieve cachetijd is handig voor origin offload wanneer veel gebruikers een fragment aanvragen dat nog niet bestaat. Een voorbeeld is een livestream waarbij de pakketten niet beschikbaar zijn vanaf de oorsprong van die tweede. Het langere caching-interval helpt ook aanvragen van de oorsprong te verwijderen omdat video-inhoud doorgaans niet wordt gewijzigd.
 

|   | Algemene weblevering | Algemene mediastreaming | Video-on-demand mediastreaming  
--- | --- | --- | ---
Caching: Positief <br> HTTP 200, 203, 300, <br> 301, 302 en 410 | 7 dagen |365 dagen | 365 dagen   
Caching: Negatief <br> HTTP 204, 305, 404, <br> en 405 | Geen | 1 seconde | 1 seconde
 
### <a name="deal-with-origin-failure"></a>Omgaan met oorsprong smislukking  

Algemene medialevering en video-on-demand medialevering hebben ook een time-out voor de oorsprong en een logboek opnieuw proberen op basis van aanbevolen procedures voor typische aanvraagpatronen. Omdat algemene medialevering bijvoorbeeld voor live en video-on-demand medialevering is, wordt een kortere time-out van de verbinding gebruikt vanwege het tijdsgevoelige karakter van live streaming.

Wanneer een verbinding een time-out heeft, probeert het CDN een aantal keren opnieuw voordat het een fout met een time-out van 504 - gateway naar de client verzendt. 

Wanneer een bestand overeenkomt met de lijst met bestandstypen en groottevoorwaarden, gebruikt het CDN het gedrag voor mediastreaming. Anders maakt het gebruik van algemene weblevering.
   
### <a name="conditions-for-media-streaming-optimization"></a>Voorwaarden voor media streaming optimalisatie 

In de volgende tabel worden de criteria weergegeven waaraan moet worden voldaan voor optimalisatie van mediastreaming: 
 
Ondersteunde streamingtypen | Bestandsextensies  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, toets, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Seg-Frag URL-structuur <br> (overeenkomende regex: ^(/.*)Seq(\d+)-Frag(\d+)
Dash | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4, <br> sidx, webm, mp4a, m4a, isma
Vloeiende streaming | /manifest/, /QualityLevels/Fragments/
  
 
