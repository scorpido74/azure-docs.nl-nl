---
title: Azure Internet Analyzer | Microsoft Documenten
description: Meer informatie over Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73501944"
---
# <a name="what-is-internet-analyzer-preview"></a>Wat is Internet Analyzer? (Preview)

Internet Analyzer is een meetplatform aan de clientzijde om te testen hoe wijzigingen in netwerkinfrastructuur de prestaties van uw klanten beïnvloeden. Of u nu een migratie uitvoert van on-premises naar Azure of een nieuwe Azure-service evalueert, u met Internet Analyzer kunt u leren van informatie uit de gegevens van uw gebruikers en uitgebreide analyses van Microsoft om meer inzicht te krijgen in uw netwerkarchitectuur en om deze te optimaliseren met Azure: voordat u de migratie uitvoert.

Internet Analyzer maakt gebruik van een kleine JavaScript-client die is ingesloten in uw webtoepassing om de latentie van uw eindgebruikers naar uw geselecteerde set netwerkbestemmingen te meten, we noemen _eindpunten._ Met Internet Analyzer u meerdere side-by-side tests instellen, zodat u verschillende scenario's evalueren naarmate uw infrastructuur en klantbehoeften evolueren. Internet Analyzer biedt aangepaste en vooraf geconfigureerde eindpunten, zodat u zowel het gemak als de flexibiliteit hebt om vertrouwde prestatiebeslissingen te nemen voor uw eindgebruikers. 


> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Snel & aanpasbare tests

Internet Analyzer behandelt prestatiegerelateerde vragen voor cloudmigratie, implementatie naar een nieuwe of aanvullende Azure-regio's of het testen van nieuwe platforms voor het leveren van toepassingen en inhoud in Azure, zoals [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) en Microsoft Azure [CDN.](https://azure.microsoft.com/services/cdn/) 

Elke test die u maakt in Internet Analyzer bestaat uit twee eindpunten: eindpunt A en Endpoint B. De prestaties van Endpoint B worden geanalyseerd ten opzichte van Eindpunt A. 

U uw eigen aangepaste eindpunt configureren of kiezen uit verschillende vooraf geconfigureerde Azure-eindpunten. Aangepaste eindpunten moeten worden gebruikt om on-premises workloads, uw exemplaren in andere cloudproviders of uw aangepaste Azure-configuraties te evalueren. De tests kunnen bestaan uit twee aangepaste eindpunten; Er moet echter ten minste één aangepast eindpunt worden gehost in Azure. Vooraf geconfigureerde Azure-eindpunten zijn een snelle en eenvoudige manier om de prestaties van populaire Azure-netwerkplatforms zoals Azure Front Door, Azure Traffic Manager en Azure CDN te evalueren. 

Tijdens de preview zijn de volgende vooraf geconfigureerde eindpunten beschikbaar: 

* **Azure-regio's**
    * Brazilië - zuid
    * India - centraal
    * VS - centraal
    * Azië - oost
    * VS - oost
    * Japan - west
    * Europa - noord
    * Zuid-Afrika Noord
    * Azië - zuidoost 
    * VAE Noord
    * Verenigd Koninkrijk West  
    * Europa -west
    * VS - west 
    * VS - west 2
* **Meerdere Azure-regiocombinaties** 
    * Oost-VS, Brazilië Zuid 
    * Oost-VS, Oost-Azië 
    * West-Europa, Brazilië Zuid
    * West-Europa, Zuidoost-Azië
    * West-Europa, VAE Noord
    * West US, Oost-VS 
    * West-VS, West-Europa
    * West US, VAE Noord
    * West-Europa, VAE Noord, Zuidoost-Azië
    * West-VS, West-Europa, Oost-Azië
    * West-VS, Noord-Europa, Zuidoost-Azië, VAE Noord, Zuid-Afrika Noord 
* **Azure + Azure Front Door** - geïmplementeerd op een enkele of meerdere Azure-regiocombinaties hierboven
* **Azure + Azure CDN van Microsoft** - geïmplementeerd op een enkele Azure-regiocombinatie die hierboven wordt vermeld
* **Azure + Azure Traffic Manager** - geïmplementeerd op een combinatie met meerdere Azure-regio's hierboven vermeld

## <a name="suggested-test-scenarios"></a>Voorgestelde testscenario's 

