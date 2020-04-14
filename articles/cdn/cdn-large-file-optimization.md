---
title: Optimalisatie voor het downloaden van grote bestanden met Azure CDN
description: In dit artikel wordt uitgelegd hoe groot bestandsdownloads kunnen worden geoptimaliseerd.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 28b3c4faf62bcd9f9495810927ece03e2dadc1fc
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260527"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimalisatie voor het downloaden van grote bestanden met Azure CDN

Bestandsgrootte van inhoud die via internet wordt geleverd, blijft groeien dankzij verbeterde functionaliteit, verbeterde graphics en rich media-inhoud. Deze groei wordt gedreven door vele factoren: breedbandpenetratie, grotere goedkope opslagapparaten, wijdverspreide toename van high-definition video en met internet verbonden apparaten (IoT). Een snel en efficiënt leveringsmechanisme voor grote bestanden is essentieel om een soepele en plezierige consumentenervaring te garanderen.

Levering van grote bestanden heeft verschillende uitdagingen. Ten eerste kan de gemiddelde tijd om een groot bestand te downloaden aanzienlijk zijn omdat toepassingen mogelijk niet alle gegevens opeenvolgend downloaden. In sommige gevallen kunnen toepassingen het laatste deel van een bestand downloaden voor het eerste deel. Wanneer slechts een kleine hoeveelheid van een bestand wordt aangevraagd of een gebruiker een download pauzeert, kan de download mislukken. De download kan ook worden uitgesteld tot na het content delivery network (CDN) het hele bestand van de origin-server heeft opgehaald. 

Ten tweede bepaalt de latentie tussen de machine van een gebruiker en het bestand de snelheid waarmee ze inhoud kunnen bekijken. Daarnaast hebben netwerkcongestie en capaciteitsproblemen ook invloed op de doorvoer. Grotere afstanden tussen servers en gebruikers creëren extra mogelijkheden voor pakketverlies, wat de kwaliteit vermindert. De vermindering van de kwaliteit als gevolg van beperkte doorvoer en een verhoogd pakketverlies kan de wachttijd voor het downloaden van een bestand verhogen. 

Ten derde worden veel grote bestanden niet in hun geheel geleverd. Gebruikers kunnen een download halverwege annuleren of alleen de eerste paar minuten van een lange MP4-video bekijken. Daarom willen software- en mediabezorgers alleen het gedeelte van een aangevraagd bestand leveren. Efficiënte verdeling van de gevraagde gedeelten vermindert het uitgangsverkeer van de oorsprongsserver. Efficiënte distributie vermindert ook het geheugen en de I/O-druk op de origin-server. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimaliseren voor de levering van grote bestanden met Azure CDN van Microsoft

**Azure CDN Standard van** Microsoft-eindpunten leveren grote bestanden zonder limiet op bestandsgrootte. Extra functies zijn standaard ingeschakeld om de levering van grote bestanden sneller te maken.

### <a name="object-chunking"></a>Objectchunking 

**Azure CDN Standard van Microsoft** maakt gebruik van een techniek genaamd object chunking. Wanneer een groot bestand wordt aangevraagd, haalt het CDN kleinere stukken van het bestand terug van de oorsprong. Nadat de CDN POP-server een volledige of byte-range bestandsaanvraag heeft ontvangen, vraagt de CDN-edgeserver het bestand van de oorsprong aan in brokken van 8 MB. 

Nadat de brok aan de CDN-rand is aangekomen, wordt het in de cache opgeslagen en onmiddellijk aan de gebruiker geserveerd. Het CDN prefetches vervolgens de volgende brok parallel. Deze prefetch zorgt ervoor dat de inhoud één stuk voor blijft op de gebruiker, wat de latentie vermindert. Dit proces wordt voortgezet totdat het hele bestand is gedownload (indien gevraagd), alle bytebereiken beschikbaar zijn (indien gevraagd) of de client de verbinding beëindigt. 

