---
title: Ethiek en verantwoord gebruik - Personalizer
titleSuffix: Azure Cognitive Services
description: Deze richtlijnen zijn erop gericht u te helpen personalisatie te implementeren op een manier die u helpt vertrouwen in uw bedrijf en service op te bouwen. Zorg ervoor dat u pauze om onderzoek, leren en beraadslagen over de impact van de personalisatie op het leven van mensen. Bij twijfel, zoek begeleiding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: e422284b871214dbeca31b5dd17b9177a18ad3c8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478107"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Richtlijnen voor verantwoorde implementatie van Personalizer

Om mensen en de samenleving het volledige potentieel van AI te laten realiseren, moeten implementaties zo worden ontworpen dat ze het vertrouwen winnen van degenen die AI toevoegen aan hun toepassingen en de gebruikers van toepassingen die met AI zijn gebouwd. Deze richtlijnen zijn erop gericht u te helpen Personalizer te implementeren op een manier die u helpt vertrouwen in uw bedrijf en service op te bouwen. Zorg ervoor dat u pauze om onderzoek, leren en beraadslagen over de impact van de personalisatie op het leven van mensen. Bij twijfel, zoek begeleiding.

Deze richtlijnen zijn niet bedoeld als juridisch advies en u dient er afzonderlijk voor te zorgen dat uw aanvraag voldoet aan de snelle ontwikkelingen in de wet op dit gebied en in uw sector.

