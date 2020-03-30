---
title: Metingen van echte gebruikers in Azure Traffic Manager
description: Lees in deze inleiding hoe Azure Traffic Manager Real User Measurements werkt.
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
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938438"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Overzicht voor real-user-metingen in Traffic Manager

Wanneer u een Traffic Manager-profiel instelt om de methode voor prestatieroutering te gebruiken, wordt in de service gekeken waar de DNS-queryaanvragen vandaan komen en neemt u routeringsbeslissingen om deze aanvragers naar de Azure-regio te leiden, waardoor ze de laagste latentie hebben. Dit wordt bereikt door gebruik te maken van de netwerklatentie intelligentie die Traffic Manager onderhoudt voor verschillende netwerken van eindgebruikers.

Met real user measurements u netwerklatentiemetingen meten naar Azure-regio's, van de clienttoepassingen die uw eindgebruikers gebruiken, en laat Traffic Manager die informatie ook overwegen bij het nemen van routeringsbeslissingen. Door ervoor te kiezen de metingen van de werkelijke gebruiker te gebruiken, u de nauwkeurigheid van de routering voor aanvragen van die netwerken waar uw eindgebruikers wonen, verhogen. 

## <a name="how-real-user-measurements-work"></a>Hoe echte gebruikersmetingen werken

Echte gebruikersmetingen werken door uw clienttoepassingen latentie te latentie te latentie naar Azure-regio's te laten zoals deze wordt gezien vanuit de netwerken van eindgebruikers waarin ze worden gebruikt. Als u bijvoorbeeld een webpagina hebt die toegankelijk is voor gebruikers op verschillende locaties (bijvoorbeeld in de Noord-Amerikaanse regio's), u echte gebruikersmetingen met methode voor prestatieroutering gebruiken om ze naar de beste Azure-regio te brengen waarin uw server toepassing wordt gehost.

Het begint met het insluiten van een door Azure geleverde JavaScript (met een unieke sleutel erin) in uw webpagina's. Zodra dat is gebeurd, wanneer een gebruiker die webpagina bezoekt, vraagt JavaScript Traffic Manager om informatie te krijgen over de Azure-regio's die het moet meten. De service retourneert een set eindpunten naar het script die deze regio's vervolgens achtereenvolgens meten door een enkele pixelafbeelding te downloaden die wordt gehost in die Azure-regio's en de latentie te noteren tussen het tijdstip waarop de aanvraag is verzonden en het tijdstip waarop de eerste byte is ontvangen . Deze metingen worden vervolgens gerapporteerd aan de Traffic Manager-service.

Na verloop van tijd gebeurt dit vele malen en in veel netwerken, wat leidt tot Traffic Manager om nauwkeurigere informatie te krijgen over de latentiekenmerken van de netwerken waarin uw eindgebruikers zich bevinden. Deze informatie wordt steeds opgenomen in de routeringsbeslissingen van Traffic Manager. Als gevolg hiervan leidt het tot een grotere nauwkeurigheid in die beslissingen op basis van de verzonden echte gebruikersmetingen.

Wanneer u echte gebruikersmetingen gebruikt, wordt u gefactureerd op basis van het aantal metingen dat naar Traffic Manager wordt verzonden. Ga voor meer informatie over de prijzen naar de [prijspagina van Traffic Manager.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn de voordelen van het gebruik van Real User Measurements?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Kan ik echte gebruikersmetingen gebruiken met niet-Azure-regio's?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Welke routeringsmethode profiteert van real user measurements?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Moet ik elke profielafzonderlijk echte gebruikersmetingen inschakelen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Hoe schakel ik echte gebruikersmetingen voor mijn abonnement uit?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Kan ik echte gebruikersmetingen gebruiken met andere clienttoepassingen dan webpagina's?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Hoeveel metingen worden er elke keer verricht wanneer mijn webpagina met echte gebruikersmetingen wordt weergegeven?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Is er een vertraging voordat Real User Measurements script wordt uitgevoerd in mijn webpagina?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Kan ik echte gebruikersmetingen gebruiken met alleen de Azure-regio's die ik wil meten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Kan ik het aantal metingen beperken tot een bepaald aantal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Kan ik de metingen van mijn clientapplicatie zien als onderdeel van Real User Measurements?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Kan ik het meetscript van Traffic Manager wijzigen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Is het mogelijk voor anderen om de sleutel te zien die ik gebruik met Real User Measurements?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Kunnen anderen misbruik maken van mijn RUM-sleutel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Moet ik de meting JavaScript in al mijn webpagina's plaatsen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Kan informatie over mijn eindgebruikers door Traffic Manager worden geïdentificeerd als ik echte gebruikersmetingen gebruik?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Moet de webpagina die echte gebruikersmetingen meet, Traffic Manager gebruiken voor routering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Moet ik een service hosten op Azure-regio's die kunnen worden gebruikt met echte gebruikersmetingen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Neemt mijn Azure-bandbreedtegebruik toe wanneer ik echte gebruikersmetingen gebruik?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het gebruik [van echte gebruikersmetingen met webpagina's](traffic-manager-create-rum-web-pages.md)
- Meer informatie over [hoe Traffic Manager werkt](traffic-manager-overview.md)
- Meer informatie over [Mobiel Centrum](https://docs.microsoft.com/mobile-center/)
- Meer informatie over de [verkeersrouteringsmethoden](traffic-manager-routing-methods.md) die worden ondersteund door Traffic Manager
- Meer informatie over het [maken van een Traffic Manager-profiel](traffic-manager-create-profile.md)