Zie [RFC 7233](https://tools.ietf.org/html/rfc7233)voor meer informatie over de byte-range aanvraag.

De CDN slaat alle brokken in de cache wanneer ze worden ontvangen. Het hele bestand hoeft niet in de cache te worden opgeslagen in de CDN-cache. Latere aanvragen voor het bestand of bytebereiken worden weergegeven vanuit de CDN-cache. Als niet alle segmenten op het CDN in de cache worden opgeslagen, wordt prefetch gebruikt om brokken van de oorsprong op te vragen. Deze optimalisatie is afhankelijk van de mogelijkheid van de oorsprongsserver om byte-range aanvragen te ondersteunen; Als de origin-server geen byte-range aanvragen ondersteunt, is deze optimalisatie niet effectief. 

### <a name="conditions-for-large-file-optimization"></a>Voorwaarden voor grote bestandsoptimalisatie
Grote bestandsoptimalisatiefuncties voor **Azure CDN Standard van Microsoft** zijn standaard ingeschakeld wanneer u het algemene optimalisatietype voor webweergave gebruikt. Er zijn geen beperkingen op de maximale bestandsgrootte.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimaliseren voor de levering van grote bestanden met Azure CDN van Verizon

**Azure CDN Standard van Verizon** en **Azure CDN Premium van** Verizon-eindpunten leveren grote bestanden zonder een limiet op bestandsgrootte. Extra functies zijn standaard ingeschakeld om de levering van grote bestanden sneller te maken.

### <a name="complete-cache-fill"></a>Volledige cachevulling

Met de standaardfunctie voor volledige cachevulling kan het CDN een bestand in de cache optrekken wanneer een eerste aanvraag wordt opgegeven of verloren gaat. 

Volledige cachevulling is het meest nuttig voor grote activa. Gebruikers downloaden ze meestal niet van begin tot eind. Ze maken gebruik van progressieve download. Met het standaardgedrag dwingt de edge-server om een achtergrondophalen van het item van de oorsprongsserver te starten. Daarna bevindt het item zich in de lokale cache van de edge-server. Nadat het volledige object zich in de cache bevindt, vervult de edge-server byte-range aanvragen voor het CDN voor het object in de cache.

Het standaardgedrag kan worden uitgeschakeld via de rules engine in **Azure CDN Premium van Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Peer-cache vullen hot-filing

De standaard peer cache fill hot-filing functie maakt gebruik van een geavanceerde eigen algoritme. Het maakt gebruik van extra edge caching servers op basis van bandbreedte en geaggregeerde aanvragen metrics om client aanvragen voor grote, zeer populaire objecten te voldoen. Deze functie voorkomt een situatie waarin grote aantallen extra aanvragen naar de oorspronkelijke server van een gebruiker worden verzonden. 

### <a name="conditions-for-large-file-optimization"></a>Voorwaarden voor grote bestandsoptimalisatie

Grote functies voor bestandsoptimalisatie voor **Azure CDN Standard van Verizon** en Azure **CDN Premium van Verizon** zijn standaard ingeschakeld wanneer u het algemene optimalisatietype voor weblevering gebruikt. Er zijn geen beperkingen op de maximale bestandsgrootte. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimaliseren voor de levering van grote bestanden met Azure CDN-standaard van Akamai

**Azure CDN Standard van** Akamai-profieleindpunten bieden een functie die grote bestanden efficiënt levert aan gebruikers over de hele wereld op schaal. De functie vermindert latencies omdat het de belasting op de origin-servers vermindert.

De functie groot bestandsoptimalisatietype schakelt netwerkoptimalisaties en -configuraties in om grote bestanden sneller en responsiever te leveren. Algemene weblevering met **Azure CDN Standard van Akamai-eindpunten** caches bestanden alleen onder 1,8 GB en kan tunnel (niet cache) bestanden tot 150 GB. Grote bestandsoptimalisatie caches bestanden tot 150 GB.

Grote bestandsoptimalisatie is effectief wanneer aan bepaalde voorwaarden is voldaan. Voorwaarden zijn onder meer hoe de oorsprongsserver werkt en de grootte en soorten van de bestanden die worden aangevraagd. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Een Akamai CDN-eindpunt configureren om de levering van grote bestanden te optimaliseren

U uw **Azure CDN-standaard configureren vanaf Akamai-eindpunt** om de levering voor grote bestanden via de Azure-portal te optimaliseren. U hiervoor ook de REST API's of een van de Client SDK's gebruiken. In de volgende stappen wordt het proces weergegeven via de Azure-portal voor een **Azure CDN-standaard vanuit Akamai-profiel:**

1. Als u een nieuw eindpunt wilt toevoegen, selecteert u Op een Akamai **CDN-profielpagina** **Eindpunt**.

    ![Nieuw eindpunt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Selecteer Groot **bestand downloaden**in de vervolgkeuzelijst **Geoptimaliseerd voor** de vervolgkeuzelijst .

    ![Grote bestandsoptimalisatie geselecteerd](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Nadat u het CDN-eindpunt hebt gemaakt, worden de grote bestandsoptimalisaties voor alle bestanden die aan bepaalde criteria voldoen, van toepassing. In de volgende sectie wordt dit proces beschreven.

### <a name="object-chunking"></a>Objectchunking 

Grote bestandsoptimalisatie met **Azure CDN Standard van Akamai** maakt gebruik van een techniek genaamd objectchunking. Wanneer een groot bestand wordt aangevraagd, haalt het CDN kleinere stukken van het bestand terug van de oorsprong. Nadat de CDN POP-server een volledige of byte-range bestandsaanvraag heeft ontvangen, wordt gecontroleerd of het bestandstype wordt ondersteund voor deze optimalisatie. Ook wordt gecontroleerd of het bestandstype voldoet aan de vereisten voor bestandsgrootte. Als de bestandsgrootte groter is dan 10 MB, vraagt de CDN-edgeserver het bestand van de oorsprong in brokken van 2 MB aan. 

Nadat de brok aan de CDN-rand is aangekomen, wordt het in de cache opgeslagen en onmiddellijk aan de gebruiker geserveerd. Het CDN prefetches vervolgens de volgende brok parallel. Deze prefetch zorgt ervoor dat de inhoud één stuk voor blijft op de gebruiker, wat de latentie vermindert. Dit proces wordt voortgezet totdat het hele bestand is gedownload (indien gevraagd), alle bytebereiken beschikbaar zijn (indien gevraagd) of de client de verbinding beëindigt. 

Zie [RFC 7233](https://tools.ietf.org/html/rfc7233)voor meer informatie over de byte-range aanvraag.

De CDN slaat alle brokken in de cache wanneer ze worden ontvangen. Het hele bestand hoeft niet in de cache te worden opgeslagen in de CDN-cache. Latere aanvragen voor het bestand of bytebereiken worden weergegeven vanuit de CDN-cache. Als niet alle segmenten op het CDN in de cache worden opgeslagen, wordt prefetch gebruikt om brokken van de oorsprong op te vragen. Deze optimalisatie is afhankelijk van de mogelijkheid van de oorsprongsserver om byte-range aanvragen te ondersteunen; Als de origin-server geen byte-range aanvragen ondersteunt, is deze optimalisatie niet effectief.

### <a name="caching"></a>Caching
Grote bestandsoptimalisatie maakt gebruik van verschillende standaardcache-vervaldatums van algemene webbezorging. Het maakt onderscheid tussen positieve caching en negatieve caching op basis van HTTP-antwoordcodes. Als de oorsprongsserver een vervaldatum opgeeft via een cachebeheer of een koptekst in het antwoord verloopt, eert het CDN die waarde. Wanneer de oorsprong niet wordt opgegeven en het bestand overeenkomt met de type- en groottevoorwaarden voor dit optimalisatietype, gebruikt het CDN de standaardwaarden voor grote bestandsoptimalisatie. Anders gebruikt het CDN standaardinstellingen voor algemene webbezorging.


|    | Algemeen web | Optimalisatie van grote bestanden 
--- | --- | --- 
Caching: Positief <br> HTTP 200, 203, 300, <br> 301, 302 en 410 | 7 dagen |1 dag  
Caching: Negatief <br> HTTP 204, 305, 404, <br> en 405 | Geen | 1 seconde 

### <a name="deal-with-origin-failure"></a>Omgaan met oorsprong smislukking

De lengte van de uitleestime-out van de oorsprong neemt toe van twee seconden voor algemene weblevering tot twee minuten voor het type grote bestandsoptimalisatie. Deze toename is goed voor de grotere bestandsgroottes om een voortijdige time-outverbinding te voorkomen.

Wanneer een verbinding een time-out heeft, probeert het CDN een aantal keren opnieuw voordat het een fout met een time-out van 504 - gateway naar de client verzendt. 

### <a name="conditions-for-large-file-optimization"></a>Voorwaarden voor grote bestandsoptimalisatie

In de volgende tabel worden de criteria weergegeven waaraan moet worden voldaan voor grote bestandsoptimalisatie:

Voorwaarde | Waarden 
--- | --- 
Ondersteunde bestandstypen | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, teer, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minimale bestandsgrootte | 10 MB 
Maximale bestandsgrootte | 150 GB 
Oorsprongsserverkenmerken | Moet byte-range aanvragen ondersteunen 

## <a name="additional-considerations"></a>Aanvullende overwegingen

Houd rekening met de volgende aanvullende aspecten voor dit optimalisatietype:

- Het chunking-proces genereert extra aanvragen voor de oorspronkelijke server. De totale hoeveelheid gegevens die van de oorsprong wordt geleverd, is echter veel kleiner. Chunking resulteert in betere caching kenmerken op het CDN.

- Geheugen en I/O druk worden verminderd bij de oorsprong omdat kleinere stukken van het bestand worden geleverd.

- Voor segmenten die in de cache op het CDN worden opgeslagen, zijn er geen aanvullende aanvragen voor de oorsprong totdat de inhoud is verlopen of deze uit de cache is verwijderd.

- Gebruikers kunnen bereikaanvragen indienen bij het CDN, die worden behandeld als elk normaal bestand. Optimalisatie is alleen van toepassing als het een geldig bestandstype is en het bytebereik tussen 10 MB en 150 GB ligt. Als de gevraagde gemiddelde bestandsgrootte kleiner is dan 10 MB, gebruikt u in plaats daarvan algemene webbezorging.

