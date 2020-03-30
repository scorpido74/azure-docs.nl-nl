---
title: Inleiding tot microservices op Azure
description: Een overzicht van waarom het bouwen van cloudapplicaties met een microservices-aanpak belangrijk is voor de ontwikkeling van moderne toepassingen en hoe Azure Service Fabric een platform biedt om dit te bereiken.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750629"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Waarom gebruik maken van een microservices benadering van het bouwen van applicaties

Voor softwareontwikkelaars is het incalculeren van een toepassing in onderdelen niets nieuws. Meestal wordt een gelaagde benadering gebruikt, met een back-end store, bedrijfslogica op het middenniveau en een front-end gebruikersinterface (UI). Wat *de* afgelopen jaren is veranderd, is dat ontwikkelaars gedistribueerde applicaties voor de cloud bouwen.

Hier zijn enkele veranderende zakelijke behoeften:

* Een service die op grote schaal is gebouwd en uitgevoerd om klanten in nieuwe geografische regio's te bereiken.
* Snellere levering van functies en mogelijkheden om op een flexibele manier te reageren op de eisen van klanten.
* Verbeterde gebruik van resources om de kosten te verlagen.

Deze zakelijke behoeften zijn van invloed op *de manier waarop* we applicaties bouwen.

Zie [Microservices: Een toepassingsrevolutie die wordt aangedreven door de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)voor meer informatie over de Azure-benadering van microservices.

## <a name="monolithic-vs-microservices-design-approach"></a>Monolithische vs. microservices ontwerpbenadering

Toepassingen evolueren in de loop van de tijd. Succesvolle toepassingen evolueren door nuttig te zijn voor mensen. Mislukte toepassingen evolueren niet en worden uiteindelijk afgeschaft. Hier is de vraag: hoeveel weet je over uw eisen vandaag en wat ze zullen worden in de toekomst? Stel dat u een rapportageaanvraag bouwt voor een afdeling in uw bedrijf. U weet zeker dat de toepassing alleen binnen het bereik van uw bedrijf valt en dat de rapporten niet lang worden bewaard. Uw aanpak zal anders zijn dan die van bijvoorbeeld het bouwen van een service die video-inhoud levert aan tientallen miljoenen klanten.

Soms is het verkrijgen van iets uit de deur als een proof of concept is de drijvende factor. U weet dat de toepassing later opnieuw kan worden ontworpen. Er is weinig zin in over-engineering iets dat nooit wordt gebruikt. Aan de andere kant, wanneer bedrijven bouwen voor de cloud, is de verwachting groei en gebruik. Groei en schaal zijn onvoorspelbaar. We willen snel prototype en weten ook dat we op een pad zijn dat toekomstig succes aankan. Dit is de lean startup aanpak: bouwen, meten, leren en herhalen.

Tijdens het client/server tijdperk, hebben we de neiging om ons te concentreren op het bouwen van gelaagde toepassingen met behulp van specifieke technologieën in elke laag. De term *monolithische* toepassing is ontstaan om deze benaderingen te beschrijven. De interfaces meestal tussen de lagen, en een meer strak gekoppeld ontwerp werd gebruikt tussen componenten binnen elke laag. Ontwikkelaars ontwierpen en refactoren klassen die werden gecompileerd in bibliotheken en gekoppeld aan een paar uitvoerbare bestanden en DLL's.

Er zijn voordelen aan een monolithische ontwerpbenadering. Monolithische toepassingen zijn vaak eenvoudiger te ontwerpen en gesprekken tussen componenten zijn sneller omdat deze gesprekken vaak via interprocess communicatie (IPC) gaan. Ook, iedereen test een enkel product, die de neiging om een efficiënter gebruik van human resources. Het nadeel is dat er een strakke koppeling is tussen gelaagde lagen en dat je geen afzonderlijke componenten schalen. Als u correcties of upgrades moet uitvoeren, moet u wachten tot anderen hun tests voltooien. Het is moeilijker om wendbaar te zijn.