Ook bij het ontwerpen van uw toepassing met behulp van Personalizer, moet u rekening houden met een brede set van verantwoordelijkheden die u hebt bij het ontwikkelen van een data-centric AI-systeem, met inbegrip van ethiek, privacy, veiligheid, veiligheid, inclusie, transparantie en verantwoording. U hier meer over lezen in de [sectie Aanbevolen lezen.](#recommended-reading)

U de volgende inhoud gebruiken als een startchecklist en deze aanpassen en verfijnen aan uw scenario. Dit document bevat twee hoofdsecties: de eerste is gewijd aan het benadrukken van overwegingen voor verantwoord gebruik bij het kiezen van scenario's, functies en beloningen voor Personalizer. De tweede nemen van een set van waarden Microsoft is van mening moet worden overwogen bij het bouwen van AI-systemen, en biedt bruikbare suggesties en risico's over hoe uw gebruik van Personalizer hen beïnvloedt.


## <a name="your-responsibility"></a>Uw verantwoordelijkheid

Alle richtlijnen voor verantwoorde implementatie bouwen voort op de basis dat ontwikkelaars en bedrijven die Personalizer gebruiken verantwoordelijk en verantwoordelijk zijn voor de effecten van het gebruik van deze algoritmen in de samenleving. Als u een toepassing ontwikkelt die uw organisatie zal implementeren, moet u uw rol en verantwoordelijkheid voor de werking ervan erkennen en hoe deze van invloed is op mensen. Als u een toepassing ontwerpt die door een derde partij moet worden geïmplementeerd, komt u tot een gedeeld begrip met hen over wie uiteindelijk verantwoordelijk is voor het gedrag van de toepassing en documenteert u dat begrip.

Vertrouwen is gebaseerd op het begrip nagekomen verplichtingen - houd rekening met uw gebruikers, de samenleving en het juridische kader waarin uw toepassingen werken, om expliciete en impliciete toezeggingen te identificeren die ze kunnen hebben.

Microsoft zet voortdurend in op de tools en documenten om u te helpen deze verantwoordelijkheden op te nemen. [Geef feedback aan Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) als u denkt dat extra hulpprogramma's, productfuncties en documenten u zouden helpen bij het implementeren van deze richtlijnen voor het gebruik van Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Factoren voor het verantwoord implementeren van Personalizer

Het implementeren van Personalizer kan van grote waarde zijn voor uw gebruikers en uw bedrijf. Als u Personalizer op een verantwoorde manier wilt implementeren, moet u eerst de volgende richtlijnen overwegen wanneer:

* Het kiezen van use cases om personalisatie toe te passen.
* [Beloningsfuncties](concept-rewards.md)bouwen.
* Kiezen welke [functies](concepts-features.md) over de context en mogelijke acties die u zal gebruiken voor personalisatie.


## <a name="choosing-use-cases-for-personalizer"></a>Use cases kiezen voor Personalizer

Het gebruik van een service die leert om inhoud en gebruikersinterfaces te personaliseren is nuttig. Het kan ook verkeerd worden toegepast als de manier waarop de personalisatie negatieve bijwerkingen in de echte wereld creëert, ook als gebruikers zich niet bewust zijn van content personalisatie.

Voorbeelden van gebruik van Personalizer met verhoogde mogelijkheden voor negatieve bijwerkingen of een gebrek aan transparantie omvatten scenario's waarin de "beloning" afhankelijk is van veel complexe factoren op lange termijn die, wanneer over-vereenvoudigd in een onmiddellijke beloning ongunstige resultaten voor individuen kan hebben. Deze hebben de neiging om te worden beschouwd als "consequente" keuzes, of keuzes die een risico van schade te betrekken. Bijvoorbeeld:


* **Financiën**: Het personaliseren van aanbiedingen op lening, financiële en verzekeringsproducten, waarbij risicofactoren zijn gebaseerd op gegevens die de individuen niet kennen, niet kunnen verkrijgen of niet kunnen betwisten.
* **Onderwijs**: Het personaliseren van rangen voor schoolcursussen en onderwijsinstellingen waar aanbevelingen vooroordelen kunnen uitdragen en het bewustzijn van gebruikers over andere opties kunnen verminderen.
* **Democratie en burgerparticipatie**: Het personaliseren van inhoud voor gebruikers met als doel het beïnvloeden van meningen is consequent en manipulatief.
* **Evaluatie van de beloning**van derden : Het personaliseren van items waarbij de beloning is gebaseerd op een laatste evaluatie door derden van de gebruiker, in plaats van een beloning te hebben die wordt gegenereerd door het eigen gedrag van de gebruiker.
* **Intolerantie voor exploratie:** Elke situatie waarin het verkenningsgedrag van Personalizer schade kan veroorzaken.

Bij het kiezen van use cases voor Personalizer:

* Start het ontwerpproces, rekening houdend met de manier waarop de personalisatie uw gebruikers helpt.
* Houd rekening met de negatieve gevolgen in de echte wereld als sommige items niet zijn gerangschikt voor gebruikers als gevolg van personalisatie patronen of exploratie.
* Overweeg of uw use case geautomatiseerde verwerking is die een aanzienlijke invloed heeft op betrokkenen die onder [avg-artikel](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) 22 of andere wetten vallen.
* Denk aan self-fulfilling prophecy loops. Dit kan gebeuren als een personalisatiebeloning een model traint, zodat een demografische groep vervolgens verder kan worden uitgesloten van toegang tot relevante inhoud. Bijvoorbeeld, de meeste mensen in een buurt met een laag inkomen niet krijgen van een premie verzekering te bieden, en langzaam niemand in de buurt heeft de neiging om het aanbod te zien op alle als er niet genoeg exploratie.
* Sla kopieën van modellen en leerbeleid op voor het geval het nodig is om Personalizer in de toekomst te reproduceren. U dit periodiek of elke vernieuwingsperiode van het model doen.
* Overweeg het niveau van exploratie voldoende voor de ruimte en hoe het te gebruiken als een instrument om "echo kamer" effecten te beperken.


## <a name="selecting-features-for-personalizer"></a>Functies selecteren voor personalizer

Het personaliseren van inhoud is afhankelijk van het hebben van nuttige informatie over de inhoud en de gebruiker. Houd er rekening mee dat sommige gebruikersfuncties voor sommige toepassingen en industrieën direct of indirect als discriminerend en mogelijk illegaal kunnen worden beschouwd.

Houd rekening met het effect van deze functies:

* **Demografische gegevens van gebruikers**: Kenmerken met betrekking tot geslacht, geslacht, leeftijd, ras, religie: deze functies kunnen niet worden toegestaan in bepaalde toepassingen om regelgevende redenen, en het kan niet ethisch zijn om te personaliseren om hen heen, omdat de personalisatie zou verdragen generalisaties en bias. Een voorbeeld van deze bias propagatie is een baan die voor techniek wordt gepost die niet aan bejaarde of geslachtsgebaseerde doelgroepen wordt getoond.
* **Locale informatie**: Op veel plaatsen in de wereld kan locatie-informatie (zoals een postcode, postcode of buurtnaam) sterk gecorreleerd zijn met inkomen, ras en religie.
* **User Perception of Fairness:** Zelfs in gevallen waarin uw toepassing is het maken van goede beslissingen, rekening houden met het effect van gebruikers waarnemen dat de inhoud weergegeven in uw toepassing verandert op een manier die lijkt te zijn gecorreleerd aan functies die discriminerend zou zijn.
* **Onbedoelde bias in features:** Er zijn soorten vooroordelen die kunnen worden geïntroduceerd met behulp van functies die alleen van invloed zijn op een subset van de bevolking. Dit vereist extra aandacht als functies algoritmisch worden gegenereerd, bijvoorbeeld bij het gebruik van beeldanalyse om items in een afbeelding of tekstanalyse te extraheren om entiteiten in tekst te ontdekken. Maak uzelf bewust van de kenmerken van de services die u gebruikt om deze functies te maken.

Pas de volgende praktijken toe bij het kiezen van functies die u in contexten en acties wilt verzenden naar Personalizer:

* Denk aan de wettigheid en ethiek van het gebruik van bepaalde functies voor sommige toepassingen, en of onschuldig uitziende functies volmachten kunnen zijn voor anderen die u wilt of moet vermijden,
* Wees transparant voor gebruikers dat algoritmen en gegevensanalyse worden gebruikt om de opties die ze zien te personaliseren.
* Vraag jezelf af: Zou mijn gebruikers zorg en blij zijn als ik deze informatie gebruikt om de inhoud voor hen te personaliseren? Zou ik me op mijn gemak voelen om hen te laten zien hoe de beslissing is genomen om bepaalde items te markeren of te verbergen?
* Gebruik gedrags- in plaats van classificatie- of segmentatiegegevens op basis van andere kenmerken. Demografische informatie werd traditioneel gebruikt door detailhandelaren om historische redenen - demografische kenmerken leken eenvoudig te verzamelen en te handelen vóór een digitaal tijdperk, maar vraag me af hoe relevant demografische informatie is wanneer u werkelijke interactie, contextuele en historische gegevens hebt die nauwer verband houden met de voorkeuren en identiteit van gebruikers.
* Bedenk hoe u voorkomen dat functies worden 'vervalst' door kwaadwillende gebruikers, wat, als ze in grote aantallen worden uitgebuit, ertoe kunnen leiden dat personalizer op misleidende manieren wordt getraind om bepaalde klassen gebruikers doelbewust te verstoren, in verlegenheid te brengen en lastig te vallen.
* Indien nodig en haalbaar, ontwerpt u uw toepassing zodat uw gebruikers zich kunnen aanmelden of afmelden voor het gebruik van bepaalde persoonlijke functies. Deze kunnen worden gegroepeerd, zoals 'Locatiegegevens', 'Apparaatgegevens', 'Aankoopgeschiedenis in het verleden' enz.


## <a name="computing-rewards-for-personalizer"></a>Computerbeloningen voor personalizer

Personalizer streeft ernaar om de keuze te verbeteren van welke actie te belonen op basis van de beloningsscore die door uw toepassing bedrijfslogica wordt verstrekt.

Een goed gebouwde beloningsscore zal fungeren als een korte termijn proxy voor een zakelijk doel, dat is gekoppeld aan de missie van een organisatie.

Bijvoorbeeld, belonen op klikken zal de Personalizer Service klikken op de kosten van al het andere, zelfs als wat wordt geklikt is storend of niet gebonden aan een zakelijk resultaat.

Als contrasterend voorbeeld wil een nieuwssite mogelijk beloningen instellen die gekoppeld zijn aan iets dat zinvoller is dan klikken, zoals 'Heeft de gebruiker genoeg tijd besteed aan het lezen van de inhoud?' "Hebben ze op relevante artikelen of referenties geklikt?". Met Personalizer is het eenvoudig om statistieken nauw aan beloningen te koppelen. Maar wees voorzichtig niet te verwarren op korte termijn betrokkenheid van de gebruiker met goede resultaten.

### <a name="unintended-consequences-from-reward-scores"></a>Onbedoelde gevolgen van beloningsscores
Beloningsscores kunnen worden opgebouwd met de beste bedoelingen, maar kunnen nog steeds onverwachte gevolgen of onbedoelde resultaten opleveren voor de manier waarop Personalizer inhoud rangschikt.

Bekijk de volgende voorbeelden:

* Belonen video-inhoud personalisatie op het percentage van de video lengte bekeken zal waarschijnlijk de neiging om kortere video's rang.
* Het belonen van sociale media-aandelen, zonder sentimentanalyse van hoe het wordt gedeeld of de inhoud zelf, kan leiden tot het rangschikken van aanstootgevende, ongemodereerde of opruiende inhoud, die veel "betrokkenheid" heeft, maar weinig waarde toevoegt.
* Het belonen van de actie op gebruikersinterface-elementen die gebruikers niet verwachten te veranderen, kan interfereren met de bruikbaarheid en voorspelbaarheid van de gebruikersinterface, waar knoppen verrassend van locatie of doel veranderen zonder waarschuwing, waardoor het voor bepaalde groepen gebruikers moeilijker wordt om productief te blijven.

Implementeer deze best practices:

* Voer offline experimenten met uw systeem uit met behulp van verschillende beloningsbenaderingen om de impact en bijwerkingen te begrijpen.
* Evalueer uw beloningsfuncties en vraag uzelf af hoe een uiterst naïef persoon zijn interpretatie zou buigen en er ongewenste uitkomsten mee zou bereiken.


## <a name="responsible-design-considerations"></a>Verantwoord ontwerp overwegingen

Hieronder volgen ontwerpgebieden voor verantwoorde implementaties van AI. Meer informatie over dit framework in [The Future Computed](https://news.microsoft.com/futurecomputed/).

![AI-waarden uit toekomstige berekende](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Verantwoordelijkheid
*Mensen die AI-systemen ontwerpen en implementeren, moeten verantwoordelijk zijn voor de werking van hun systemen.*

* Maak interne richtlijnen voor het implementeren van personalizer, documenteren en communiceren naar uw team, leidinggevenden en leveranciers.
* Voer periodieke beoordelingen uit van hoe beloningsscores worden berekend, voer offline evaluaties uit om te zien welke functies van invloed zijn op Personalizer en gebruik de resultaten om onnodige en onnodige functies te elimineren.
* Communiceer duidelijk met uw gebruikers hoe Personalizer wordt gebruikt, naar welk doel en met welke gegevens.
* Archiveer informatie en assets - zoals modellen, leerbeleid en andere gegevens - die Personalizer gebruikt om te functioneren, om resultaten te kunnen reproduceren.

### <a name="transparency"></a>Transparantie
*AI-systemen moeten begrijpelijk zijn.* Met Personalizer:

* *Geef gebruikers informatie over hoe de inhoud is gepersonaliseerd.* U uw gebruikers bijvoorbeeld een `Why These Suggestions?` knop laten zien met het label met het label welke functies van de gebruiker en acties een rol hebben gespeeld in de resultaten van Personalizer.
* Zorg ervoor dat uw gebruiksvoorwaarden vermelden dat u informatie over gebruikers en hun gedrag gebruikt om de ervaring te personaliseren.

### <a name="fairness"></a>Eerlijkheid
*AI Systems moet alle mensen eerlijk behandelen.*

* Gebruik Personalizer niet voor use cases waarbij de uitkomsten op lange termijn, consequent zijn of echte schade veroorzaken.
* Gebruik geen functies die niet geschikt zijn om inhoud mee te personaliseren of die ongewenste vooroordelen kunnen helpen verspreiden. Iedereen met vergelijkbare financiële omstandigheden moet bijvoorbeeld dezelfde gepersonaliseerde aanbevelingen voor financiële producten zien.
* Begrijp vooroordelen die kunnen bestaan in functies die afkomstig zijn van editors, algoritmische tools of gebruikers zelf.

### <a name="reliability-and-safety"></a>Betrouwbaarheid en veiligheid
*AI-systemen moeten betrouwbaar en veilig presteren.* Voor personalizer:

* *Geef geen acties aan Personalizer die niet moeten worden gekozen.* Films moeten bijvoorbeeld uit de acties worden gefilterd om te personaliseren als een aanbeveling voor een anonieme of minderjarige gebruiker wordt gedaan.
* *Beheer uw personalizermodel als bedrijfsmiddel.*  Bedenk hoe vaak u het model- en leerbeleid achter uw Personalizer Loop opslaan en een back-up maken en anders u dit als een belangrijk bedrijfsmiddel behandelen. Het reproduceren van resultaten uit het verleden is belangrijk voor zelfaudit en het meten van verbetering.
* *Geef kanalen om directe feedback van gebruikers te krijgen.* Naast het coderen van veiligheidscontroles om ervoor te zorgen dat alleen de juiste doelgroepen de juiste inhoud zien, biedt u gebruikers een feedbackmechanisme om inhoud te rapporteren die verrassend of storend kan zijn. Vooral als uw inhoud afkomstig is van gebruikers of derde partijen, u overwegen microsoft-inhoudsmoderator of aanvullende hulpprogramma's te gebruiken om inhoud te bekijken en te valideren.
* *Voer frequente offline evaluaties uit*. Dit zal u helpen trends te volgen en ervoor te zorgen dat de effectiviteit bekend is.
* *Stel een proces op om kwaadaardige manipulatie te detecteren en te reageren.* Er zijn actoren die zullen profiteren van machine learning en het vermogen van AI-systemen om te leren van hun omgeving om het resultaat te verschuiven naar hun doelen. Als uw gebruik van Personalizer in staat is om belangrijke keuzes te beïnvloeden, zorg er dan voor dat u over de juiste middelen beschikt om deze aanvallenklassen, waaronder menselijke beoordeling in de juiste omstandigheden, op te sporen en te beperken.

### <a name="security-and-privacy"></a>Beveiliging en privacy
*AI-systemen moeten veilig zijn en de privacy respecteren.* Bij gebruik van Personalizer:

* *Informeer gebruikers vooraf over de gegevens die worden verzameld en hoe deze worden gebruikt en verkrijg vooraf hun toestemming*, volgens uw lokale en brancheregelgeving.
* *Zorg voor privacybeschermende gebruikerscontroles.* Voor toepassingen die persoonlijke gegevens opslaan, u overwegen een eenvoudig te vinden knop te bieden voor functies zoals:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

In sommige gevallen kunnen deze wettelijk verplicht zijn. Houd rekening met de afwegingen in omscholingsmodellen, zodat ze geen sporen van verwijderde gegevens bevatten.

### <a name="inclusiveness"></a>Inclusiviteit
*Een breed scala aan menselijke behoeften en ervaringen*aanpakken.
* *Bied gepersonaliseerde ervaringen voor interfaces met toegankelijkheid.* De efficiëntie die voortkomt uit een goede personalisatie - toegepast om de hoeveelheid inspanning, beweging en onnodige herhaling in interacties te verminderen - kan vooral gunstig zijn voor mensen met een handicap.
* *Toepassingsgedrag aanpassen aan context*. U Personalizer gebruiken om tussen intenties in een chatbot te disambiguate, bijvoorbeeld omdat de juiste interpretatie contextueel kan zijn en één maat mogelijk niet allemaal past.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proactieve bereidheid voor meer gegevensbescherming en governance

Het is moeilijk om specifieke veranderingen in regelgevingscontexten te voorspellen, maar in het algemeen zou het verstandig zijn om verder te gaan dan het minimale wettelijke kader om respectvol gebruik van persoonsgegevens te garanderen en transparantie en keuze te bieden met betrekking tot algoritmische besluitvorming.


* Overweeg om vooruit te plannen naar een situatie waarin er nieuwe beperkingen kunnen zijn op gegevens die van individuen worden verzameld, en er is behoefte om te laten zien hoe het werd gebruikt om beslissingen te nemen.
* Houd rekening met extra bereidheid waarbij gebruikers gemarginaliseerde kwetsbare bevolkingsgroepen, kinderen, gebruikers in economische kwetsbaarheid of gebruikers die anderszins gevoelig zijn voor invloed van algoritmische manipulatie kunnen omvatten.
* Denk aan de wijdverbreide ontevredenheid over hoe doelgroeptargeting en publieksbeïnvloedende programma's en algoritmen voor het verzamelen van gegevens zich hebben afgespeeld en hoe bewezen strategische fouten kunnen worden voorkomen.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proactieve beoordelingen tijdens de levenscyclus van uw project

Overweeg methoden te maken voor teamleden, gebruikers en bedrijfseigenaren om zorgen over verantwoord gebruik te melden en een proces te maken dat prioriteit geeft aan hun oplossing en vergelding voorkomt.

Elke persoon denken over bijwerkingen van het gebruik van een technologie wordt beperkt door hun perspectief en levenservaring. Breid het beschikbare meningenaanbod uit door meer uiteenlopende stemmen in uw teams, gebruikers of adviesraden te brengen; zodanig dat het mogelijk is en aangemoedigd voor hen om zich uit te spreken. Overweeg trainings- en leermateriaal om de teamkennis op dit gebied verder uit te breiden en om mogelijkheden toe te voegen om complexe en gevoelige onderwerpen te bespreken.

Overweeg taken met betrekking tot verantwoord gebruik te behandelen, net als andere crosscutting-taken in de levenscyclus van de toepassing, zoals taken die verband houden met gebruikerservaring, beveiliging of DevOps. Deze taken en hun vereisten kunnen geen bijzaak zijn. Verantwoord gebruik moet worden besproken en geverifieerd gedurende de levenscyclus van de toepassing.

## <a name="questions-and-feedback"></a>Vragen en feedback

Microsoft zet zich voortdurend in voor hulpprogramma's en documenten om u te helpen deze verantwoordelijkheden op te nemen. Ons team nodigt u uit om [feedback te geven aan Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) als u denkt dat extra tools, productfuncties en documenten u zouden helpen bij het implementeren van deze richtlijnen voor het gebruik van Personalizer.

## <a name="recommended-reading"></a>Aanbevolen lezen

* Zie de zes principes van Microsoft voor de verantwoorde ontwikkeling van AI die zijn gepubliceerd in het boek [The Future Computed](https://news.microsoft.com/futurecomputed/) van januari 2018
* [Wie is eigenaar van de toekomst?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) van Jaron Lanier.
* [Wapens van Math Destruction](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) door - Cathy O'Neil
* [Ethiek en Data Science](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) door DJ Patil, Hilary Mason, Mike Loukides.
* [ACM-ethische code](https://www.acm.org/code-of-ethics)
* [Wet op de non-discriminatie van genetische informatie - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML-principes voor verantwoordelijke algoritmen](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Volgende stappen

[Kenmerken: actie en context](concepts-features.md).
