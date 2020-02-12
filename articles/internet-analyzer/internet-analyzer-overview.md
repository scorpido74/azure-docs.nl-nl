---
title: Azure Internet Analyzer | Microsoft Docs
description: Meer informatie over Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501944"
---
# <a name="what-is-internet-analyzer-preview"></a>Wat is Internet Analyzer? (Preview)

Internet Analyzer is een meting platform aan de client zijde om te testen hoe de wijzigingen in de netwerk infrastructuur van invloed zijn op de prestaties van uw klanten. Of u nu migreert van on-premises naar Azure of een nieuwe Azure-service wilt evalueren, met Internet Analyzer kunt u leren van de gegevens van uw gebruikers en de uitgebreide analyse van micro soft om uw netwerk architectuur beter te begrijpen en te optimaliseren met Azure — voordat u migrat.

Internet Analyzer maakt gebruik van een kleine Java script-client die is inge sloten in uw webtoepassing om de latentie van uw eind gebruikers te meten naar uw geselecteerde set netwerk bestemmingen. we bellen _eind punten_. Met Internet Analyzer kunt u meerdere tests naast elkaar instellen, zodat u verschillende scenario's kunt evalueren als uw infra structuur en de behoeften van de klant zich ontwikkelen. Internet Analyzer biedt aangepaste en vooraf geconfigureerde eind punten die u het gemak en de flexibiliteit bieden voor het nemen van vertrouwde prestatie beslissingen voor uw eind gebruikers. 


> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Snelle & aanpas bare tests

Internet Analyzer behandelt vragen met betrekking tot de prestaties van de Cloud, implementeert naar een nieuwe of meer Azure-regio's, of test nieuwe toepassingen voor toepassings-en inhouds levering in azure, zoals [Azure front deur](https://azure.microsoft.com/services/frontdoor/) en [Microsoft Azure CDN ](https://azure.microsoft.com/services/cdn/). 

Elke test die u in Internet Analyzer maakt, bestaat uit twee eind punten: eind punt A en eind punt B. de prestaties van eind punt B worden geanalyseerd ten opzichte van eind punt A. 

U kunt uw eigen aangepaste eind punt configureren of een keuze selecteren uit een aantal vooraf geconfigureerde Azure-eind punten. Aangepaste eind punten moeten worden gebruikt om on-premises workloads, uw instanties in andere cloud providers of uw aangepaste Azure-configuraties te evalueren. Tests kunnen bestaan uit twee aangepaste eind punten; ten minste één aangepast eind punt moet echter worden gehost in Azure. Vooraf geconfigureerde Azure-eind punten zijn een snelle en eenvoudige manier om de prestaties van populaire Azure-netwerk platforms te evalueren, zoals Azure front deur, Azure Traffic Manager en Azure CDN. 

Tijdens de preview zijn de volgende vooraf geconfigureerde eind punten beschikbaar: 

* **Azure-regio's**
    * Brazilië - zuid
    * India - centraal
    * VS - centraal
    * Azië - oost
    * VS - oost
    * Japan - west
    * Europa - noord
    * Zuid-Afrika - noord
    * Azië - zuidoost 
    * VAE - noord
    * Verenigd Koninkrijk West  
    * Europa -west
    * VS - west 
    * VS - west 2
* **Meerdere Azure-regio combinaties** 
    * VS-Oost, Brazilië-zuid 
    * VS-Oost, Azië-oost 
    * Europa-west, Brazilië-zuid
    * Europa-west, Zuidoost-Azië
    * Europa-west, UAE-noord
    * VS-West, VS-Oost 
    * VS-West, Europa-west
    * VS-West, UAE-noord
    * Europa-west, UAE-noord, Zuidoost-Azië
    * VS-West, Europa-west, Azië-oost
    * VS-West, Europa-noord, Zuidoost-Azië, UAE-noord, Zuid-Afrika-noord 
* **Azure + Azure front-deur** -geïmplementeerd op een combi natie van één of meer Azure-regio's die hierboven wordt vermeld
* **Azure + Azure CDN van micro soft** geïmplementeerd op een enkele combi natie van Azure-regio's die hierboven wordt vermeld
* **Azure + azure Traffic Manager** -geïmplementeerd op een combi natie van meerdere Azure-regio's die hierboven wordt vermeld