Microservices pakken deze nadelen aan en sluiten beter aan bij de voorgaande bedrijfsvereisten. Maar ze hebben ook zowel voordelen als verplichtingen. De voordelen van microservices zijn dat elk doorgaans eenvoudigere bedrijfsfunctionaliteit inkapselt, die u zelfstandig opschalen, testen, implementeren en beheren. Een belangrijk voordeel van een microservices-aanpak is dat teams meer worden gedreven door bedrijfsscenario's dan door technologie. Kleinere teams ontwikkelen een microservice op basis van een klantscenario en gebruiken alle technologieën die ze willen gebruiken.

Met andere woorden, de organisatie hoeft geen technologie te standaardiseren om microservice-toepassingen te onderhouden. Individuele teams die eigen diensten kunnen doen wat zinvol is voor hen op basis van teamexpertise of wat het meest geschikt is om het probleem op te lossen. In de praktijk verdient een reeks aanbevolen technologieën, zoals een bepaalde NoSQL-winkel of webtoepassingsframework, de voorkeur.

Het nadeel van microservices is dat u meer afzonderlijke entiteiten moet beheren en moet omgaan met complexere implementaties en versiebeheer. Netwerkverkeer tussen de microservices neemt toe, evenals de bijbehorende netwerklatencies. Veel spraakzame, gedetailleerde diensten kunnen leiden tot een performance nachtmerrie. Zonder tools om u te helpen deze afhankelijkheden te bekijken, is het moeilijk om het hele systeem te zien.

Standaarden maken de microservices aanpak werken door te specificeren hoe te communiceren en tolereren alleen de dingen die je nodig hebt van een dienst, in plaats van rigide contracten. Het is belangrijk om deze contracten vooraf in het ontwerp te definiëren, omdat services onafhankelijk van elkaar worden bijgewerkt. Een andere beschrijving bedacht voor het ontwerpen met een microservices aanpak is "fijnkorrelige service-georiënteerde architectuur (SOA)."

***Op zijn eenvoudigste, de microservices ontwerp benadering gaat over een ontkoppelde federatie van diensten, met onafhankelijke wijzigingen in elk en overeengekomen normen voor communicatie.***

Naarmate er meer cloudtoepassingen worden geproduceerd, hebben mensen ontdekt dat deze afbraak van de algehele toepassing in onafhankelijke, scenariogerichte services een betere langetermijnbenadering is.

## <a name="comparison-between-application-development-approaches"></a>Vergelijking tussen benaderingen voor toepassingsontwikkeling

![Ontwikkeling van servicefabric-platformtoepassingen][Image1]

1) Een monolithische toepassing bevat domeinspecifieke functionaliteit en is normaal gesproken onderverdeeld in functionele lagen zoals web, business en gegevens.

2) U schaalt een monolithische toepassing door deze te klonen op meerdere servers/virtuele machines/containers.

3) Een microservice-toepassing scheidt functionaliteit in afzonderlijke kleinere services.

4) De aanpak van microservices wordt geschaald door elke service onafhankelijk te implementeren en instances van deze services te maken voor servers/virtuele machines/containers.

Ontwerpen met een microservices-benadering is niet geschikt voor alle projecten, maar het sluit wel beter aan bij de eerder beschreven bedrijfsdoelstellingen. Beginnen met een monolithische benadering kan zinvol zijn als je weet dat je de mogelijkheid hebt om de code later opnieuw te werken in een microservices-ontwerp. Meer in het algemeen, je begint met een monolithische toepassing en langzaam breken in fasen, te beginnen met de functionele gebieden die moeten meer schaalbaar of wendbaar.

Wanneer u een microservices-benadering gebruikt, stelt u uw toepassing van vele kleine services samen. Deze services worden uitgevoerd in containers die worden geïmplementeerd in een cluster van machines. Kleinere teams ontwikkelen een service die zich richt op een scenario en elke service onafhankelijk test, versie, implementatie en schaal, zodat de hele toepassing kan evolueren.

## <a name="what-is-a-microservice"></a>Wat is een microservice?

Er zijn verschillende definities van microservices. Maar de meeste van deze kenmerken van microservices worden algemeen aanvaard:

