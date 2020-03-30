---
title: Beveiligingswaarschuwingen in Azure Security Center | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd wat beveiligingswaarschuwingen zijn en welke verschillende typen beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415859"
---
# <a name="security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen in Azure Security Center

In Azure Security Center zijn er verschillende waarschuwingen voor veel verschillende resourcetypen. Security Center genereert waarschuwingen voor resources die zijn geïmplementeerd op Azure, en ook voor resources die zijn geïmplementeerd op on-premises en hybride cloudomgevingen.

Beveiligingswaarschuwingen worden geactiveerd door geavanceerde detecties en zijn alleen beschikbaar in de standaardlaag van Azure Security Center. Er is een gratis proefversie beschikbaar. U kunt uw versie opwaarderen door Prijscategorie te selecteren in het gedeelte [Beveiligingsbeleid](security-center-pricing.md). Ga naar de [Security Center-pagina](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie over tarieven.

## <a name="responding-to-todays-threats"></a>Reageren op de <a name="respond-threats"> </a> bedreigingen van vandaag

De bedreigingen zijn de afgelopen 20 jaar aanzienlijk veranderd. In het verleden, bedrijven meestal alleen zorgen te maken over website defacement door individuele aanvallers die vooral geïnteresseerd waren in het zien van "wat ze konden doen". De aanvallers van vandaag zijn veel geavanceerder en georganiseerder. Ze hebben vaak specifieke financiële en strategische doelstellingen. Ook hebben ze meer middelen beschikbaar, omdat ze mogelijk worden gefinancierd door staten of de georganiseerde misdaad.

Deze veranderende realiteit hebben geleid tot een ongekend niveau van professionaliteit in de aanvaller gelederen. Ze zijn niet langer geïnteresseerd in het beschadigen van websites. Ze zijn nu geïnteresseerd in het stelen van informatie, financiële rekeningen en privégegevens - die ze allemaal kunnen gebruiken om geld te genereren op de open markt of om een bepaalde zakelijke, politieke of militaire positie te benutten. Nog zorgwekkender dan de aanvallers met financiële oogmerken zijn de aanvallers die netwerken kraken om schade te berokkenen aan de infrastructuur en personen.

Organisaties implementeren als antwoord vaak verschillende afzonderlijke oplossingen die zich richten op het verdedigen van de bedrijfsomtrek of de eindpunten waarbij specifieke aanvalskenmerken in de gaten worden gehouden. Deze oplossingen genereren meestal een groot aantal onbetrouwbare waarschuwingen die een beveiligingsanalist vervolgens moet bekijken en onderzoeken. De meeste organisaties hebben niet de tijd en expertise die vereist zijn om te reageren op deze waarschuwingen - vele worden niet nader onderzocht.  

Bovendien hebben aanvallers hun methoden ontwikkeld om veel op handtekeningen gebaseerde verdedigingen te ondermijnen en [zich aan te passen aan cloudomgevingen.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) Nieuwe handelswijzen zijn vereist om nieuwe bedreigingen sneller te identificeren, te detecteren en erop te reageren.

## <a name="what-are-security-alerts-and-security-incidents"></a>Wat zijn beveiligingswaarschuwingen en beveiligingsincidenten? 

**Waarschuwingen** zijn de meldingen die Security Center genereert wanneer het bedreigingen op uw resources detecteert. Security Center geeft prioriteit aan en geeft een overzicht van de waarschuwingen, samen met de informatie die nodig is om het probleem snel te onderzoeken. Security Center geeft ook aanbevelingen voor hoe u een aanval kunt oplossen.

**Een beveiligingsincident** is een verzameling gerelateerde waarschuwingen, in plaats van elke waarschuwing afzonderlijk te vermelden. Security Center maakt gebruik van [Cloud Smart Alert Correlatie](security-center-alerts-cloud-smart.md) om verschillende waarschuwingen en low fidelity signalen te correleren in beveiligingsincidenten.

Met behulp van incidenten biedt Security Center u één overzicht van een aanvalscampagne en alle gerelateerde waarschuwingen. Met deze weergave u snel begrijpen welke acties de aanvaller heeft uitgevoerd en welke resources zijn beïnvloed. Zie [Cloud smart alert-correlatie](security-center-alerts-cloud-smart.md)voor meer informatie.



## <a name="how-does-security-center-detect-threats"></a>Hoe detecteert Security Center bedreigingen? <a name="detect-threats"> </a>

Beveiligingsonderzoekers van Microsoft zijn voortdurend op zoek naar bedreigingen. Vanwege de wereldwijde aanwezigheid van Microsoft in de cloud en on-premises, hebben ze toegang tot een uitgebreide set telemetrie. De uitgebreide en gevarieerde verzameling datasets maakt het ontdekken van nieuwe aanvalspatronen en trends mogelijk in zijn on-premises consumenten- en bedrijfsproducten, evenals zijn online diensten. Als gevolg hiervan kan Security Center de detectie-algoritmen snel bijwerken wanneer aanvallers met nieuwe en steeds meer geavanceerde aanvallen komen. Met deze benadering kunt u de snel veranderende bedreigingen bijhouden.

Om echte bedreigingen te detecteren en fout-positieven te verminderen, verzamelt, analyseert en integreert Security Center logboekgegevens van uw Azure-bronnen en het netwerk. Het werkt ook met connected partner oplossingen, zoals firewall en endpoint protection oplossingen. Security Center analyseert deze informatie, vaak correleren informatie uit meerdere bronnen, om bedreigingen te identificeren.

![Gegevensverzameling en -presentatie in Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center maakt gebruik van geavanceerde beveiligingsanalyses die veel verder gaan dan op handtekeningen gebaseerde benaderingen. Doorbraken in big data- en [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)-technologieën worden gebruikt om gebeurtenissen overal in de cloudinfrastructuur te evalueren - waarbij bedreigingen worden gedetecteerd die onmogelijk te identificeren waren geweest met behulp van handmatige benaderingen en het voorspellen van de ontwikkeling van aanvallen. Deze beveiligingsanalyses omvatten:

* **Geïntegreerde threat intelligence**: Microsoft heeft een enorme hoeveelheid wereldwijde threat intelligence. Telemetrie komt uit meerdere bronnen, zoals Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, de Microsoft Digital Crimes Unit (DCU) en Microsoft Security Response Center (MSRC). Onderzoekers ontvangen ook informatie over bedreigingsinformatie die wordt gedeeld door grote cloudserviceproviders en feeds van andere derden. Azure Security Center kan deze informatie gebruiken om u te waarschuwen over bedreigingen van bekende beveiligingsrisico’s.

* **Gedragsanalyse**: Gedragsanalyse is een techniek die gegevens analyseert en vergelijkt met een verzameling bekende patronen. Deze patronen zijn echter geen eenvoudige handtekeningen. Ze worden vastgesteld aan de hand van complexe machine learning-algoritmen die worden toegepast op grote gegevenssets. Ze worden ook vastgesteld via de zorgvuldige analyse van schadelijk gedrag door deskundige analisten. Azure Security Center kan gedragsanalyses gebruiken om gecompromitteerde bronnen te identificeren op basis van analyse van logboeken van virtuele machines, logboeken van virtuele netwerkapparaten, fabriclogs, crashdumps en andere bronnen.

* **Anomaliedetectie**: Azure Security Center gebruikt ook anomaliedetectie om bedreigingen te identificeren. In tegenstelling tot gedragsanalyse (die afhankelijk is van bekende patronen die zijn afgeleid van grote gegevenssets), is anomaliedetectie meer "gepersonaliseerd" en richt het zich op basislijnen die specifiek zijn voor uw implementaties. Machine learning wordt toegepast om de normale activiteit voor uw implementaties te bepalen en vervolgens worden regels gegenereerd om afwijkende omstandigheden te definiëren die een veiligheidsrisico zouden kunnen vormen.

## <a name="how-are-alerts-classified"></a>Hoe worden waarschuwingen geclassificeerd?

Security Center wijst een ernst toe aan waarschuwingen, zodat u prioriteit geven aan de volgorde waarin u elke waarschuwing bijwoont, zodat wanneer een resource wordt gecompromitteerd, u er meteen bij komen. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.

> [!NOTE]
> De ernst van de waarschuwing wordt anders weergegeven in de portal en versies van de REST API die voorafgaan aan 01-01-2019. Als u een oudere versie van de API gebruikt, upgradet u voor de consistente ervaring die hieronder wordt beschreven.

- **Hoog:** Er is een grote kans dat uw bron is aangetast. Je moet er meteen naar kijken. Security Center heeft veel vertrouwen in zowel de kwaadaardige opzet en in de bevindingen die worden gebruikt om de waarschuwing uit te geven. Bijvoorbeeld een waarschuwing die de uitvoering van een bekend kwaadaardig hulpmiddel zoals Mimikatz detecteert, een veelgebruikte tool die wordt gebruikt voor diefstal van referenties.
- **Medium:** Dit is waarschijnlijk een verdachte activiteit kan erop wijzen dat een bron is aangetast.
Security Center's vertrouwen in de analytische of het vinden is gemiddeld en het vertrouwen van de kwaadaardige intentie is gemiddeld tot hoog. Dit zou meestal machine learning of anomalie-gebaseerde detecties. Bijvoorbeeld een aanmeldingspoging vanaf een afwijkende locatie.
- **Laag:** Dit kan een goedaardigpositief of een geblokkeerde aanval zijn.
   * Security Center is niet zeker genoeg dat de bedoeling kwaadaardig is en de activiteit kan onschuldig zijn. Log clear is bijvoorbeeld een actie die kan plaatsvinden wanneer een aanvaller zijn sporen probeert te verbergen, maar in veel gevallen een routinebewerking is die door beheerders wordt uitgevoerd.
   * Security Center vertelt je meestal niet wanneer aanvallen werden geblokkeerd, tenzij het een interessant geval is waar je naar aanraadt. 
- **Informatief:** U ziet alleen informatieve waarschuwingen wanneer u inzoomt op een beveiligingsincident of als u de REST-API gebruikt met een specifieke waarschuwings-ID. Een incident bestaat meestal uit een aantal waarschuwingen, waarvan sommige op zichzelf kunnen verschijnen als slechts informatief, maar in de context van de andere waarschuwingen kan het waard zijn om nader te bekijken. 

## <a name="continuous-monitoring-and-assessments"></a>Continue monitoring en beoordelingen

Azure Security Center profiteert van beveiligingsonderzoek en data science-teams in heel Microsoft die continu controleren op wijzigingen in het bedreigingslandschap. Dit omvat de volgende initiatieven:

* **Monitoring van bedreigingsinformatie**: Threat intelligence omvat mechanismen, indicatoren, implicaties en bruikbare adviezen over bestaande of opkomende bedreigingen. Deze informatie wordt gedeeld in de beveiligingscommunity en Microsoft volgt continu feeds met informatie over bedreigingen uit interne en externe bronnen.
* **Signaaldelen:** Inzichten van beveiligingsteams in het brede portfolio van cloud- en on-premises services, servers en clienteindpuntapparaten van Microsoft worden gedeeld en geanalyseerd.
* **Microsoft-beveiligingsspecialisten**: continue inzet van teams overal bij Microsoft die op gespecialiseerde beveiligingsgebieden werken, zoals forensisch onderzoek en webaanvaldetectie.
* **Detectieafstemming**: algoritmen worden uitgevoerd op echte gegevenssets van klanten en beveiligingsonderzoekers werken samen met klanten om de resultaten te valideren. Echte en fout-positieven worden gebruikt voor het verfijnen van machine learning-algoritmen.

Deze gecombineerde inspanningen culmineren in nieuwe en verbeterde detecties, waar u direct van profiteren - er is geen actie voor u om te ondernemen.


## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de verschillende soorten waarschuwingen die beschikbaar zijn in Security Center. Zie voor meer informatie:

* [Bescherming tegen bedreigingen in Azure Security Center](threat-protection.md) - Voor een korte beschrijving van de bronnen van de beveiligingswaarschuwingen die worden weergegeven door Azure Security Center 
* **Beveiligingswaarschuwingen in Azure Activity Log - Beveiligingswaarschuwingen** en incidenten worden niet alleen beschikbaar in de Azure-portal of programmatisch, maar worden ook gecontroleerd als gebeurtenissen in Azure Activity [Log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Zie [Beveiligingswaarschuwingen in azure-activiteitenlogboek](https://go.microsoft.com/fwlink/?linkid=2114113) voor meer informatie over het gebeurtenisschema

