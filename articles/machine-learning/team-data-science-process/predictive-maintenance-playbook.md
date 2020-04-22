---
title: Azure AI-handleiding voor voorspellende onderhoudsoplossingen - Team Data Science Process
description: Een uitgebreide beschrijving van de data science die voorspellende onderhoudsoplossingen in meerdere verticale industrieën stimuleert.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 301e2be0c8b971a0236de6a8b5c8bd5f278c3aee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686755"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-handleiding voor voorspellende onderhoudsoplossingen

## <a name="summary"></a>Samenvatting

Predictive maintenance **(PdM)** is een populaire toepassing van predictive analytics die bedrijven in verschillende sectoren kan helpen om hoge assetgebruik en operationele kosten te realiseren. Deze handleiding brengt de zakelijke en analytische richtlijnen en best practices samen om PdM-oplossingen succesvol te ontwikkelen en te implementeren met behulp van de [Microsoft Azure AI-platformtechnologie.](https://azure.microsoft.com/overview/ai-platform)

Om te beginnen introduceert deze handleiding branchespecifieke bedrijfsscenario's en het proces om deze scenario's voor PdM in aanmerking te komen. De gegevensvereisten en modelleringstechnieken om PdM-oplossingen te bouwen worden ook geleverd. De belangrijkste inhoud van de gids is over het data science-proces - inclusief de stappen van gegevensvoorbereiding, functieengineering, modelcreatie en modeloperationalisering. Om deze belangrijke concepten aan te vullen, bevat deze handleiding een reeks oplossingssjablonen om de ontwikkeling van PdM-toepassingen te versnellen. De gids wijst ook op nuttige trainingsbronnen voor de beoefenaar om meer te weten te komen over de AI achter de data science. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science gids overzicht en doelgroep
De eerste helft van deze handleiding beschrijft typische zakelijke problemen, de voordelen van de implementatie van PdM om deze problemen aan te pakken, en bevat een aantal veelvoorkomende use cases. Zakelijke besluitvormers (BDM's) zullen profiteren van deze inhoud. De tweede helft legt de data science achter PdM uit en biedt een lijst van PdM-oplossingen die zijn gebouwd met behulp van de principes die in deze gids worden beschreven. Het biedt ook leerpaden en aanwijzingen voor trainingsmateriaal. Technische besluitvormers (TDM's) zullen deze inhoud nuttig vinden.

| Begin met ... | Als je ... |
|:---------------|:---------------|
| [Business case voor voorspellend onderhoud](#business-case-for-predictive-maintenance) |een zakelijke beslisser (BDM) die de downtime en operationele kosten wil verlagen en het gebruik van apparatuur wil verbeteren |
| [Data Science voor voorspellend onderhoud](#data-science-for-predictive-maintenance) |een technische beslisser (TDM) die PdM-technologieën evalueert om inzicht te krijgen in de unieke gegevensverwerking en AI-vereisten voor voorspellend onderhoud |
| [Oplossingssjablonen voor voorspellend onderhoud](#solution-templates-for-predictive-maintenance)|een software architect of AI Developer die snel een demo of een proof-of-concept wil opstaan |
| [Trainingsbronnen voor voorspellend onderhoud](#training-resources-for-predictive-maintenance) | een of alle van de bovenstaande, en willen de fundamentele concepten achter de data science, tools en technieken te leren.

### <a name="prerequisite-knowledge"></a>Vereiste voorkennis
De BDM-inhoud verwacht niet dat de lezer enige voorafgaande data science kennis heeft. Voor de TDM-inhoud is basiskennis van statistieken en data science nuttig. Kennis van Azure Data en AI-services, Python, R, XML en JSON wordt aanbevolen. AI-technieken worden geïmplementeerd in Python- en R-pakketten. Oplossingssjablonen worden geïmplementeerd met Azure-services, ontwikkeltools en SDK's.

## <a name="business-case-for-predictive-maintenance"></a>Business case voor voorspellend onderhoud

Bedrijven vereisen kritieke apparatuur om te draaien op piekefficiëntie en gebruik om hun rendement op kapitaalinvesteringen te realiseren. Deze activa kunnen variëren van vliegtuigmotoren, turbines, liften of industriële koelers - die miljoenen kosten - tot alledaagse apparaten zoals kopieerapparaten, koffiemachines of waterkoelers.
- Standaard vertrouwen de meeste bedrijven op _correctief onderhoud,_ waarbij onderdelen worden vervangen als en wanneer ze uitvallen. Correctief onderhoud zorgt ervoor dat onderdelen volledig worden gebruikt (waardoor de levensduur van onderdelen niet wordt verspeeld), maar het bedrijf in downtime, arbeid en ongeplande onderhoudsvereisten (vrije uren of ongelegen locaties) wordt gekost.
- Op het volgende niveau oefenen bedrijven _preventief onderhoud_, waarbij ze de nuttige levensduur voor een onderdeel bepalen en het onderhouden of vervangen voordat een storing optreedt. Preventief onderhoud voorkomt ongeplande en catastrofale storingen. Maar de hoge kosten van geplande downtime, onderbenutting van de component tijdens de nuttige levensduur, en arbeid blijven.
- Het doel van _voorspellend onderhoud_ is het optimaliseren van de balans tussen correctief en preventief onderhoud, door _net op tijd_ vervanging van componenten mogelijk te maken. Deze aanpak vervangt deze componenten alleen wanneer ze dicht bij een storing zijn. Door de levensduur van componenten te verlengen (in vergelijking met preventief onderhoud) en ongeplande onderhoudskosten en arbeidskosten te verlagen (meer dan correctief onderhoud), kunnen bedrijven kostenbesparingen en concurrentievoordelen behalen.

## <a name="business-problems-in-pdm"></a>Zakelijke problemen in PdM
Bedrijven lopen een hoog operationeel risico als gevolg van onverwachte storingen en hebben beperkt inzicht in de oorzaak van problemen in complexe systemen. Enkele van de belangrijkste zakelijke vragen zijn:

- Detecteren afwijkingen in apparatuur of systeemprestaties of functionaliteit.
- Voorspel of een actief in de nabije toekomst kan mislukken.
- Schat de resterende gebruiksduur van een actief.
- Identificeer de belangrijkste oorzaken van het falen van een actief.
- Bepaal welke onderhoudsacties moeten worden uitgevoerd, op wanneer, op een asset.

Typische doelverklaringen van PdM zijn:

- Verminder het operationele risico van bedrijfskritieke apparatuur.
- Verhoog het rendement op activa door fouten te voorspellen voordat ze zich voordoen.
- Beheers de kosten van onderhoud door just-in-time onderhoudswerkzaamheden mogelijk te maken.
- Lagere klantenuitputting, verbetering van het merkimago en omzetverlies.
- Verlaag de voorraadkosten door voorraadniveaus te verlagen door het bestelpunt te voorspellen.
- Ontdek patronen die verband houden met verschillende onderhoudsproblemen.
- Kpi's (key performance indicators) bieden, zoals gezondheidsscores voor assetvoorwaarden.
- Schat de resterende levensduur van activa.
- Beveel tijdige onderhoudsactiviteiten aan.
- Schakel net op tijd voorraad in door orderdatums te schatten voor vervanging van onderdelen.

Deze doelverklaringen zijn de uitgangspunten voor:

- _data scientists_ om specifieke voorspellende problemen te analyseren en op te lossen.
- _cloud architecten en ontwikkelaars_ om samen een end-to-end oplossing samen te stellen.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Problemen in aanmerking komen voor voorspellend onderhoud
Het is belangrijk om te benadrukken dat niet alle use cases of zakelijke problemen effectief kunnen worden opgelost door PdM. Er zijn drie belangrijke kwalificatiecriteria die in aanmerking moeten worden genomen tijdens de probleemselectie:

- Het probleem moet voorspellend van aard zijn; dat wil zeggen, er moet een doel of een uitkomst te voorspellen. Het probleem moet ook een duidelijk pad van actie om fouten te voorkomen wanneer ze worden gedetecteerd.
- Het probleem moet een verslag van de operationele geschiedenis van de apparatuur die _zowel goede als slechte resultaten_bevat . De reeks maatregelen die zijn genomen om slechte resultaten te beperken, moet ook beschikbaar zijn als onderdeel van deze records. Foutrapporten, onderhoudslogboeken van prestatiedegradatie, reparatie en vervanging van logboeken zijn ook belangrijk. Daarnaast zijn reparaties uitgevoerd om ze te verbeteren, en vervangende records zijn ook nuttig.
- De geregistreerde geschiedenis moet worden weerspiegeld in _relevante_ gegevens die _voldoende_ kwaliteit hebben om de use case te ondersteunen. Zie [Gegevensvereisten voor voorspellend onderhoud](#data-requirements-for-predictive-maintenance)voor meer informatie over gegevensrelevantie en -toereikendheid.
- Ten slotte moet het bedrijf domeinexperts hebben die een duidelijk begrip van het probleem hebben. Zij moeten zich bewust zijn van de interne processen en praktijken om de analist te kunnen helpen de gegevens te begrijpen en te interpreteren. Zij moeten ook in staat zijn om de nodige wijzigingen aan te brengen in bestaande bedrijfsprocessen om te helpen bij het verzamelen van de juiste gegevens voor de problemen, indien nodig.

## <a name="sample-pdm-use-cases"></a>Voorbeeld van PdM-gebruiksaanvragen
Deze sectie richt zich op een verzameling van PdM use cases uit verschillende industrieën zoals Aerospace, Utilities en Transportation. Elke sectie begint met een zakelijk probleem en bespreekt de voordelen van PdM, de relevante gegevens rond het bedrijfsprobleem en ten slotte de voordelen van een PdM-oplossing.

| Zakelijk probleem | Voordelen van PdM |
|:-----------------|-------------------|
|**Luchtvaart**      |                   |
|_Vertraging en annuleringen_ als gevolg van mechanische problemen. Storingen die niet op tijd kunnen worden hersteld, kunnen ertoe leiden dat vluchten worden geannuleerd en de planning en bewerkingen verstoren. |PdM-oplossingen kunnen voorspellen of de kans dat een vliegtuig wordt vertraagd of geannuleerd als gevolg van mechanische storingen.|
|_Het motoronderdelenfalen van_vliegtuigen : Vervangingen van vliegtuigmotoren behoren tot de meest voorkomende onderhoudstaken binnen de luchtvaartindustrie. Onderhoudsoplossingen vereisen een zorgvuldig beheer van de beschikbaarheid, levering en planning van onderdelenvoorraad|Het verzamelen van informatie over de betrouwbaarheid van componenten leidt tot een aanzienlijke verlaging van de investeringskosten.|
|**Financieel** |                         |
|_Atm mislukking_ is een veel voorkomend probleem binnen de banksector. Het probleem hier is om de kans dat een atm cash opname transactie wordt onderbroken als gevolg van een papier jam of een deel mislukking in de geldautomaat te melden. Op basis van voorspellingen van transactiefouten kunnen geldautomaten proactief worden onderhouden om storingen te voorkomen.| In plaats van de machine halverwege een transactie te laten mislukken, is het wenselijke alternatief om de machine te programmeren om service te weigeren op basis van de voorspelling.|
|**Energie** |                          |
|_Windturbinestoringen_: Windturbines zijn de belangrijkste energiebron in milieuvriendelijke landen/regio's en brengen hoge kapitaalkosten met zich mee. Een belangrijk onderdeel in windturbines is de generator motor, waarvan de storing maakt de turbine ineffectief. Het is ook zeer duur om op te lossen.|Het voorspellen van KPI's zoals MTTF (gemiddelde tijd tot uitval) kan de energiebedrijven helpen turbinestoringen te voorkomen en minimale downtime te garanderen. Faalwaarschijnlijkheid zal technici informeren om turbines te controleren die waarschijnlijk binnenkort zullen mislukken, en tijdgebaseerde onderhoudsregimes plannen. Voorspellende modellen geven inzicht in verschillende factoren die bijdragen aan de storing, waardoor technici de onderliggende oorzaken van problemen beter begrijpen.|
|_Stroomonderbrekerstoringen_: Voor de distributie van elektriciteit naar woningen en bedrijven moeten elektriciteitsleidingen te allen tijde operationeel zijn om de energielevering te garanderen. Stroomonderbrekers helpen bij het beperken of voorkomen van schade aan elektriciteitsleidingen tijdens overbelasting of ongunstige weersomstandigheden. Het zakelijke probleem hier is om circuit breaker mislukkingen te voorspellen.| PdM-oplossingen helpen de reparatiekosten te verlagen en de levensduur van apparatuur zoals stroomonderbrekers te verlengen. Ze helpen de kwaliteit van het elektriciteitsnetwerk te verbeteren door onverwachte storingen en serviceonderbrekingen te verminderen.|
|**Transport en logistiek** |    |
|_Lift deur mislukkingen_: Grote lift bedrijven bieden een full stack service voor miljoenen functionele liften over de hele wereld. Lift veiligheid, betrouwbaarheid, en uptime zijn de belangrijkste zorgen voor hun klanten. Deze bedrijven volgen deze en diverse andere attributen via sensoren, om hen te helpen met correctief en preventief onderhoud. In een lift, de meest prominente klant probleem is defecte lift deuren. Het zakelijke probleem in dit geval is het bieden van een knowledge base voorspellende toepassing die de mogelijke oorzaken van deurstoringen voorspelt.| Liften zijn kapitaalinvesteringen voor mogelijk een levensduur van 20-30 jaar. Dus elke potentiële verkoop kan zeer concurrerend zijn; daarom zijn de verwachtingen voor service en ondersteuning hoog. Voorspellend onderhoud kan deze bedrijven een voordeel bieden ten opzichte van hun concurrenten in hun product- en serviceaanbod.|
|_Wielstoringen_: Wielstoringen zijn verantwoordelijk voor de helft van alle ontsporingen van treinen en kosten miljarden voor de wereldwijde spoorwegindustrie. Wielstoringen zorgen er ook voor dat rails verslechteren, waardoor de rail soms voortijdig kapot gaat. Spoorbreuken leiden tot catastrofale gebeurtenissen zoals ontsporingen. Om dergelijke gevallen te voorkomen, controleren de spoorwegen de prestaties van wielen en vervangen ze op een preventieve manier. Het zakelijke probleem hier is de voorspelling van wielstoringen.| Voorspellend onderhoud van wielen zal helpen bij just-in-time vervanging van wielen |
|_Metro trein deur mislukkingen_: Een belangrijke reden voor vertragingen in de metro operaties is deur storingen van treinwagons. Het zakelijke probleem hier is het voorspellen van treindeur storingen.|Vroege bewustzijn van een deur storing, of het aantal dagen tot een deur storing, zal helpen het bedrijf te optimaliseren trein deur onderhoud schema's.|

De volgende sectie krijgt in de details van hoe de PdM voordelen hierboven besproken realiseren.

## <a name="data-science-for-predictive-maintenance"></a>Data Science voor voorspellend onderhoud

Deze sectie bevat algemene richtlijnen van data science principes en praktijk voor PdM. Het is bedoeld om een TDM, solution architect of een ontwikkelaar te helpen de vereisten en het proces te begrijpen voor het bouwen van end-to-end AI-toepassingen voor PdM. U deze sectie lezen samen met een overzicht van de demo's en proof-of-concept sjablonen die worden vermeld in [oplossingssjablonen voor voorspellend onderhoud.](#solution-templates-for-predictive-maintenance) U deze principes en aanbevolen procedures vervolgens gebruiken om uw PdM-oplossing in Azure te implementeren.

> [!NOTE]
> Deze gids is NIET bedoeld om de lezer Data Science te leren. In de sectie voor trainingsbronnen voor [voorspellend onderhoud](#training-resources-for-predictive-maintenance)worden verschillende nuttige bronnen verstrekt. De [oplossingssjablonen](#solution-templates-for-predictive-maintenance) in de handleiding tonen een aantal van deze AI-technieken voor specifieke PdM-problemen.

## <a name="data-requirements-for-predictive-maintenance"></a>Gegevensvereisten voor voorspellend onderhoud

Het succes van elk leerproces hangt af van (a) de kwaliteit van wat er wordt onderwezen, en (b) het vermogen van de leerling. Voorspellende modellen leren patronen van historische gegevens en voorspellen toekomstige resultaten met een bepaalde waarschijnlijkheid op basis van deze waargenomen patronen. De voorspellende nauwkeurigheid van een model is afhankelijk van de relevantie, toereikendheid en kwaliteit van de trainings- en testgegevens. De nieuwe gegevens die met dit model worden 'gescoord' moeten dezelfde functies en schema hebben als de trainings-/testgegevens. De kenmerken van het kenmerk (type, dichtheid, distributie, enzovoort) van nieuwe gegevens moeten overeenkomen met die van de trainings- en testgegevenssets. De focus van deze sectie ligt op dergelijke gegevensvereisten.

### <a name="relevant-data"></a>Relevante gegevens

Ten eerste moeten de gegevens relevant zijn _voor het probleem_. Houd rekening met de hierboven besproken _gebruikscase_ voor wielstoringen - de trainingsgegevens moeten functies bevatten die verband houden met de wielbewerkingen. Als het probleem was om het falen van het _tractiesysteem_te voorspellen, moeten de trainingsgegevens alle verschillende componenten voor het tractiesysteem omvatten. Het eerste geval is gericht op een specifiek onderdeel, terwijl het tweede geval gericht is op het falen van een groter subsysteem. De algemene aanbeveling is het ontwerpen van voorspellingssystemen over specifieke componenten in plaats van grotere subsystemen, omdat deze meer verspreide gegevens zullen hebben. De domeinexpert (zie [Problemen kwalificeren voor voorspellend onderhoud)](#qualifying-problems-for-predictive-maintenance)moet helpen bij het selecteren van de meest relevante subsets van gegevens voor de analyse. De relevante gegevensbronnen worden nader besproken in [De voorbereiding van gegevens voor voorspellend onderhoud](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Voldoende gegevens
Twee vragen worden vaak gesteld met betrekking tot de gegevens over de geschiedenis van fouten: (1) "Hoeveel foutgebeurtenissen zijn er nodig om een model te trainen?" (2) "Hoeveel records worden beschouwd als "genoeg"?" Er zijn geen definitieve antwoorden, maar alleen vuistregels. Voor (1), meer het aantal faalgebeurtenissen, beter het model. Voor (2) en het exacte aantal foutgebeurtenissen is afhankelijk van de gegevens en de context van het probleem dat wordt opgelost. Maar aan de andere kant, als een machine niet vaak dan het bedrijf zal vervangen, die zal verminderen mislukking exemplaren. Ook hier is de begeleiding van de domeinexpert belangrijk. Er zijn echter methoden om het probleem van _zeldzame gebeurtenissen_het hoofd te bieden . Ze worden besproken in de sectie [Omgaan met onevenwichtige gegevens](#handling-imbalanced-data).

### <a name="quality-data"></a>Kwaliteitsgegevens
De kwaliteit van de gegevens is van cruciaal belang - elke waarde van het voorspellerkenmerk moet _nauwkeurig_ zijn in combinatie met de waarde van de doelvariabele. Datakwaliteit is een goed bestudeerd gebied in statistieken en datamanagement, en dus buiten het bereik van deze gids.

> [!NOTE]
> Er zijn verschillende resources en bedrijfsproducten om kwaliteitsgegevens te leveren. Hieronder wordt een steekproef van referenties gegeven:
> - Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning, Wiley, 2003.
> - [Verkennende data-analyse, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Kwantitatieve Data Cleaning voor grote databases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Inleiding tot Data Cleaning met R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Gegevensvoorbereiding voor voorspellend onderhoud

### <a name="data-sources"></a>Gegevensbronnen

De relevante gegevensbronnen voor voorspellend onderhoud omvatten, maar zijn niet beperkt tot:
- Faalgeschiedenis
- Onderhouds-/reparatiegeschiedenis
- Machinebedrijfsomstandigheden
- Metagegevens van apparatuur

#### <a name="failure-history"></a>Faalgeschiedenis
Foutgebeurtenissen zijn zeldzaam in PdM-toepassingen. Echter, bij het bouwen van voorspellingsmodellen, moet het algoritme meer te weten komen over het normale operationele patroon van een component, evenals de faalpatronen. De opleidingsgegevens moeten dus voldoende voorbeelden uit beide categorieën bevatten. Onderhoudsrecords en onderdelenvervangingsgeschiedenis zijn goede bronnen om foutgebeurtenissen te vinden. Met behulp van enige domeinkennis kunnen afwijkingen in de trainingsgegevens ook worden gedefinieerd als fouten.

#### <a name="maintenancerepair-history"></a>Onderhouds-/reparatiegeschiedenis
Onderhoudsgeschiedenis van een asset bevat details over vervangen onderdelen, uitgevoerde reparatieactiviteiten etc. Deze gebeurtenissen registreren degradatiepatronen. Het ontbreken van deze cruciale informatie in de opleidingsgegevens kan leiden tot misleidende modelresultaten. Foutgeschiedenis kan ook worden gevonden in de onderhoudsgeschiedenis als speciale foutcodes of besteldatums voor onderdelen. Aanvullende gegevensbronnen die foutpatronen beïnvloeden, moeten worden onderzocht en geleverd door domeinexperts.

#### <a name="machine-operating-conditions"></a>Machinebedrijfsomstandigheden
Sensorgebaseerde (of andere) streaminggegevens van de gebruikte apparatuur is een belangrijke gegevensbron. Een belangrijke aanname in PdM is dat de gezondheidsstatus van een machine na verloop van tijd tijdens de routinewerking degradeert. De gegevens zullen naar verwachting tijd-variërende functies bevatten die dit verouderingspatroon vastleggen, en eventuele afwijkingen die leiden tot degradatie. Het temporele aspect van de gegevens is vereist voor het algoritme om de fout- en niet-foutpatronen in de loop van de tijd te leren. Op basis van deze gegevenspunten leert het algoritme te voorspellen hoeveel tijdseenheden een machine kan blijven werken voordat deze mislukt.

#### <a name="static-feature-data"></a>Statische functiegegevens
Statische functies zijn metadata over de apparatuur. Voorbeelden zijn het materiaalmerk, model, vervaardigde datum, startdatum van de dienst, locatie van het systeem en andere technische specificaties.

Voorbeelden van relevante gegevens voor de [voorbeeld-PdM-use cases](#sample-pdm-use-cases) worden hieronder weergegeven:

| Aanvraag gebruiken | Voorbeelden van relevante gegevens |
|:---------|---------------------------|
|_Vertraging en annuleringen van vluchten_ | Vluchtroute-informatie in de vorm van vluchtbenen en paginalogboeken. Vluchtbeengegevens omvatten routegegevens zoals vertrek-/aankomstdatum, tijd, luchthaven, tussenstops enz. Het logboek bevat een reeks fout- en onderhoudscodes die zijn geregistreerd door het grondonderhoudspersoneel.|
|_Het uitvallen van vliegtuigmotoronderdelen_ | Gegevens verzameld van sensoren in het vliegtuig die informatie geven over de toestand van de verschillende onderdelen. Onderhoudsrecords helpen bij het identificeren wanneer er fouten in onderdelen zijn opgetreden en wanneer ze zijn vervangen.|
|_ATM-storing_ | Sensormetingen voor elke transactie (storten van contant geld/cheque) en het verstrekken van contant geld. Informatie over het meten van de kloof tussen noten, notadikte, nota aankomstafstand, controleattributen enz. Onderhoudsgegevens met foutcodes, reparatiegegevens, de laatste keer dat de geldautomaat werd bijgevuld.|
|_Windturbinestoring_ | Sensoren monitoren turbineomstandigheden zoals temperatuur, windrichting, opgewektvermogen, generatorsnelheid enz. Gegevens worden verzameld van meerdere windturbines van windparken in verschillende regio's. Typisch, zal elke turbine veelvoudige sensorlezingen hebben die metingen bij een vast tijdinterval doorgeven.|
|_Circuit breaker storingen_ | Onderhoudslogboeken die corrigerende, preventieve en systematische acties omvatten. Operationele gegevens die automatische en handmatige opdrachten bevatten die naar stroomonderbrekers worden verzonden, zoals voor open en close-acties. Apparaatmetadata zoals productiedatum, locatie, model, enz. Circuit breaker specificaties zoals spanningsniveaus, geolocatie, omgevingsomstandigheden.|
|_Lift deur mislukkingen_| Lift metadata zoals type lift, vervaardigde datum, onderhoudsfrequentie, gebouw type, enzovoort. Operationele informatie zoals het aantal deurcycli, gemiddelde deursluitingstijd. Faalgeschiedenis met oorzaken.|
|_Wielstoringen_ | Sensorgegevens die wielacceleratie, reminstanties, rijafstand, snelheid enz. Statische informatie op wielen zoals fabrikant, vervaardigde datum. Foutgegevens die zijn afgeleid uit de database met onderdeelordergegevens die orderdatums en -hoeveelheden bijhouden.|
|_Metro trein deur mislukkingen_ | Deuropeningen en sluitingstijden, andere operationele gegevens zoals de huidige toestand van de treindeuren. Statische gegevens bevatten kolommen met asset-id, tijd en voorwaardewaarde.|

### <a name="data-types"></a>Gegevenstypen
Gezien de bovenstaande gegevensbronnen zijn de twee belangrijkste gegevenstypen die in het PdM-domein worden waargenomen:

- _Tijdelijke gegevens_: Operationele telemetrie, machineomstandigheden, werkordertypen, prioriteitscodes met tijdstempels op het moment van opname. Voor uitval, onderhoud/reparatie en gebruiksgeschiedenis zijn ook tijdstempels gekoppeld aan elke gebeurtenis.
- _Statische gegevens_: Machinefuncties en bedieningskenmerken in het algemeen zijn statisch omdat ze de technische specificaties van machines of operatorkenmerken beschrijven. Als deze functies in de loop van de tijd kunnen veranderen, moeten ze ook tijdstempels bevatten die eraan zijn gekoppeld.

Voorspeller- en doelvariabelen moeten vooraf worden verwerkt/omgezet in [numerieke, categorische en andere gegevenstypen,](https://www.statsdirect.com/help/basics/measurement_scales.htm) afhankelijk van het algoritme dat wordt gebruikt.

### <a name="data-preprocessing"></a>Gegevens voorbewerking
Als voorwaarde voor _functie engineering,_ de gegevens van verschillende stromen voor te bereiden op een schema waaruit het gemakkelijk is om functies te bouwen samenstellen. Visualiseer de gegevens eerst als een tabel met records. Elke rij in de tabel vertegenwoordigt een trainingsinstantie en de kolommen vertegenwoordigen _voorspellers_ (ook wel onafhankelijke kenmerken of variabelen genoemd). Organiseer de gegevens zodanig dat de laatste kolom(en) het _doel_ is (afhankelijke variabele). Wijs voor elke trainingsinstantie een _label_ toe als de waarde van deze kolom.

Voor tijdelijke gegevens, verdeel de duur van sensorgegevens in tijdeenheden. Elke record moet deel uitmaken van een tijdseenheid voor een actief _en moet afzonderlijke informatie bieden_. Tijdseenheden worden gedefinieerd op basis van zakelijke behoeften in veelvouden van seconden, minuten, uren, dagen, maanden, enzovoort. De tijdseenheid _hoeft niet dezelfde te zijn als de frequentie van het verzamelen van gegevens_. Als de frequentie hoog is, kunnen de gegevens geen significant verschil van één eenheid aan andere tonen. Stel dat de omgevingstemperatuur elke 10 seconden wordt verzameld. Als u hetzelfde interval voor trainingsgegevens gebruikt, worden alleen het aantal voorbeelden opgeblazen zonder aanvullende informatie te verstrekken. In dit geval zou een betere strategie zijn om de gegevens gemiddeld over 10 minuten te gebruiken, of een uur op basis van de zakelijke rechtvaardiging.

Voor statische gegevens
- _Onderhoudsrecords_: Ruwe onderhoudsgegevens hebben een asset-id en tijdstempel met informatie over onderhoudsactiviteiten die op een bepaald moment zijn uitgevoerd. Zet onderhoudsactiviteiten om in _categorische_ kolommen, waarbij elke categoriebeschrijving op unieke wijze wordt toegewezen aan een specifieke onderhoudsactie. Het schema voor onderhoudsrecords bevat asset-id, tijd en onderhoudsactie.

- _Foutrecords_: fouten of faalredenen kunnen worden geregistreerd als specifieke foutcodes of foutgebeurtenissen die worden gedefinieerd door specifieke bedrijfsomstandigheden. In gevallen waarin de apparatuur meerdere foutcodes heeft, moet de domeinexpert helpen bij het identificeren van de codes die relevant zijn voor de doelvariabele. Gebruik de resterende foutcodes of voorwaarden om _voorspellerfuncties_ te construeren die correleren met deze fouten. Het schema voor foutrecords bevat asset-id, tijd, fout of foutreden - indien beschikbaar.

- _Metagegevens van machines en operatoren_: Voeg de gegevens van de machine en de operator samen in één schema om een element aan de operator te koppelen, samen met hun respectieve kenmerken. Het schema voor machinevoorwaarden omvat asset-id, assetfuncties, operator-id en operatorfuncties.

Andere voorbewerkingsstappen voor gegevens omvatten _het verwerken van ontbrekende waarden_ en _normalisatie_ van kenmerkwaarden. Een gedetailleerde discussie valt buiten het bereik van deze gids - zie de volgende sectie voor een aantal nuttige referenties.

Met de bovenstaande vooraf verwerkte gegevensbronnen in de plaats, de definitieve transformatie voordat feature engineering is om de bovenstaande tabellen op basis van de asset-id en timestamp. De resulterende tabel heeft null-waarden voor de foutkolom wanneer de machine normaal werkt. Deze null-waarden kunnen worden toegerekend door een indicator voor normaal gebruik. Gebruik deze foutkolom om labels te maken _voor het voorspellende model_. Zie voor meer informatie de sectie over [modelleringstechnieken voor voorspellend onderhoud.](#modeling-techniques-for-predictive-maintenance)

## <a name="feature-engineering"></a>Functie-engineering
Feature engineering is de eerste stap voorafgaand aan het modelleren van de gegevens. Zijn rol in het proces van de gegevenswetenschap [wordt beschreven hier](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). Een _functie_ is een voorspellend kenmerk voor het model - zoals temperatuur, druk, trillingen, enzovoort. Voor PdM omvat feature engineering het abstraheren van de gezondheid van een machine over historische gegevens die gedurende een aanzienlijke duur zijn verzameld. In die zin is het anders dan zijn collega's, zoals bewaking op afstand, anomaliedetectie en foutdetectie. 

### <a name="time-windows"></a>Tijdvensters
Monitoring op afstand houdt in dat de gebeurtenissen worden gemeld die vanaf _de tijd smaken_. Anomaliedetectiemodellen evalueren (score)binnenkomende gegevensstromen om afwijkingen te markeren vanaf punten in de tijd. Foutdetectie classificert fouten als van specifieke typen wanneer ze zich voordoen in de tijd. PdM gaat daarentegen in om het voorspellen van fouten over een _toekomstige periode_, op basis van functies die het gedrag van de machine gedurende de _historische periode_vertegenwoordigen . Voor PdM zijn functiegegevens van afzonderlijke tijdpunten te luidruchtig om voorspellend te zijn. Dus de gegevens voor elke functie moet worden _gladgestreken_ door het aggregeren van gegevenspunten in de tijd vensters.

### <a name="lag-features"></a>Lag-functies
De bedrijfsvereisten bepalen hoe ver het model in de toekomst moet voorspellen. Deze duur helpt op zijn beurt bij het definiëren van 'hoe ver terug het model moet kijken' om deze voorspellingen te doen. Deze 'terugkijkende' periode heet de _vertraging_, en functies ontworpen over deze lag periode worden genoemd _lag functies_. In deze sectie worden vertragingsfuncties besproken die kunnen worden opgebouwd uit gegevensbronnen met tijdstempels en functiecreatie uit statische gegevensbronnen. Lag-functies zijn meestal _numeriek_ van aard.

> [!IMPORTANT]
> De venstergrootte wordt bepaald door middel van experimenten, en moet worden afgerond met de hulp van een domein expert. Hetzelfde voorbehoud geldt voor de selectie en definitie van vertragingfuncties, hun aggregaties en het type vensters.

#### <a name="rolling-aggregates"></a>Rollende aggregaten
Voor elke record van een actief wordt een rollend venster van grootte "W" gekozen als het aantal eenheden tijd om de aggregaten te berekenen. Lag-functies worden vervolgens berekend met behulp van de W-perioden _vóór de datum_ van die record. In figuur 1 geven de blauwe lijnen sensorwaarden weer die voor een asset voor elke tijdseenheid zijn geregistreerd. Ze geven een voortschrijdend gemiddelde van functiewaarden aan over een venster van grootte W=3. Het voortschrijdend gemiddelde wordt berekend over alle records met tijdstempels in het bereik t<sub>1</sub> (in oranje) tot t<sub>2</sub> (in het groen). De waarde voor W is meestal in minuten of uren, afhankelijk van de aard van de gegevens. Maar voor bepaalde problemen, het kiezen van een grote W (zeg 12 maanden) kan de hele geschiedenis van een actief tot het moment van het record.

![Afbeelding 1. Rollende geaggregeerde functies](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Afbeelding 1. Rollende geaggregeerde functies

Voorbeelden van rollende aggregaten over een tijdvenster zijn telling, gemiddelde, CUMESUM (cumulatieve som) maten, min / max waarden. Bovendien worden variantie, standaarddeviatie en het aantal uitschieters buiten N-standaarddeviaties vaak gebruikt. Voorbeelden van aggregaten die kunnen worden toegepast voor de [use cases](#sample-pdm-use-cases) in deze handleiding worden hieronder vermeld. 
- _Vluchtvertraging_: aantal foutcodes over de laatste dag/week.
- _Motor onderdeel storing_: rollen middelen, standaard deviatie, en som over de afgelopen dag, week etc. Deze statistiek moet worden bepaald samen met de business domein expert.
- _ATM-storingen_: rollend middel, mediaan, bereik, standaarddeviaties, aantal uitschieters boven drie standaarddeviaties, bovenste en onderste CUMESUM.
- _Metro trein deur mislukkingen_: Telling van de gebeurtenissen over de afgelopen dag, week, twee weken etc.
- _Circuit breaker mislukkingen_: Mislukking telt over afgelopen week, jaar, drie jaar etc.

Een andere nuttige techniek in PdM is het vastleggen van trendveranderingen, pieken en niveauwijzigingen met behulp van algoritmen die afwijkingen in gegevens detecteren.

#### <a name="tumbling-aggregates"></a>Tuimelende aggregaten
Voor elke gelabelde record van een actief wordt een venster van grootte _W-<sub>k</sub> _ gedefinieerd, waarbij _k_ het aantal vensters van grootte _W_is. Aggregaten worden vervolgens gemaakt over _k_ _tumbling windows_ _W-k, W-<sub>(k-1)</sub>, ..., W-<sub>2</sub>, W-<sub>1</sub> _ voor de perioden vóór de tijdstempel van een record. _k_ kan een klein aantal zijn om kortetermijneffecten vast te leggen, of een groot aantal om afbraakpatronen op lange termijn vast te leggen. (zie figuur 2).

![Afbeelding 2. Tumbling geaggregeerde functies](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Afbeelding 2. Tumbling geaggregeerde functies

Vertragingsfuncties voor de use case van windturbines kunnen bijvoorbeeld worden gemaakt met W=1 en k=3. Zij impliceren de vertraging voor elk van de afgelopen drie maanden met behulp van boven-en onderkant uitschieters.

### <a name="static-features"></a>Statische functies

Technische specificaties van de apparatuur, zoals de datum van vervaardiging, modelnummer, locatie, zijn enkele voorbeelden van statische functies. Ze worden behandeld als _categorische_ variabelen voor modellering. Enkele voorbeelden voor de use case van de stroomonderbreker zijn spanning, stroom, vermogen, transformatortype en energiebron. Voor wielstoringen is het type wielwielen (lichtmetalen versus staal) een voorbeeld.

De tot nu toe besproken inspanningen voor de voorbereiding van gegevens moeten ertoe leiden dat de gegevens worden georganiseerd, zoals hieronder wordt weergegeven. Trainings-, test- en validatiegegevens moeten dit logische schema hebben (in dit voorbeeld wordt de tijd in eenheden dagen weergegeven).

| Asset-id | Time | \<Functiekolommen> | Label |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

De laatste stap in feature engineering is **het labelen** van de doelvariabele. Dit proces is afhankelijk van de modelleringstechniek. Op zijn beurt is de modelleringstechniek afhankelijk van het bedrijfsprobleem en de aard van de beschikbare gegevens. Etikettering wordt besproken in de volgende sectie.

> [!IMPORTANT]
> Datavoorbereiding en feature engineering zijn net zo belangrijk als modelleringstechnieken om tot succesvolle PdM-oplossingen te komen. De domeinexpert en de behandelaar moeten veel tijd investeren in het verkrijgen van de juiste functies en gegevens voor het model. Een kleine steekproef van vele boeken over eigenschaptechniek wordt hieronder vermeld:
> - Pyle, D. Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems), 1999
> - Zheng, A., Casari, A. Feature Engineering voor Machine Learning: Principles and Techniques for Data Scientists, O'Reilly, 2018.
> - Dong, G. Liu, H. (Editors), Feature Engineering voor Machine Learning en Data Analytics (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modelleringstechnieken voor voorspellend onderhoud

Deze sectie bespreekt de belangrijkste modelleringstechnieken voor PdM-problemen, samen met hun specifieke labelbouwmethoden. Merk op dat een enkele modelleringstechniek kan worden gebruikt in verschillende industrieën. De modelleringstechniek is gekoppeld aan het probleem van de gegevenswetenschap, in plaats van de context van de gegevens bij de hand.

> [!IMPORTANT]
> De keuze van labels voor de fail cases en de etiketteringsstrategie  
> in overleg met de domeinexpert worden vastgesteld.

### <a name="binary-classification"></a>Binaire classificatie
Binaire classificatie wordt gebruikt om _de waarschijnlijkheid te voorspellen dat een apparaat binnen een toekomstige periode faalt_ - de toekomstige _horizonperiode X_genoemd . X wordt bepaald door het bedrijfsprobleem en de gegevens bij de hand, in overleg met de domeinexpert. Een aantal voorbeelden:
- _minimale doorlooptijd_ die nodig is om onderdelen te vervangen, onderhoudsmiddelen te implementeren, onderhoud uit te voeren om een probleem te voorkomen dat zich in die periode waarschijnlijk zal voordoen.
- _minimaal aantal gebeurtenissen_ die kunnen gebeuren voordat er een probleem optreedt.

In deze techniek worden twee soorten trainingsvoorbeelden geïdentificeerd. Een positief voorbeeld, _dat een mislukking aangeeft,_ met etiket = 1. Een negatief voorbeeld, dat normale bewerkingen aangeeft, met label = 0. De doelvariabele, en dus de labelwaarden, zijn _categorisch_. Het model moet elk nieuw voorbeeld identificeren als waarschijnlijk mislukken of normaal werken tijdens de volgende X-tijdeenheden.

#### <a name="label-construction-for-binary-classification"></a>Labelconstructie voor binaire classificatie
De vraag hier is: "Wat is de kans dat het actief zal mislukken in de volgende X-eenheden van de tijd?" Om deze vraag te beantwoorden, label X records voorafgaand aan het falen van een actief als "over te mislukken" (label = 1), en label alle andere records als zijnde "normaal" (label =0). (zie figuur 3).

![Afbeelding 3. Labeling voor binaire classificatie](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Afbeelding 3. Labeling voor binaire classificatie

Voorbeelden van etiketteringsstrategie voor sommige use cases staan hieronder vermeld.
- _Vluchtvertragingen_: X kan worden gekozen als een dag, om vertragingen te voorspellen in de komende 24 uur. Dan zijn alle vluchten die binnen 24 uur voor mislukkingen zijn gelabeld als 1.
- _ATM cash dispense mislukkingen_: Een doel kan zijn om de kans op mislukking van een transactie te bepalen in het komende een uur. In dat geval worden alle transacties die in het afgelopen uur na de fout zijn uitgevoerd, aangeduid als 1. Om de kans op mislukking te voorspellen ten opzichte van de volgende n-valutanota's die worden verstrekt, worden alle notities die worden verstrekt in de laatste N-nota's van een fout gelabeld als 1.
- _Circuit breaker mislukkingen_: Het doel kan zijn om de volgende stroomonderbreker commando mislukking te voorspellen. In dat geval wordt X gekozen als een toekomstig commando.
- _Trein deur storingen_: X kan worden gekozen als twee dagen.
- _Windturbine storingen_: X kan worden gekozen als twee maanden.

### <a name="regression-for-predictive-maintenance"></a>Regressie voor voorspellend onderhoud
Regressiemodellen worden gebruikt om _de resterende gebruiksduur (RUL) van een actief_te berekenen . RUL wordt gedefinieerd als de hoeveelheid tijd die een actief operationeel is voordat de volgende fout optreedt. Elk trainingsvoorbeeld is een record dat behoort tot een tijdeenheid _nY_ voor een asset, waarbij _n_ het veelvoud is. Het model moet de RUL van elk nieuw voorbeeld berekenen als een _continu getal_. Dit getal geeft de resterende periode aan voordat de fout is mislukt.

#### <a name="label-construction-for-regression"></a>Labelconstructie voor regressie
De vraag is hier: "Wat is de resterende levensduur (RUL) van de apparatuur?" Berekent voor elke record voorafgaand aan de fout het label als het aantal eenheden dat nog resterend is vóór de volgende fout. In deze methode zijn labels continue variabelen. (Zie figuur 4)

![Afbeelding 4: Labeling voor regressie](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Afbeelding 4: Labeling voor regressie

Voor regressie wordt etikettering uitgevoerd met betrekking tot een faalpunt. De berekening ervan is niet mogelijk zonder te weten hoe lang het actief heeft overleefd voordat een mislukking. Dus in tegenstelling tot binaire classificatie, activa zonder fouten in de gegevens kunnen niet worden gebruikt voor modellering. Dit probleem kan het best worden aangepakt door een andere statistische techniek genaamd [Survival Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Maar potentiële complicaties kunnen zich voordoen bij het toepassen van deze techniek op PdM use cases die tijd-variërende gegevens met frequente intervallen te betrekken. Voor meer informatie over Survival Analysis, zie [deze one-pager](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificatie van meerdere klassen voor voorspellend onderhoud
Classificatietechnieken van meerdere klassen kunnen worden gebruikt in PdM-oplossingen voor twee scenario's:
- Twee _toekomstige resultaten voorspellen_: Het eerste resultaat is een _tijdsslange tijd_ voor een actief. Het actief wordt toegewezen aan een van de vele mogelijke perioden. Het tweede resultaat is de kans op mislukking in een toekomstige periode als gevolg _van een van de vele onderliggende oorzaken_. Deze voorspelling stelt de onderhoudsploeg in staat om te kijken naar symptomen en onderhoudsschema's te plannen.
- Voorspel _de meest waarschijnlijke oorzaak_ van een bepaalde fout. Dit resultaat beveelt de juiste set onderhoudsacties aan om een storing op te lossen. Een gerangschikte lijst met onderliggende oorzaken en aanbevolen reparaties kan technici helpen hun reparatieacties te prioriteren na een storing.

#### <a name="label-construction-for-multi-class-classification"></a>Labelconstructie voor classificatie van meerdere klassen
De vraag hier is: "Wat is de kans dat een actief zal mislukken in de volgende _nZ-eenheden_ van de tijd waar _n_ is het aantal perioden?" Om deze vraag te beantwoorden, label nZ records voorafgaand aan het falen van een actief met behulp van emmers van de tijd (3Z, 2Z, Z). Label alle andere records als "normaal" (label = 0). Bij deze methode bevat de doelvariabele _categorische_ waarden. (Zie figuur 5).

![Afbeelding 5. Fouttijdvoorspellingslabels voor classificatie van meerdere klassen](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Afbeelding 5. Labeling voor classificatie in meerdere klassen voor fouttijdvoorspelling

De vraag hier is: "Wat is de kans dat het actief zal mislukken in de volgende X-eenheden van de tijd als gevolg van de oorzaak / probleem _P<sub>i?"</sub>_ waar _ik_ is het aantal mogelijke oorzaken. Om deze vraag te beantwoorden, label X records voorafgaand aan het falen van een actief als "over te mislukken als gevolg van oorzaak _P<sub>i</sub>_" (label = _P<sub>i</sub>_). Label alle andere records als zijnde "normaal" (label = 0). Ook bij deze methode zijn etiketten categorisch (Zie figuur 6).

![Afbeelding 6. Hoofdoorzaakvoorspellingslabels voor classificatie met meerdere klassen](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Afbeelding 6. Labeling voor classificatie met meerdere klassen voor voorspelling van de hoofdoorzaak

Het model wijst een kans op een mislukking toe als gevolg van elke _P<sub>i</sub> _ evenals de kans op geen mislukking. Deze waarschijnlijkheden kunnen worden geordend op omvang om voorspelling van de problemen die het meest waarschijnlijk optreden in de toekomst mogelijk te maken.

De vraag hier is: "Welke onderhoudsacties raden jullie aan na een storing?" Om deze vraag te beantwoorden, vereist etikettering _geen toekomstige horizon die moet worden gekozen,_ omdat het model geen mislukking in de toekomst voorspelt. Het is gewoon het voorspellen van de meest waarschijnlijke oorzaak _zodra de mislukking al is gebeurd_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Trainings-, validatie- en testmethoden voor voorspellend onderhoud
Het [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) biedt een volledige dekking van de modeltrein-test-valideringscyclus. In deze sectie worden aspecten besproken die uniek zijn voor PdM.

### <a name="cross-validation"></a>Kruisvalidatie
Het doel van [cross validatie](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) is het definiëren van een gegevensset om het model in de trainingsfase te "testen". Deze gegevensset wordt de _validatieset genoemd._ Deze techniek helpt problemen zoals _overfitting_ te beperken en geeft inzicht in hoe het model zal generaliseren tot een onafhankelijke gegevensset. Dat wil zeggen, een onbekende dataset, die van een echt probleem zou kunnen zijn. De trainings- en testroutine voor PdM moet rekening houden met de tijdsvariërende aspecten om beter te generaliseren over ongeziene toekomstige gegevens.

Veel machine learning-algoritmen zijn afhankelijk van een aantal hyperparameters die de prestaties van het model aanzienlijk kunnen veranderen. De optimale waarden van deze hyperparameters worden niet automatisch berekend bij het trainen van het model. Ze moeten worden gespecificeerd door de gegevenswetenschapper. Er zijn verschillende manieren om goede waarden van hyperparameters te vinden.

De meest voorkomende is _k-fold cross-validatie_ die de voorbeelden willekeurig splitst in _k_ plooien. Voer voor elke set hyperparameterswaarden het leeralgoritme _k-tijden_ uit. Gebruik bij elke iteratie de voorbeelden in de huidige vouw als validatieset en de rest van de voorbeelden als trainingsset. Train het algoritme over trainingsvoorbeelden en bereken de prestatiestatistieken op validatievoorbeelden. Aan het einde van deze lus bereken je het gemiddelde van _k_ performance metrics. Kies voor elke set hyperparameterwaarden de waarden met de beste gemiddelde prestaties. De taak van het kiezen van hyperparameters is vaak experimenteel van aard.

In PdM-problemen worden gegevens geregistreerd als een tijdreeks gebeurtenissen die afkomstig zijn uit verschillende gegevensbronnen. Deze records kunnen worden besteld op basis van het tijdstip van etikettering. Als de gegevensset _dus willekeurig_ wordt opgesplitst in trainings- en validatieset, _kunnen sommige trainingsvoorbeelden later op tijd zijn dan enkele validatievoorbeelden._ Toekomstige prestaties van hyperparameterwaarden worden geschat op basis van bepaalde gegevens die zijn aangekomen _voordat het_ model werd getraind. Deze schattingen kunnen te optimistisch zijn, vooral als de tijdreeks niet stationair is en in de loop van de tijd evolueert. Als gevolg hiervan kunnen de gekozen hyperparameterwaarden suboptimaal zijn.

De aanbevolen manier is om de voorbeelden op te splitsen in training en validatie ingesteld in een _tijd-afhankelijke_ manier, waar alle validatie voorbeelden zijn later op tijd dan alle training voorbeelden. Train voor elke set hyperparameterwaarden het algoritme over de trainingsgegevensset. Meet de prestaties van het model over dezelfde validatieset. Kies hyperparameterwaarden die de beste prestaties weergeven. Hyperparameterwaarden die worden gekozen door trein/validatie split resulteren in betere toekomstige modelprestaties dan met de waarden die willekeurig worden gekozen door cross-validatie.

Het uiteindelijke model kan worden gegenereerd door het trainen van een leeralgoritme over volledige trainingsgegevens met behulp van de beste hyperparameterwaarden.

### <a name="testing-for-model-performance"></a>Testen op modelprestaties
Zodra een model is gebouwd, is een schatting van de toekomstige prestaties op nieuwe gegevens vereist. Een goede schatting is de prestatiestatistiek van hyperparameterwaarden die zijn berekend over de validatieset of een gemiddelde prestatiestatistiek berekend op basis van crossvalidatie. Deze schattingen zijn vaak te optimistisch. Het bedrijf heeft mogelijk een aantal aanvullende richtlijnen over hoe ze het model willen testen.

De aanbevolen manier voor PdM is om de voorbeelden op een _tijdsafhankelijke_ manier op te splitsen in trainings-, validatie- en testgegevenssets. Alle testvoorbeelden moeten later op tijd zijn dan alle trainings- en validatievoorbeelden. Na de splitsing genereert u het model en meet u de prestaties zoals eerder beschreven.

Wanneer tijdreeksen stil staan en gemakkelijk te voorspellen zijn, genereren zowel willekeurige als tijdsafhankelijke benaderingen vergelijkbare schattingen van toekomstige prestaties. Maar wanneer tijd-series niet-stationair zijn, en/of moeilijk te voorspellen, zal de tijdsafhankelijke benadering meer realistische schattingen van toekomstige prestaties genereren.

### <a name="time-dependent-split"></a>Tijdsafhankelijke splitsing
In deze sectie worden aanbevolen procedures beschreven voor het implementeren van tijdsafhankelijke splitsing. Hieronder wordt een tijdsafhankelijke tweerichtingsverdeling tussen training en testsets beschreven.

Ga uit van een stroom van tijdstempelgebeurtenissen, zoals metingen van verschillende sensoren. Definieer functies en labels van trainings- en testvoorbeelden in tijdframes die meerdere gebeurtenissen bevatten. Maak bijvoorbeeld voor binaire classificatie functies op basis van gebeurtenissen uit het verleden en maak labels op basis van toekomstige gebeurtenissen binnen 'X'-eenheden in de toekomst (zie de secties over [functie-engineering-](#feature-engineering) en modelleringstechnieken). Zo komt het labelingtijdsbestek van een voorbeeld later dan het tijdsbestek van de functies.

Voor tijdafhankelijke split, kies een _training cutoff tijd T<sub>c</sub> _ om een model te trainen, met hyperparameters afgestemd met behulp van historische gegevens tot T<sub>c</sub>. Als u wilt voorkomen dat toekomstige labels die verder gaan dan T<sub>c</sub> in de trainingsgegevens lekken, kiest u de meest recente tijd om trainingsvoorbeelden te labelen als X-eenheden vóór T<sub>c</sub>. In het voorbeeld in figuur 7 vertegenwoordigt elk vierkant een record in de gegevensset waarin functies en labels worden berekend zoals hierboven beschreven. De figuur toont de records die moeten gaan in de opleiding en testen sets voor X = 2 en W = 3:

![Afbeelding 7. Tijdsafhankelijke splitsing voor binaire classificatie](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Afbeelding 7. Tijdsafhankelijke splitsing voor binaire classificatie

De groene vierkanten vertegenwoordigen records die behoren tot de tijdeenheden die kunnen worden gebruikt voor training. Elk trainingsvoorbeeld wordt gegenereerd door rekening te houden met de afgelopen drie perioden voor het genereren van functies en twee toekomstige perioden voor het labelen vóór T<sub>c</sub>. Wanneer een deel van de twee toekomstige perioden verder is dan T<sub>c</sub>, sluit u dat voorbeeld uit van de trainingsgegevensset omdat er geen zichtbaarheid wordt aangenomen buiten T<sub>c</sub>.

De zwarte vierkanten vertegenwoordigen de records van de uiteindelijke gelabelde gegevensset die niet mag worden gebruikt in de trainingsgegevensset, gezien de bovenstaande beperking. Deze records zullen ook niet worden gebruikt in het testen van gegevens, omdat ze vóór T<sub>c</sub>. Bovendien zijn hun labelingstermijnen gedeeltelijk afhankelijk van het trainingstijdsbestek, wat niet ideaal is. Trainings- en testgegevens moeten afzonderlijke etiketteringstermijnen hebben om het lekken van labelinformatie te voorkomen.

De techniek die tot nu toe wordt besproken, zorgt voor overlap tussen trainings- en testvoorbeelden met tijdstempels in de buurt van T<sub>c.</sub> Een oplossing voor een grotere scheiding is het uitsluiten van voorbeelden die zich binnen W-tijdeenheden van T<sub>c</sub> bevinden uit de testset. Maar zo'n agressieve splitsing hangt af van voldoende beschikbaarheid van gegevens.

Regressiemodellen die worden gebruikt voor het voorspellen van RUL worden ernstiger beïnvloed door het lekprobleem. Het gebruik van de random split methode leidt tot extreme over-fitting. Voor regressieproblemen moet de splitsing zodanig zijn dat de records die behoren tot activa met fouten voordat T<sub>c</sub> in de trainingsset gaat. Records van activa die fouten hebben na de cutoff gaan in de testset.

Een andere best practice voor het splitsen van gegevens voor training en testen is het gebruik van een split by asset ID. De splitsing moet zodanig zijn dat geen van de middelen die in de trainingsset worden gebruikt, wordt gebruikt bij het testen van de modelprestaties. Met behulp van deze aanpak heeft een model een betere kans om realistischere resultaten te leveren met nieuwe activa.

### <a name="handling-imbalanced-data"></a>Omgaan met onevenwichtige gegevens
Als er bij classificatieproblemen meer voorbeelden van één klasse zijn dan van de andere klasse, wordt gezegd dat de gegevensset _onevenwichtig_is . Idealiter hebben voldoende vertegenwoordigers van elke klasse in de trainingsgegevens de voorkeur om differentiatie tussen verschillende klassen mogelijk te maken. Als één klasse minder dan 10% van de gegevens bedraagt, worden de gegevens geacht onevenwichtig te zijn. De ondervertegenwoordigde klasse wordt een _minderheidsklasse_genoemd.

Veel PdM-problemen worden geconfronteerd met dergelijke onevenwichtige gegevenssets, waarbij de ene klasse ernstig ondervertegenwoordigd is in vergelijking met de andere klasse of klassen. In sommige situaties kan de minderheidsklasse slechts 0,001% van de totale gegevenspunten uitmaken. Klasse onbalans is niet uniek voor PdM. Andere domeinen waar storingen en afwijkingen zeldzame gebeurtenissen zijn, worden geconfronteerd met een soortgelijk probleem, bijvoorbeeld, fraudedetectie en netwerkinbraak. Deze mislukkingen vormen de voorbeelden van de minderheidsklasse.

Met klasse onbalans in gegevens, prestaties van de meeste standaard leeralgoritmen is aangetast, omdat ze gericht zijn op het minimaliseren van de totale foutpercentage. Voor een gegevensset met 99% negatieve en 1% positieve voorbeelden kan worden aangetoond dat een model 99% nauwkeurigheid heeft door alle instanties als negatief te labelen. Maar het model zal alle positieve voorbeelden verkeerd classificeren; dus zelfs als de nauwkeurigheid hoog is, is het algoritme niet nuttig. Bijgevolg zijn conventionele evaluatiestatistieken, zoals _de algehele nauwkeurigheid van het foutenpercentage,_ onvoldoende voor onevenwichtig leren. Wanneer u geconfronteerd wordt met onevenwichtige gegevenssets, worden andere statistieken gebruikt voor modelevaluatie:
- Precisie
- Terughalen
- F1 scores
- Kostenaangepast ROC (bedieningseigenschappen van de ontvanger)

Zie [modelevaluatie voor](#model-evaluation)meer informatie over deze statistieken.

Echter, er zijn een aantal methoden die helpen bij het oplossen van klasse onbalans probleem. De twee belangrijkste zijn _bemonsteringstechnieken_ en _kostengevoelig leren._

#### <a name="sampling-methods"></a>Bemonsteringsmethoden
Onevenwichtige leertijd omvat het gebruik van bemonsteringsmethoden om de trainingsgegevensset te wijzigen in een evenwichtige gegevensset. Bemonsteringsmethoden mogen niet op de testset worden toegepast. Hoewel er verschillende bemonsteringstechnieken zijn, zijn de meeste rechttoe-rechtaan-technieken _willekeurige oversampling_ en _onder bemonstering_.

_Willekeurige oversampling_ omvat het selecteren van een willekeurige steekproef uit minderheidsklasse, het repliceren van deze voorbeelden en het toevoegen ervan aan trainingsgegevensset. Bijgevolg wordt het aantal voorbeelden in minderheidsklasse verhoogd en uiteindelijk het aantal voorbeelden van verschillende klassen in evenwicht gebracht. Een nadeel van oversampling is dat meerdere exemplaren van bepaalde voorbeelden ervoor kunnen zorgen dat de classificatie te specifiek wordt, wat leidt tot overfitting. Het model kan een hoge trainingsnauwkeurigheid vertonen, maar de prestaties op ongeziene testgegevens kunnen suboptimaal zijn.

Omgekeerd, _random onder bemonstering_ is het selecteren van een willekeurige steekproef uit een meerderheid klasse en het verwijderen van deze voorbeelden uit de opleiding gegevens set. Het verwijderen van voorbeelden uit meerderheidsklasse kan er echter voor zorgen dat de classificatie belangrijke concepten met betrekking tot de meerderheidsklasse mist. _Hybride bemonstering_ waarbij minderheidsklasse overbemonsterd is en de meerderheidsklasse tegelijkertijd onderbemonsterd is, is een andere haalbare benadering.

Er zijn veel geavanceerde bemonsteringstechnieken. De gekozen techniek is afhankelijk van de data-eigenschappen en resultaten van iteratieve experimenten door de data scientist.

#### <a name="cost-sensitive-learning"></a>Kostengevoelig leren
In PdM zijn mislukkingen die de minderheidsklasse vormen van meer belang dan normale voorbeelden. De focus ligt dus vooral op de prestaties van het algoritme op storingen. Het onjuist voorspellen van een positieve klasse als negatieve klasse kan meer kosten dan vice versa. Deze situatie wordt algemeen aangeduid als ongelijk verlies of asymmetrische kosten van verkeerd classificeren van elementen in verschillende klassen. De ideale classificatie moet een hoge voorspellingnauwkeurigheid leveren ten opzichte van de minderheidsklasse, zonder in te leveren op de nauwkeurigheid voor de meerderheidsklasse.

Er zijn meerdere manieren om dit evenwicht te bereiken. Als u het probleem van ongelijk verlies wilt beperken, wijst u hoge kosten toe aan een verkeerde classificatie van de minderheidsklasse en probeert u de totale kosten te minimaliseren. Algoritmen zoals _SVMs (Support Vector Machines)_ nemen deze methode inherent, door dat de kosten van positieve en negatieve voorbeelden worden opgegeven tijdens de training. Op dezelfde manier, het stimuleren van methoden, zoals _gestimuleerd beslissing bomen_ tonen meestal goede prestaties met onevenwichtige gegevens.

## <a name="model-evaluation"></a>Modelevaluatie
Verkeerde classificatie is een belangrijk probleem voor PdM-scenario's waarbij de kosten van vals alarm voor het bedrijf hoog zijn. Een beslissing om een vliegtuig aan de grond te houden op basis van een onjuiste voorspelling van motoruitval kan bijvoorbeeld schema's en reisplannen verstoren. Het offline halen van een machine vanaf een lopende band kan leiden tot inkomstenderving. Modelevaluatie met de juiste prestatiestatistieken ten opzichte van nieuwe testgegevens is dus van cruciaal belang.

Typische prestatiestatistieken die worden gebruikt om PdM-modellen te evalueren, worden hieronder besproken:

- [Nauwkeurigheid](https://en.wikipedia.org/wiki/Accuracy_and_precision) is de meest populaire statistiek die wordt gebruikt voor het beschrijven van de prestaties van een classificatie. Maar nauwkeurigheid is gevoelig voor gegevensdistributies en is een ineffectieve maatregel voor scenario's met onevenwichtige gegevenssets. In plaats daarvan worden andere statistieken gebruikt. Tools zoals [verwarring matrix](https://en.wikipedia.org/wiki/Confusion_matrix) worden gebruikt om te berekenen en reden over de nauwkeurigheid van het model.
- [Precisie](https://en.wikipedia.org/wiki/Precision_and_recall) van PdM-modellen heeft betrekking op de snelheid van vals alarm. Lagere precisie van het model komt over het algemeen overeen met een hoger percentage valse alarmen.
- [Het terugroeppercentage](https://en.wikipedia.org/wiki/Precision_and_recall) geeft aan hoeveel van de fouten in de testset correct door het model zijn geïdentificeerd. Hogere terugroeppercentages betekenen dat het model succesvol is in het identificeren van de ware fouten.
- [F1 score](https://en.wikipedia.org/wiki/F1_score) is het harmonische gemiddelde van precisie en terugroepen, met zijn waarde variërend tussen 0 (slechtste) tot 1 (beste).

Voor binaire classificatie,
- [Receiver operating curves (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is ook een populaire statistiek. In ROC-curven worden modelprestaties geïnterpreteerd op basis van één vast bedieningspunt op het ROC.
- Maar voor PdM problemen, _decile tabellen_ en _lift grafieken_ zijn meer informatief. Ze richten zich alleen op de positieve klasse (fouten) en geven een complexer beeld van de algoritmprestaties dan ROC-curven.
  - _Decile-tabellen_ worden gemaakt met behulp van testvoorbeelden in een aflopende volgorde van de kans op mislukkingen. De bestelde monsters worden vervolgens gegroepeerd in deciles (10% van de monsters met de hoogste waarschijnlijkheid, dan 20%, 30%, enzovoort). De verhouding (true positive rate)/(random baseline) voor elke decile helpt bij het schatten van de algoritmeprestaties bij elke decile. De willekeurige basislijn neemt waarden 0.1, 0.2, enzovoort over.
  - [Lift grafieken](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) plot de decile true positive rate versus random true positive rate voor alle deciles. De eerste deciles zijn meestal de focus van de resultaten, omdat ze de grootste winsten laten zien. Eerste deciles kunnen ook worden gezien als representatief voor "in gevaar", wanneer gebruikt voor PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modeloperationalisatie voor voorspellend onderhoud

Het voordeel van de data science oefening wordt pas gerealiseerd wanneer het getrainde model operationeel wordt gemaakt. Dat wil zeggen, het model moet worden ingezet in de bedrijfssystemen om voorspellingen te doen op basis van nieuwe, voorheen onzichtbare, gegevens.  De nieuwe gegevens moeten op twee manieren exact in overeenstemming zijn met de _modelhandtekening_ van het getrainde model:
- alle functies moeten aanwezig zijn in elke logische instantie (bijvoorbeeld een rij in een tabel) van de nieuwe gegevens.
- de nieuwe gegevens moeten worden voorverwerkt en elk van de functies die zijn ontworpen, moeten op precies dezelfde manier worden ontworpen als de trainingsgegevens.

Het bovenstaande proces wordt op vele manieren vermeld in de academische en industriële literatuur. Maar alle volgende uitspraken betekenen hetzelfde:
- _Nieuwe gegevens scoren_ met behulp van het model
- _Het model toepassen_ op nieuwe gegevens
- Het model _operationaliseren_
- _Het_ model implementeren
- _Het model uitvoeren_ op basis van nieuwe gegevens

Zoals eerder vermeld, model operationalisering voor PdM is anders dan zijn collega's. Scenario's met anomaliedetectie en foutdetectie implementeren doorgaans _online scores_ (ook wel _realtime scoren_genoemd). Hier _scoort_ het model elke binnenkomende record en geeft het een voorspelling. Voor anomaliedetectie is de voorspelling een indicatie dat er een anomalie is opgetreden (Voorbeeld: SVM van één klasse). Voor foutdetectie is dit het type of de klasse van de fout.

In tegenstelling, PdM gaat _batch scoren_. Om aan de modelhandtekening te voldoen, moeten de functies in de nieuwe gegevens op dezelfde manier worden ontworpen als de trainingsgegevens. Voor de grote gegevenssets die typisch zijn voor nieuwe gegevens, worden functies in de loop van de tijd samengevoegd en in batch gescoord. Batchscores worden meestal uitgevoerd in gedistribueerde systemen zoals [Spark](https://spark.apache.org/) of [Azure Batch.](https://docs.microsoft.com/azure/batch/batch-api-basics) Er zijn een paar alternatieven - beide suboptimaal:
- Streaming data engines ondersteunen aggregatie over vensters in het geheugen. Zo zou men kunnen debatteren dat zij online het noteren steunen. Maar deze systemen zijn geschikt voor dichte gegevens in smalle vensters van tijd, of schaarse elementen over bredere vensters. Ze kunnen niet goed schalen voor de dichte gegevens over bredere tijdvensters, zoals te zien in PdM-scenario's.
- Als batchscores niet beschikbaar zijn, is de oplossing om online scores aan te passen om nieuwe gegevens in kleine batches tegelijk te verwerken.

## <a name="solution-templates-for-predictive-maintenance"></a>Oplossingssjablonen voor voorspellend onderhoud

Het laatste gedeelte van deze handleiding bevat een lijst met pdm-oplossingssjablonen, zelfstudies en experimenten die zijn geïmplementeerd in Azure. Deze PdM-toepassingen kunnen in sommige gevallen binnen enkele gevallen binnen enkele gevallen worden geïmplementeerd in een Azure-abonnement. Ze kunnen worden gebruikt als proof-of-concept demo's, sandboxes om te experimenteren met alternatieven, of versnellers voor de werkelijke productie-implementaties. Deze sjablonen bevinden zich in de [Azure AI Gallery](https://gallery.azure.ai) of [Azure GitHub.](https://github.com/Azure) Deze verschillende voorbeelden worden in de loop van de tijd in deze oplossingssjabloon gerold.

| # | Titel | Beschrijving |
|--:|:------|-------------|
| 2 | [Sjabloon voor Azure Predictive Maintenance-oplossing](https://github.com/Azure/AI-PredictiveMaintenance) | Een sjabloon voor open-sourceoplossingen die Azure ML-modellering en een complete Azure-infrastructuur demonstreren die voorspellende onderhoudsscenario's kan ondersteunen in het kader van IoT-bewaking op afstand. |
| 3 | [Deep Learning voor predictief onderhoud](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Notebook met een demo-oplossing voor het gebruik van LSTM-netwerken (Long Short-Term Memory) (een klasse van terugkerende neurale netwerken) voor voorspellend onderhoud, met een [blogpost in dit voorbeeld](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Handleiding voor voorspellende onderhoudsmodellering in R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | PdM-modelleringshandleiding met scripts in R.|
| 5 | [Azure Voorspellend onderhoud voor lucht- en ruimtevaart](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Een van de eerste PdM-oplossingssjablonen op basis van Azure ML v1.0 voor vliegtuigonderhoud. Deze gids is voortgekomen uit dit project. |
| 6 | [Azure AI Toolkit voor IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI in de IoT Edge met TensorFlow; toolkit-pakketten deep learning-modellen in Azure IoT Edge-compatibele Docker-containers en deze modellen blootstellen als REST-API's.
| 7 | [Azure IoT-voorspellend onderhoud](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS - Vooraf geconfigureerde oplossing. Vliegtuigonderhoud PdM-sjabloon met IoT Suite. [Een ander document](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) en [walkthrough](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) met betrekking tot hetzelfde project. |
| 8 | [Sjabloon voorspellend onderhoud met SQL Server R-services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo van de resterende gebruiksduur scenario op basis van R-diensten. |
| 9 | [Handleiding voor voorspellende onderhoudsmodellering](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | De gegevensset gegevensset voor vliegtuigonderhoud is ontworpen met Behulp van R met [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) en [gegevenssets](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) en [Azure-notitieblok](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) en [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) in AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Trainingsbronnen voor voorspellend onderhoud

Microsoft Azure biedt leerpaden voor de fundamentele concepten achter PdM-technieken, naast inhoud en training over algemene AI-concepten en -praktijken.

| Trainingsbron  | Beschikbaarheid |
|:-------------------|--------------|
| [Leerpad voor PdM met bomen en willekeurig bos](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Leerpad voor PdM met deep learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [AI-ontwikkelaar op Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Microsoft AI-school](https://aischool.microsoft.com/learning-paths) | Public |
| [Azure AI Leren van GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Public |
| [Microsoft AI YouTube-webinars](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Microsoft AI-show](https://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partners |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partners |

Daarnaast worden gratis MOOCS (massive open online courses) op AI online aangeboden door academische instellingen zoals Stanford en MIT, en andere onderwijsbedrijven.