* Een klant- of bedrijfsscenario inkapselen. Welk probleem los je op?
* Ontwikkeld door een klein engineeringteam.
* Geschreven in elke programmeertaal, met behulp van elk kader.
* Bestaan uit code, en optioneel staat, die beide onafhankelijk zijn geversioned, geïmplementeerd en geschaald.
* Communiceer met andere microservices via goed gedefinieerde interfaces en protocollen.
* Hebben unieke namen (URL's) die worden gebruikt om hun locatie op te lossen.
* Blijf consistent en beschikbaar in aanwezigheid van storingen.

Om dat samen te vatten:

***Microservice-toepassingen bestaan uit kleine, onafhankelijk geversieerde en schaalbare klantgerichte services die met elkaar communiceren via standaardprotocollen met goed gedefinieerde interfaces.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Geschreven in elke programmeertaal, met behulp van elk kader

Als ontwikkelaars willen we vrij zijn om een taal of kader te kiezen, afhankelijk van onze vaardigheden en de behoeften van de service die we creëren. Voor sommige services u de prestatievoordelen van C++ boven alles waarderen. Voor anderen, het gemak van beheerde ontwikkeling die je krijgt van C # of Java misschien wel belangrijker. In sommige gevallen moet u mogelijk een specifieke partnerbibliotheek, gegevensopslagtechnologie of methode gebruiken om de service aan clients bloot te stellen.

Nadat u een technologie hebt gekozen, moet u rekening houden met het operationele of levenscyclusbeheer en de schaling van de service.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Hiermee kunnen code en status onafhankelijk worden geversioneerd, geïmplementeerd en geschaald

Het maakt niet uit hoe u uw microservices, de code en de status schrijft, onafhankelijk moet implementeren, upgraden en schalen. Dit probleem is moeilijk op te lossen omdat het neerkomt op uw keuze van technologieën. Voor schalen is het een uitdaging om te begrijpen hoe u zowel de code als de status verdelen (of sharden). Wanneer de code en status verschillende technologieën gebruiken, wat tegenwoordig gebruikelijk is, moeten de implementatiescripts voor uw microservice ze beide kunnen schalen. Deze scheiding gaat ook over flexibiliteit en flexibiliteit, zodat u een aantal van de microservices upgraden zonder ze allemaal tegelijk te hoeven upgraden.

Laten we even terugkomen op onze vergelijking van de monolithische en microservices benaderingen. Dit diagram toont de verschillen in de benadering van de opslagstatus:

#### <a name="state-storage-for-the-two-approaches"></a>Staatsopslag voor de twee benaderingen

![Service Fabric-platformstatusopslag][Image2]

***De monolithische benadering, aan de linkerkant, heeft een enkele database en lagen van specifieke technologieën.***

***De microservices benadering, aan de rechterkant, heeft een grafiek van onderling verbonden microservices waar de staat is meestal scoped om de microservice en verschillende technologieën worden gebruikt.***

In een monolithische benadering gebruikt de toepassing doorgaans één database. Het voordeel van het gebruik van één database is dat deze zich op één locatie bevindt, waardoor het eenvoudig te implementeren is. Elk onderdeel kan één tabel hebben om de status op te slaan. Teams moeten de staat strikt scheiden, wat een uitdaging is. Het is onvermijdelijk dat iemand in de verleiding komt om een kolom toe te voegen aan een bestaande klantentabel, een join tussen tabellen uit te brengen en afhankelijkheden te maken op de opslaglaag. Nadat dit gebeurt, u geen afzonderlijke componenten schalen.

In de aanpak van microservices beheert en slaat elke service zijn eigen status op. Elke service is verantwoordelijk voor het schalen van zowel code als status samen om te voldoen aan de eisen van de service. Een nadeel is dat wanneer u weergaven of query's van de gegevens van uw toepassing moet maken, u in meerdere statusopslagen moet opvragen. Dit probleem wordt meestal opgelost door een aparte microservice die een weergave over een verzameling microservices bouwt. Als u meerdere geïmproviseerde query's op de gegevens moet uitvoeren, u overwegen de gegevens van elke microservice te schrijven naar een gegevensopslagservice voor offline analyses.

Microservices zijn geversioned. Het is mogelijk voor verschillende versies van een microservice naast elkaar te draaien. Een nieuwere versie van een microservice kan mislukken tijdens een upgrade en moet worden teruggedraaid naar een eerdere versie. Versiebeheer is ook handig voor A /B-tests, waarbij verschillende gebruikers verschillende versies van de service ervaren. Het is bijvoorbeeld gebruikelijk om een microservice te upgraden voor een specifieke set klanten om nieuwe functionaliteit te testen voordat deze breder wordt uitgerold.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Werkt samen met andere microservices via goed gedefinieerde interfaces en protocollen

In de afgelopen 10 jaar is uitgebreide informatie gepubliceerd waarin communicatiepatronen in servicegerichte architecturen worden beschreven. Over het algemeen gebruikt servicecommunicatie een REST-benadering met HTTP- en TCP-protocollen en XML of JSON als serialisatie-indeling. Vanuit een interface perspectief, het gaat over het nemen van een web design aanpak. Maar niets zou u van het gebruiken van binaire protocollen of uw eigen gegevensformaten moeten tegenhouden. Wees ervan bewust dat mensen een hardere tijd met behulp van uw microservices hebben als deze protocollen en formaten niet openlijk beschikbaar zijn.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Heeft een unieke naam (URL) die wordt gebruikt om de locatie op te lossen

Uw microservice moet adresseerbaar zijn, waar deze ook draait. Als je denkt over machines en welke is het uitvoeren van een bepaalde microservice, dingen kunnen snel gaan slecht.

Op dezelfde manier waarop DNS een bepaalde URL naar een bepaalde machine oplost, heeft uw microservice een unieke naam nodig, zodat de huidige locatie kan worden ontdekt. Microservices hebben adresseerbare namen nodig die onafhankelijk zijn van de infrastructuur waarop ze draaien. Dit houdt in dat er een interactie is tussen de manier waarop uw service wordt geïmplementeerd en hoe deze wordt ontdekt, omdat er een serviceregister moet zijn. Wanneer een machine uitvalt, moet de registerservice u vertellen waar de service naartoe is verplaatst.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Blijft consistent en beschikbaar in aanwezigheid van storingen

Omgaan met onverwachte storingen is een van de moeilijkste problemen op te lossen, vooral in een gedistribueerd systeem. Veel van de code die we schrijven als ontwikkelaars is voor het afhandelen van uitzonderingen. Tijdens het testen besteden we ook de meeste tijd aan exception handling. Het proces is meer betrokken dan het schrijven van code om fouten te behandelen. Wat gebeurt er als de machine waarop de microservice draait uitvalt? Je moet de storing detecteren, wat een moeilijk probleem is. Maar je moet ook je microservice opnieuw opstarten.

Voor beschikbaarheid moet een microservice bestand zijn tegen storingen en in staat zijn om opnieuw op een andere machine te starten. Naast deze tolerantievereisten mogen gegevens niet verloren gaan en moeten gegevens consistent blijven.

Tolerantie is moeilijk te bereiken wanneer er storingen optreden tijdens een upgrade van de toepassing. De microservice, die met het implementatiesysteem werkt, hoeft niet te herstellen. Het moet bepalen of het kan doorgaan naar de nieuwere versie of terugdraaien naar een vorige versie om een consistente status te behouden. U moet rekening houden met een paar vragen, zoals de vraag of er genoeg machines beschikbaar zijn om vooruit te blijven gaan en hoe u eerdere versies van de microservice herstellen. Om deze beslissingen te nemen, hebt u de microservice nodig om gezondheidsinformatie uit te stoten.

### <a name="reports-health-and-diagnostics"></a>Rapporteert gezondheid en diagnostiek

Het lijkt misschien voor de hand liggend, en het wordt vaak over het hoofd gezien, maar een microservice moet de gezondheid en diagnostiek rapporteren. Anders heb je weinig inzicht in de gezondheid vanuit een operations perspectief. Het correleren van diagnostische gebeurtenissen in een reeks onafhankelijke services en het omgaan met machineklokscheefheid om de gebeurtenisvolgorde te begrijpen, is een uitdaging. Op dezelfde manier dat u interactie met een microservice over overeengekomen protocollen en gegevensformaten, moet u standaardiseren hoe de gezondheid en diagnostische gebeurtenissen die uiteindelijk zal eindigen in een gebeurtenis op te slaan voor query's en bekijken log. Met een microservices-benadering moeten verschillende teams het eens worden over één logging-indeling. Er moet een consistente benadering zijn voor het bekijken van diagnostische gebeurtenissen in de toepassing als geheel.

Gezondheid is anders dan diagnostiek. Gezondheid gaat over de microservice die de huidige status rapporteert om passende acties te ondernemen. Een goed voorbeeld is het werken met upgrade- en implementatiemechanismen om de beschikbaarheid te behouden. Hoewel een service momenteel mogelijk niet in orde is vanwege een procescrash of het opnieuw opstarten van de machine, kan de service nog steeds operationeel zijn. Het laatste wat je nodig hebt is om de situatie nog erger door het starten van een upgrade. De beste aanpak is om eerst te onderzoeken of de tijd voor de microservice om te herstellen. Gezondheidsgebeurtenissen van een microservice helpen ons weloverwogen beslissingen te nemen en in feite te helpen bij het creëren van zelfhelende diensten.

## <a name="guidance-for-designing-microservices-on-azure"></a>Richtlijnen voor het ontwerpen van microservices op Azure

Ga naar het Azure-architectuurcentrum voor richtlijnen voor het [ontwerpen en bouwen van microservices op Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric als microservices platform

Azure Service Fabric ontstond toen Microsoft overstapte van het leveren van boxed-producten, die doorgaans monolithisch waren, naar het leveren van services. De ervaring van het bouwen en bedienen van grote services, zoals Azure SQL Database en Azure Cosmos DB, vormde Service Fabric. Het platform evolueerde in de loop van de tijd naarmate meer diensten het overnamen. Service Fabric moest niet alleen in Azure worden uitgevoerd, maar ook in zelfstandige Windows Server-implementaties.

***Het doel van Service Fabric is om de moeilijke problemen van het bouwen en uitvoeren van een service op te lossen en infrastructuurbronnen efficiënt te gebruiken, zodat teams zakelijke problemen kunnen oplossen door gebruik te maken van een microservices-benadering.***

Service Fabric helpt u bij het bouwen van toepassingen die gebruik maken van een microservices-benadering door:

* Een platform dat systeemservices biedt voor het implementeren, upgraden, detecteren en opnieuw starten van mislukte services, het detecteren van services, het routeren van berichten, het beheren van de status en het bewaken van de status.
* De mogelijkheid om toepassingen te implementeren die worden uitgevoerd in containers of als processen. Service Fabric is een container- en procesorchestrator.
* Productieve programmeer-API's om u te helpen applicaties te bouwen als microservices: [ASP.NET Core, Reliable Actors en Reliable Services](service-fabric-choose-framework.md). U bijvoorbeeld informatie over gezondheid en diagnostiek krijgen of u profiteren van de ingebouwde hoge beschikbaarheid.

***Service Fabric is agnostisch over hoe u uw service bouwt en u elke technologie gebruiken. Maar het biedt wel ingebouwde programmeer-API's die het eenvoudiger maken om microservices te bouwen.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Bestaande toepassingen migreren naar Service Fabric

Met Service Fabric u bestaande code hergebruiken en moderniseren met nieuwe microservices. Er zijn vijf fasen aan toepassingmodernisering, en u beginnen en in elk stadium stoppen. De etappes zijn:

1) Begin met een traditionele monolithische toepassing.  
2) Migreren. Gebruik containers of uitvoerbare gasten om bestaande code in Service Fabric te hosten.  
3) Moderniseren. Voeg nieuwe microservices toe naast bestaande containercode.  
4) Innoveren. Breek de monolithische toepassing in microservices op basis van behoefte.  
5) Transformeer toepassingen in microservices. Transformeer bestaande monolithische toepassingen of bouw nieuwe greenfield-toepassingen.