## <a name="suggested-test-scenarios"></a>Voorgestelde test scenario's 

Om u te helpen de beste prestatie beslissingen te nemen voor uw klanten, kunt u met Internet Analyzer twee eind punten voor uw specifieke populatie van eind gebruikers evalueren. 

Hoewel Internet Analyzer een breed scala aan vragen kan beantwoorden, zijn de meest voorkomende problemen: 
* Wat is de invloed van de prestaties van de migratie naar de Cloud? 
    * *Aanbevolen test: aangepast (uw huidige on-premises infra structuur) versus Azure (elk vooraf geconfigureerd eind punt)*
* Wat is de waarde van het plaatsen van mijn gegevens aan de rand versus in een Data Center? 
    *  *Aanbevolen test: Azure versus Azure front-deur, Azure versus Azure CDN van micro soft*
* Wat is het voor deel van de prestaties van Azure front-deur?
    *  *Voorgestelde test: aangepast/Azure/CDN versus Azure front-deur*
* Wat is het voor deel van de prestaties van Azure CDN van micro soft? 
    *  *Aanbevolen test: Custom/Azure/AFD versus Azure CDN van micro soft*
* Hoe werkt Azure CDN van micro soft? 
    *  *Voorgestelde test: aangepast (ander CDN-eind punt) versus Azure CDN van micro soft*
* Wat is de beste Cloud voor uw eindgebruikers populatie in elke regio? 
    *  *Aanbevolen test: aangepast (andere Cloud service) versus Azure (elk vooraf geconfigureerd eind punt)*

## <a name="how-it-works"></a>Het werkt als volgt

Als u Internet Analyzer wilt gebruiken, stelt u een Internet Analyzer-resource in het Microsoft Azure-portal in en installeert u de kleine Java script-client in uw toepassing. De client meet de latentie van uw eind gebruikers naar de geselecteerde eind punten door een afbeelding van één pixel te downloaden via HTTPS. Nadat latentie metingen zijn verzameld, verzendt de client de meet gegevens naar Internet Analyzer.

Wanneer een gebruiker de webtoepassing bezoekt, selecteert de Java script-client twee eind punten om te meten over alle geconfigureerde tests. Voor elk eind punt voert de client een _koude_ en _warme_ meting uit. De _koude_ meting leidt tot een extra latentie naast de zuivere netwerk latentie tussen de gebruiker en het eind punt, zoals DNS-omzetting, TCP-verbindings-HANDSHAKE en SSL/TLS-onderhandeling. De _warme_ meting volgt gewoon nadat de _koude_ meting is voltooid en maakt gebruik van het permanente TCP-verbindings beheer van moderne browsers om een nauw keurige meting van de end-to-end latentie te verkrijgen. Wanneer het wordt ondersteund door de browser van de gebruiker, wordt de W3C resource timing-API gebruikt voor een nauw keurige meet tijd. Op dit moment worden alleen warme latentie metingen gebruikt voor analyse.

![architectuur](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Score cards 

Zodra een test is gestart, worden telemetriegegevens weer gegeven in uw Internet Analyzer-resource op het tabblad Score Card. Deze gegevens worden altijd geaggregeerd. Gebruik de volgende filters om te wijzigen welke weer gave van de gegevens u ziet: 

* **Testen:** Selecteer de test waarvoor u de resultaten wilt weer geven. Test gegevens worden weer gegeven zodra er voldoende gegevens zijn om de analyse te volt ooien – in de meeste gevallen binnen 24 uur. 
* **Eind datum van de tijds periode &:** Internet Analyzer genereert dagelijks drie Score cards: elke Score Card weerspiegelt een andere aggregatie periode – de 24 uur vóór (dag), de zeven dagen vóór (week) en 30 dagen vóór (maand). Gebruik het filter eind datum om de periode te selecteren die u wilt weer geven. 
* **Land:** Gebruik dit filter om gegevens te bekijken die specifiek zijn voor eind gebruikers die zich in een land bevinden. Met het globale filter worden gegevens over alle geografische gebieden weer gegeven.  

Meer informatie over Score Cards kunt u vinden op de pagina [uw Score Card interpreteren](internet-analyzer-scorecard.md) . 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van uw eerste Internet Analyzer-resource](internet-analyzer-create-test-portal.md).
* Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md). 
