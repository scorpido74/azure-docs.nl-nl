---
title: Azure AI-handleiding voor oplossingen voor Voorspellend onderhoud - Team Data Science Process
description: Een uitgebreide beschrijving van de wetenschap dat wordt gebruikt door oplossingen voor Voorspellend onderhoud in meerdere verticale industrieën.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 5cd16280ba942404ffb23fd1c9d0e1a20af8c7c4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721809"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-handleiding voor oplossingen voor Voorspellend onderhoud

## <a name="summary"></a>Samenvatting

Voor speld onderhoud iseen populaire toepassing van Predictive Analytics die bedrijven in verschillende branches kan helpen bij het realiseren van het gebruik van hoge activa en besparingen in operationele kosten. Deze hand leiding bevat de bedrijfs-en analyse richtlijnen en best practices voor het ontwikkelen en implementeren van PdM-oplossingen met behulp van de [Microsoft Azure AI-platform](https://azure.microsoft.com/overview/ai-platform) technologie.

Om te beginnen, introduceert deze handleiding branchespecifieke scenario's en het proces van in aanmerking komende deze scenario's voor PdM. De vereiste gegevens en modeltechnieken PdM oplossingen bouwen worden ook geleverd. De belangrijkste inhoud van de handleiding is op het data science process - met inbegrip van de procedure voor het voorbereiden van gegevens, feature-engineering, model maken en model uitoefening. Als aanvulling op de belangrijkste concepten, vindt deze handleiding u een set van sjablonen voor oplossingen om u te helpen sneller PdM toepassingen te ontwikkelen. De handleiding verwijst ook naar nuttige cursussen voor de beginner voor meer informatie over de AI achter de data science. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science handleiding overzicht en doel doelgroep
De eerste helft van deze handleiding worden beschreven in de meeste zakelijke problemen, de voordelen van de implementatie van PdM om deze problemen te verhelpen en geeft een lijst van enkele veelvoorkomende use cases. Besluitvormers (BDM) profiteren van deze inhoud. De tweede helft wordt uitgelegd van de data science achter PdM en geeft een lijst van PdM oplossingen gebouwd met behulp van de principes die worden beschreven in deze handleiding. Het biedt ook leertrajecten en verwijzingen naar trainingsmateriaal. Technische besluitvormers (TDMs) handig deze inhoud.

| Beginnen met... | Als u... |
|:---------------|:---------------|
| [Business Case voor predictief onderhoud](#business-case-for-predictive-maintenance) |een besluitvormer (Besluitvormer) wilt u minder uitvaltijd en operationele kosten en gebruik van apparatuur te verbeteren |
| [Gegevens wetenschap voor voor speld onderhoud](#data-science-for-predictive-maintenance) |een technisch beleidsmaker (TDM) PdM technologieën evalueren om te begrijpen van de unieke gegevensverwerking en AI-vereisten voor predictief onderhoud |
| [Oplossings sjablonen voor voor speld onderhoud](#solution-templates-for-predictive-maintenance)|een softwarearchitect of wilt u snel een demo's of een proof-of-concept een paar AI-ontwikkelaar |
| [Trainings bronnen voor predictief onderhoud](#training-resources-for-predictive-maintenance) | een of meer van de bovenstaande, en voor meer informatie over de basisconcepten achter de wetenschappelijke gegevens, hulpmiddelen en technieken.

### <a name="prerequisite-knowledge"></a>Vereiste voorkennis
De inhoud Besluitvormer hoofdverwerkingsknooppunt verwacht niet dat de lezer is voorgaande data science kennis hebben. Basiskennis van statistieken en datatechnologie is voor de inhoud TDM handig. Kennis van Azure Data en AI-services, Python, R, XML en JSON wordt aanbevolen. AI-technieken worden geïmplementeerd in Python / R-pakketten. Sjablonen voor oplossingen worden geïmplementeerd met behulp van Azure-services, ontwikkelprogramma's en SDK's.

## <a name="business-case-for-predictive-maintenance"></a>Bedrijfsscenario voor predictief onderhoud

Bedrijven vereisen essentiële uitrusting piek efficiëntie en om te profiteren van hun rendement op investeringen worden uitgevoerd. Deze apparaten kunnen variëren van vliegtuigmotoren, maar, liften of industriële chillers - die miljoenen - omlaag dagelijkse apparaten zoals kopieerapparaten, koffiezetmachines of Waterkoelers kosten.
- Standaard zijn de meeste bedrijven afhankelijk van het _corrigerende onderhoud_, waarbij onderdelen worden vervangen als en wanneer ze mislukken. Corrigerende onderhoud zorgt ervoor dat onderdelen volledig worden gebruikt (dus niet verspild onderdeel leven), kosten, maar het bedrijf in downtime, arbeid en onderhoudsvereisten voor niet-gepland (niet uren of onhandig locaties).
- Op het volgende niveau gebruiken bedrijven _preventief onderhoud_, waar ze de nuttige levens duur voor een deel bepalen, en deze hand haven of vervangen vóór een storing. Preventief onderhoud wordt niet-geplande en onherstelbare fouten voorkomen. Maar de hoge kosten van de geplande downtime, onder het gebruik van het onderdeel tijdens de levens duur, en arbeid blijven actief.
- Het doel van _voorspellend onderhoud_ is het optimaliseren van het evenwicht tussen corrigerend en preventief onderhoud door _just-in-time_ vervanging van onderdelen in te scha kelen. Deze methode vervangt alleen die onderdelen wanneer ze zich dicht bij een storing. Door uit te breiden onderdeel lifespans (vergeleken met preventief onderhoud) en niet-gepland onderhoud en kosten bespaard (via corrigerende onderhoud) vermindert, bedrijven kunnen krijgen kosten te besparen en krachtige voordelen.

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

- _gegevens wetenschappers_ om specifieke voorspellende problemen te analyseren en op te lossen.
- _Cloud architecten en-ontwikkel aars_ om een end-to-end-oplossing samen te brengen.

## <a name="qualifying-problems-for-predictive-maintenance"></a>In aanmerking komende problemen voor predictief onderhoud
Het is belangrijk om te benadrukken dat niet alle use-cases of bedrijfsproblemen effectief kunnen worden opgelost door PdM. Er zijn drie belangrijke in aanmerking komend criteria die moeten worden overwogen bij de selectie van het probleem:

- Het probleem moet worden voorspeld van aard; dat wil zeggen, moet er een doel of een resultaat om te voorspellen. Het probleem hebt ook een duidelijk pad van de actie om te voorkomen dat fouten wanneer ze worden gedetecteerd.
- Het probleem moet een record bevatten van de operationele geschiedenis van de apparatuur die _zowel goede als slechte resultaten_bevat. De set acties die worden uitgevoerd om onjuiste resultaten moeten ook beschikbaar als onderdeel van deze records. Foutenrapporten, logboeken voor onderhoud van prestatievermindering, herstellen en vervangen Logboeken ook belangrijk zijn. Reparaties die wordt uitgevoerd voor het verbeteren en vervangende records zijn ook nuttig.
- De vastgelegde geschiedenis moet worden weer gegeven in _relevante_ gegevens die _voldoende_ kwaliteit hebben om de use-case te ondersteunen. Zie voor meer informatie over relevantie en bekrachtiging van [gegevens, vereisten voor voor speld onderhoud](#data-requirements-for-predictive-maintenance).
- Domein-experts die een duidelijk beeld van het probleem krijgt hebt ten slotte van het bedrijf. Ze moeten rekening houden met het interne processen en procedures om u te helpen bij de analist begrijpen en interpreteren van de gegevens. Ze moeten ook worden de benodigde wijzigingen aanbrengen in bestaande bedrijfsprocessen om te verzamelen van de juiste gegevens voor de problemen, indien nodig.

## <a name="sample-pdm-use-cases"></a>Voorbeeld PdM use-cases
Deze sectie richt zich op een verzameling van PdM van use cases in diverse branches, zoals ruimtevaart, hulpprogramma's en transport. Elke sectie begint met een zakelijk probleem, en beschrijft de voordelen van PdM, de relevante gegevens rond het zakelijke probleem en tot slot de voordelen van een oplossing PdM.

| Zakelijk probleem | Voordelen van PdM |
|:-----------------|-------------------|
|**Burger**      |                   |
|_Vlucht vertraging en annuleringen_ vanwege mechanische problemen. Fouten die in de tijd kunnen niet worden hersteld kunnen ertoe leiden dat vluchten te annuleren en planning en bewerkingen worden onderbroken. |PdM oplossingen kunnen voorspellen de kans dat een vliegtuig worden vertraagd of geannuleerd vanwege mechanische storingen.|
|_Defecte vliegtuig motor onderdelen_: vervanging van het vliegtuig motor onderdeel ligt onder de meest voorkomende onderhouds taken binnen de luchtvaart industrie. Onderhoud-oplossingen vereisen een zorgvuldig beheer van het onderdeel voorraad, levering en planning|Is het kunnen voor het verzamelen van intelligence op onderdeel betrouwbaarheid leidt tot een aanzienlijke vermindering van investeringskosten.|
|**Projectfinancierings** |                         |
|_ATM-fout_ is een veelvoorkomend probleem binnen de Bank-industrie. Het probleem hier is voor het rapporteren van de kans dat een transactie ATM geld intrekking wordt onderbroken vanwege een fout papier zit vast of een deel in de dispenser geld. Gebaseerd op voorspellingen van transactiefouten optreden, kunnen geldautomaten worden onderhouden proactief om te voorkomen dat er fouten optreden.| In plaats van dat de machine halverwege een transactie mislukt, wordt het wenselijk alternatief is naar de machine service weigeren op basis van de voorspelling-programma.|
|**Sector** |                          |
|_Wind turbine-storingen_: wind turbines zijn de belangrijkste energie bronnen in omgevings bewuste landen/regio's, en omvatten hoge kapitaal kosten. Een belang rijk onderdeel in wind turbines is de generator motor, waarvan de fout de turbine ingaat weer geven. Het is ook zeer kostbaar zijn om op te lossen.|KPI's te voorspellen, zoals MTTF (gemiddelde tijd bij een storing) kunt u de energiebedrijven turbine fouten voorkomen, en controleer of de minimale downtime. Fout kansen wordt in kennis technici om te controleren, maar die waarschijnlijk zal mislukken snel en gepland onderhoud op basis van tijd regelingen. Voorspellende modellen bieden inzicht in verschillende factoren die bijdragen aan de fout, waarmee technici beter begrip van dat de belangrijkste oorzaken van problemen.|
|_Storingen van de circuit onderbreker_: voor de distributie van elektriciteit naar huizen en bedrijven moeten stroom lijnen altijd operationeel zijn om de energie levering te garanderen. Stroomonderbrekers te beperken of te voorkomen dat schade aan power regels tijdens overbelasting of schadelijke weersomstandigheden. Het zakelijke probleem hier is Circuitonderbreker fouten kunt voorspellen.| PdM oplossingen helpen herstellen kosten te verlagen en de levensduur van de uitrusting zoals stroomonderbrekers verhogen. Ze helpen de kwaliteit van het netwerk power verbeteren door te verminderen onverwachte fouten en serviceonderbrekingen.|
|**Trans Port en logistiek** |    |
|_Storingen in lift deuren_: grote lift bedrijven bieden een volledige stack-service voor miljoenen functionele liften over de hele wereld. Lift-veiligheid, betrouwbaarheid en beschikbaarheid zijn de belangrijkste zorgen voor hun klanten. Deze bedrijven volgen deze en verschillende andere kenmerken via sensoren, zodat ze kunnen met corrigerende en preventief onderhoud. In een lift, is het probleem van de meest prominente klant lift deuren defect. Het zakelijke probleem wordt in dit geval een voorspellende knowledge base-toepassing die voorspelt dat de mogelijke oorzaken van de deur van fouten.| Liften zijn kapitaalinvesteringen voor mogelijk een 20-30-jaar geldig. Zodat elke potentiële verkoop uiterst concurrerende; verwachtingen voor de service en ondersteuning zijn daarom hoog. Voorspeld onderhoud kan deze bedrijven voorzien van een voordeel ten opzichte van hun concurrenten in hun product en serviceaanbiedingen.|
|_Wiel storingen_: account voor wiel fouten voor de helft van alle trein-en kosten miljarden aan de wereld wijde spoorweg industrie. Wheel fouten ook toe leiden dat de rails te verslechteren, soms veroorzaakt de spoor voortijdig verbreken. Spoor onderbrekingen leiden tot onherstelbare gebeurtenissen, zoals ontsporingen. Om te voorkomen dat deze instanties, spoorwegen de prestaties van wheels controleren en deze op een manier die preventieve vervangen. Het zakelijke probleem hier is de voorspelling van wheel-fouten.| Predictief onderhoud van wheels helpt met just-in-time wheels te vervangen |
|_Storingen_in de Subway-trein: een grote reden voor vertragingen in de Subway-activiteiten zijn deur storingen bij het trainen van auto's. Het zakelijke probleem hier is train klep van fouten kunt voorspellen.|Vroege bewust te maken van een storing klep, of het aantal dagen tot een fout opgetreden in de deur helpt het bedrijf optimaliseren trein van klep van gepland onderhoud.|

De volgende sectie haalt de details van hoe u kunt profiteren van de PdM voordelen die hierboven wordt beschreven.

## <a name="data-science-for-predictive-maintenance"></a>Wetenschappelijke gegevens voor voorspeld onderhoud

Deze sectie bevat algemene richtlijnen van data science principes en praktijken voor PdM. Het is bedoeld ter ondersteuning van een TDM, oplossingsarchitect, of een ontwikkelaar informatie over de vereisten en proces voor het ontwikkelen van AI-toepassingen end-to-end voor PdM. U kunt deze sectie lezen, samen met een overzicht van de demo's en sjabloon testen die worden vermeld in [oplossings sjablonen voor predictief onderhoud](#solution-templates-for-predictive-maintenance). U kunt vervolgens deze beginselen en aanbevolen procedures voor het implementeren van uw oplossing PdM in Azure.

> [!NOTE]
> Deze handleiding is niet bedoeld om te leren van de Data Science-lezer. Er zijn verschillende nuttige bronnen voor het verder lezen van de sectie voor [trainings bronnen voor voor speld onderhoud](#training-resources-for-predictive-maintenance). De [oplossings sjablonen](#solution-templates-for-predictive-maintenance) die in de hand leiding worden vermeld, demonstreren enkele van deze AI-technieken voor specifieke PdM-problemen.

## <a name="data-requirements-for-predictive-maintenance"></a>Gegevensvereisten voor predictief onderhoud

Het succes van elk learning, is afhankelijk van (a) de kwaliteit van wat is wordt geleerd, en (b) de mogelijkheid van de cursist. Voorspellende modellen patronen van historische gegevens leert en toekomstige resultaten voorspellen met bepaalde kans op basis van deze patronen waargenomen. Van een model voorspellende nauwkeurigheid is afhankelijk van de relevantie, toereikend waren en kwaliteit van de trainings- en testset gegevens. De nieuwe gegevens die 'beoordeeld' met behulp van dit model moet hebben de dezelfde functies en het schema als de gegevens voor training en testen. De functie-kenmerken (type, dichtheid, distributie, enzovoort) van nieuwe gegevens moeten overeenkomen met die van de trainings- en testset gegevenssets. De focus van deze sectie is van de behoeften van dergelijke gegevens.

### <a name="relevant-data"></a>Relevante gegevens

Ten eerste moeten de gegevens relevant zijn _voor het probleem_. Bekijk de hierboven beschreven situatie voor het gebruik van een _Wheel-fout_ . de trainings gegevens moeten functies bevatten die betrekking hebben op de wiel bewerkingen. Als het probleem zich voordoet om de storing van het _tractie systeem_te voors pellen, moeten de opleidings gegevens alle verschillende onderdelen voor het tractie systeem omvatten. Het eerste geval is bedoeld voor een specifiek onderdeel dat is gericht op het tweede geval het uitvallen van een grotere subsysteem. De algemene aanbeveling is voorspelling om systemen te ontwerpen over specifieke onderdelen in plaats van grotere subsystemen, sinds de laatste hebben meer gegevens verspreide wordt. De domein expert (Zie [problemen kwalificeren voor voor speld onderhoud](#qualifying-problems-for-predictive-maintenance)) moet helpen bij het selecteren van de meest relevante subsets met gegevens voor de analyse. De relevante gegevens bronnen worden uitgebreid beschreven in [gegevens voorbereiding voor voor speld onderhoud](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Onvoldoende gegevens
Twee vragen vaak worden gesteld met betrekking tot de gegevens van geschiedenis van fout: (1) 'hoeveel mislukte gebeurtenissen zijn vereist voor een model te trainen?' (2) "hoeveel records worden als" voldoende beschouwd "?" Er zijn geen definitieve antwoorden, maar alleen regels voor de duim. Meer het aantal gebeurtenissen die betrekking hebben, betere voor (1), het model. Voor (2), en het exacte aantal mislukte gebeurtenissen is afhankelijk van de gegevens en de context van het probleem wordt opgelost. Maar op de andere kant hebben als een virtuele machine te vaak mislukt vervolgens het bedrijf wordt vervangen, waarmee exemplaren van de fout wordt beperkt. Hier nogmaals, is de richtlijnen van het domein deskundige belangrijk. Er zijn echter methoden om het probleem van _zeldzame gebeurtenissen_op te lossen. Deze worden besproken in de sectie het verwerken van niet- [sluitende gegevens](#handling-imbalanced-data).

### <a name="quality-data"></a>Kwaliteitsgegevens
De kwaliteit van de gegevens is kritiek: elke waarde van het Voorspellings kenmerk moet _nauw keurig_ zijn in combi natie met de waarde van de doel variabele. Kwaliteit van de gegevens is een goed studied gebied in statistieken en gegevensbeheer, en dus buiten bereik voor deze handleiding.

> [!NOTE]
> Er zijn verschillende resources en enterprise-producten om kwaliteitsgegevens te geven. Hieronder vindt u een voorbeeld van verwijzingen naar:
> - Dasu, T, Johnson, T., experimentele gegevensanalyse en het opschonen van gegevens, Wiley, 2003.
> - [Experimentele gegevens analyse, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, kwantitatieve gegevens reiniging voor grote data bases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de jonge, E, van der Loo, M, inleiding tot het opschonen van gegevens met R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

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

Voor beelden van relevante gegevens voor de voor beelden van het [gebruik van PdM-aanvragen](#sample-pdm-use-cases) worden hieronder in de tabel beschreven:

| Toepassing | Voorbeelden van relevante gegevens |
|:---------|---------------------------|
|_Vlucht vertraging en annuleringen_ | Informatie over de route in de vorm van vertragingen van vluchten zijden Flight en pagina-Logboeken. Vlucht gegevens omvatten routerings gegevens, zoals vertrek/aankomst datum, tijd, lucht haven, layovers, enzovoort. Het pagina logboek bevat een reeks fout-en onderhouds codes die zijn vastgelegd door het personeel van het terrein onderhoud.|
|_Storing in vliegtuig motor onderdelen_ | Gegevens verzameld van sensoren in het vliegtuig met informatie over de toestand van de verschillende onderdelen. Onderhoudsrecords te identificeren wanneer component fouten zijn opgetreden en wanneer ze zijn vervangen door.|
|_ATM-fout_ | Sensorwaarden, serverlogs, voor elke transactie (storten geld/selectievakje) en aflevering geld. Informatie over de tussenruimte meting tussen notities, de notitie breedte, de aankomst afstand van de notitie, kenmerken, enzovoort. Onderhouds records met fout codes, herstel gegevens, laatste keer dat de kassa dispenser is gevuld.|
|_Wind turbine-fout_ | Sens oren bewaken de turbine-omstandigheden, zoals Tempe ratuur, wind richting, gegenereerde stroom, Generator snelheid, enzovoort. Gegevens worden verzameld uit meerdere wind turbines van Wind-Farms die zich in verschillende regio's bevinden. Elke turbine heeft meestal meerdere sensorwaarden, serverlogs, metingen relay op een vast tijdsinterval.|
|_Storingen van circuit onderbreker_ | Onderhoud-logboeken die corrigerende preventieve en systematische acties opnemen. Operationele gegevens met automatische en handmatige opdrachten verzonden naar stroomonderbrekers zoals voor bewerkingen voor openen en sluiten. Meta gegevens van apparaten, zoals de datum van productie, locatie, model enz. Specificaties van circuit onderbreker, zoals spannings niveaus, geolocatie en omgevings omstandigheden.|
|_Storingen in lift deuren_| Lift-metagegevens zoals type lift, productiedatum, onderhoud frequentie, type gebouw, enzovoort. Operationele informatie zoals het aantal klep cycli, gemiddelde klep sluiten tijd. Foutgeschiedenis met oorzaken.|
|_Wiel fouten_ | Sensor gegevens die de wiel versnelling, het remmen, de afstand, de snelheid enz. Statische informatie over wielen zoals fabrikant, productie datum. Foutgegevens afgeleid van de database voor klantorders onderdeel die volgorde datums en aantallen bijhouden.|
|_Storingen in Subway-trein deuren_ | De deur openen en sluiten tijden, andere operationele gegevens, zoals de huidige toestand van de trein deuren. Statische gegevens omvat activa-id, de tijd en de voorwaarde waarde kolommen.|

### <a name="data-types"></a>Gegevenstypen
De bovenstaande gegevensbronnen worden gegeven, zijn de twee belangrijkste gegevenstypen waargenomen in PdM domein:

- _Tijdelijke gegevens_: operationele telemetrie, computer voorwaarden, typen werk orders, prioriteits codes met tijds tempels op het moment van de registratie. Fout, onderhoud/reparatie en gebruiksgeschiedenis heeft ook tijdstempels die zijn gekoppeld aan elke gebeurtenis.
- _Statische gegevens_: computer functies en operator functies in het algemeen zijn statisch, omdat hierin de technische specificaties van computers of operator kenmerken worden beschreven. Als deze functies na verloop van tijd wijzigt kunnen, moeten ze ook die zijn gekoppeld aan deze tijdstempels hebben.

Voorspellings-en doel variabelen moeten worden voorverwerkt/getransformeerd in [numerieke, categorische en andere gegevens typen](https://www.statsdirect.com/help/basics/measurement_scales.htm) , afhankelijk van de algoritme die wordt gebruikt.

### <a name="data-preprocessing"></a>Gegevens voorverwerken
Als een vereiste voor het ontwikkelen van functies, _moet u de_gegevens voorbereiden op basis van verschillende stromen om een schema op te stellen van waaruit het eenvoudig is om onderdelen te bouwen. Visualiseer de gegevens eerst als een tabel met records. Elke rij in de tabel vertegenwoordigt een trainings exemplaar en de kolommen bevatten _Voorspellings_ functies (ook wel onafhankelijke kenmerken of variabelen genoemd). Organiseer de gegevens zo dat de laatste kolom (men) het _doel_ (afhankelijke variabele) is. Wijs voor elk trainings exemplaar een _Label_ toe als de waarde van deze kolom.

Voor tijdelijke gegevens, de duur van de sensorgegevens in tijdseenheden te verdelen. Elke record moet behoren tot een tijds eenheid voor een Asset _en moet afzonderlijke informatie_bevatten. Eenheden worden gedefinieerd op basis van de behoeften van uw bedrijf in veelvouden van seconden, minuten, uren, dagen, maanden, enzovoort. De tijds eenheid hoeft _niet hetzelfde te zijn als de frequentie van gegevens verzameling_. Als de frequentie hoog is, kan de gegevens die een belangrijk verschil van de ene eenheid naar de andere niet weer. Bijvoorbeeld, wordt ervan uitgegaan dat de omgevingstemperatuur elke tien seconden is verzameld. Met behulp van datzelfde interval voor trainingsgegevens alleen wordt vergroot het aantal voorbeelden zonder op te geven aanvullende informatie. In dit geval worden een strategie voor een betere gemiddelde de gegevens die meer dan 10 minuten, of een uur op basis van de zakelijke rechtvaardiging gebruikt.

Voor statische gegevens
- _Onderhouds records_: onbewerkte onderhouds gegevens hebben een activa-id en tijds tempel met informatie over onderhouds activiteiten die op een bepaald moment zijn uitgevoerd. Onderhouds activiteiten transformeren in _categorische_ -kolommen, waarbij elke categorie descriptor uniek wordt toegewezen aan een specifieke onderhouds actie. Het schema voor onderhoudsrecords omvat activa-id, de tijd en de bewerking voor siteonderhoud.

- _Fout records_: fouten of fout oorzaken kunnen worden vastgelegd als specifieke fout codes of fout gebeurtenissen die door specifieke bedrijfs voorwaarden worden gedefinieerd. In gevallen waar de apparatuur meerdere foutcodes heeft, kunt het domein deskundige identificeren die relevant voor de doelvariabele zijn. Gebruik de resterende fout codes of voor waarden om _Voorspellings_ functies samen te stellen die met deze fouten overeenkomen. Het schema voor mislukte records omvat activa-id, tijd, mislukt of de reden van fout - indien beschikbaar.

- _Meta gegevens van de computer en de operator_: de machine-en operator gegevens samen voegen in één schema om een Asset te koppelen aan de bijbehorende operator, samen met de bijbehorende kenmerken. Het schema voor de voorwaarden van de machine omvat activa-id, asset functies, operator-id en functies van de operator.

Andere stappen voor voor verwerking van gegevens zijn onder andere het _verwerken van ontbrekende waarden_ en _normalisatie_ van kenmerk waarden. Een gedetailleerde beschrijving valt buiten het bereik van deze handleiding: Zie de volgende sectie voor sommige nuttig verwijzingen.

Met de bovenstaande voorverwerkte gegevensbronnen aanwezig is, de laatste transformatie voordat feature-engineering, is de bovenstaande tabellen op basis van de activa-id en tijdstempel moeten worden samengevoegd. De resulterende tabel moet null-waarden voor de kolom fout wanneer machine zich in de normale werking. Deze null-waarden kunnen worden toegeschreven door een indicator voor normale werking. Gebruik deze fout kolom om _labels voor het voorspellende model_te maken. Zie de sectie over [model technieken voor voor speld onderhoud](#modeling-techniques-for-predictive-maintenance)voor meer informatie.

## <a name="feature-engineering"></a>Functie-engineering
Feature-engineering is de eerste stap voorafgaand aan het modelleren van de gegevens. De rol in het data Science proces [wordt hier beschreven](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). Een _functie_ is een voorspellend kenmerk voor het model, zoals de Tempe ratuur, druk, trilling, enzovoort. Voor PdM omvat feature-engineering het om een virtuele machine de status van historische gegevens verzameld over de duur van een aanzienlijke te abstraheren. In die zin is het verschilt van de peers zoals externe controle, detectie van afwijkingen en detecteren van storingen. 

### <a name="time-windows"></a>Tijdvensters
Externe controle omvat het rapporteren van de gebeurtenissen die vanaf _tijdstippen_plaatsvinden. Anomaly detection modellen evalueren de binnenkomende gegevensstromen (score) van de gegevens op de vlag anomalieën vanaf punten in de tijd. Foutdetectie classificeert worden specifieke typen wanneer deze zich punten in de tijd voordoen, mislukte aanmeldingen. Daarentegen omvat PdM het voors pellen van fouten in een _toekomstige tijds periode_, op basis van functies die het gedrag van de computer voor de _historische_periode vertegenwoordigen. Voor PdM zijn de gegevens van de functie van afzonderlijke punten van de tijd te veel lawaai worden voorspeld. De gegevens voor elke functie moeten dus worden _smoothened_ door het samen voegen van gegevens punten in een periode van Windows.

### <a name="lag-features"></a>De functies lag
De zakelijke vereisten definiëren hoe ver moet het model in de toekomst te voorspellen. Op zijn beurt deze duur zorgt 'hoe ver terug het-model heeft om te controleren of' definiëren om deze voorspellingen te doen. Deze ' gezochte periode ' wordt de _vertraging_genoemd en functies die zijn toegepast op deze vertragings periode worden _vertragings functies_genoemd. Deze sectie wordt besproken lag-functies die kunnen worden samengesteld uit gegevensbronnen met tijdstempels en de functie voor het maken van statische gegevensbronnen. Vertragings functies zijn _doorgaans_ genummerd.

> [!IMPORTANT]
> Grootte van het venster wordt bepaald via experimenten en met behulp van een deskundige domein moet worden voltooid. De dezelfde voorbehoud bevat voor de selectie en de definitie van de functies lag, hun aggregaties en het type van windows.

#### <a name="rolling-aggregates"></a>Rolling statistische functies
Voor elke record van een asset, een rolling venster van de grootte "letter W" gekozen als het aantal eenheden van de tijd voor het berekenen van de statistische functies. De vertragings functies worden vervolgens berekend met behulp van de W-Peri Oden _vóór de datum_ van die record. De blauwe regels worden weergegeven in afbeelding 1, sensorwaarden voor een asset met elke eenheid van de tijd geregistreerd. Ze geven aan een oplopende gemiddelde van waarden van de functie over een venster van de grootte W = 3. Het voortschrijdende gemiddelde wordt berekend over alle records met tijds tempels in het bereik t<sub>1</sub> (oranje) tot t<sub>2</sub> (groen). De waarde voor W is doorgaans in minuten of uren, afhankelijk van de aard van de gegevens. Maar voor bepaalde problemen met een grote W (bijvoorbeeld 12 maanden) verzamelen over de volledige geschiedenis van een asset tot het moment van de record.

![Afbeelding 1. Rolling statistische functies](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Afbeelding 1. Rolling statistische functies

Voorbeelden van het huidige statistische functies gedurende een bepaalde periode zijn aantal, gemiddelde, CUMESUM (cumulatieve som) metingen, min/max-waarden. Bovendien worden afwijking, standaarddeviatie en aantal van de uitschieters dan N standaardafwijkingen vaak gebruikt. Hieronder vindt u voor beelden van statistische functies die kunnen worden toegepast voor de [use cases](#sample-pdm-use-cases) in deze hand leiding. 
- _Vlucht vertraging_: aantal fout codes in de afgelopen dag/week.
- Fout in het onderdeel van het _lucht vaartuig_: Rolling gemiddelde, standaard afwijking en som over de afgelopen dag, week, enzovoort. Deze metriek moet samen met de expert voor het zakelijke domein worden bepaald.
- _ATM-fouten_: Rolling gemiddelden, mediaan, bereik, standaard afwijking, aantal uitschieters tot meer dan drie standaard afwijkingen, bovenste en onderste CUMESUM.
- _Storingen in Subway_: het aantal gebeurtenissen in de afgelopen dag, week, twee weken etc.
- _Storingen in de circuit onderbreker_: aantal fouten in de afgelopen week, het jaar, drie jaar, enzovoort.

Er is een andere handige techniek in PdM trend wijzigingen, pieken en verandert met algoritmen die in de gegevens afwijkingen vast te leggen.

#### <a name="tumbling-aggregates"></a>Tumblingvenstertrigger statistische functies
Voor elke gelabelde record van een Asset wordt een venster met een grootte van _w-<sub>k</sub>_  gedefinieerd, waarbij _k_ het aantal vensters van grootte _w_is. Aggregaties worden vervolgens gemaakt over _k_ _tumblingvenstertriggers Windows_ _w-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, W-<sub>1</sub>_  voor de peri Oden vóór de tijds tempel van een record. _k_ kan een klein getal zijn voor het vastleggen van korte-termijn effecten of een groot aantal voor het vastleggen van patronen met een lange termijn vermindering. (Zie afbeelding 2).

![Afbeelding 2. Statistische functies tumbling](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Afbeelding 2. Statistische functies tumbling

Functies bijvoorbeeld vertragingstijd voor de wind maar use-case kan worden gemaakt met W = 1 en k = 3. Ze impliceren de vertraging voor elk van de afgelopen drie maanden met behulp van de boven- en uitbijters.

### <a name="static-features"></a>Statische functies

Technische specificaties van de uitrusting zoals datum van de productie, modelnummer, locatie, zijn enkele voorbeelden van statische functies. Ze worden behandeld als _categorische_ -variabelen voor model lering. Enkele voorbeelden voor de Circuitonderbreker use-case zijn spanning, huidige, energiecapaciteit, transformatortype en voeding. Voor wheel-fouten is het type band wheels (gelegeerd vs staal) een voorbeeld.

De gegevens voorbereiden pogingen tot nu toe is behandeld moeten leiden tot de gegevens wordt geordend zoals hieronder wordt weergegeven. Training, test en validatie van gegevens, moet deze logische schema (in dit voorbeeld laat zien in eenheden van dagen) hebben.

| Activa-id | Time | \<functie kolommen > | Label |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

De laatste stap in Feature engineering is de **labeling** van de doel variabele. Dit proces is afhankelijk van de techniek modelleren. Op zijn beurt afhankelijk de techniek modellering is van het zakelijke probleem en de aard van de beschikbare gegevens. Labels wordt in de volgende sectie besproken.

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
- _Mini maal vereiste doorloop tijd_ voor het vervangen van onderdelen, het implementeren van onderhouds resources, het uitvoeren van onderhoud om een probleem te voor komen dat in die periode zou kunnen optreden.
- het _minimum aantal gebeurtenissen_ dat kan plaatsvinden voordat er een probleem optreedt.

Bij deze techniek worden twee soorten training voorbeelden aangeduid. Een positief voor beeld, _wat een fout aangeeft_, met label = 1. Een negatieve voorbeeld, dat aangeeft dat de normale bewerkingen, met het label = 0. De doel variabele en daarom zijn de label waarden _categorische_. Het model moet elke nieuwe voorbeeld als waarschijnlijk zal mislukken of normaal werken via de volgende X tijdseenheden bepalen.

#### <a name="label-construction-for-binary-classification"></a>Label-constructie voor binaire classificatie
De vraag hier is: "Wat is de kans dat de asset niet wordt in de volgende X-tijdseenheid?" Beantwoord deze vraag, label X records vóór de fout van een asset als "over naar de mislukt" (label = 1), en alle andere records als zijnde "normale" labelen (label = 0). (Zie afbeelding 3).

![Afbeelding 3. Labels voor binaire classificatie](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Afbeelding 3. Labels voor binaire classificatie

Voorbeelden van de strategie voor enkele van de use cases labels worden hieronder vermeld.
- _Vlucht vertragingen_: X kan worden gekozen als één dag, om vertragingen in de komende 24 uur te voors pellen. Vervolgens alle vluchten die zich binnen 24 uur vóór de fouten zijn gelabeld als 1.
- Afwijkings _storingen_in de ATM: een doel is het bepalen van de kans op fouten bij een trans actie in het volgende uur. In dat geval worden alle transacties die hebben plaatsgevonden binnen het afgelopen uur van de fout zijn gelabeld als 1. Om te voorspellen kans op mislukte via de volgende N valuta worden vrijgesteld, notities alle opmerkingen bij de aangeboden in de laatste N toelichting van een storing gelabeld als 1.
- _Storingen van circuit onderbreker_: het doel is om de volgende storing van de circuit onderbreker te voors pellen. In dat geval is X gekozen als één toekomstige opdracht.
- _Problemen met de trein deuren_: X kan worden gekozen als twee dagen.
- _Wind turbine-fouten_: X kan worden gekozen als twee maanden.

### <a name="regression-for-predictive-maintenance"></a>Regressie voor predictief onderhoud
Regressie modellen worden gebruikt voor _het berekenen van de resterende levens duur (resterende levens duur) van een Asset_. Resterende Levensduur wordt gedefinieerd als de hoeveelheid tijd die een asset operationeel is voordat de volgende fout zich voordoet. Elk voor beeld van een training is een record die deel uitmaakt van een tijds eenheid- _nY_ voor een Asset, waarbij _n_ het veelvoud is. Het model moet het resterende levens duur van elk nieuw voor beeld berekenen als een _doorlopend nummer_. Dit nummer geeft aan dat de periode resterend voor hervatting van de fout.

#### <a name="label-construction-for-regression"></a>Label-constructie voor regressie
De vraag hier is: "Wat is de resterende levensduur (RUL) van de apparatuur?" Bereken het label om te worden van het aantal eenheden van de resterende voorafgaand aan de volgende fout tijd voor elke record vóór de fout. Labels zijn bij deze methode hoeft continue variabelen. (Zie afbeelding 4)

![Afbeelding 4. Labels voor regressie](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Afbeelding 4. Labels voor regressie

Voor regressie, wordt labeling gedaan met een verwijzing naar een punt van mislukken. De berekening is niet mogelijk zonder te weten hoe lang de asset is hebt voordat er een fout. Dus worden daarentegen binaire indeling, activa zonder fouten in de gegevens niet gebruikt voor modellen. Dit probleem wordt het best aangepakt door een andere statistische techniek die [overlevings analyse](https://en.wikipedia.org/wiki/Survival_analysis)wordt genoemd. Maar potentiële problemen kunnen ontstaan wanneer deze techniek wordt toegepast op PdM van use cases die betrekking hebben op de tijd heen variëren gegevens met regelmatige intervallen. Zie [Deze pagina](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)voor meer informatie over het opoverlevings analyse.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Meerdere klasse-classificatie voor voorspeld onderhoud
Meerdere klasse classificatietechnieken kunnen worden gebruikt in PdM oplossingen voor twee scenario's:
- Voor speling _twee toekomstige uitkomsten_: het eerste resultaat is _een periode_ voor het mislukken van een Asset. De asset is toegewezen aan een van meerdere mogelijke perioden. Het tweede resultaat is de kans op fouten in een toekomstige periode als gevolg _van een van de verschillende hoofd oorzaken_. Deze voorspelling kan de bemanning onderhoud om te controleren op symptomen en plan onderhoudsschema's.
- _De meest waarschijnlijke hoofd oorzaak_ van een bepaalde fout voors pellen. Dit resultaat raadt aan om de juiste set onderhoudsacties om op te lossen van een storing. Een gerangschikte lijst van de belangrijkste oorzaken en aanbevolen reparaties kunt technici prioriteren van hun acties voor herstellen na een storing.

#### <a name="label-construction-for-multi-class-classification"></a>Label-constructie voor ROC-classificatie
De vraag is als volgt: ' wat is de kans dat een Asset mislukt in de volgende _Nz_ -eenheden, waarbij _n_ het aantal Peri Oden? ' Als u wilt deze vraag te beantwoorden, een label nZ records vóór de fout van een asset met behulp van buckets van tijd (3Z, 2Z, Z). Label alle andere records "normale" (label = 0). In deze methode bevatten de doel variabele _categorische_ -waarden. (Zie afbeelding 5).

![Afbeelding 5. Voorspellings labels voor uitval tijd voor classificatie met een multi klasse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Afbeelding 5. Labels voor classificatie ROC voor fout tijd voorspelling

De vraag is als volgt: "wat is de kans dat de activa in de volgende X tijds eenheden mislukken vanwege hoofd oorzaak/probleem _P<sub>i</sub>_ ?" waar _het_ aantal mogelijke hoofd oorzaken is. Als u deze vraag wilt beantwoorden, labelt u X records vóór het mislukken van een Asset als ' overs welke mislukken vanwege hoofd oorzaak _p<sub>i</sub>_ ' (label = _P<sub>i</sub>_ ). Label alle records als zijnde "normale" (label = 0). In deze methode zijn labels ook categorische (Zie afbeelding 6).

![Afbeelding 6. Voor Spellings labels voor de hoofd oorzaak voor de classificatie met een klasse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Afbeelding 6. Labels voor classificatie ROC voor hoofdmap oorzaak voorspelling

Het model wijst een fout kans toe als gevolg van elke _P<sub>i</sub>_  en de kans op geen fout. Deze kansen kunnen worden besteld door magnitude om toe te staan voorspelling van de problemen die zijn ondergebracht in de toekomst.

De vraag hier is: "welke onderhoudsacties raden u aan om na een storing?" Voor het beantwoorden van deze vraag is het _niet nodig om een toekomstige horizon te worden gekozen_, omdat het model niet in de toekomst fout voorspelt. De meest waarschijnlijke hoofd oorzaak wordt alleen voor speld _wanneer de fout al is opgetreden_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Training, validatie en het testen van de methoden voor predictief onderhoud
Het [proces van de team data Science](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) biedt een volledige dekking van het model Train-Test-validate Cycle. Deze sectie wordt besproken aspecten die uniek is voor PdM.

### <a name="cross-validation"></a>Kruisvalidatie
Het doel van [Kruis validatie](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) is het definiëren van een gegevensset om het model in de trainings fase te testen. Deze gegevensset wordt de _validatieset_genoemd. Deze techniek helpt u bij het beperken van _problemen zoals het_ overschaalpen en biedt inzicht in de manier waarop het model voor een onafhankelijke gegevensset wordt gegeneraliseerd. Dat wil zeggen, de gegevens in een onbekende ingesteld die kan worden van een echt probleem. De routine training en testen voor PdM moet rekening gehouden met de verschillende aspecten van de tijd beter generaliseren op verborgen gegevens uit de toekomst.

Veel machine learning-algoritmen, is afhankelijk van een aantal hyperparameters die de modelprestaties aanzienlijk kunt wijzigen. De optimale waarden van deze hyperparameters worden niet automatisch worden berekend wanneer het model te trainen. Deze moeten worden opgegeven door de gegevenswetenschapper. Er zijn verschillende manieren uit het vinden van goede waarden van hyperparameters.

Het meest voorkomende is een _Kruis validatie met k-vouwen_ waarmee de voor beelden wille keurig worden gesplitst in _k_ vouwen. Voer voor elke set Hyper parameters waarden het leer algoritme _k_ keer uit. Bij elke iteratie gebruikt u de voorbeelden in de huidige Vouw als een set validatie en de rest van de voorbeelden als een trainingsset. Het algoritme trainen via training voorbeelden en de maatstaven voor prestaties compute via validatie voorbeelden. Aan het einde van deze lus berekent u het gemiddelde van de metrische gegevens voor prestaties van _k_ . Kiezen welke waarvoor de optimale gemiddelde prestaties voor elke set van hyperparameter waarden. De taak van het kiezen van hyperparameters is vaak experimentele karakter.

Problemen met PdM, worden gegevens geregistreerd als een tijdreeks van gebeurtenissen die afkomstig uit verschillende gegevensbronnen zijn. Deze records kunnen worden geordend op basis van de tijd van labels. Als de gegevensset in _wille keurige volg orde_ wordt gesplitst in de trainings-en validatieset, _kunnen enkele voor beelden van de training later in de tijd worden opgegeven dan sommige validatie voorbeelden_. Toekomstige prestaties van afstemming-waarden worden geschat op basis van een aantal gegevens die zijn aangekomen _voordat_ het model werd getraind. Deze schattingen mogelijk overmatig optimistische, met name als de time series niet een stilstaande is en zich verder na verloop van tijd ontwikkelt. Als gevolg hiervan kunnen de waarden van de gekozen hyperparameter suboptimale zijn.

De aanbevolen manier is om de voor beelden te splitsen in trainings-en validatie sets op een _tijdrovende_ manier, waarbij alle validatie voorbeelden later in de tijd vallen dan alle voor beelden van training. Voor elke set hyperparameter waarden, de algoritme te trainen via de set trainingsgegevens. Meten van het model prestaties ten opzichte van de dezelfde set voor validatie. Kies hyperparameter waarden waarin de beste prestaties. Hyperparameter waarden door train/validatie split resulteren in betere toekomstige modelprestaties dan door de waarden willekeurig gekozen door kruisvalidatie gekozen.

Het uiteindelijke model kan worden gegenereerd door een leeralgoritme training over de hele trainingsgegevens met behulp van de beste hyperparameter-waarden.

### <a name="testing-for-model-performance"></a>Voor de modelprestaties testen
Als een model is gemaakt, is een schatting van de toekomstige prestaties op nieuwe gegevens vereist. Een goede schatting maken van de metrische gegevens voor prestaties van hyperparameter waarden berekend ten opzichte van de validatie-set, of een gemiddelde prestaties metrische gegevens, berekend vanaf kruisvalidatie. Deze schattingen zijn vaak overmatig optimistische. Het bedrijf wellicht vaak enkele aanvullende richtlijnen over hoe ze graag willen testen van het model.

De aanbevolen manier voor PdM is de voor beelden op een _tijdrovende_ manier te splitsen in trainings-, validatie-en test gegevens sets. Alle voorbeelden van de test moet later in de tijd dan alle de trainings- en validatie voorbeelden. Na de splitsing, het model is gegenereerd en meten van de prestaties, zoals eerder beschreven.

Wanneer tijdreeksen zijn stilstaande en gemakkelijk te voorspellen, genereert willekeurige en tijdsafhankelijke benaderingen vergelijkbaar schattingen van toekomstige prestaties. Maar wanneer tijdreeksen zijn niet-stilstand en/of moeilijk te voorspellen, de aanpak van afhankelijk zijn van tijd meer realistische schattingen van toekomstige prestaties wordt gegenereerd.

### <a name="time-dependent-split"></a>Tijdafhankelijke splitsen
Deze sectie wordt beschreven aanbevolen procedures voor het implementeren van afhankelijk zijn van tijd splitsen. Een wederzijdse splitsing tijdsafhankelijke tussen trainings- en testsets wordt hieronder beschreven.

Wordt ervan uitgegaan dat u een stroom gebeurtenissen, zoals de metingen van diverse sensoren voorzien van een tijdstempel. Functies en labels van training en test voorbeelden via meteen met meerdere gebeurtenissen gedefinieerd. U kunt bijvoorbeeld voor binaire classificatie functies maken op basis van gebeurtenissen in het verleden en labels maken op basis van toekomstige gebeurtenissen binnen ' X ' tijds eenheden in de toekomst (Zie de secties over [functie techniek](#feature-engineering) en model technieken). Het labelen tijdsbestek van een voorbeeld komt dus hoger is dan de periode van de functies ervan.

Voor een tijdgebonden splitsing kiest u een time-outwaarde voor de _training T<sub>c</sub>_  waarmee een model kan worden getraind, waarbij Hyper parameters is afgestemd op het gebruik van historische gegevens tot T<sub>c</sub>. Om te voor komen dat toekomstige labels meer dan T<sub>c</sub> in de trainings gegevens worden gelekt, kiest u de meest recente tijd om de voor beelden van de training te markeren als X eenheden vóór T<sub>c</sub>. Elke vierkant vertegenwoordigt in het voorbeeld in afbeelding 7 wordt weergegeven, een record in de gegevensset waarop functies en labels worden berekend, zoals hierboven beschreven. De afbeelding ziet u de records die moeten worden geplaatst in trainings- en testsets voor X = 2 en W = 3:

![Afbeelding 7. Tijdafhankelijke splitsing voor binaire classificatie](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Afbeelding 7. Tijdafhankelijke splitsing voor binaire classificatie

De groene kwadraten vertegenwoordigen records die behoren tot de tijdseenheden die kunnen worden gebruikt voor de training. Elk voor beeld van een training wordt gegenereerd door de laatste drie Peri Oden te overwegen voor het genereren van onderdelen, en twee toekomstige Peri Oden voor het labelen vóór T<sub>c</sub>. Wanneer een deel van de twee toekomstige Peri Oden groter is dan T<sub>c</sub>, moet u dat voor beeld uitsluiten van de gegevensset voor training, omdat er geen zicht baarheid wordt aangenomen buiten t<sub>c</sub>.

De zwarte kwadraten vertegenwoordigen de records van de laatste gelabelde gegevensset die niet kunnen worden gebruikt in de set trainingsgegevens, krijgt de bovengenoemde beperking. Deze records worden ook niet gebruikt voor het testen van gegevens, omdat ze vóór T<sub>c</sub>vallen. Bovendien afhankelijk hun labelen meteen gedeeltelijk van het tijdsbestek training die is niet ideaal. Trainings- en testset gegevens moet afzonderlijke labelen meteen om te voorkomen dat gegevens worden gelekt label hebben.

De techniek die tot nu toe is besproken, maakt overlap ping tussen de voor beelden van training en testen die een tijds tempel in de buurt van T<sub>c</sub>hebben. Een oplossing voor een grotere schei ding is het uitsluiten van voor beelden die binnen een tijds eenheid van T<sub>c</sub> vallen van de testset. Maar een agressief splitsen, is afhankelijk van beschikbaarheid van voldoende gegevens.

Regressiemodellen gebruikt voor het voorspellen van de resterende Levensduur meer ondervindt van het Lekkageprobleem. Met behulp van de methode willekeurige split leidt tot een extreme sprake van redundante aanpassing van labels. Voor regressie problemen moet de splitsing zodanig zijn dat de records die deel uitmaken van activa met storingen voordat T<sub>c</sub> naar de Trainingsset gaat. Records van activa met fouten na de afsluitdatum gaat u in de testset.

Een andere aanbevolen procedure voor het splitsen van gegevens voor trainings- en testdoeleinden is het gebruik van een splitsing van asset-ID. De splitsing moet zodanig zijn dat geen van de activa die worden gebruikt in de trainingsset worden gebruikt voor het testen van de modelprestaties. Met deze benadering is een model voor een grotere kans realistischer resultaten voorzien van nieuwe activa.

### <a name="handling-imbalanced-data"></a>Imbalanced gegevens verwerken
Als er meer voor beelden zijn van een klasse dan andere, wordt in de classificatie problemen aangegeven dat de gegevensset niet meer in _balans_moet worden gegeven. In het ideale geval voldoende vertegenwoordigers van elke klasse in de trainingsgegevens voorkeur om in te schakelen onderscheid worden gemaakt tussen verschillende klassen. Als een klasse minder dan 10% van de gegevens is, wordt de gegevens geacht imbalanced. De ondervertegenwoordigde klasse wordt een _minderheids klasse_genoemd.

Problemen met veel PdM geconfronteerd met dergelijke imbalanced gegevenssets, waarbij een klasse is ernstig vertegenwoordigde vergeleken met de andere klasse of klassen. In sommige gevallen kan de klasse oversampling alleen 0,001% van het totale aantal gegevenspunten vormen. Klasse imbalance is niet uniek is voor PdM. Andere domeinen waar problemen en afwijkingen zelden worden geconfronteerd met een vergelijkbaar probleem voor voorbeelden, fraude te detecteren en binnendringing in het netwerk. Deze fouten zijn vormen van de klasse oversampling voorbeelden.

Met de klasse uit balans zijn gegevens, prestaties van de meeste standaard learning-algoritmen ermee is geknoeid, omdat ze zijn bedoeld om te minimaliseren, de frequentie van algemene fouten. Voor een verzameling met 99% negatieve en positieve voorbeelden van 1%, kan een model dat 99% nauwkeurigheid door alle exemplaren negatief labels worden weergegeven. Maar het model wordt niet het classificeren van alle positieve voorbeelden. dus zelfs als de nauwkeurigheid hoog is, het algoritme niet een nuttig is. Daarom zijn conventionele evaluatie-metrische gegevens, zoals de _algehele nauw keurigheid van de fout_ , onvoldoende voor Learning. Wanneer u wordt geconfronteerd met imbalanced gegevenssets, worden andere metrische gegevens worden gebruikt voor de evaluatie van model:
- Precisie
- Terughalen
- F1-scores
- Kosten aangepast ROC (ontvanger operationele kenmerken)

Zie [Model Evaluation](#model-evaluation)(Engelstalig) voor meer informatie over deze metrische gegevens.

Er zijn echter enkele methoden die verhelpen klasse imbalance probleem. De twee belangrijkste zijn de _bemonsterings technieken_ en _kosten gevoelige lessen_.

#### <a name="sampling-methods"></a>Steekproeven methoden
Imbalanced learning omvat het gebruik van methoden voor het wijzigen van de set trainingsgegevens aan een set met gelijke taakverdeling gegevens. Steekproeven methoden zijn niet moet worden toegepast op de testset. Hoewel er verschillende bemonsterings technieken zijn, zijn de meeste direct doorstuur servers _wille keurige oversteek proef_ en _onder steek proeven_.

_Wille keurige oversteek proeven_ moeten een wille keurig voor beeld van een minderheids klasse selecteren, deze voor beelden repliceren en toevoegen aan de set met trainings gegevens. Als gevolg daarvan kan het aantal voorbeelden in klasse oversampling wordt verhoogd en uiteindelijk in balans brengen het aantal voorbeelden van verschillende klassen. Een nadeel van minority is dat de classificatie worden ook specifieke, waardoor er sprake van redundante aanpassing van labels kunnen leiden tot meerdere exemplaren van bepaalde voorbeelden. Training voor hoge nauwkeurigheid kan worden weergegeven in het model, maar het is mogelijk dat de prestaties niet waren herkend testgegevens suboptimale.

Daarentegen _wordt een wille keurig voor_ beeld van een meerderheids klasse geselecteerd en worden de voor beelden uit de set met trainings gegevens verwijderd. Voorbeelden van de meeste klasse verwijderen kan echter de classificatie belangrijke concepten met betrekking tot de meeste klasse missen veroorzaken. _Hybride steek proeven_ waarbij de klasse minderheids meer wordt bemonsterd en de hoofd klasse is op hetzelfde moment een andere levensvat bare benadering.

Er zijn veel steekproeven geavanceerde technieken. De techniek die is gekozen, is afhankelijk van de eigenschappen van gegevens en de resultaten van iteratieve experimenten door de gegevenswetenschapper.

#### <a name="cost-sensitive-learning"></a>Kosten van gevoelige learning
In PdM zijn de fouten die deel uitmaken van de klasse oversampling meer geïnteresseerd dan de normale voorbeelden. Daarom is de focus voornamelijk op de prestaties van het algoritme op fouten. Onjuist voorspellen van een klasse positief als negatief zijn klasse kunt duurder dan het vice versa. Deze situatie wordt vaak aangeduid als ongelijk verlies of onjuiste classificeren elementen asymmetrische kosten voor verschillende klassen. De ideale classificatie moet hoge nauwkeurigheid via de klasse oversampling leveren zonder toe te boeten op de nauwkeurigheid voor de meeste-klasse.

Er zijn meerdere manieren om deze afstemming. Om te beperken van het probleem van ongelijk verlies, een hoge kosten toewijzen aan foutieve indeling van de klasse oversampling en probeer het minimaliseren van de totale kosten. Met algoritmen als _SVMs (Support Vector machines)_ wordt deze methode inherent toegepast, doordat de kosten van positieve en negatieve voor beelden tijdens de training kunnen worden opgegeven. Op dezelfde manier geven de Boosting methoden zoals _versterkte beslissings structuren_ doorgaans goede prestaties weer met gegevens die niet in evenwicht zijn.

## <a name="model-evaluation"></a>Model-evaluatie
Profieltoewijzingen classificatie is een belangrijk probleem voor PdM scenario's waar de kosten van vals alarm voor het bedrijf hoog is. Bijvoorbeeld, een beslissing op grond van een vliegtuig op basis van een onjuist voorspelling van motoren ontstaan planningen worden onderbroken en reisplannen. Een machine offline van een assemblagelijn duurt kan leiden tot verlies van omzet. Model-evaluatie met de juiste metrische prestatiegegevens tegen nieuwe testgegevens is daarom essentieel.

Metrische gegevens voor normale prestaties gebruikt voor het evalueren van modellen PdM worden hieronder besproken:

- [Nauw keurigheid](https://en.wikipedia.org/wiki/Accuracy_and_precision) is de populairste waarde die wordt gebruikt voor het beschrijven van de prestaties van een classificatie. Maar nauwkeurigheid gevoelig is voor gegevens-distributies en is een inefficiënte meting voor scenario's met imbalanced gegevenssets. Andere metrische gegevens worden in plaats daarvan gebruikt. Hulpprogram ma's als [Verwar ring matrix](https://en.wikipedia.org/wiki/Confusion_matrix) worden gebruikt om de nauw keurigheid van het model te berekenen en daarom te bepalen.
- De [precisie](https://en.wikipedia.org/wiki/Precision_and_recall) van PdM-modellen heeft betrekking op de frequentie van valse waarschuwingen. Lagere precisie van het model is in het algemeen komt overeen met een hoger aantal vals alarm.
- Frequentie van [intrekken](https://en.wikipedia.org/wiki/Precision_and_recall) geeft aan hoeveel fouten in de testset correct zijn geïdentificeerd door het model. Hogere intrekken tarieven betekenen dat het model is voltooid bij het identificeren van de waarde true fouten.
- [F1 Score](https://en.wikipedia.org/wiki/F1_score) is het harmonische gemiddelde van Precision en intrekken, waarbij de waarde tussen 0 (slechtste) en 1 (beste) ligt.

Voor binaire classificatie
- De [werk curven van ontvangers (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is ook een populaire metrische waarde. In de ROC-curve, wordt de modelprestaties op basis van een vaste operationele punt op de ROC geïnterpreteerd.
- _Decile-tabellen_ en _Lift grafieken_ zijn echter meer informatie over PdM-problemen. Ze richten op de positieve klasse (fouten) en een complexere beeld van de algoritme-prestaties dan ROC-curve bevatten.
  - _Decile-tabellen_ worden gemaakt met behulp van test voorbeelden in een aflopende volg orde van fout kansen. De geordende voorbeelden worden vervolgens gegroepeerd in deciles (10% van de voorbeelden met de hoogste kans en 20%, 30%, enzovoort). De /(random baseline) verhouding (true-positief-ratio) voor elke decile helpt de prestaties van de algoritme bij elke decile schatten. De willekeurige basislijn duurt op basis van waarden 0.1, 0.2, enzovoort.
  - Met een [Lift diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) wordt het decile ware positieve rente vergeleken met een wille keurig positief positieve rente voor alle deciles. De eerste deciles zijn meestal de focus van de resultaten, omdat ze de grootste voordelen worden weergegeven. Eerste deciles kunnen ook worden gezien als representatief zijn voor 'risico', wanneer die wordt gebruikt voor PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model uitoefening voor predictief onderhoud

Het voordeel is van de data science oefening gerealiseerde alleen wanneer het getrainde model wordt gemaakt operationele. Dat wil zeggen, moet het model worden geïmplementeerd in de bedrijfssystemen om voorspellingen op basis van nieuwe, eerder niet waren herkend, gegevens te maken.  De nieuwe gegevens moeten op twee manieren overeenkomen met de _model handtekening_ van het getrainde model:
- alle functies moet aanwezig zijn in elke logische instantie (bijvoorbeeld een rij in een tabel) van de nieuwe gegevens.
- de nieuwe gegevens vooraf moeten worden verwerkt en elk van de functies ontworpen, op dezelfde manier als de trainingsgegevens.

Het bovenstaande proces wordt vermeld op veel manieren in academische en documentatie. Maar met de volgende instructies betekent dat hetzelfde te doen:
- _Nieuwe gegevens scoren_ met behulp van het model
- _Het model Toep assen_ op nieuwe gegevens
- Het model _operationeel maken_
- Het model _implementeren_
- _Voer het model uit_ op basis van nieuwe gegevens

Zoals eerder gezegd, wijkt model uitoefening voor PdM af van de peers. Scenario's met betrekking tot anomalie detectie en fout detectie implementeren doorgaans _online scores_ (ook wel _realtime scores_genoemd). Hier vertrouwt _het model elk_ binnenkomend record en retourneert een voor spelling. Voor detectie van afwijkingen, de voorspelling wordt aangegeven dat er een anomalie is opgetreden (voorbeeld: een klasse SVM). Foutdetectie is het type of de klasse van de fout.

In tegens telling tot het uitvoeren van een _batch-Score_. Om te voldoen aan de handtekening van het model, moeten de functies in de nieuwe gegevens op dezelfde manier als de trainingsgegevens worden ontworpen. Voor de grote gegevenssets die wordt gebruikt voor nieuwe gegevens, worden de functies samengevoegd tijdens een bepaalde tijdvensters en beoordeeld in batch. Batch scores worden doorgaans uitgevoerd in gedistribueerde systemen zoals [Spark](https://spark.apache.org/) of [Azure batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Er zijn een aantal alternatieven - beide suboptimale:
- Streaming gegevensengines ondersteunt aggregatie in windows in het geheugen. Zo kan worden aangevoerd dat deze ondersteuning bieden voor online scoren. Maar deze systemen zijn geschikt voor compacte gegevens in smalle windows van tijd, of sparse-elementen via breder windows. Ze kunnen niet schalen voor de compacte gegevens op bredere tijdvensters, zoals te zien is in PdM scenario's.
- Als het batch scoring niet beschikbaar is, wordt de oplossing is om aan te passen online scoring voor het afhandelen van nieuwe gegevens in batches van kleine tegelijk.

## <a name="solution-templates-for-predictive-maintenance"></a>Sjablonen voor oplossingen voor Voorspellend onderhoud

Het laatste gedeelte van deze handleiding bevat een lijst van PdM oplossingssjablonen, zelfstudies en experimenten die zijn geïmplementeerd in Azure. Deze toepassingen PdM kunnen worden geïmplementeerd in een Azure-abonnement binnen enkele minuten in sommige gevallen. Ze kunnen worden gebruikt als proof of concept demo's, sandboxes om te experimenteren met alternatieven of accelerators voor daadwerkelijke productie-implementaties. Deze sjablonen bevinden zich in de [Azure AI Gallery](https://gallery.azure.ai) of [Azure github](https://github.com/Azure). Deze verschillende voorbeelden zal worden opgenomen in deze oplossingssjabloon na verloop van tijd.

| # | Titel | Beschrijving |
|--:|:------|-------------|
| 2 | [Oplossings sjabloon Azure predictief onderhoud](https://github.com/Azure/AI-PredictiveMaintenance) | Een open-source oplossings sjabloon waarmee Azure ML-modellering en een volledige Azure-infra structuur kunnen worden ondersteund die ondersteuning bieden voor voorspellende onderhouds scenario's in de context van IoT externe controle. |
| 3 | [Diep gaande informatie voor predictief onderhoud](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure notebook met een demo oplossing voor het gebruik van LSTM (Long Short-Term Memory) netwerken (een klasse van huidige Neural-netwerken) voor predictief onderhoud, met een [blog bericht over dit voor beeld](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Model gids voor voor speld onderhoud in R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Handleiding voor PdM modellen met behulp van scripts in R.|
| 5 | [Azure predictief onderhoud voor B.v.](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Een van de eerste PdM oplossingssjablonen op basis van Azure ML v1.0 voor het onderhoud van vliegtuigmotoren. Deze handleiding afkomstig is van dit project. |
| 6 | [Azure AI-werkset voor IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI in het IoT Edge met behulp van tensor flow; Toolkit verpakt uitgebreide leer modellen in Azure IoT Edge-compatibele docker-containers en maakt deze modellen beschikbaar als REST Api's.
| 7 | [Azure IoT-voor speld onderhoud](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite, pc's - vooraf geconfigureerde oplossing. Onderhoud van vliegtuigmotoren-PdM sjabloon met IoT Suite. [Een ander document en een](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) [scenario](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) met betrekking tot hetzelfde project. |
| 8 | [Voorspellende onderhouds sjabloon met SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo van resterende levensduur scenario op basis van R services. |
| 9 | [Model gids voor voor speld onderhoud](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Functie voor het bijhouden van vlieg tuigen die zijn ontworpen met R met [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) en [gegevens sets](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) en [Azure notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) en [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) in AzureML v 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Cursussen voor voorspeld onderhoud

Microsoft Azure biedt leertrajecten voor de basisconcepten achter PdM technieken, naast inhoud en -training voor algemene AI-concepten en procedures.

| Training-resource  | Beschikbaarheid |
|:-------------------|--------------|
| [Leer traject voor PdM met behulp van structuren en wille keurige forests](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Openbaar | 
| [Leer traject voor PdM met behulp van diep gaande lessen](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Openbaar |
| [AI-ontwikkelaar op Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Openbaar |
| [Micro soft AI school](https://aischool.microsoft.com/learning-paths) | Openbaar |
| [Azure AI Learning vanuit GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Openbaar |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Openbaar |
| [Micro soft AI YouTube webinars](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Openbaar |
| [Micro soft AI-weer gave](https://channel9.msdn.com/Shows/AI-Show) | Openbaar |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partners |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partners |

Bovendien zijn gratis MOOCS (massive open online cursussen) op AI aangeboden online door academische instellingen, zoals Stanford en MIT en andere educatieve bedrijven.
