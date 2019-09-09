---
title: 'Locaties en connectiviteitsproviders: Azure ExpressRoute | Microsoft Docs'
description: Dit artikel bevat een gedetailleerd overzicht van de locaties waar services worden aangeboden en hoe u verbinding maakt met Azure-regio's. Gesorteerd op locatie.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2019
ms.author: cherylmc
ms.openlocfilehash: 84dff3c4948109654cf08fa1390ac4e6bf2717dc
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812155"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partners en peeringlocaties voor ExpressRoute

> [!div class="op_single_selector"]
> * [Locaties per provider](expressroute-locations.md)
> * [Providers per locatie](expressroute-locations-providers.md)


De tabellen in dit artikel bevatten informatie over ExpressRoute geografische dekking en locaties, ExpressRoute-connectiviteits providers en ExpressRoute-systeem integrators (SIs).

> [!Note]
> Azure-regio's en ExpressRoute-locaties zijn twee verschillende concepten, en het verschil tussen de twee is van cruciaal belang voor het verkennen van een Azure Hybrid Networking-verbinding. 
>
>

## <a name="azure-regions"></a>Azure-regio's
Azure-regio's zijn wereld wijde data centers waar Azure compute-, netwerk-en opslag bronnen zich bevinden. Bij het maken van een Azure-resource moet een klant een resource locatie selecteren. De resource locatie bepaalt in welk Azure-Data Center (of beschikbaarheids zone) de resource wordt gemaakt.

## <a name="expressroute-locations"></a>ExpressRoute-locaties
ExpressRoute-locaties (soms ook wel peering-locaties of Vergader locaties genoemd) zijn co-locatie faciliteiten waar micro soft Enter prise Edge (MSEE)-apparaten zich bevinden. ExpressRoute locaties zijn het ingangs punt naar het netwerk van micro soft, en zijn wereld wijd gedistribueerd, waardoor klanten de mogelijkheid hebben om verbinding te maken met het netwerk van micro soft over de hele wereld. Deze locaties zijn waar ExpressRoute partners en ExpressRoute direct-klanten cross-connections naar het netwerk van micro soft verlenen. Over het algemeen hoeft de ExpressRoute-locatie niet overeen te komen met de Azure-regio. Een klant kan bijvoorbeeld een ExpressRoute-circuit maken met de resource locatie *VS-Oost*, op de vestiging *Seattle* peering.

U hebt toegang tot Azure-services in alle regio's binnen een geopolitieke regio als u bent verbonden met ten minste één ExpressRoute-locatie in die geopolitieke regio. 

## <a name="locations"></a>Azure-regio's naar ExpressRoute-locaties binnen een geopolitieke regio
In de volgende tabel vindt u een toewijzing van Azure-regio's aan ExpressRoute-locaties binnen een geopolitieke regio.

| **Geopolitieke regio** | **Zone** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- | --- |
| **Australië - overheid** | 1 | Australië Centraal, Australië Centraal 2 |Canberra, Canberra2 |
| **Europa** | 1 |Frankrijk - centraal, Frankrijk - zuid, Europa - noord, Europa - west, VK - west, VK - zuid |Amsterdam, Amsterdam2, kopen Hagen, Dublin, Frankfurt, Londen, London2, Marseille, Newport (Wales), Parijs, Stockholm, Zürich |
| **Noord-Amerika** | 1 |US - oost, US - west, US - oost 2, US - west 2, US - centraal, US - zuid-centraal, US - noord-centraal, US - west-centraal, Canada Centraal, Canada Oost |Atlanta, Chicago, Amsterdam, Denver, Las Miami, Los Angeles,, New York, San Antonio, Seattle, Silicon dal, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec, Amsterdam |
| **Azië** | 2 |Azië - oost, Azië - zuidoost |Hong Kong SAR, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **India** | 2 |India - west, India - centraal, India - zuid |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | 2 |Japan - west, Japan - oost |Osaka, Tokio |
| **Oceania** | 2 |Australië - zuidoost, Australië - oost |Auckland, Melbourne, Perth, Sydney | 
| **Zuid-Korea** | 2 |Korea Centraal, Korea Zuid |Busan, Seoul|
| **VAE** | 3 | UAE-centraal, UAE-noord | Dubai, Dubai2 |
| **Zuid-Afrika** | 3 |Zuid-Afrika-west, Zuid-Afrika-noord |Kaapstad, Johannesburg |
| **Zuid-Amerika** | 3 |Brazilië - zuid |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Azure-regio's en geopolitieke grenzen voor nationale Clouds
De volgende tabel bevat informatie over regio's en geopolitieke grenzen voor nationale clouds.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Cloud van de Amerikaanse overheid** |US Gov - AZ, US Gov - Iowa, US Gov - TX, US Gov - Virginia, US DoD - centraal, US DoD - oost  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **China - oost** |China - oost, China - oost2 |Shanghai, Shanghai2 |
| **China - noord** |China - noord, China - noord2 |Beijing, Beijing2 |
| **Duitsland** |Duitsland Centraal, Duitsland Oost |Berlijn, Frankfurt |

