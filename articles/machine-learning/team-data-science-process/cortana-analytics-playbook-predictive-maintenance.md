---
title: Azure AI-handleiding voor oplossingen voor Voorspellend onderhoud - Team Data Science Process
description: Een uitgebreide beschrijving van de wetenschap dat wordt gebruikt door oplossingen voor Voorspellend onderhoud in meerdere verticale industrieën.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/11/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: ec87146c721222702073eae067a259aa9848d0f7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048988"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-handleiding voor oplossingen voor Voorspellend onderhoud

## <a name="summary"></a>Samenvatting

Voorspeld onderhoud (**PdM**) is een populaire toepassing van predictive analytics waarmee u kunt bedrijven in diverse branches hoge asset gebruik bereiken en besparingen op operationele kosten. Deze handleiding combineert de zakelijke en analytische richtlijnen en aanbevolen procedures voor het met succes ontwikkelen en implementeren van PdM oplossingen met behulp van de [Microsoft Azure AI-platform](https://azure.microsoft.com/overview/ai-platform) technologie.

Om te beginnen, introduceert deze handleiding branchespecifieke scenario's en het proces van in aanmerking komende deze scenario's voor PdM. De vereiste gegevens en modeltechnieken PdM oplossingen bouwen worden ook geleverd. De belangrijkste inhoud van de handleiding is op het data science process - met inbegrip van de procedure voor het voorbereiden van gegevens, feature-engineering, model maken en model uitoefening. Als aanvulling op de belangrijkste concepten, vindt deze handleiding u een set van sjablonen voor oplossingen om u te helpen sneller PdM toepassingen te ontwikkelen. De handleiding verwijst ook naar nuttige cursussen voor de beginner voor meer informatie over de AI achter de data science. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science handleiding overzicht en doel doelgroep
De eerste helft van deze handleiding worden beschreven in de meeste zakelijke problemen, de voordelen van de implementatie van PdM om deze problemen te verhelpen en geeft een lijst van enkele veelvoorkomende use cases. Besluitvormers (BDM) profiteren van deze inhoud. De tweede helft wordt uitgelegd van de data science achter PdM en geeft een lijst van PdM oplossingen gebouwd met behulp van de principes die worden beschreven in deze handleiding. Het biedt ook leertrajecten en verwijzingen naar trainingsmateriaal. Technische besluitvormers (TDMs) handig deze inhoud.

| Beginnen met... | Als u... |
|:---------------|:---------------|
| [Bedrijfsscenario voor predictief onderhoud](#business-case-for-predictive-maintenance) |een besluitvormer (Besluitvormer) wilt u minder uitvaltijd en operationele kosten en gebruik van apparatuur te verbeteren |
| [Wetenschappelijke gegevens voor voorspeld onderhoud](#data-science-for-predictive-maintenance) |een technisch beleidsmaker (TDM) PdM technologieën evalueren om te begrijpen van de unieke gegevensverwerking en AI-vereisten voor predictief onderhoud |
| [Sjablonen voor oplossingen voor Voorspellend onderhoud](#solution-templates-for-predictive-maintenance)|een softwarearchitect of wilt u snel een demo's of een proof-of-concept een paar AI-ontwikkelaar |
| [Cursussen voor voorspeld onderhoud](#training-resources-for-predictive-maintenance) | een of meer van de bovenstaande, en voor meer informatie over de basisconcepten achter de wetenschappelijke gegevens, hulpmiddelen en technieken.

### <a name="prerequisite-knowledge"></a>Vereiste voorkennis
De inhoud Besluitvormer hoofdverwerkingsknooppunt verwacht niet dat de lezer is voorgaande data science kennis hebben. Basiskennis van statistieken en datatechnologie is voor de inhoud TDM handig. Kennis van Azure Data en AI-services, Python, R, XML en JSON wordt aanbevolen. AI-technieken worden geïmplementeerd in Python / R-pakketten. Sjablonen voor oplossingen worden geïmplementeerd met behulp van Azure-services, ontwikkelprogramma's en SDK's.

## <a name="business-case-for-predictive-maintenance"></a>Bedrijfsscenario voor predictief onderhoud

Bedrijven vereisen dat essentiële apparaten op topcapaciteit en met een piekbezetting werken om het rendement op hun kapitaalinvesteringen te realiseren. Dit kan gaan om vliegtuigmotoren, turbines, liften of industriële koelers, die miljoenen kosten, maar ook om alledaagse apparaten zoals kopieerapparaten, koffiezetters of waterkoelers.
- Standaard de meeste bedrijven afhankelijk zijn van _corrigerende onderhoud_, waarbij de onderdelen worden vervangen als en wanneer ze mislukken. Correctief onderhoud zorgt ervoor dat onderdelen volledig worden verbruikt (en de levensduur van de onderdelen niet wordt verspild), maar dit kost het bedrijf wel downtime, arbeid en ongepland onderhoud (buiten bedrijfsuren of op lastige locaties).
- Op het volgende niveau, bedrijven praktijk _preventief onderhoud_, waar ze de nuttige levensduur voor een onderdeel bepalen, en onderhouden of vervang het voordat u een fout. Preventief onderhoud voorkomt ongepland onderhoud en onherstelbare fouten. Maar de hoge kosten van geplande downtime te voorzichtige gebruik van het onderdeel vóór de volledige levensduur van het gebruik en arbeid nog steeds blijven.
- Het doel van _Voorspellend onderhoud_ is het optimaliseren van de balans tussen corrigerende en preventief onderhoud, doordat _just-in-time_ vervanging van onderdelen. Bij deze benadering worden alleen die onderdelen vervangen die bijna versleten zijn. Door de levensduur van onderdelen te verlengen (in vergelijking met preventief onderhoud) en de kosten voor ongepland onderhoud en ongeplande en arbeid te verminderen (ten opzichten van correctief onderhoud) kunnen bedrijven kosten besparen en concurrentievoordelen realiseren.

## <a name="business-problems-in-pdm"></a>Zakelijke problemen in PdM
Bedrijven geconfronteerd hoog operationele risico vanwege onverwachte fouten en beperkt inzicht in de hoofdoorzaak van problemen in complexe systemen. Enkele van de belangrijkste zakelijke vragen zijn:

- Detecteer afwijkingen in apparatuur- of systeemprestaties of functionaliteit.
- Voorspel of een activum in de nabije toekomst mislukken.
- Maak een schatting van de resterende levensduur van een asset.
- Identificeer de belangrijkste oorzaken van uitval van een asset.
- Identificeren welke onderhoudsacties moeten worden uitgevoerd, door wanneer voor een asset.

Typische doel instructies uit PdM zijn:

- Operationele risico van essentiële uitrusting van essentiële worden verminderd.
- Rendement van assets vergroten door fouten te voorspellen voordat ze optreden.
- Kosten voor onderhoud door in te schakelen van just-in-time-onderhoudsbewerkingen beheren.
- Klant afslijting verlagen, verbetering van de installatiekopie van het merk en verloren verkoop.
- Lagere kosten van de inventaris door voorraadniveaus verlagen met het voorspellen van het punt opnieuw wilt rangschikken.
- Ontdek patronen die zijn verbonden met verschillende problemen met het onderhoud.
- KPI's (key performance indicators) opgeven, zoals de gezondheid van scores voor asset voorwaarden.
- Maak een schatting van resterende levensduur van activa.
- Beste tijdige onderhoudswerkzaamheden.
- Just-in-time-inventarisatie inschakelen door te schatten orderdatums voor vervanging van onderdelen.

Deze instructies doel zijn het startpunt voor:

- _gegevenswetenschappers_ te analyseren en oplossen van specifieke voorspellende problemen.
- _cloud-architecten en ontwikkelaars_ voor het samenstellen van een end-to-end-oplossing.

## <a name="qualifying-problems-for-predictive-maintenance"></a>In aanmerking komende problemen voor predictief onderhoud
Het is belangrijk om te benadrukken dat niet alle use-cases of bedrijfsproblemen effectief kunnen worden opgelost door PdM. Er zijn drie belangrijke in aanmerking komend criteria die moeten worden overwogen bij de selectie van het probleem:

- Het probleem moet worden voorspeld van aard; dat wil zeggen, moet er een doel of een resultaat om te voorspellen. Het probleem hebt ook een duidelijk pad van de actie om te voorkomen dat fouten wanneer ze worden gedetecteerd.
- Het probleem moet een record van de operationele geschiedenis van de apparatuur die bevat _goed of slecht resultaten_. De set acties die worden uitgevoerd om onjuiste resultaten moeten ook beschikbaar als onderdeel van deze records. Foutenrapporten, logboeken voor onderhoud van prestatievermindering, herstellen en vervangen Logboeken ook belangrijk zijn. Reparaties die wordt uitgevoerd voor het verbeteren en vervangende records zijn ook nuttig.
- De opgenomen geschiedenis moet worden weergegeven in _relevante_ gegevens _voldoende_ genoeg hoogwaardige ter ondersteuning van de use-case. Zie voor meer informatie over gegevens relevantie en toereikend waren [gegevensvereisten voor predictief onderhoud](#data-requirements-for-predictive-maintenance).
- Domein-experts die een duidelijk beeld van het probleem krijgt hebt ten slotte van het bedrijf. Ze moeten rekening houden met het interne processen en procedures om u te helpen bij de analist begrijpen en interpreteren van de gegevens. Ze moeten ook worden de benodigde wijzigingen aanbrengen in bestaande bedrijfsprocessen om te verzamelen van de juiste gegevens voor de problemen, indien nodig.

## <a name="sample-pdm-use-cases"></a>Voorbeeld PdM use-cases
Deze sectie richt zich op een verzameling van PdM van use cases in diverse branches, zoals ruimtevaart, hulpprogramma's en transport. Elke sectie begint met een zakelijk probleem, en beschrijft de voordelen van PdM, de relevante gegevens rond het zakelijke probleem en tot slot de voordelen van een oplossing PdM.

| Zakelijk probleem | Voordelen van PdM |
|:-----------------|-------------------|
|**Luchtvaart**      |                   |
|_Flight vertraging en annuleringen_ vanwege problemen met de mechanische. Fouten die in de tijd kunnen niet worden hersteld kunnen ertoe leiden dat vluchten te annuleren en planning en bewerkingen worden onderbroken. |PdM oplossingen kunnen voorspellen de kans dat een vliegtuig worden vertraagd of geannuleerd vanwege mechanische storingen.|
|_Vliegtuig engine delen fout_: vliegtuig engine onderdeel vervangingen zijn een van de meest voorkomende onderhoudstaken uitvoeren in de luchtvaartsector. Onderhoud-oplossingen vereisen een zorgvuldig beheer van het onderdeel voorraad, levering en planning|Is het kunnen voor het verzamelen van intelligence op onderdeel betrouwbaarheid leidt tot een aanzienlijke vermindering van investeringskosten.|
|**Financiën** |                         |
|_ATM-fout_ is een veelvoorkomend probleem in het bankwezen. Het probleem hier is voor het rapporteren van de kans dat een transactie ATM geld intrekking wordt onderbroken vanwege een fout papier zit vast of een deel in de dispenser geld. Gebaseerd op voorspellingen van transactiefouten optreden, kunnen geldautomaten worden onderhouden proactief om te voorkomen dat er fouten optreden.| In plaats van dat de machine halverwege een transactie mislukt, wordt het wenselijk alternatief is naar de machine service weigeren op basis van de voorspelling-programma.|
|**Energie** |                          |
|_Wind turbine-storingen_: wind turbines zijn de belangrijkste energie bronnen in omgevings bewuste landen/regio's, en omvatten hoge kapitaal kosten. Een belangrijk onderdeel voor wind maar is de motor generator. de fout wordt weergegeven de turbine ongeschikt. Het is ook zeer kostbaar zijn om op te lossen.|KPI's te voorspellen, zoals MTTF (gemiddelde tijd bij een storing) kunt u de energiebedrijven turbine fouten voorkomen, en controleer of de minimale downtime. Fout kansen wordt in kennis technici om te controleren, maar die waarschijnlijk zal mislukken snel en gepland onderhoud op basis van tijd regelingen. Voorspellende modellen bieden inzicht in verschillende factoren die bijdragen aan de fout, waarmee technici beter begrip van dat de belangrijkste oorzaken van problemen.|
|_Circuitonderbreker fouten_: distributie van elektriciteit aan huizen en bedrijven power regels te allen tijde om te waarborgen van energie-levering operationele zijn vereist. Stroomonderbrekers te beperken of te voorkomen dat schade aan power regels tijdens overbelasting of schadelijke weersomstandigheden. Het zakelijke probleem hier is Circuitonderbreker fouten kunt voorspellen.| PdM oplossingen helpen herstellen kosten te verlagen en de levensduur van de uitrusting zoals stroomonderbrekers verhogen. Ze helpen de kwaliteit van het netwerk power verbeteren door te verminderen onverwachte fouten en serviceonderbrekingen.|
|**Vervoer en logistiek** |    |
|_Lift klep fouten_: grote lift bedrijven bieden een volledige stack-service voor miljoenen functionele liften over de hele wereld. Lift-veiligheid, betrouwbaarheid en beschikbaarheid zijn de belangrijkste zorgen voor hun klanten. Deze bedrijven volgen deze en verschillende andere kenmerken via sensoren, zodat ze kunnen met corrigerende en preventief onderhoud. In een lift, is het probleem van de meest prominente klant lift deuren defect. Het zakelijke probleem wordt in dit geval een voorspellende knowledge base-toepassing die voorspelt dat de mogelijke oorzaken van de deur van fouten.| Liften zijn kapitaalinvesteringen voor mogelijk een 20-30-jaar geldig. Zodat elke potentiële verkoop uiterst concurrerende; verwachtingen voor de service en ondersteuning zijn daarom hoog. Voorspeld onderhoud kan deze bedrijven voorzien van een voordeel ten opzichte van hun concurrenten in hun product en serviceaanbiedingen.|
|_Fouten steeds_: steeds fouten-account voor de helft van alle ontsporingen trainen en miljarden aan de globale spoor-industrie kosten. Wheel fouten ook toe leiden dat de rails te verslechteren, soms veroorzaakt de spoor voortijdig verbreken. Spoor onderbrekingen leiden tot onherstelbare gebeurtenissen, zoals ontsporingen. Om te voorkomen dat deze instanties, spoorwegen de prestaties van wheels controleren en deze op een manier die preventieve vervangen. Het zakelijke probleem hier is de voorspelling van wheel-fouten.| Predictief onderhoud van wheels helpt met just-in-time wheels te vervangen |
|_Fouten bij de deur van de trein train_: een primaire reden voor vertragingen bij bewerkingen van de trein klep van fouten van de trein auto's is. Het zakelijke probleem hier is train klep van fouten kunt voorspellen.|Vroege bewust te maken van een storing klep, of het aantal dagen tot een fout opgetreden in de deur helpt het bedrijf optimaliseren trein van klep van gepland onderhoud.|

De volgende sectie haalt de details van hoe u kunt profiteren van de PdM voordelen die hierboven wordt beschreven.

## <a name="data-science-for-predictive-maintenance"></a>Wetenschappelijke gegevens voor voorspeld onderhoud

Deze sectie bevat algemene richtlijnen van data science principes en praktijken voor PdM. Het is bedoeld ter ondersteuning van een TDM, oplossingsarchitect, of een ontwikkelaar informatie over de vereisten en proces voor het ontwikkelen van AI-toepassingen end-to-end voor PdM. U vindt in deze sectie, samen met een overzicht van de demo's en proof of concept sjablonen die worden vermeld in [sjablonen voor oplossingen voor Voorspellend onderhoud](#solution-templates-for-predictive-maintenance). U kunt vervolgens deze beginselen en aanbevolen procedures voor het implementeren van uw oplossing PdM in Azure.

> [!NOTE]
> Deze handleiding is niet bedoeld om te leren van de Data Science-lezer. Vindt u enkele nuttige bronnen voor meer informatie in de sectie voor [trainingsbronnen voor predictief onderhoud](#training-resources-for-predictive-maintenance). De [oplossingssjablonen](#solution-templates-for-predictive-maintenance) die worden vermeld in de handleiding voor enkele van de volgende manieren AI voor specifieke problemen met het PdM laten zien.

## <a name="data-requirements-for-predictive-maintenance"></a>Gegevensvereisten voor predictief onderhoud

Het succes van elk learning, is afhankelijk van (a) de kwaliteit van wat is wordt geleerd, en (b) de mogelijkheid van de cursist. Voorspellende modellen patronen van historische gegevens leert en toekomstige resultaten voorspellen met bepaalde kans op basis van deze patronen waargenomen. Van een model voorspellende nauwkeurigheid is afhankelijk van de relevantie, toereikend waren en kwaliteit van de trainings- en testset gegevens. De nieuwe gegevens die 'beoordeeld' met behulp van dit model moet hebben de dezelfde functies en het schema als de gegevens voor training en testen. De functie-kenmerken (type, dichtheid, distributie, enzovoort) van nieuwe gegevens moeten overeenkomen met die van de trainings- en testset gegevenssets. De focus van deze sectie is van de behoeften van dergelijke gegevens.

### <a name="relevant-data"></a>Relevante gegevens

Eerst moet de gegevens worden _relevant zijn voor het probleem_. Houd rekening met de _fout steeds_ use-case besproken hoger - de trainingsgegevens functies met betrekking tot de wheel-bewerkingen moet bevatten. Als het probleem is om te voorspellen van het mislukken van de _steun die krijgt system_, het trainen van gegevens is uitgebreid met de verschillende onderdelen voor het systeem steun die krijgt. Het eerste geval is bedoeld voor een specifiek onderdeel dat is gericht op het tweede geval het uitvallen van een grotere subsysteem. De algemene aanbeveling is voorspelling om systemen te ontwerpen over specifieke onderdelen in plaats van grotere subsystemen, sinds de laatste hebben meer gegevens verspreide wordt. De domeinexpert (Zie [in aanmerking komende problemen voor predictief onderhoud](#qualifying-problems-for-predictive-maintenance)) kunnen helpen bij het selecteren van de meest relevante subsets met gegevens voor de analyse. De relevante gegevensbronnen worden besproken in meer detail in [gegevens voor te bereiden voor predictief onderhoud](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Onvoldoende gegevens
Twee vragen vaak worden gesteld met betrekking tot de gegevens van geschiedenis van fout: (1) 'hoeveel mislukte gebeurtenissen zijn vereist voor een model te trainen?' (2) "hoeveel records worden als" voldoende beschouwd "?" Er zijn geen definitieve antwoorden, maar alleen regels voor de duim. Meer het aantal gebeurtenissen die betrekking hebben, betere voor (1), het model. Voor (2), en het exacte aantal mislukte gebeurtenissen is afhankelijk van de gegevens en de context van het probleem wordt opgelost. Maar op de andere kant hebben als een virtuele machine te vaak mislukt vervolgens het bedrijf wordt vervangen, waarmee exemplaren van de fout wordt beperkt. Hier nogmaals, is de richtlijnen van het domein deskundige belangrijk. Er zijn echter methoden te kunnen omgaan met de uitgifte van _zeldzaam gebeurtenissen_. Ze worden besproken in de sectie [imbalanced gegevens verwerken](#handling-imbalanced-data).

### <a name="quality-data"></a>Kwaliteitsgegevens
De kwaliteit van de gegevens is van essentieel belang - elk kenmerk voorspelde moet _nauwkeurige_ in combinatie met de waarde van de doelvariabele. Kwaliteit van de gegevens is een goed studied gebied in statistieken en gegevensbeheer, en dus buiten bereik voor deze handleiding.

> [!NOTE]
> Er zijn verschillende resources en enterprise-producten om kwaliteitsgegevens te geven. Hieronder vindt u een voorbeeld van verwijzingen naar:
> - Dasu, T, Johnson, T., experimentele gegevensanalyse en het opschonen van gegevens, Wiley, 2003.
> - [Verkennende Data-analyse, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, "j", kwantitatieve gegevens opschonen voor grote Databases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, inleiding tot het opschonen van gegevens met R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Gegevens voor te bereiden voor predictief onderhoud

### <a name="data-sources"></a>Gegevensbronnen

De relevante gegevensbronnen voor predictief onderhoud bevatten, maar zijn niet beperkt tot:
- Foutgeschiedenis
- Geschiedenis van onderhoudsmodus/herstellen
- De operationele voorwaarden machine
- De metagegevens van apparaten

#### <a name="failure-history"></a>Foutgeschiedenis
Mislukte gebeurtenissen zijn zeldzaam binnen PdM toepassingen. Bij het bouwen van voorspellende modellen, moet het algoritme moet echter voor meer informatie over van een onderdeel van de normale operationele patroon, evenals de patronen van de fout. De trainingsgegevens moet dus een voldoende aantal voorbeelden van beide categorieën bevatten. Onderhoud-records en -onderdelen vervanging geschiedenis zijn goede bronnen voor het vinden van gebeurtenissen die betrekking hebben. Met behulp van enige kennis van het domein, worden afwijkingen in de trainingsgegevens ook gedefinieerd als fouten.

#### <a name="maintenancerepair-history"></a>Geschiedenis van onderhoudsmodus/herstellen
De onderhouds geschiedenis van een Asset bevat details over de vervangen onderdelen, de herstel activiteiten die zijn uitgevoerd, enzovoort. Met deze gebeurtenissen worden patronen voor degradatie vastgelegd. Afwezigheid van deze belangrijke informatie in de trainingsgegevens kan leiden tot misleidend modelresultaten. Foutgeschiedenis worden ook gevonden in de geschiedenis van onderhoud als speciale foutcodes of orderdatums voor delen. Extra gegevensbronnen die invloed hebben op patronen fout moeten worden onderzocht en geleverd door de domein-experts.

#### <a name="machine-operating-conditions"></a>De operationele voorwaarden machine
Sensor op basis van (of andere) streaming-gegevens van de apparatuur in de bewerking is een belangrijke gegevensbron. Een sleutel veronderstelling in PdM is dat vermindert de gezondheidsstatus van een virtuele machine na verloop van tijd tijdens het dagelijkse beheer. De gegevens wordt bevatten van de tijd heen variëren functies die dit patroon meebrengt, en eventuele afwijkingen die leidt tot degradatie vastleggen verwacht. De tijdelijke aspect van de gegevens is vereist voor het algoritme voor meer informatie over de fout en zonder fouten patronen na verloop van tijd. Op basis van deze gegevenspunten, aanleren de algoritme om te voorspellen hoe veel extra eenheden van de tijd een virtuele machine kunt blijven werken voordat deze is mislukt.

#### <a name="static-feature-data"></a>Gegevens van de statische functie
Statische functies zijn metagegevens over de apparatuur. Voorbeelden zijn de fabrikant, model, productiedatum, start de service, de locatie van het systeem en andere technische specificaties.

Voorbeelden van relevante gegevens voor de [voorbeeld PdM use-cases](#sample-pdm-use-cases) zijn Zie tabel hieronder:

| Toepassing | Voorbeelden van relevante gegevens |
|:---------|---------------------------|
|_Vertraging en annuleringen_ | Informatie over de route in de vorm van vertragingen van vluchten zijden Flight en pagina-Logboeken. Vlucht gegevens omvatten routerings gegevens, zoals vertrek/aankomst datum, tijd, lucht haven, layovers, enzovoort. Het pagina logboek bevat een reeks fout-en onderhouds codes die zijn vastgelegd door het personeel van het terrein onderhoud.|
|_Vliegtuig motoren delen ontstaan_ | Gegevens verzameld van sensoren in het vliegtuig met informatie over de toestand van de verschillende onderdelen. Onderhoudsrecords te identificeren wanneer component fouten zijn opgetreden en wanneer ze zijn vervangen door.|
|_ATM-fout_ | Sensorwaarden, serverlogs, voor elke transactie (storten geld/selectievakje) en aflevering geld. Informatie over de tussenruimte meting tussen notities, de notitie breedte, de aankomst afstand van de notitie, kenmerken, enzovoort. Onderhouds records met fout codes, herstel gegevens, laatste keer dat de kassa dispenser is gevuld.|
|_Wind turbine fout_ | Sens oren bewaken de turbine-omstandigheden, zoals Tempe ratuur, wind richting, gegenereerde stroom, Generator snelheid, enzovoort. Gegevens worden verzameld uit meerdere wind turbines van Wind-Farms die zich in verschillende regio's bevinden. Elke turbine heeft meestal meerdere sensorwaarden, serverlogs, metingen relay op een vast tijdsinterval.|
|_Circuitonderbreker fouten_ | Onderhoud-logboeken die corrigerende preventieve en systematische acties opnemen. Operationele gegevens met automatische en handmatige opdrachten verzonden naar stroomonderbrekers zoals voor bewerkingen voor openen en sluiten. Meta gegevens van apparaten, zoals de datum van productie, locatie, model enz. Specificaties van circuit onderbreker, zoals spannings niveaus, geolocatie en omgevings omstandigheden.|
|_Lift klep van fouten_| Lift-metagegevens zoals type lift, productiedatum, onderhoud frequentie, type gebouw, enzovoort. Operationele informatie zoals het aantal klep cycli, gemiddelde klep sluiten tijd. Foutgeschiedenis met oorzaken.|
|_Wheel-fouten_ | Sensor gegevens die de wiel versnelling, het remmen, de afstand, de snelheid enz. Statische informatie over wielen zoals fabrikant, productie datum. Foutgegevens afgeleid van de database voor klantorders onderdeel die volgorde datums en aantallen bijhouden.|
|_Fouten bij de deur van de trein train_ | De deur openen en sluiten tijden, andere operationele gegevens, zoals de huidige toestand van de trein deuren. Statische gegevens omvat activa-id, de tijd en de voorwaarde waarde kolommen.|

### <a name="data-types"></a>Gegevenstypen
De bovenstaande gegevensbronnen worden gegeven, zijn de twee belangrijkste gegevenstypen waargenomen in PdM domein:

- _Tijdelijke gegevens_: operationele telemetrie, machine voorwaarden, typen werk, prioriteitscodes die op het moment van opname tijdstempels hebben. Fout, onderhoud/reparatie en gebruiksgeschiedenis heeft ook tijdstempels die zijn gekoppeld aan elke gebeurtenis.
- _Statische gegevens_: Machine functies en onderdelen van de operator in het algemeen statisch zijn omdat ze de technische specificaties van machines of operator kenmerken beschreven. Als deze functies na verloop van tijd wijzigt kunnen, moeten ze ook die zijn gekoppeld aan deze tijdstempels hebben.

Variabelen voor de voorspelde en het doel moeten worden voorverwerkt/omgezet in [numerieke, categorische en andere gegevenstypen](https://www.statsdirect.com/help/basics/measurement_scales.htm) , afhankelijk van de algoritme die wordt gebruikt.

### <a name="data-preprocessing"></a>Gegevens voorverwerken
Als een vereiste voor het _functie-engineering_, voorbereiden van de gegevens uit verschillende gegevensstromen voor het opstellen van een schema van waaruit het is gemakkelijk om het ontwikkelen van functies. Visualiseer de gegevens eerst als een tabel met records. Elke rij in de tabel vertegenwoordigt een exemplaar van de training, en de kolommen vertegenwoordigen _voorspelde_ functies (ook wel onafhankelijk kenmerken of variabelen). Organiseer de gegevens zodanig dat de laatste kolom(men) is de _doel_ (afhankelijke variabele). Voor elk exemplaar training toewijzen een _label_ als de waarde van deze kolom.

Voor tijdelijke gegevens, de duur van de sensorgegevens in tijdseenheden te verdelen. Elke record moet deel uitmaken van een tijdseenheid voor een asset _en moet bieden verschillende informatie_. Eenheden worden gedefinieerd op basis van de behoeften van uw bedrijf in veelvouden van seconden, minuten, uren, dagen, maanden, enzovoort. De tijdseenheid _hoeft niet te zijn hetzelfde als de frequentie van verzamelen van gegevens_. Als de frequentie hoog is, kan de gegevens die een belangrijk verschil van de ene eenheid naar de andere niet weer. Bijvoorbeeld, wordt ervan uitgegaan dat de omgevingstemperatuur elke tien seconden is verzameld. Met behulp van datzelfde interval voor trainingsgegevens alleen wordt vergroot het aantal voorbeelden zonder op te geven aanvullende informatie. In dit geval worden een strategie voor een betere gemiddelde de gegevens die meer dan 10 minuten, of een uur op basis van de zakelijke rechtvaardiging gebruikt.

Voor statische gegevens
- _Onderhoudsrecords_: onbewerkte onderhoud gegevens heeft een activa-id en tijdstempel met informatie over het van onderhoudsactiviteiten die zijn uitgevoerd op een bepaald tijdstip. Transformeren onderhoudswerkzaamheden in _categorische_ kolommen, waarbij elke categorie descriptor unieke aan een specifieke onderhoudsactie toegewezen. Het schema voor onderhoudsrecords omvat activa-id, de tijd en de bewerking voor siteonderhoud.

- _Fout records_: fouten of oorzaken voor het mislukken kunnen worden vastgelegd als specifieke foutcodes of gebeurtenissen die betrekking hebben gedefinieerd door specifieke bedrijfsomgeving. In gevallen waar de apparatuur meerdere foutcodes heeft, kunt het domein deskundige identificeren die relevant voor de doelvariabele zijn. De resterende foutcodes of voorwaarden gebruiken om samen te _voorspelde_ functies die met deze fouten samenhangen. Het schema voor mislukte records omvat activa-id, tijd, mislukt of de reden van fout - indien beschikbaar.

- _Metagegevens voor de machine en de operator_: de machine en de operator gegevens samenvoegen in één schema om te koppelen van een asset met de operator, samen met hun respectieve kenmerken. Het schema voor de voorwaarden van de machine omvat activa-id, asset functies, operator-id en functies van de operator.

Andere gegevens voorverwerkingsstappen bevatten _verwerken van de ontbrekende waarden_ en _normalisering_ van kenmerkwaarden. Een gedetailleerde beschrijving valt buiten het bereik van deze handleiding: Zie de volgende sectie voor sommige nuttig verwijzingen.

Met de bovenstaande voorverwerkte gegevensbronnen aanwezig is, de laatste transformatie voordat feature-engineering, is de bovenstaande tabellen op basis van de activa-id en tijdstempel moeten worden samengevoegd. De resulterende tabel moet null-waarden voor de kolom fout wanneer machine zich in de normale werking. Deze null-waarden kunnen worden toegeschreven door een indicator voor normale werking. Deze fout-kolom gebruiken om te maken _labels voor het voorspellende model_. Zie voor meer informatie de sectie over [technieken voor predictief onderhoud model](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Functie-engineering
Feature-engineering is de eerste stap voorafgaand aan het modelleren van de gegevens. De rol in het data science process [wordt hier beschreven](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). Een _functie_ is een voorspellende kenmerk voor de model - zoals temperatuur, druk te verlichten, trillingen, enzovoort. Voor PdM omvat feature-engineering het om een virtuele machine de status van historische gegevens verzameld over de duur van een aanzienlijke te abstraheren. In die zin is het verschilt van de peers zoals externe controle, detectie van afwijkingen en detecteren van storingen. 

### <a name="time-windows"></a>Tijdvensters
Externe controle houdt de gebeurtenissen die zich als van voordoen rapportage _tijdstippen_. Anomaly detection modellen evalueren de binnenkomende gegevensstromen (score) van de gegevens op de vlag anomalieën vanaf punten in de tijd. Foutdetectie classificeert worden specifieke typen wanneer deze zich punten in de tijd voordoen, mislukte aanmeldingen. Daarentegen PdM omvat het voorspellen van fouten een _toekomstige periode_, op basis van functies die het gedrag van de machine via vertegenwoordigen _historische periode_. Voor PdM zijn de gegevens van de functie van afzonderlijke punten van de tijd te veel lawaai worden voorspeld. Daarom moet de gegevens voor elke functie zijn _smoothened_ door samenvoeging van gegevenspunten binnen bepaalde tijdvensters.

### <a name="lag-features"></a>De functies lag
De zakelijke vereisten definiëren hoe ver moet het model in de toekomst te voorspellen. Op zijn beurt deze duur zorgt 'hoe ver terug het-model heeft om te controleren of' definiëren om deze voorspellingen te doen. Deze 'Wat zijn' periode heet de _lag_, en functies die zijn ontworpen gedurende deze periode lag worden aangeroepen _vertraging functies_. Deze sectie wordt besproken lag-functies die kunnen worden samengesteld uit gegevensbronnen met tijdstempels en de functie voor het maken van statische gegevensbronnen. Vertraging van onderdelen zijn meestal _numerieke_ van aard.

> [!IMPORTANT]
> Grootte van het venster wordt bepaald via experimenten en met behulp van een deskundige domein moet worden voltooid. De dezelfde voorbehoud bevat voor de selectie en de definitie van de functies lag, hun aggregaties en het type van windows.

#### <a name="rolling-aggregates"></a>Rolling statistische functies
Voor elke record van een asset, een rolling venster van de grootte "letter W" gekozen als het aantal eenheden van de tijd voor het berekenen van de statistische functies. Lag functies vervolgens worden berekend met behulp van de perioden W _vóór de datum_ van dat record. De blauwe regels worden weergegeven in afbeelding 1, sensorwaarden voor een asset met elke eenheid van de tijd geregistreerd. Ze geven aan een oplopende gemiddelde van waarden van de functie over een venster van de grootte W = 3. Het oplopende gemiddelde wordt berekend voor alle records met een tijdstempel in het bereik t<sub>1</sub> (in oranje) op t<sub>2</sub> (in het groen). De waarde voor W is doorgaans in minuten of uren, afhankelijk van de aard van de gegevens. Maar voor bepaalde problemen met een grote W (bijvoorbeeld 12 maanden) verzamelen over de volledige geschiedenis van een asset tot het moment van de record.

![Afbeelding 1. Statistische functies rolling](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) afbeelding 1. Rolling statistische functies

Voorbeelden van het huidige statistische functies gedurende een bepaalde periode zijn aantal, gemiddelde, CUMESUM (cumulatieve som) metingen, min/max-waarden. Bovendien worden afwijking, standaarddeviatie en aantal van de uitschieters dan N standaardafwijkingen vaak gebruikt. Voorbeelden van statistische functies die kunnen worden toegepast voor de [gebruiksvoorbeelden](#sample-pdm-use-cases) in deze handleiding worden hieronder vermeld. 
- _Vertraging Flight_: aantal foutcodes in de afgelopen dag/week.
- Fout in het onderdeel van het _lucht vaartuig_: Rolling gemiddelde, standaard afwijking en som over de afgelopen dag, week, enzovoort. Deze metriek moet samen met de expert voor het zakelijke domein worden bepaald.
- _ATM-fouten_: rolling middelen, gemiddelde, bereik, standaardafwijkingen, aantal uitbijters meer dan drie standaardafwijkingen, boven en ondergrenzen CUMESUM.
- _Fouten bij de deur van de trein train_: telt het aantal gebeurtenissen gedurende de afgelopen dag, week, twee weken enzovoort.
- _Circuitonderbreker fouten_: fout het aantal in afgelopen week, jaar, drie jaar enzovoort.

Er is een andere handige techniek in PdM trend wijzigingen, pieken en verandert met algoritmen die in de gegevens afwijkingen vast te leggen.

#### <a name="tumbling-aggregates"></a>Tumblingvenstertrigger statistische functies
Voor elke gelabelde record van een Asset wordt een venster met een grootte van _w-<sub>k</sub>_  gedefinieerd, waarbij _k_ het aantal vensters van grootte _w_is. Aggregaties worden vervolgens gemaakt over _k_ _tumblingvenstertriggers Windows_ _w-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, W-<sub>1</sub>_  voor de peri Oden vóór de tijds tempel van een record. _k_ mag bestaan uit een klein aantal om vast te leggen op korte termijn effecten of een groot aantal om vast te leggen op de lange termijn verslechtering van de patronen. (Zie afbeelding 2).

![Afbeelding 2. Tumblingvenstertrigger statistische functies](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) afbeelding 2. Statistische functies tumbling

Functies bijvoorbeeld vertragingstijd voor de wind maar use-case kan worden gemaakt met W = 1 en k = 3. Ze impliceren de vertraging voor elk van de afgelopen drie maanden met behulp van de boven- en uitbijters.

### <a name="static-features"></a>Statische functies

Technische specificaties van de uitrusting zoals datum van de productie, modelnummer, locatie, zijn enkele voorbeelden van statische functies. Ze worden behandeld als _categorische_ variabelen voor modellen. Enkele voorbeelden voor de Circuitonderbreker use-case zijn spanning, huidige, energiecapaciteit, transformatortype en voeding. Voor wheel-fouten is het type band wheels (gelegeerd vs staal) een voorbeeld.

De gegevens voorbereiden pogingen tot nu toe is behandeld moeten leiden tot de gegevens wordt geordend zoals hieronder wordt weergegeven. Training, test en validatie van gegevens, moet deze logische schema (in dit voorbeeld laat zien in eenheden van dagen) hebben.

| Activa-id | Time | \<functie kolommen > | Label |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

De laatste stap in feature-engineering is de **labels** van de doelvariabele. Dit proces is afhankelijk van de techniek modelleren. Op zijn beurt afhankelijk de techniek modellering is van het zakelijke probleem en de aard van de beschikbare gegevens. Labels wordt in de volgende sectie besproken.

> [!IMPORTANT]
> Gegevens voor te bereiden en feature-engineering zijn net zo belangrijk als het modelleren van technieken voor geslaagde PdM oplossingen aankomen. De domeinexpert en de arts in moeten investeren veel tijd in die binnenkomen in de juiste functies en de gegevens voor het model. Hieronder vindt u een voorbeeld van een groot aantal boeken over feature-engineering:
> - Pyle, D. gegevens voor te bereiden voor gegevensanalyse (de Morgan Kaufmann-serie in Data Management Systems), dat 1999
> - Zheng, A., Casari, a Feature-Engineering voor Machine Learning: principes en technieken voor Data Scientists, O'Reilly, 2018.
> - Dong, G. Liu, H. (editors), functie techniek voor Machine Learning en gegevens analyse (Chapman & hal/CRC data mining en Knowledge Discovery Series), CRC pers, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Technieken voor predictief onderhoud model maken

In deze sectie worden de belangrijkste modeltechnieken PdM problemen, samen met hun specifieke label bouw methoden beschreven. U ziet dat een techniek die één modellen kan worden gebruikt in verschillende branches. De techniek modellering is gekoppeld aan het probleem van data science, in plaats van de context van de gegevens bij de hand.

> [!IMPORTANT]
> De keuze van de labels voor de mislukte aanvragen en de strategie voor labelen  
> moet in overleg met het domein deskundige worden bepaald.

### <a name="binary-classification"></a>Binaire classificatie
Binaire classificatie wordt gebruikt om te voors _pellen dat een stuk van apparatuur in een toekomstige periode uitvalt_ , met de naam van de _toekomstige periode X_. X wordt bepaald door het bedrijfs probleem en de gegevens die bij de hand worden geleverd, in overleg met het domein expert. Een aantal voorbeelden:
- _Minimale levertijd_ vereist voor het vervangen van onderdelen, onderhoud resources implementeren, uitvoeren van onderhoud om te voorkomen dat een probleem dat is dat waarschijnlijk ook voorkomt in die periode.
- _minimum aantal gebeurtenissen_ dat kan gebeuren voordat er een probleem optreedt.

Bij deze techniek worden twee soorten training voorbeelden aangeduid. Voorbeeld van een positieve _wat aangeeft dat het een storing_, met het label = 1. Een negatieve voorbeeld, dat aangeeft dat de normale bewerkingen, met het label = 0. De doelvariabele, en die dus de labelwaarden, _categorische_. Het model moet elke nieuwe voorbeeld als waarschijnlijk zal mislukken of normaal werken via de volgende X tijdseenheden bepalen.

#### <a name="label-construction-for-binary-classification"></a>Label-constructie voor binaire classificatie
De vraag hier is: "Wat is de kans dat de asset niet wordt in de volgende X-tijdseenheid?" Beantwoord deze vraag, label X records vóór de fout van een asset als "over naar de mislukt" (label = 1), en alle andere records als zijnde "normale" labelen (label = 0). (Zie afbeelding 3).

![Afbeelding 3. Labels voor binaire classificatie](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) afbeelding 3. Labels voor binaire classificatie

Voorbeelden van de strategie voor enkele van de use cases labels worden hieronder vermeld.
- _Flight vertragingen_: X 1 dag kan worden gekozen voor het voorspellen van vertragingen in de volgende 24 uur. Vervolgens alle vluchten die zich binnen 24 uur vóór de fouten zijn gelabeld als 1.
- _ATM-geld afzien fouten_: een doel is mogelijk om te bepalen van de kans op fouten van een transactie in het volgende uur. In dat geval worden alle transacties die hebben plaatsgevonden binnen het afgelopen uur van de fout zijn gelabeld als 1. Om te voorspellen kans op mislukte via de volgende N valuta worden vrijgesteld, notities alle opmerkingen bij de aangeboden in de laatste N toelichting van een storing gelabeld als 1.
- _Circuitonderbreker fouten_: het doel kan worden te voorspellen van de volgende fout van de Circuitonderbreker-opdracht. In dat geval is X gekozen als één toekomstige opdracht.
- _Fouten voor de deur van de trein_: X kan worden gekozen als twee dagen.
- _Liquidatie turbine fouten_: X kan worden gekozen als twee maanden.

### <a name="regression-for-predictive-maintenance"></a>Regressie voor predictief onderhoud
Regressiemodellen worden gebruikt om _compute van de resterende levensduur (RUL) van een asset_. Resterende Levensduur wordt gedefinieerd als de hoeveelheid tijd die een asset operationeel is voordat de volgende fout zich voordoet. Elk voorbeeld training is een record die deel uitmaakt van een tijdeenheid _nY_ voor een asset, waar _n_ het veelvoud is. Het model moet berekenen van de resterende bruikbare Levensduur van elke nieuwe voorbeeld als een _doorlopend nummer_. Dit nummer geeft aan dat de periode resterend voor hervatting van de fout.

#### <a name="label-construction-for-regression"></a>Label-constructie voor regressie
De vraag hier is: "Wat is de resterende levensduur (RUL) van de apparatuur?" Bereken het label om te worden van het aantal eenheden van de resterende voorafgaand aan de volgende fout tijd voor elke record vóór de fout. Labels zijn bij deze methode hoeft continue variabelen. (Zie afbeelding 4)

![Afbeelding 4. Labels voor regressie](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) afbeelding 4. Labels voor regressie

Voor regressie, wordt labeling gedaan met een verwijzing naar een punt van mislukken. De berekening is niet mogelijk zonder te weten hoe lang de asset is hebt voordat er een fout. Dus worden daarentegen binaire indeling, activa zonder fouten in de gegevens niet gebruikt voor modellen. Dit probleem beste worden verwerkt met een andere statistische techniek [Overlevingsanalyse](https://en.wikipedia.org/wiki/Survival_analysis). Maar potentiële problemen kunnen ontstaan wanneer deze techniek wordt toegepast op PdM van use cases die betrekking hebben op de tijd heen variëren gegevens met regelmatige intervallen. Zie voor meer informatie over Overlevingsanalyse [dit één pager](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Meerdere klasse-classificatie voor voorspeld onderhoud
Meerdere klasse classificatietechnieken kunnen worden gebruikt in PdM oplossingen voor twee scenario's:
- Voorspellen _twee toekomstige resultaten_: het eerste resultaat is _een tijdsbereik voor fouten in_ voor een asset. De asset is toegewezen aan een van meerdere mogelijke perioden. Het tweede resultaat is de kans op fouten in een toekomstige periode vanwege _een van de meerdere hoofdmap zorgt ervoor dat_. Deze voorspelling kan de bemanning onderhoud om te controleren op symptomen en plan onderhoudsschema's.
- Voorspellen _het meest waarschijnlijke oorzaak_ van een opgegeven fout. Dit resultaat raadt aan om de juiste set onderhoudsacties om op te lossen van een storing. Een gerangschikte lijst van de belangrijkste oorzaken en aanbevolen reparaties kunt technici prioriteren van hun acties voor herstellen na een storing.

#### <a name="label-construction-for-multi-class-classification"></a>Label-constructie voor ROC-classificatie
De vraag hier is: "Wat is de kans dat een asset niet wordt in de volgende _nZ_ tijdseenheden waar _n_ is het aantal perioden?" Als u wilt deze vraag te beantwoorden, een label nZ records vóór de fout van een asset met behulp van buckets van tijd (3Z, 2Z, Z). Label alle andere records "normale" (label = 0). Bij deze methode hoeft de doelvariabele bevat _categorische_ waarden. (Zie afbeelding 5).

![Afbeelding 5. Fout tijd voorspelling labels voor multiklassen classificatie](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) afbeelding 5. Labels voor classificatie ROC voor fout tijd voorspelling

De vraag hier is: "Wat is de kans dat de asset niet wordt in de volgende X-tijdseenheid vanwege hoofdmap oorzaak/probleem _P<sub>ik</sub>_ ?" waar _ik_ is het aantal mogelijke oorzaken. Beantwoord deze vraag, label X records vóór de fout van een asset als ' over mislukken vanwege hoofdoorzaak _P<sub>ik</sub>_ ' (label = _P<sub>ik</sub>_ ). Label alle records als zijnde "normale" (label = 0). In deze methode zijn labels ook categorische (Zie afbeelding 6).

![Afbeelding 6. Hoofdoorzaak voorspelling labels voor multiklassen classificatie](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) afbeelding 6. Labels voor classificatie ROC voor hoofdmap oorzaak voorspelling

Het model wordt de kans op mislukte vanwege elk toegewezen _P<sub>ik</sub>_  en de kans op fouten. Deze kansen kunnen worden besteld door magnitude om toe te staan voorspelling van de problemen die zijn ondergebracht in de toekomst.

De vraag hier is: "welke onderhoudsacties raden u aan om na een storing?" Deze vragen te beantwoorden labels _is niet vereist voor een toekomstige horizon moeten worden verzameld_, omdat de fout is niet in de toekomst voorspellen van het model. Het is net voorspellen van het meest waarschijnlijke oorzaak _nadat de fout is opgetreden_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Training, validatie en het testen van de methoden voor predictief onderhoud
De [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) biedt een volledige dekking van de cyclus model trainen test valideren. Deze sectie wordt besproken aspecten die uniek is voor PdM.

### <a name="cross-validation"></a>Kruisvalidatie
Het doel van [kruisvalidatie](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) is voor het definiëren van een gegevensset aan 'test' het model in de fase training. Deze gegevensset heet de _validatie set_. Deze techniek helpt limiet problemen zoals _overfitting_ en biedt inzicht in hoe het model met een onafhankelijke gegevensset wordt generaliseren. Dat wil zeggen, de gegevens in een onbekende ingesteld die kan worden van een echt probleem. De routine training en testen voor PdM moet rekening gehouden met de verschillende aspecten van de tijd beter generaliseren op verborgen gegevens uit de toekomst.

Veel machine learning-algoritmen, is afhankelijk van een aantal hyperparameters die de modelprestaties aanzienlijk kunt wijzigen. De optimale waarden van deze hyperparameters worden niet automatisch worden berekend wanneer het model te trainen. Deze moeten worden opgegeven door de gegevenswetenschapper. Er zijn verschillende manieren uit het vinden van goede waarden van hyperparameters.

De meest voorkomende reden is _k-Vouw kruisvalidatie_ die in de voorbeelden willekeurig in splitst _k_ vouwen. Voor elke set van hyperparameters waarden, voert u het learning-algoritme _k_ tijden. Bij elke iteratie gebruikt u de voorbeelden in de huidige Vouw als een set validatie en de rest van de voorbeelden als een trainingsset. Het algoritme trainen via training voorbeelden en de maatstaven voor prestaties compute via validatie voorbeelden. Aan het einde van deze lus, berekent het gemiddelde van _k_ maatstaven voor prestaties. Kiezen welke waarvoor de optimale gemiddelde prestaties voor elke set van hyperparameter waarden. De taak van het kiezen van hyperparameters is vaak experimentele karakter.

Problemen met PdM, worden gegevens geregistreerd als een tijdreeks van gebeurtenissen die afkomstig uit verschillende gegevensbronnen zijn. Deze records kunnen worden geordend op basis van de tijd van labels. Dus als de gegevensset is gesplitst _willekeurig_ in trainings- en validatie is ingesteld, _sommige van de training-voorbeelden zijn mogelijk niet in de tijd later zijn dan enkele voorbeelden van validatie_. Toekomstige prestaties van hyperparameter waarden worden geschat op basis van aantal gegevens dat is ontvangen _voordat_ model is getraind. Deze schattingen mogelijk overmatig optimistische, met name als de time series niet een stilstaande is en zich verder na verloop van tijd ontwikkelt. Als gevolg hiervan kunnen de waarden van de gekozen hyperparameter suboptimale zijn.

De aanbevolen manier is om te splitsen in de voorbeelden in trainings- en validatie instellen in een _tijdsafhankelijke_ wijze, waar alle validatie van de voorbeelden verderop in de tijd dan alle training voorbeelden zijn. Voor elke set hyperparameter waarden, de algoritme te trainen via de set trainingsgegevens. Meten van het model prestaties ten opzichte van de dezelfde set voor validatie. Kies hyperparameter waarden waarin de beste prestaties. Hyperparameter waarden door train/validatie split resulteren in betere toekomstige modelprestaties dan door de waarden willekeurig gekozen door kruisvalidatie gekozen.

Het uiteindelijke model kan worden gegenereerd door een leeralgoritme training over de hele trainingsgegevens met behulp van de beste hyperparameter-waarden.

### <a name="testing-for-model-performance"></a>Voor de modelprestaties testen
Als een model is gemaakt, is een schatting van de toekomstige prestaties op nieuwe gegevens vereist. Een goede schatting maken van de metrische gegevens voor prestaties van hyperparameter waarden berekend ten opzichte van de validatie-set, of een gemiddelde prestaties metrische gegevens, berekend vanaf kruisvalidatie. Deze schattingen zijn vaak overmatig optimistische. Het bedrijf wellicht vaak enkele aanvullende richtlijnen over hoe ze graag willen testen van het model.

De aanbevolen manier voor PdM is om te splitsen in de voorbeelden in training, validatie en testgegevens Hiermee stelt u in een _tijdsafhankelijke_ manier. Alle voorbeelden van de test moet later in de tijd dan alle de trainings- en validatie voorbeelden. Na de splitsing, het model is gegenereerd en meten van de prestaties, zoals eerder beschreven.

Wanneer tijdreeksen zijn stilstaande en gemakkelijk te voorspellen, genereert willekeurige en tijdsafhankelijke benaderingen vergelijkbaar schattingen van toekomstige prestaties. Maar wanneer tijdreeksen zijn niet-stilstand en/of moeilijk te voorspellen, de aanpak van afhankelijk zijn van tijd meer realistische schattingen van toekomstige prestaties wordt gegenereerd.

### <a name="time-dependent-split"></a>Tijdafhankelijke splitsen
Deze sectie wordt beschreven aanbevolen procedures voor het implementeren van afhankelijk zijn van tijd splitsen. Een wederzijdse splitsing tijdsafhankelijke tussen trainings- en testsets wordt hieronder beschreven.

Wordt ervan uitgegaan dat u een stroom gebeurtenissen, zoals de metingen van diverse sensoren voorzien van een tijdstempel. Functies en labels van training en test voorbeelden via meteen met meerdere gebeurtenissen gedefinieerd. U kunt bijvoorbeeld voor binaire classificatie functies maken op basis van gebeurtenissen in het verleden en labels maken op basis van toekomstige gebeurtenissen binnen ' X ' tijds eenheden in de toekomst (Zie de secties over [functie techniek](#feature-engineering) en model technieken). Het labelen tijdsbestek van een voorbeeld komt dus hoger is dan de periode van de functies ervan.

Voor tijdsafhankelijke splitsing, kies een _training breekpunt tijd T<sub>c</sub>_  op waarop u wilt een model te trainen met hyperparameters afgestemd op met behulp van historische gegevens maximaal T<sub>c</sub>. Om te voorkomen dat lekken van toekomstige labels die buiten T<sub>c</sub> in de trainingsgegevens, kiest u de meest recente tijd voor het label training voorbeelden moet X eenheden voordat T<sub>c</sub>. Elke vierkant vertegenwoordigt in het voorbeeld in afbeelding 7 wordt weergegeven, een record in de gegevensset waarop functies en labels worden berekend, zoals hierboven beschreven. De afbeelding ziet u de records die moeten worden geplaatst in trainings- en testsets voor X = 2 en W = 3:

![Afbeelding 7. Tijdafhankelijke splitsing voor binaire classificatie](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) afbeelding 7. Tijdafhankelijke splitsing voor binaire classificatie

De groene kwadraten vertegenwoordigen records die behoren tot de tijdseenheden die kunnen worden gebruikt voor de training. Elk voorbeeld training wordt gegenereerd op basis van de afgelopen drie perioden voor het genereren van de functie en twee toekomstige perioden voor labelen voordat T<sub>c</sub>. Wanneer een deel van de twee toekomstige perioden is meer dan T<sub>c</sub>, dat voorbeeld uitsluiten van de set trainingsgegevens, omdat er geen zichtbaarheid aangenomen meer dan T dat wordt<sub>c</sub>.

De zwarte kwadraten vertegenwoordigen de records van de laatste gelabelde gegevensset die niet kunnen worden gebruikt in de set trainingsgegevens, krijgt de bovengenoemde beperking. Deze records niet ook worden gebruikt in de testfase van gegevens, omdat deze zich vóór T<sub>c</sub>. Bovendien afhankelijk hun labelen meteen gedeeltelijk van het tijdsbestek training die is niet ideaal. Trainings- en testset gegevens moet afzonderlijke labelen meteen om te voorkomen dat gegevens worden gelekt label hebben.

De techniek die tot nu toe worden besproken, kunt u overlapping tussen de trainings- en testdoeleinden voorbeelden met tijdstempels in de buurt T<sub>c</sub>. Een oplossing voor een groter scheiding is het uitsluiten van voorbeelden die zich binnen de tijdseenheden W van T<sub>c</sub> instellen van de test. Maar een agressief splitsen, is afhankelijk van beschikbaarheid van voldoende gegevens.

Regressiemodellen gebruikt voor het voorspellen van de resterende Levensduur meer ondervindt van het Lekkageprobleem. Met behulp van de methode willekeurige split leidt tot een extreme sprake van redundante aanpassing van labels. Voor problemen met de regressie, de splitsing moet zodanig zijn dat de records die behoren tot activa met mislukte aanmeldingen voordat T<sub>c</sub> gaat u naar de trainingsset. Records van activa met fouten na de afsluitdatum gaat u in de testset.

Een andere aanbevolen procedure voor het splitsen van gegevens voor trainings- en testdoeleinden is het gebruik van een splitsing van asset-ID. De splitsing moet zodanig zijn dat geen van de activa die worden gebruikt in de trainingsset worden gebruikt voor het testen van de modelprestaties. Met deze benadering is een model voor een grotere kans realistischer resultaten voorzien van nieuwe activa.

### <a name="handling-imbalanced-data"></a>Imbalanced gegevens verwerken
Problemen met classificatie, dat als er meer voorbeelden van een klasse dan van de andere, de gegevensset wordt gezegd dat deze _imbalanced_. In het ideale geval voldoende vertegenwoordigers van elke klasse in de trainingsgegevens voorkeur om in te schakelen onderscheid worden gemaakt tussen verschillende klassen. Als een klasse minder dan 10% van de gegevens is, wordt de gegevens geacht imbalanced. De klasse vertegenwoordigde heet een _oversampling klasse_.

Problemen met veel PdM geconfronteerd met dergelijke imbalanced gegevenssets, waarbij een klasse is ernstig vertegenwoordigde vergeleken met de andere klasse of klassen. In sommige gevallen kan de klasse oversampling alleen 0,001% van het totale aantal gegevenspunten vormen. Klasse imbalance is niet uniek is voor PdM. Andere domeinen waar problemen en afwijkingen zelden worden geconfronteerd met een vergelijkbaar probleem voor voorbeelden, fraude te detecteren en binnendringing in het netwerk. Deze fouten zijn vormen van de klasse oversampling voorbeelden.

Met de klasse uit balans zijn gegevens, prestaties van de meeste standaard learning-algoritmen ermee is geknoeid, omdat ze zijn bedoeld om te minimaliseren, de frequentie van algemene fouten. Voor een verzameling met 99% negatieve en positieve voorbeelden van 1%, kan een model dat 99% nauwkeurigheid door alle exemplaren negatief labels worden weergegeven. Maar het model wordt niet het classificeren van alle positieve voorbeelden. dus zelfs als de nauwkeurigheid hoog is, het algoritme niet een nuttig is. Als gevolg hiervan conventionele evaluatie metrische gegevens zoals _algehele nauwkeurigheid van de frequentie van fouten_ zijn onvoldoende voor imbalanced learning. Wanneer u wordt geconfronteerd met imbalanced gegevenssets, worden andere metrische gegevens worden gebruikt voor de evaluatie van model:
- Precisie
- Terughalen
- F1-scores
- Kosten aangepast ROC (ontvanger operationele kenmerken)

Zie voor meer informatie over deze metrische gegevens, [model evaluatie](#model-evaluation).

Er zijn echter enkele methoden die verhelpen klasse imbalance probleem. De twee belangrijkste zijn _technieken steekproeven_ en _kosten gevoelige learning_.

#### <a name="sampling-methods"></a>Steekproeven methoden
Imbalanced learning omvat het gebruik van methoden voor het wijzigen van de set trainingsgegevens aan een set met gelijke taakverdeling gegevens. Steekproeven methoden zijn niet moet worden toegepast op de testset. Hoewel er verschillende steekproeven technieken zijn, meeste duidelijk zijn _willekeurige minority_ en _onder steekproeven_.

_Willekeurige minority_ omvat het selecteren van een steekproef van oversampling klasse, repliceren van deze voorbeelden en deze toevoegen aan een set trainingsgegevens. Als gevolg daarvan kan het aantal voorbeelden in klasse oversampling wordt verhoogd en uiteindelijk in balans brengen het aantal voorbeelden van verschillende klassen. Een nadeel van minority is dat de classificatie worden ook specifieke, waardoor er sprake van redundante aanpassing van labels kunnen leiden tot meerdere exemplaren van bepaalde voorbeelden. Training voor hoge nauwkeurigheid kan worden weergegeven in het model, maar het is mogelijk dat de prestaties niet waren herkend testgegevens suboptimale.

Daarentegen _willekeurige onder steekproeven_ is een steekproef van een meerderheid klasse selecteren en verwijderen van deze voorbeelden uit set trainingsgegevens. Voorbeelden van de meeste klasse verwijderen kan echter de classificatie belangrijke concepten met betrekking tot de meeste klasse missen veroorzaken. _Hybride steekproeven_ waarbij oversampling klasse is te veel sample en meerderheid klasse onder-voorbeelden worden gemaakt op de tijd is een andere levensvatbare benadering.

Er zijn veel steekproeven geavanceerde technieken. De techniek die is gekozen, is afhankelijk van de eigenschappen van gegevens en de resultaten van iteratieve experimenten door de gegevenswetenschapper.

#### <a name="cost-sensitive-learning"></a>Kosten van gevoelige learning
In PdM zijn de fouten die deel uitmaken van de klasse oversampling meer geïnteresseerd dan de normale voorbeelden. Daarom is de focus voornamelijk op de prestaties van het algoritme op fouten. Onjuist voorspellen van een klasse positief als negatief zijn klasse kunt duurder dan het vice versa. Deze situatie wordt vaak aangeduid als ongelijk verlies of onjuiste classificeren elementen asymmetrische kosten voor verschillende klassen. De ideale classificatie moet hoge nauwkeurigheid via de klasse oversampling leveren zonder toe te boeten op de nauwkeurigheid voor de meeste-klasse.

Er zijn meerdere manieren om deze afstemming. Om te beperken van het probleem van ongelijk verlies, een hoge kosten toewijzen aan foutieve indeling van de klasse oversampling en probeer het minimaliseren van de totale kosten. Algoritmen, zoals _SVMs (Machines Support Vector)_ vast van deze methode inherent, doordat de kosten van positieve en negatieve voorbeelden worden opgegeven tijdens de training. Op dezelfde manier zoals versterking methoden _boosted decision trees_ meestal goede prestaties met imbalanced gegevens weergeven.

## <a name="model-evaluation"></a>Model-evaluatie
Profieltoewijzingen classificatie is een belangrijk probleem voor PdM scenario's waar de kosten van vals alarm voor het bedrijf hoog is. Bijvoorbeeld, een beslissing op grond van een vliegtuig op basis van een onjuist voorspelling van motoren ontstaan planningen worden onderbroken en reisplannen. Een machine offline van een assemblagelijn duurt kan leiden tot verlies van omzet. Model-evaluatie met de juiste metrische prestatiegegevens tegen nieuwe testgegevens is daarom essentieel.

Metrische gegevens voor normale prestaties gebruikt voor het evalueren van modellen PdM worden hieronder besproken:

- [Nauwkeurigheid](https://en.wikipedia.org/wiki/Accuracy_and_precision) is de populairste metrische gegevens die worden gebruikt voor het beschrijven van de prestaties van een classificatie. Maar nauwkeurigheid gevoelig is voor gegevens-distributies en is een inefficiënte meting voor scenario's met imbalanced gegevenssets. Andere metrische gegevens worden in plaats daarvan gebruikt. Hulpprogramma's zoals [verwarringsmatrix](https://en.wikipedia.org/wiki/Confusion_matrix) worden gebruikt om te berekenen en reden over de nauwkeurigheid van het model.
- [Precisie](https://en.wikipedia.org/wiki/Precision_and_recall) van PdM modellen betrekking hebben op het aantal vals alarm. Lagere precisie van het model is in het algemeen komt overeen met een hoger aantal vals alarm.
- [Intrekken](https://en.wikipedia.org/wiki/Precision_and_recall) frequentie geeft aan hoeveel van de fouten in de testset 's juist geïdentificeerd door het model. Hogere intrekken tarieven betekenen dat het model is voltooid bij het identificeren van de waarde true fouten.
- [F1 score](https://en.wikipedia.org/wiki/F1_score) is het harmonische gemiddelde van precisie- en zoals eerder vermeld, met de waarde die variëren tussen 0 (slechtste) op 1 (aanbevolen).

Voor binaire classificatie
- [Besturingssysteem van de curve (RECEIVER) ontvanger](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is ook een populaire metrische gegevens. In de ROC-curve, wordt de modelprestaties op basis van een vaste operationele punt op de ROC geïnterpreteerd.
- Maar voor problemen met PdM _decile tabellen_ en _grafieken lift_ meer informatieve zijn. Ze richten op de positieve klasse (fouten) en een complexere beeld van de algoritme-prestaties dan ROC-curve bevatten.
  - _Decile tabellen_ worden gemaakt met behulp van de voorbeelden test in aflopende volgorde van de fout kansen. De geordende voorbeelden worden vervolgens gegroepeerd in deciles (10% van de voorbeelden met de hoogste kans en 20%, 30%, enzovoort). De /(random baseline) verhouding (true-positief-ratio) voor elke decile helpt de prestaties van de algoritme bij elke decile schatten. De willekeurige basislijn duurt op basis van waarden 0.1, 0.2, enzovoort.
  - [Grafieken lift](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) de decile true-positief-ratio ten opzichte van willekeurige waar positief-ratio voor alle deciles tekenen. De eerste deciles zijn meestal de focus van de resultaten, omdat ze de grootste voordelen worden weergegeven. Eerste deciles kunnen ook worden gezien als representatief zijn voor 'risico', wanneer die wordt gebruikt voor PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model uitoefening voor predictief onderhoud

Het voordeel is van de data science oefening gerealiseerde alleen wanneer het getrainde model wordt gemaakt operationele. Dat wil zeggen, moet het model worden geïmplementeerd in de bedrijfssystemen om voorspellingen op basis van nieuwe, eerder niet waren herkend, gegevens te maken.  De nieuwe gegevens precies moet voldoen aan de _model handtekening_ van het getrainde model op twee manieren:
- alle functies moet aanwezig zijn in elke logische instantie (bijvoorbeeld een rij in een tabel) van de nieuwe gegevens.
- de nieuwe gegevens vooraf moeten worden verwerkt en elk van de functies ontworpen, op dezelfde manier als de trainingsgegevens.

Het bovenstaande proces wordt vermeld op veel manieren in academische en documentatie. Maar met de volgende instructies betekent dat hetzelfde te doen:
- _Beoordeling van nieuwe gegevens_ met behulp van het model
- _Het model toepassen_ op nieuwe gegevens
- _Operationeel_ het model
- _Implementeer_ het model
- _Het model uitvoeren_ voor nieuwe gegevens

Zoals eerder gezegd, wijkt model uitoefening voor PdM af van de peers. Scenario's met betrekking tot detectie van afwijkingen en foutdetectie doorgaans implementeren _online scoren_ (ook wel genoemd _realtime scoren_). Hier, het model _scores_ elke binnenkomende record en retourneert een voorspelling. Voor detectie van afwijkingen, de voorspelling wordt aangegeven dat er een anomalie is opgetreden (voorbeeld: een klasse SVM). Foutdetectie is het type of de klasse van de fout.

Daarentegen PdM omvat _batchscore_. Om te voldoen aan de handtekening van het model, moeten de functies in de nieuwe gegevens op dezelfde manier als de trainingsgegevens worden ontworpen. Voor de grote gegevenssets die wordt gebruikt voor nieuwe gegevens, worden de functies samengevoegd tijdens een bepaalde tijdvensters en beoordeeld in batch. Batch scoren gewoonlijk wordt uitgevoerd in gedistribueerde systemen, zoals [Spark](https://spark.apache.org/) of [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Er zijn een aantal alternatieven - beide suboptimale:
- Streaming gegevensengines ondersteunt aggregatie in windows in het geheugen. Zo kan worden aangevoerd dat deze ondersteuning bieden voor online scoren. Maar deze systemen zijn geschikt voor compacte gegevens in smalle windows van tijd, of sparse-elementen via breder windows. Ze kunnen niet schalen voor de compacte gegevens op bredere tijdvensters, zoals te zien is in PdM scenario's.
- Als het batch scoring niet beschikbaar is, wordt de oplossing is om aan te passen online scoring voor het afhandelen van nieuwe gegevens in batches van kleine tegelijk.

## <a name="solution-templates-for-predictive-maintenance"></a>Sjablonen voor oplossingen voor Voorspellend onderhoud

Het laatste gedeelte van deze handleiding bevat een lijst van PdM oplossingssjablonen, zelfstudies en experimenten die zijn geïmplementeerd in Azure. Deze toepassingen PdM kunnen worden geïmplementeerd in een Azure-abonnement binnen enkele minuten in sommige gevallen. Ze kunnen worden gebruikt als proof of concept demo's, sandboxes om te experimenteren met alternatieven of accelerators voor daadwerkelijke productie-implementaties. Deze sjablonen bevinden zich in de [Azure AI Gallery](https://gallery.azure.ai) of [Azure GitHub](https://github.com/Azure). Deze verschillende voorbeelden zal worden opgenomen in deze oplossingssjabloon na verloop van tijd.

| # | Titel | Beschrijving |
|--:|:------|-------------|
| 2 | [De sjabloon voor Voorspellend onderhoud van Azure-oplossing](https://github.com/Azure/AI-PredictiveMaintenance) | Een sjabloon voor open-source oplossing die laat zien van ML-modellen en een volledige Azure-infrastructuur kan ondersteunen van scenario's voor Voorspellend onderhoud in de context van het externe controle IoT. |
| 3 | [Deep Learning voor Predictief onderhoud](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure-notitieblok met een demo-oplossing van het gebruik van LSTM (lange termijn geheugen)-netwerken (een klasse van terugkerende Neurale netwerken) voor Voorspellend onderhoud, met een [blogbericht op dit voorbeeld](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Voorspeld onderhoud model handleiding in R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Handleiding voor PdM modellen met behulp van scripts in R.|
| 5 | [Azure Voorspellend onderhoud voor lucht-en ruimtevaart](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Een van de eerste PdM oplossingssjablonen op basis van Azure ML v1.0 voor het onderhoud van vliegtuigmotoren. Deze handleiding afkomstig is van dit project. |
| 6 | [Azure AI-werkset voor IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI in de IoT edge met behulp van TensorFlow; Toolkit pakketten deep learning-modellen in Azure IoT Edge-compatibele Docker-containers en deze modellen als REST-API's weer te geven.
| 7 | [Azure IoT voor voorspeld onderhoud](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite, pc's - vooraf geconfigureerde oplossing. Onderhoud van vliegtuigmotoren-PdM sjabloon met IoT Suite. [Een ander document](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) en [scenario](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) die betrekking hebben op hetzelfde project. |
| 8 | [Sjabloon voor Voorspellend onderhoud met behulp van SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo van resterende levensduur scenario op basis van R services. |
| 9 | [Handleiding voor voorspeld onderhoud model](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Gegevensset-functie voor vliegtuigen onderhoud is gebouwd met behulp van R met [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) en [gegevenssets](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) en [Azure notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) en [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)in AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Cursussen voor voorspeld onderhoud

Microsoft Azure biedt leertrajecten voor de basisconcepten achter PdM technieken, naast inhoud en -training voor algemene AI-concepten en procedures.

| Training-resource  | Beschikbaarheid |
|:-------------------|--------------|
| [Leertraject voor PdM met behulp van structuren en willekeurige Forest](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Leertraject voor PdM met behulp van Deep Learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [AI-ontwikkelaar op Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Microsoft AI-School](https://aischool.microsoft.com/learning-paths) | Public |
| [Azure AI-training vanuit GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Public |
| [Micro soft AI YouTube webinars](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Microsoft AI-Show](https://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partners |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partners |

Bovendien zijn gratis MOOCS (massive open online cursussen) op AI aangeboden online door academische instellingen, zoals Stanford en MIT en andere educatieve bedrijven.