![Migratie naar microservices][Image3]

Vergeet niet, u *beginnen en stoppen bij een van deze fasen*. Je hoeft niet door te gaan naar de volgende fase. 

Laten we eens kijken naar voorbeelden voor elk van deze fasen.

**Migreren**  
Om twee redenen migreren veel bedrijven bestaande monolithische toepassingen naar containers:

* Kostenreductie, hetzij als gevolg van consolidatie en verwijdering van bestaande hardware of als gevolg van het uitvoeren van toepassingen met een hogere dichtheid.
* Een consistent implementatiecontract tussen ontwikkeling en bewerkingen.

Kostenbesparingen zijn eenvoudig. Bij Microsoft worden veel bestaande toepassingen gecontaineriseerd, wat leidt tot miljoenen dollars aan besparingen. Consistente implementatie is moeilijker te evalueren, maar even belangrijk. Het betekent dat ontwikkelaars de technologieën kunnen kiezen die bij hen passen, maar operations accepteren slechts één methode voor het implementeren en beheren van de toepassingen. Het verlicht operaties van het moeten omgaan met de complexiteit van het ondersteunen van verschillende technologieën zonder ontwikkelaars te dwingen om alleen bepaalde technologieën te kiezen. In wezen wordt elke toepassing gecontaineriseerd tot op zichzelf staande implementatieafbeeldingen.

