---
title: Verkeersweergave in Azure Traffic Manager
description: Lees in deze inleiding hoe verkeersmanager Verkeersweergave werkt.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938344"
---
# <a name="traffic-manager-traffic-view"></a>Verkeersmanager Verkeersoverzicht

Traffic Manager biedt u dns-niveauroutering, zodat uw eindgebruikers worden doorverwezen naar gezonde eindpunten op basis van de routeringsmethode die is opgegeven toen u het profiel hebt gemaakt. Traffic View biedt Traffic Manager een weergave van uw gebruikersbases (op een granulariteitsniveau van DNS-resolver) en hun verkeerspatroon. Wanneer u Traffic View inschakelt, wordt deze informatie verwerkt om u bruikbare inzichten te bieden. 

Door de verkeersweergave te gebruiken, u:
- begrijpen waar uw gebruikersbases zich bevinden (tot een lokale granulariteit op DNS-resolverniveau).
- het volume van het verkeer weergeven (waargenomen als DNS-query's die worden verwerkt door Azure Traffic Manager) afkomstig uit deze regio's.
- krijg inzicht in wat de representatieve latentie is die deze gebruikers ervaren.
- duik diep in de specifieke verkeerspatronen van elk van deze gebruikersbases naar Azure-regio's waar u eindpunten hebt. 

U bijvoorbeeld verkeerweergeven gebruiken om te begrijpen welke regio's een groot aantal verkeer hebben, maar last hebben van hogere latencies. Vervolgens u deze informatie gebruiken om uw voetafdrukuitbreiding te plannen naar nieuwe Azure-regio's, zodat deze gebruikers een lagere latentie-ervaring kunnen hebben.

## <a name="how-traffic-view-works"></a>Hoe de verkeersweergave werkt

Traffic View werkt door Traffic Manager te laten kijken naar de binnenkomende query's die in de afgelopen zeven dagen zijn ontvangen tegen een profiel dat deze functie heeft ingeschakeld. Uit de informatie over binnenkomende query's haalt Traffic View het bron-IP van de DNS-resolver uit dat wordt gebruikt als weergave van de locatie van de gebruikers. Deze worden vervolgens gegroepeerd op een granulariteit op DNS-resolverniveau om gebruikersbasisregio's te maken met behulp van de geografische informatie van IP-adressen die door Traffic Manager worden onderhouden. Traffic Manager kijkt vervolgens naar de Azure-regio's waarnaar de query is gerouteerd en maakt een verkeersstroomkaart voor gebruikers uit die regio's.  
In de volgende stap correleert Traffic Manager het gebruikersbasisgebied met Azure-regiotoewijzing met de latentietabellen voor netwerkintelligentie die wordt onderhouden voor verschillende netwerken van eindgebruikers om inzicht te krijgen in de gemiddelde latentie die gebruikers uit die regio's ervaren wanneer verbinding maken met Azure-regio's. Al deze berekeningen worden vervolgens gecombineerd op een per lokaal DNS resolver IP-niveau voordat het aan u wordt gepresenteerd. U de informatie op verschillende manieren consumeren.

De frequentie van de gegevensupdate van de verkeersweergave is afhankelijk van meerdere interne servicevariabelen. De gegevens worden echter meestal eens per 24 uur bijgewerkt.

>[!NOTE]
>De latentie die in de verkeersweergave wordt beschreven, is een representatieve latentie tussen de eindgebruiker en de Azure-regio's waarmee ze verbinding hadden gemaakt, en is niet de latentie van DE DNS-lookup. Traffic View maakt een best effort schatting van de latentie tussen de lokale DNS resolver en de Azure-regio waarnaar de query is gerouteerd, als er onvoldoende gegevens beschikbaar zijn, dan is de geretourneerde latentie nietig. 

## <a name="visual-overview"></a>Visueel overzicht

Wanneer u naar de sectie **Verkeersweergave** op de pagina Verkeersbeheer navigeert, krijgt u een geografische kaart met een overlay van traffic view-inzichten te zien. De kaart bevat informatie over het gebruikersbestand en de eindpunten voor uw Traffic Manager-profiel.

![Geografische weergave Traffic Manager Traffic View][1]

### <a name="user-base-information"></a>Gebruikersbasisgegevens

Voor de lokale DNS-resolvers voor welke locatie-informatie beschikbaar is, worden ze weergegeven in de kaart. De kleur van de DNS-resolver geeft de gemiddelde latentie aan die wordt ervaren door eindgebruikers die die DNS-resolver hebben gebruikt voor hun Traffic Manager-query's.

Als u de plaats van een DNS-resolver in de kaart over een DNS-resolver-locatie heen zet, wordt het als:
- het IP-adres van de DNS-resolver
- het volume dns-queryverkeer dat Traffic Manager van het verkeer ziet
- de eindpunten naar welke verkeer van de DNS-resolver is doorgestuurd, als een regel tussen het eindpunt en de DNS-resolver 
- de gemiddelde latentie van die locatie naar het eindpunt, weergegeven als de kleur van de lijn die hen verbindt

### <a name="endpoint-information"></a>Eindpuntinformatie

De Azure-gebieden waarin de eindpunten zich bevinden, worden weergegeven als blauwe stippen in de kaart. Als uw eindpunt extern is en er geen Azure-regio aan is toegewezen, wordt het boven aan de kaart weergegeven. Klik op een eindpunt om de verschillende locaties te zien (op basis van de gebruikte DNS-resolver) van waaruit het verkeer naar dat eindpunt is geleid. De verbindingen worden weergegeven als een lijn tussen het eindpunt en de DNS-resolver-locatie en worden gekleurd volgens de representatieve latentie tussen dat paar. Bovendien u de naam zien van het eindpunt, het Azure-gebied waarin het wordt uitgevoerd en het totale aantal aanvragen dat naar het eindpunt is gericht door dit Traffic Manager-profiel.


## <a name="tabular-listing-and-raw-data-download"></a>Tabelvermelding en downloaden van ruwe gegevens

U de verkeersweergavegegevens weergeven in een tabelindeling in Azure-portal. Er is een vermelding voor elk IP/endpoint-paar van de DNS-resolver die het IP-adres van de DNS-resolver, de naam en geografische locatie van het Azure-gebied waarin het eindpunt zich bevindt (indien beschikbaar), het aantal aanvragen dat aan dat eindpunt is gekoppeld, en de representatieve latentie die is gekoppeld aan eindgebruikers die die DNS gebruiken (indien beschikbaar), weergeeft. U de Traffic View-gegevens ook downloaden als csv-bestand dat kan worden gebruikt als onderdeel van een analyseworkflow naar keuze.

## <a name="billing"></a>Billing

Wanneer u Traffic View gebruikt, wordt u gefactureerd op basis van het aantal gegevenspunten dat wordt gebruikt om de gepresenteerde inzichten te maken. Momenteel is het enige gegevenspunttype dat wordt gebruikt de query's die zijn ontvangen ten opzichte van uw Traffic Manager-profiel. Ga voor meer informatie over de prijzen naar de [prijspagina van Traffic Manager.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>Veelgestelde vragen

* [Wat doet Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Hoe kan ik profiteren van het gebruik van Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Hoe verschilt de traffic view van de Traffic Manager-statistieken die beschikbaar zijn via Azure-monitor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Gebruikt Traffic View EDNS Client Subnet-informatie?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Hoeveel dagen gegevens gebruikt Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Hoe gaat Traffic View om met externe eindpunten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Moet ik Traffic View inschakelen voor elk profiel in mijn abonnement?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hoe kan ik Traffic View uitschakelen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Hoe werkt Traffic View-facturering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [hoe Traffic Manager werkt](traffic-manager-overview.md)
- Meer informatie over de [verkeersrouteringsmethoden](traffic-manager-routing-methods.md) die worden ondersteund door Traffic Manager
- Meer informatie over het [maken van een Traffic Manager-profiel](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png