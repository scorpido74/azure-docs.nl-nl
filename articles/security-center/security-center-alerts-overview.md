---
title: Beveiligings waarschuwingen in Azure Security Center | Microsoft Docs
description: In dit onderwerp wordt uitgelegd welke beveiligings waarschuwingen er zijn en welke verschillende typen beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: memildin
ms.openlocfilehash: 69b81417e541bd6853e02065e8cee08e3e04b4a2
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433899"
---
# <a name="security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen in Azure Security Center

In Azure Security Center zijn er diverse waarschuwingen voor veel verschillende resource typen. Security Center genereert waarschuwingen voor resources die zijn geïmplementeerd op Azure en ook voor resources die zijn geïmplementeerd op on-premises en hybride Cloud omgevingen.

Geavanceerde detecties zijn beschikbaar in de Standard-laag van Azure Security Center. Er is een gratis proefversie beschikbaar. U kunt uw versie opwaarderen door Prijscategorie te selecteren in het gedeelte [Beveiligingsbeleid](security-center-pricing.md). Ga naar de [Security Center-pagina](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie over tarieven.

## Reageren op bedreigingen <a name="respond-threats"></a> van vandaag

De bedreigingen zijn de afgelopen 20 jaar aanzienlijk veranderd. In het verleden moesten bedrijven zich meestal alleen zorgen maken over beschadiging van de website door afzonderlijke kwaadwillende gebruikers die voornamelijk wilden zien 'waartoe ze in staat waren'. Vandaag de dag gaan aanvallers veel genuanceerder te werk en zijn ze beter georganiseerd. Ze hebben vaak specifieke financiële en strategische doelstellingen. Ook hebben ze meer middelen beschikbaar, omdat ze mogelijk worden gefinancierd door staten of de georganiseerde misdaad.

Deze veranderende vaststaande hebben geleid tot een ongekend niveau van professionalisatie in de aanvals classificaties. Ze zijn niet langer geïnteresseerd in het beschadigen van websites. Ze zijn nu geïnteresseerd in het stelen van informatie, financiële accounts en persoonlijke gegevens, die allemaal kunnen worden gebruikt om geld te genereren op de open markt of om gebruik te maken van een bepaalde bedrijfs-, politieke of militaire positie. Nog zorgwekkender dan de aanvallers met financiële oogmerken zijn de aanvallers die netwerken kraken om schade te berokkenen aan de infrastructuur en personen.

Organisaties implementeren als antwoord vaak verschillende afzonderlijke oplossingen die zich richten op het verdedigen van de bedrijfsomtrek of de eindpunten waarbij specifieke aanvalskenmerken in de gaten worden gehouden. Deze oplossingen genereren meestal een groot aantal onbetrouwbare waarschuwingen die een beveiligingsanalist vervolgens moet bekijken en onderzoeken. De meeste organisaties hebben niet de tijd en expertise die vereist zijn om te reageren op deze waarschuwingen - vele worden niet nader onderzocht.  

Daarnaast hebben aanvallers hun methoden ontwikkeld om veel op hand tekeningen gebaseerde verdedigingen te doen en zich aan [te passen aan Cloud omgevingen](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nieuwe handelswijzen zijn vereist om nieuwe bedreigingen sneller te identificeren, te detecteren en erop te reageren.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?

Waarschuwingen zijn de meldingen die Security Center gegenereerd wanneer er bedreigingen voor uw resources worden gedetecteerd. Security Center prioriteiten en de waarschuwingen weer gegeven, samen met de informatie die u nodig hebt om snel het probleem te onderzoeken. Security Center biedt ook aanbevelingen voor het oplossen van een aanval.

## Hoe detecteert Security Center bedreigingen? <a name="detect-threats"> </a>

Beveiligingsonderzoekers van Microsoft zijn voortdurend op zoek naar bedreigingen. Vanwege de wereld wijde aanwezigheid van micro soft in de Cloud en on-premises, hebben ze toegang tot een expansieve,-set telemetrie. Dankzij de uitgebreide en gevarieerde verzameling gegevens sets kunt u nieuwe aanvals patronen en-trends detecteren in de on-premises consumenten-en bedrijfs producten, evenals de onlineservices. Als gevolg hiervan kan Security Center de detectie-algoritmen snel bijwerken wanneer aanvallers met nieuwe en steeds meer geavanceerde aanvallen komen. Met deze benadering kunt u de snel veranderende bedreigingen bijhouden.

Security Center verzamelt, analyseert en integreert de logboek gegevens van uw Azure-resources en het netwerk om echte bedreigingen te detecteren en fout-positieven te verminderen. Het werkt ook met verbonden partner oplossingen, zoals firewall-en eindpunt beveiligings oplossingen. Security Center analyseert deze gegevens, vaak het correleren van informatie uit meerdere bronnen, om bedreigingen te identificeren.

![Gegevensverzameling en -presentatie in Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center maakt gebruik van geavanceerde beveiligingsanalyses die veel verder gaan dan op handtekeningen gebaseerde benaderingen. Doorbraken in big data- en [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)-technologieën worden gebruikt om gebeurtenissen overal in de cloudinfrastructuur te evalueren - waarbij bedreigingen worden gedetecteerd die onmogelijk te identificeren waren geweest met behulp van handmatige benaderingen en het voorspellen van de ontwikkeling van aanvallen. Deze beveiligingsanalyses omvatten:

* **Geïntegreerde bedreigings informatie**: zoekt naar bekende fout actoren door gebruik te maken van wereld wijde bedreigings informatie van micro soft-producten en-services, de micro soft Digital misdrijven Unit (DCU), micro soft Security Response Center (MSRC) en externe feeds.
* **Gedrags analyse**: past bekende patronen toe om schadelijk gedrag te ontdekken.
* **Afwijkings detectie**: gebruikt statistische profilering om een historische basis lijn te maken. Er wordt een waarschuwing gegeven bij afwijkingen van vastgestelde basislijnen die aan een mogelijke aanvalsvector voldoen.

In de volgende secties vindt u meer informatie over elk van deze analyses.

### <a name="integrated-threat-intelligence"></a>Geïntegreerde bedreigings informatie

Microsoft heeft een gigantische hoeveelheid informatie over wereldwijde bedreigingen. Telemetrie loopt over van meerdere bronnen, zoals Azure, Office 365, micro soft CRM Online, micro soft Dynamics AX, outlook.com, MSN.com, de micro soft Digital misdrijven Unit (DCU) en het micro soft Security Response Center (MSRC). Onderzoekers ontvangen ook informatie over bedreigingen die worden gedeeld door grote Cloud serviceproviders en feeds van andere derden. Azure Security Center kan deze informatie gebruiken om u te waarschuwen over bedreigingen van bekende beveiligingsrisico’s.

### <a name="behavioral-analytics"></a>Gedragsanalyse

Gedragsanalyse is een techniek waarbij gegevens worden geanalyseerd en vergeleken met een verzameling bekende patronen. Deze patronen zijn echter geen eenvoudige handtekeningen. Ze worden vastgesteld aan de hand van complexe machine learning-algoritmen die worden toegepast op grote gegevenssets. Ze worden ook vastgesteld via de zorgvuldige analyse van schadelijk gedrag door deskundige analisten. Azure Security Center kunt gedrags analyse gebruiken om gemanipuleerde resources te identificeren op basis van de analyse van logboeken van virtuele machines, logboeken voor virtuele netwerk apparaten, infrastructuur logboeken, crash dumps en andere bronnen.

Daarnaast is er een correlatie met andere signalen om te controleren op ondersteunende bewijs van een verbreide campagne. Aan de hand van dit verband kan beter worden vastgesteld welke gebeurtenissen samenhangen met de opgestelde indicatoren van inbreuk. 

### <a name="anomaly-detection"></a>Anomaliedetectie

Azure Security Center maakt ook gebruik van afwijkingsdetectie om bedreigingen te identificeren. In tegenstelling tot gedragsanalyses (die afhankelijk zijn van bekende patronen die zijn afgeleid van grote gegevenssets) is afwijkingsdetectie meer "gepersonaliseerd" en richt het zich op basislijnen die specifiek voor uw implementaties zijn. Machine learning wordt toegepast om de normale activiteit voor uw implementaties te bepalen en vervolgens worden regels gegenereerd om afwijkende omstandigheden te definiëren die een veiligheidsrisico zouden kunnen vormen.

## <a name="how-are-alerts-classified"></a>Hoe worden waarschuwingen geclassificeerd?

Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde kunt bepalen van elke waarschuwing, zodat u meteen aan de voor waarde wordt gevraagd wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

> [!NOTE]
> Ernst van waarschuwing wordt anders weer gegeven in de portal en de REST API, de verschillen worden vermeld in de onderstaande lijst.

* **Hoog:** Er is een hoge waarschijnlijkheid dat uw resource is aangetast. U ziet het meteen. Security Center heeft hoge betrouw baarheid in zowel de schadelijke intentie als de bevindingen die worden gebruikt voor het uitgeven van de waarschuwing. Een waarschuwing waarmee de uitvoering van een bekend schadelijk hulp programma, zoals Mimikatz, wordt gedetecteerd, is een algemeen hulp programma dat wordt gebruikt voor referentie diefstal.
* **Gemiddeld (laag in de rest API)** : dit is waarschijnlijk een verdachte activiteit kan erop wijzen dat een bron is aangetast.
Het vertrouwen van Security Center in het analyse programma of het zoeken is gemiddeld en het vertrouwen van de schadelijke intentie is gemiddeld voor hoog. Deze worden doorgaans machine learning en detecties op basis van anomalieën. Bijvoorbeeld een aanmeldings poging vanaf een afwijkende locatie.
* **Laag (informatie in de rest API)** : dit kan een goed aardige positieve of een geblokkeerde aanval zijn.
   * Security Center niet zeker weet dat de intentie schadelijk is en de activiteit mogelijk onschuld is. Logboek wissen is bijvoorbeeld een actie die zich kan voordoen wanneer een aanvaller hun tracks probeert te verbergen, maar in veel gevallen is een routine bewerking door beheerders uitgevoerd.
   * Security Center vertelt u doorgaans niet wanneer er aanvallen zijn geblokkeerd, tenzij dit een interessant geval is. 
* **Informatie (Silent in de rest API)** : u ziet alleen informatieve waarschuwingen wanneer u inzoomt op een beveiligings incident of als u de rest API met een specifieke waarschuwings-id gebruikt. Een incident bestaat doorgaans uit een aantal waarschuwingen, wat soms alleen ter informatie kan worden weer gegeven, maar in de context van de andere waarschuwingen kan het een betrouw bare zijn. 
 

## <a name="continuous-monitoring-and-assessments"></a>Doorlopende bewaking en evaluaties

Azure Security Center voor delen van het uitvoeren van beveiligings onderzoek en data Science teams in micro soft die voortdurend controleren op wijzigingen in de beveiliging tegen bedreigingen. Dit omvat de volgende initiatieven:

* **Threat Intelligence-bewaking**: bedreigings informatie omvat mechanismen, indica toren, implicaties en bruikbare adviezen over bestaande of opkomende bedreigingen. Deze informatie wordt gedeeld in de beveiligingscommunity en Microsoft volgt continu feeds met informatie over bedreigingen uit interne en externe bronnen.
* **Signalen delen**: inzichten van beveiligingsteams overal uit Microsofts brede portfolio van cloud- en on-premises services, servers en client-endpointapparaten worden gedeeld en geanalyseerd.
* **Microsoft-beveiligingsspecialisten**: continue inzet van teams overal bij Microsoft die op gespecialiseerde beveiligingsgebieden werken, zoals forensisch onderzoek en webaanvaldetectie.
* **Detectieafstemming**: algoritmen worden uitgevoerd op echte gegevenssets van klanten en beveiligingsonderzoekers werken samen met klanten om de resultaten te valideren. Echte en fout-positieven worden gebruikt voor het verfijnen van machine learning-algoritmen.

Deze gecombineerde inspanningen moeten resulteren in nieuwe en verbeterde detecties waarvan u onmiddellijk kunt profiteren: u hoeft helemaal geen actie te ondernemen.

## Typen <a name="security-alert-types"></a> beveiligings waarschuwingen

De volgende onderwerpen helpen u bij het door lopen van de verschillende waarschuwingen, volgens de resource typen:

* [IaaS Vm's en server waarschuwingen](security-center-alerts-iaas.md)
* [Systeem eigen Compute-waarschuwingen](security-center-alerts-compute.md)
* [Data Services-waarschuwingen](security-center-alerts-data-services.md)

In de volgende onderwerpen wordt uitgelegd hoe Security Center de verschillende telemetrie gebruikt die wordt verzameld van integratie met de Azure-infra structuur, om extra beveiligings lagen toe te passen voor resources die in azure zijn geïmplementeerd:

* [Waarschuwingen voor service lagen](security-center-alerts-service-layer.md)
* [Integratie met Azure-beveiligings producten](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Wat zijn beveiligings incidenten?

Een beveiligings incident is een verzameling van gerelateerde waarschuwingen, in plaats van elke waarschuwing afzonderlijk weer te geven. Security Center maakt gebruik van de correlatie van een [Smart-waarschuwing](security-center-alerts-cloud-smart.md) in de cloud om verschillende waarschuwingen en signalen met lage betrouw baarheid te correleren in beveiligings incidenten

Met behulp van incidenten Security Center biedt u één weer gave van een aanvals campagne en alle gerelateerde waarschuwingen. Met deze weer gave kunt u snel inzicht krijgen in welke acties de aanvaller heeft geduurd en welke resources zijn beïnvloed. Zie [Cloud Smart alert correlatie](security-center-alerts-cloud-smart.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de verschillende soorten waarschuwingen die beschikbaar zijn in Security Center. Zie voor meer informatie:

* [Plannings- en bedieningsgids voor Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Veelgestelde vragen over Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)