Connectiviteit tussen de geopolitieke regio's wordt niet ondersteund op de standaard ExpressRoute-SKU. U moet de invoegtoepassing ExpressRoute Premium inschakelen voor ondersteuning van globale connectiviteit. Connectiviteit met nationale cloudomgevingen wordt niet ondersteund. U kunt met uw connectiviteitsprovider samenwerken als de noodzaak daartoe zich voordoet.

## <a name="partners"></a>ExpressRoute-connectiviteitsproviders

De volgende tabel geeft de connectiviteitslocaties en de serviceproviders voor elke locatie weer. Als u serviceproviders en de locaties waarvoor zij deze service kunnen bieden, wilt bekijken, raadpleegt u [Locaties per serviceprovider](expressroute-locations.md). 

**Lokale Azure-regio's** zijn degene die [ExpressRoute lokaal](expressroute-faqs.md) op elke peering-locatie hebben. **n.v.t.** geeft aan dat ExpressRoute lokaal niet beschikbaar is op die peering-locatie.


### <a name="production-azure"></a>Productie-Azure
| **Location** | **Eigenaar en adres** | **Lokale Azure-regio's** | **Serviceproviders** |
| --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | Europa -west | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | Europa -west | CenturyLink Cloud Connect, DE-CIX, Colt, Interxion, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | N.v.t. | Equinix, Megaport |
| **Auckland** | [Vocus groep NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | N.v.t. | Devoli, Kordia, Mega Port, Spark NZ, Vocus groep NZ |
| **Busan** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | Korea - zuid | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Australië - centraal | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Australië - centraal 2| CDC |
| **Kaapstad** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | Zuid-Afrika - west | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | India - zuid | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | India - zuid | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | US - noord-centraal | Aryaka Networks, AT & T netbond, CenturyLink Cloud Connect, Cologix, Comcast, CoreSite, Equinix, intercloud, Internet2, Level 3 Communications, Mega Port, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Kopen** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | N.v.t. | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | N.v.t. | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | US - west-centraal | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | UAE - noord | Etisalat VAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | UAE - noord | du datamena, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | Europa - noord | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | N.v.t. | DE CIX, Interxion, oranje |
| **Hong Kong SAR** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | Azië - oost | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, Mega Port, NTT Communications, oranje, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | Zuid-Afrika - noord | British Telecom, Internet oplossingen-Cloud Connect, Liquid Telecom, oranje, Teraco |
| **Kuala Lumpur** | [TIJD dotCom Menara doel stellingen](https://www.aims.com.my/co-location/points-of-presence.html) | N.v.t. | TIME dotCom |
| **Las Vegas** | [LV wijzigen](https://www.switch.com/las-vegas) | N.v.t. | CenturyLink Cloud Connect, Megaport |
| **Londen** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | Verenigd Koninkrijk Zuid | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Twee-House-Noord](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | Verenigd Koninkrijk Zuid | IX REACH, Equinix |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | N.v.t. | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | Frankrijk - zuid | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | Australië - zuidoost | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | N.v.t. | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | N.v.t. | Bell Canada, Cologix, Mega Port, Telus, Zayo |
| **Mumbai** | Tata Communications | India - west | Global CloudXchange (GCX), revertrouwen Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | India - west | Airtel, Sify, Vodafone Idea |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | N.v.t. | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | Verenigd Koninkrijk West | Britse telecommunicatie, Colt, Level 3 Communications, gegevens van de volgende generatie |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | Japan - west | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Parijs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | Frankrijk - centraal | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | N.v.t. | Megaport, NextDC |
| **Quebec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | Canada - oost | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | US - zuid-centraal | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | Brazilië - zuid | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | US - west 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | Korea - centraal | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | US - west | Aryaka Networks, op & T netbond, British Telecom, CenturyLink Cloud Connect, Comcast, CoreSite, Equinix, intercloud, Internet2, IX REACH, pakket, PacketFabric, Level 3 Communications, Mega Port, oranje, Sprint, Tata Communications, Telia-Carrier, Verizon, Zayo |
| **Silicon Valley2** | [CoreSite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | US - west | Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | Azië - zuidoost | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Algemene switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | Azië - zuidoost | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Akte** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | N.v.t. | Telia-Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | Australië - oost | AARNet, op & T netbond, British Telecom, Devoli, Equinix, Kordia, Mega Port, NEXTDC, NTT Communications, Optus, oranje, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus groep NZ |
| **Taipei** | Chief Telecom | N.v.t. | Chief telecom municatie, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | Japan - oost | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | Canada - midden | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | VS-Oost, VS-Oost 2 | Aryaka Networks, AT & T netbond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, CoreSite, Equinix, Internet2, intercloud, Level 3 Communications, Mega Port, Neutrona netwerken, NTT Communications, oranje, PacketFabric, SES, Sprint, Tata Communicatie, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [CoreSite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | VS-Oost, VS-Oost 2 |CoreSite, Viasat, Zayo | 
| **Zurich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | N.v.t. | Intercloud, Interxion |

 **+** betekent binnenkort beschikbaar

### <a name="national-cloud-environments"></a>Nationale cloudomgevingen

### <a name="us-government-cloud"></a>Cloud van de Amerikaanse overheid
| **Location** | **Serviceproviders** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Location** | **Serviceproviders** |
| --- | --- |
| **Beijing** |China Telecom |
| **Peking2** | China Telecom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

Zie [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) voor meer informatie.

### <a name="germany"></a>Duitsland
| **Location** | **Serviceproviders** |
| --- | --- |
| **Berlijn** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connectiviteit via Exchange-providers
Als uw connectiviteitsprovider niet wordt vermeld in de vorige secties, kunt u alsnog verbinding maken.

* Neem contact op met uw connectiviteitsprovider om na te gaan of ze zijn verbonden met een van de exchange-punten in de bovenstaande tabel. Via de volgende koppelingen vindt u meer informatie over services die door de exchange-providers worden verstrekt. Verschillende connectiviteitsproviders zijn al verbonden met Ethernet-exchange-punten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Vraag uw connectiviteitsprovider om uw netwerk uit te breiden tot de gewenste peeringlocatie.
  * Vergewis u ervan dat de connectiviteitsprovider uw connectiviteit uitbreidt op een maximaal beschikbare manier, zodat er geen storingspunten zijn.
* Vraag een ExpressRoute-circuit aan met het exchange-punt wanneer uw connectiviteitsprovider verbinding maakt met Microsoft.
  * Volg de stappen in [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Een ExpressRoute-circuit maken) om connectiviteit in te stellen.

## <a name="connectivity-through-satellite-operators"></a>Connectiviteit via satelliet operators
Als u extern bent en geen glasvezel connectiviteit hebt of als u andere connectiviteits opties wilt verkennen, kunt u de volgende satelliet-Opera tors controleren. 

* Intelsat
* SES
* Viasat

## <a name="c1partners"></a>Connectiviteit via aanvullende service providers
| **Location** | **Exchange** | **Connectiviteits providers** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications, Telecity | BICS, CloudXpress, euro Fiber, Fastweb S. p. A, Golf Bridge International, Kalaam Telecom Bahrein B. S. C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, TDC erhverv, Telecom Italië vonk, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Kroon Castle
| **Kaapstad** | Teraco | MTN |
| **Chicago** | Equinix| Kroon Castle, spectrum Enter prise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, kroon Castle, data found, spectrum onderneming, Transtelco |
| **Frankfurt** | Interxion, telestad | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hong Kong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londen** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Kroon Castle, spectrum Enter prise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, kroon Castle, spectrum Enter prise, Webair |
| **Parijs** | Equinix | Proximus |
| **Quebec** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, spectrum Enter prise, windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecom Limited, LGA Telecom, Verenigde informatie-weg (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, kroon Castle, GTt Communications Inc, Epsilon Telecom Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-SI's
Het inschakelen van particuliere connectiviteit conform uw specifieke behoeften kan lastig zijn, al naargelang de schaal van uw netwerk. U kunt alle SI's uit de volgende tabel gebruiken om u te helpen met de voorbereidingen voor ExpressRoute.

| **Continent** | **Systeemintegratie** |
| --- | --- |
| **Azië** |Avanade Inc., OneAs1a |
| **Australië** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Noord-Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Zuid-Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Volgende stappen
* Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Locatiekaart"