Veel organisaties stoppen hier. Ze hebben al de voordelen van containers en Service Fabric biedt de volledige beheerervaring, inclusief implementatie, upgrades, versiebeheer, rollbacks en statusbewaking.

**Moderniseren**  
Modernisering is de toevoeging van nieuwe diensten naast de bestaande containercode. Als je nieuwe code gaat schrijven, kun je het beste kleine stappen zetten op het microservicespad. Dit kan betekenen dat u een nieuw REST API-eindpunt of nieuwe bedrijfslogica toevoegt. Op deze manier start je het proces van het bouwen van nieuwe microservices en oefen je met het ontwikkelen en implementeren ervan.

**Innoveren**  
Een microservices-benadering voorkomt aan veranderende bedrijfsbehoeften. In dit stadium moet u beslissen of u wilt beginnen met het splitsen van de monolithische toepassing in services of innoveren. Een klassiek voorbeeld hiervan is wanneer een database die u gebruikt als werkstroomwachtrij een verwerkingsknelpunt wordt. Naarmate het aantal werkstroomaanvragen toeneemt, moet het werk worden gedistribueerd voor schaal. Neem dat specifieke stuk van de toepassing dat niet wordt geschaald, of dat vaker moet worden bijgewerkt, en splitt het uit als een microservice en innoveren.