Om u te helpen de beste prestatiebeslissingen te nemen voor uw klanten, u met Internet Analyzer twee eindpunten evalueren voor uw specifieke populatie eindgebruikers. 

Hoewel Internet Analyzer een veelheid aan vragen kan beantwoorden, zijn enkele van de meest voorkomende: 
* Wat is de impact van de prestaties van migratie naar de cloud? 
    * *Aanbevolen test: aangepast (uw huidige on-premises infrastructuur) versus Azure (elk vooraf geconfigureerd eindpunt)*
* Wat is de waarde van mijn gegevens op de rand ten opzichte van een datacenter? 
    *  *Aanbevolen test: Azure vs. Azure Front Door, Azure vs. Azure CDN van Microsoft*
* Wat is het prestatievoordeel van Azure Front Door?
    *  *Aanbevolen test: Aangepaste/ Azure/ CDN vs. Azure Front Door*
* Wat is het prestatievoordeel van Azure CDN van Microsoft? 
    *  *Aanbevolen test: Aangepaste/ Azure/ AFD vs. Azure CDN van Microsoft*
* Hoe stapelt Azure CDN van Microsoft zich op? 
    *  *Aanbevolen test: aangepast (ander CDN-eindpunt) versus Azure CDN van Microsoft*
* Wat is de beste cloud voor uw eindgebruikerspopulatie in elke regio? 
    *  *Aanbevolen test: aangepaste (andere cloudservice) versus Azure (elk vooraf geconfigureerd eindpunt)*

## <a name="how-it-works"></a>Hoe werkt het?

Als u Internet Analyzer wilt gebruiken, stelt u een Internet Analyzer-bron in de Microsoft Azure-portal in en installeert u de kleine JavaScript-client in uw toepassing. De client meet de latentie van uw eindgebruikers naar de geselecteerde eindpunten door een afbeelding van één pixel via HTTPS te downloaden. Na het verzamelen van latentiemetingen stuurt de client de meetgegevens naar Internet Analyzer.

Wanneer een gebruiker de webtoepassing bezoekt, selecteert de JavaScript-client twee eindpunten die u meten voor alle geconfigureerde tests. Voor elk eindpunt voert de klant een _koude_ en _warme_ meting uit. De _koude_ meting maakt extra latentie naast de pure netwerklatentie tussen de gebruiker en het eindpunt, zoals DNS-resolutie, TCP-verbindingshandshake en SSL/TLS-onderhandeling. De _warme_ meting volgt net nadat de _koude_ meting is voltooid en maakt gebruik van het aanhoudende TCP-verbindingsbeheer van moderne browsers om een nauwkeurige meting van de end-to-end latentie te krijgen. Wanneer de browser van de gebruiker wordt ondersteund, wordt de W3C-brontiming-API gebruikt voor nauwkeurige metingstiming. Momenteel worden alleen warme latentiemetingen gebruikt voor analyse.

![architectuur](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Scorecards 

Zodra een test wordt gestart, zijn telemetriegegevens zichtbaar in uw Internet Analyzer-bron onder het tabblad Scorecard. Deze gegevens worden altijd samengevoegd. Gebruik de volgende filters om te wijzigen welke weergave van de gegevens u ziet: 

* **Test:** Selecteer de test waarvoor u de resultaten wilt weergeven. Testgegevens verschijnen zodra er voldoende gegevens zijn om de analyse te voltooien – in de meeste gevallen, binnen 24 uur. 
* **Periode & einddatum:** Internet Analyzer genereert drie scorecards per dag - elke scorecard weerspiegelt een andere aggregatie periode - de 24 uur voorafgaand (dag), de zeven dagen voorafgaand (week), en de 30 dagen voorafgaand (maand). Gebruik het filter Einddatum om de gewenste periode te selecteren. 
* **Land:** Gebruik dit filter om gegevens weer te geven die specifiek zijn voor eindgebruikers die in een land wonen. Het globale filter toont gegevens in alle regio's.  

Meer informatie over scorecards is te vinden op de [pagina Interpreteren van uw scorecard.](internet-analyzer-scorecard.md) 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van uw eerste Internet Analyzer-bron.](internet-analyzer-create-test-portal.md)
* Lees de [veelgestelde vragen over internetanalyse.](internet-analyzer-faq.md) 