**Toepassingen omzetten in microservices**  
In dit stadium bestaat uw toepassing volledig uit (of opgesplitst in) microservices. Om dit punt te bereiken, heb je de microservices reis gemaakt. U hier beginnen, maar om dit te doen zonder een microservices-platform om u te helpen vereist een aanzienlijke investering.

### <a name="are-microservices-right-for-my-application"></a>Zijn microservices geschikt voor mijn toepassing?

Misschien. Bij Microsoft, naarmate meer teams om zakelijke redenen voor de cloud begonnen te bouwen, realiseerden velen van hen de voordelen van een microservice-achtige benadering. Bing maakt bijvoorbeeld al jaren gebruik van microservices. Voor andere teams was de aanpak van microservices nieuw. Teams vonden dat er harde problemen op te lossen buiten hun kerngebieden van kracht. Daarom kreeg Service Fabric tractie als technologie voor bouwdiensten.

Het doel van Service Fabric is om de complexiteit van het bouwen van microservicetoepassingen te verminderen, zodat u niet zoveel dure herontwerpen hoeft te ondergaan. Start klein, schaal wanneer dat nodig is, deprecate diensten, voeg nieuwe toe en ontwikkel met het gebruik van de klant. We weten ook dat er nog veel andere problemen moeten worden opgelost om microservices toegankelijker te maken voor de meeste ontwikkelaars. Containers en het acteur programmeringsmodel zijn voorbeelden van kleine stappen in die richting. We zijn er zeker van dat er meer innovaties zullen ontstaan om een microservices-aanpak gemakkelijker te maken.

## <a name="next-steps"></a>Volgende stappen

* [Microservices: een applicatierevolutie die wordt aangedreven door de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: microservices bouwen op Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Aanbevolen procedures voor Azure Service Fabric-toepassingen en clustertoepassingen](service-fabric-best-practices-overview.md)
* [Overzicht van de terminologie van Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
