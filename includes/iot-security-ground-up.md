---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72789727"
---
Het Internet of Things (IoT) stelt bedrijven wereldwijd voor unieke uitdagingen op het gebied van beveiliging, privacy en compliance. In tegenstelling tot traditionele cybertechnologie waarbij deze problemen draaien om software en hoe deze wordt geïmplementeerd, gaat IoT over wat er gebeurt wanneer de cyber en de fysieke werelden samenkomen. Het beschermen van IoT-oplossingen vereist een veilige inrichting van apparaten, veilige connectiviteit tussen deze apparaten en de cloud en veilige gegevensbescherming in de cloud tijdens verwerking en opslag. Werken tegen dergelijke functionaliteit, echter, zijn resource-beperkte apparaten, geografische distributie van implementaties, en een groot aantal apparaten binnen een oplossing.

In dit artikel wordt onderzocht hoe de IoT-oplossingsversnellers een veilige en privé Internet of Things-cloudoplossing bieden. De oplossingsversnellers leveren een complete end-to-end oplossing, waarbij beveiliging vanaf de grond af in elke fase is ingebouwd. Bij Microsoft maakt het ontwikkelen van veilige software deel uit van de software engineering-praktijk, geworteld in de decennialange ervaring van Microsoft met het ontwikkelen van veilige software. Om dit te garanderen, security development lifecycle (SDL) is de fundamentele ontwikkeling methodologie, in combinatie met een groot aantal infrastructuur-niveau beveiligingsdiensten zoals Operational Security Assurance (OSA) en de Microsoft Digital Crimes Unit, Microsoft Security Response Center en Microsoft Malware Protection Center.

De oplossingsversnellers bieden unieke functies die het inrichten, verbinden en opslaan van gegevens van IoT-apparaten eenvoudig en transparant en vooral veilig maken. In dit artikel wordt gekeken naar de beveiligingsfuncties en implementatiestrategieën van azure IoT-oplossingen om ervoor te zorgen dat beveiligings-, privacy- en nalevingsuitdagingen worden aangepakt.

## <a name="introduction"></a>Inleiding

Het Internet of Things (IoT) is de golf van de toekomst en biedt bedrijven onmiddellijke en reële mogelijkheden om de kosten te verlagen, de omzet te verhogen en hun bedrijf te transformeren. Veel bedrijven zijn echter huiverig om IoT in hun organisaties te implementeren vanwege zorgen over beveiliging, privacy en naleving. Een belangrijk punt van zorg komt van de uniciteit van de IoT-infrastructuur, die de cyber- en fysieke werelden samenvoegt en individuele risico's die inherent zijn aan deze twee werelden, verergert. Beveiliging van IoT heeft betrekking op het waarborgen van de integriteit van code die op apparaten wordt uitgevoerd, het verstrekken van apparaat- en gebruikersverificatie, het definiëren van duidelijk eigendom van apparaten (evenals gegevens die door die apparaten worden gegenereerd) en het bestand zijn tegen cyber- en fysieke aanvallen.

Dan is er de kwestie van de privacy. Bedrijven willen transparantie met betrekking tot het verzamelen van gegevens, zoals in wat er wordt verzameld en waarom, wie het kan zien, wie de toegang beheert, enzovoort. Ten slotte zijn er algemene veiligheidsproblemen van de apparatuur, samen met de mensen die ze bedienen, en kwesties van het handhaven van de industrie normen van naleving.

Gezien de beveiligings-, privacy-, transparantie- en nalevingsproblemen blijft het kiezen van de juiste IoT-oplossingsprovider een uitdaging. Door afzonderlijke onderdelen van IoT-software en -services van verschillende leveranciers aan elkaar te naaien, worden hiaten in beveiliging, privacy, transparantie en naleving geïntroduceerd, die moeilijk te detecteren, laat staan vast te stellen zijn. De keuze van de juiste IoT-software en serviceprovider is gebaseerd op het vinden van providers die uitgebreide ervaring hebben met het uitvoeren van services, die zich uitstrekken over verticalen en regio's, maar ook in staat zijn om op een veilige en transparante manier te schalen. Op dezelfde manier helpt het voor de geselecteerde provider om tientallen jaren ervaring te hebben met het ontwikkelen van veilige software die op miljarden machines wereldwijd draait, en de mogelijkheid heeft om het dreigingslandschap van deze nieuwe wereld van het Internet of Things te waarderen.

## <a name="secure-infrastructure-from-the-ground-up"></a>Veilige infrastructuur vanaf de grond af

De [Microsoft Cloud-infrastructuur](https://azure.microsoft.com) ondersteunt meer dan een miljard klanten in 127 landen/regio's. Op basis van de decennialange ervaring van Microsoft in het bouwen van bedrijfssoftware en het uitvoeren van enkele van de grootste onlineservices ter wereld, biedt de Microsoft Cloud hogere niveaus van verbeterde beveiligings-, privacy-, nalevings- en bedreigingsbeperkende praktijken dan de meeste klanten konden bereiken op hun eigen.

De [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) biedt een verplicht bedrijfsbreed ontwikkelingsproces dat beveiligingsvereisten insluit in de gehele levenscyclus van de software. Om ervoor te zorgen dat operationele activiteiten hetzelfde beveiligingsniveau volgen, gebruikt SDL strenge beveiligingsrichtlijnen die zijn vastgelegd in het Operational Security Assurance (OSA)-proces van Microsoft. Microsoft werkt ook samen met externe accountantskantoren voor voortdurende verificatie dat het voldoet aan haar nalevingsverplichtingen, en Microsoft werkt aan brede beveiligingsinspanningen door de oprichting van expertisecentra, waaronder de Microsoft Digital Crimes Unit, Microsoft Security Response Center en Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - veilige IoT-infrastructuur voor uw bedrijf

Microsoft Azure biedt een complete cloudoplossing, een oplossing die een voortdurend groeiende verzameling geïntegreerde cloudservices combineert: analyse, machine learning, opslag, beveiliging, netwerken en web- met een toonaangevende toewijding aan bescherming en privacy van uw gegevens. Microsoft's [assume breach](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategie maakt gebruik van een speciaal rood *team* van software security experts die aanvallen simuleren, het testen van de mogelijkheid van Azure te detecteren, te beschermen tegen opkomende bedreigingen, en herstellen van inbreuken. Het [wereldwijde incidentresponseteam](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) van Microsoft werkt de klok rond om de effecten van aanvallen en schadelijke activiteiten te beperken. Het team volgt vastgestelde procedures voor incidentbeheer, communicatie en herstel en maakt gebruik van vindbare en voorspelbare interfaces met interne en externe partners.

De systemen van Microsoft bieden continue inbraakdetectie en -preventie, preventie van serviceaanvallen, regelmatige penetratietests en forensische tools die helpen bij het identificeren en beperken van bedreigingen. [Multi-factor authenticatie](../articles/active-directory/authentication/multi-factor-authentication.md) biedt een extra beveiligingslaag voor eindgebruikers om toegang te krijgen tot het netwerk. En voor de toepassing en de hostprovider biedt Microsoft toegangscontrole, monitoring, anti-malware, het scannen van kwetsbaarheden, patches en configuratiebeheer.

De oplossingsversnellers maken gebruik van de beveiliging en privacy die in het Azure-platform zijn ingebouwd, samen met de SDL- en OSA-processen voor veilige ontwikkeling en werking van alle Microsoft-software. Deze procedures bieden infrastructuurbescherming, netwerkbescherming en identiteits- en beheerfuncties die van fundamenteel belang zijn voor de beveiliging van elke oplossing.

De [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) binnen de [IoT-oplossingsversnellers](../articles/iot-fundamentals/iot-introduction.md) biedt een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen IoT-apparaten en Azure-services zoals [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) en Azure [Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) mogelijk maakt met behulp van beveiligingsreferenties per apparaat en toegangscontrole.

Om beveiligings- en privacyfuncties die zijn ingebouwd in de versnellers van azure IoT-oplossingen het beste te communiceren, splitst dit artikel de suite op in de drie primaire beveiligingsgebieden.

![Azure IoT-oplossingsversnellers](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Veilige inrichting van apparaten en verificatie

De oplossingsversnellers beveiligen apparaten terwijl ze in het veld zijn door een unieke identiteitssleutel voor elk apparaat te bieden, die door de IoT-infrastructuur kan worden gebruikt om met het apparaat te communiceren terwijl het in bedrijf is. Het proces is snel en eenvoudig in te stellen. De gegenereerde sleutel met een door de gebruiker geselecteerde apparaat-id vormt de basis van een token dat wordt gebruikt in alle communicatie tussen het apparaat en de Azure IoT Hub.

Apparaat-id's kunnen tijdens de productie aan een apparaat worden gekoppeld (dat wil zeggen geflitst in een hardwarevertrouwensmodule) of een bestaande vaste identiteit als proxy gebruiken (bijvoorbeeld CPU-serienummers). Aangezien het wijzigen van deze identificerende informatie in het apparaat niet eenvoudig is, is het belangrijk om logische apparaat-id's te introduceren in het geval de onderliggende apparaathardware verandert, maar het logische apparaat hetzelfde blijft. In sommige gevallen kan de koppeling van een apparaatidentiteit plaatsvinden op de implementatietijd van het apparaat (bijvoorbeeld een geverifieerde veldtechnicus configureert een nieuw apparaat fysiek terwijl hij communiceert met de backend van de oplossing). Het [Azure IoT Hub-identiteitsregister](../articles/iot-hub/iot-hub-devguide.md) biedt veilige opslag van apparaatidentiteiten en beveiligingssleutels voor een oplossing. Individuele of groepen apparaatidentiteiten kunnen worden toegevoegd aan een lijst met toegestane personen of een bloklijst, zodat volledige controle over de toegang tot het apparaat mogelijk is.

Azure IoT Hub-toegangsbeheerbeleid in de cloud maakt activering en uitschakelen van een apparaatidentiteit mogelijk, zodat een apparaat kan worden losgekoppeld van een IoT-implementatie wanneer dat nodig is. Deze associatie en ontkoppeling van apparaten is gebaseerd op elke apparaatidentiteit.

Extra beveiligingsfuncties voor apparaten zijn:

* Apparaten accepteren geen ongevraagde netwerkverbindingen. Ze leggen alle verbindingen en routes op een outbound-only manier vast. Als een apparaat een opdracht van de backend wil ontvangen, moet het apparaat een verbinding starten om te controleren of de in behandeling zijnde opdrachten moeten worden verwerkt. Zodra een verbinding tussen het apparaat en iot-hub veilig tot stand is gebracht, kan berichten vanuit de cloud naar het apparaat en het apparaat naar de cloud transparant worden verzonden.

* Apparaten maken alleen verbinding met of maken routes naar bekende services waarmee ze zijn gekeken, zoals een Azure IoT Hub.

* Systeemautorisatie en verificatie gebruiken identiteiten per apparaat, waardoor toegangsreferenties en machtigingen vrijwel direct opnieuw kunnen worden opgevraagd.

### <a name="secure-connectivity"></a>Veilige connectiviteit

Duurzaamheid van berichten is een belangrijk kenmerk van elke IoT-oplossing. De noodzaak om opdrachten duurzaam te leveren en/of gegevens van apparaten te ontvangen, wordt onderstreept door het feit dat IoT-apparaten via internet zijn verbonden of andere vergelijkbare netwerken die onbetrouwbaar kunnen zijn. Azure IoT Hub biedt duurzaamheid van berichten tussen cloud en apparaten via een systeem van bevestigingen in reactie op berichten. Extra duurzaamheid voor berichten wordt bereikt door berichten in de IoT-hub maximaal zeven dagen te plaatsen voor telemetrie en twee dagen voor opdrachten.

Efficiëntie is belangrijk om de hulpbronnen en de werking in een omgeving met beperkte hulpbronnen te waarborgen. HTTPS (HTTP Secure), de standaard beveiligde versie van het populaire http-protocol, wordt ondersteund door Azure IoT Hub, waardoor efficiënte communicatie mogelijk is. Advanced Message Queuing Protocol (AMQP) en Message Queuing Telemetry Transport (MQTT), ondersteund door Azure IoT Hub, zijn niet alleen ontworpen voor efficiëntie op het gebied van resourcegebruik, maar ook voor betrouwbare berichtlevering. 

Schaalbaarheid vereist de mogelijkheid om veilig te werken met een breed scala aan apparaten. Azure IoT-hub maakt een veilige verbinding met zowel IP-apparaten als niet-IP-apparaten mogelijk. IP-apparaten kunnen rechtstreeks verbinding maken en communiceren met de IoT Hub via een beveiligde verbinding. Niet-IP-apparaten zijn beperkt door resources en maken alleen verbinding via communicatieprotocollen voor korte afstanden, zoals Zwave, ZigBee en Bluetooth. Een veldgateway wordt gebruikt om deze apparaten samen te voegen en voert protocolvertaling uit om veilige bidirectionele communicatie met de cloud mogelijk te maken.

Aanvullende beveiligingsfuncties voor verbindingen zijn:

* Het communicatiepad tussen apparaten en Azure IoT Hub, of tussen gateways en Azure IoT Hub, is beveiligd met behulp van bedrijfsstandaard Transport Layer Security (TLS) met Azure IoT Hub geverifieerd met behulp van X.509-protocol.

* Om apparaten te beschermen tegen ongevraagde inkomende verbindingen, opent Azure IoT Hub geen verbinding met het apparaat. Het apparaat initieert alle verbindingen.

* Azure IoT Hub slaat berichten blijvend op voor apparaten en wacht tot het apparaat verbinding maakt. Deze opdrachten worden gedurende twee dagen opgeslagen, zodat apparaten die sporadisch verbinding maken, vanwege problemen met stroom of connectiviteit, deze opdrachten kunnen ontvangen. Azure IoT Hub houdt een wachtrij per apparaat bij voor elk apparaat.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Veilige verwerking en opslag in de cloud

Van versleutelde communicatie tot het verwerken van gegevens in de cloud, de oplossingsversnellers helpen om gegevens veilig te houden. Het biedt flexibiliteit om extra versleuteling en beheer van beveiligingssleutels te implementeren.

Azure IoT-oplossingsversnellers kunnen een op beleid gebaseerd autorisatiemodel voor gegevens in de cloud bieden, zodat eenvoudig toegangsbeheer kan worden gecontroleerd en gecontroleerd. Dit model maakt ook bijna-onmiddellijke intrekking van de toegang tot gegevens in de cloud mogelijk en van apparaten die zijn verbonden met de Versnellers van Azure IoT-oplossingen.

Zodra gegevens zich in de cloud bevinden, kunnen ze worden verwerkt en opgeslagen in elke door de gebruiker gedefinieerde workflow. De toegang tot elk onderdeel van de gegevens wordt beheerd met Azure Active Directory, afhankelijk van de gebruikte opslagservice.

Alle sleutels die door de IoT-infrastructuur worden gebruikt, worden opgeslagen in de cloud in beveiligde opslag, met de mogelijkheid om om te draaien voor het geval sleutels opnieuw moeten worden ingericht. Gegevens kunnen worden opgeslagen in [Azure Cosmos DB](../articles/cosmos-db/introduction.md) of in [SQL-databases,](../articles/sql-database/sql-database-faq.md)waardoor de gewenste definitie van het gewenste beveiligingsniveau mogelijk is. Daarnaast biedt Azure een manier om alle toegang tot uw gegevens te controleren en te controleren om u te waarschuwen voor elke inbreuk of ongeautoriseerde toegang.

## <a name="conclusion"></a>Conclusie

Het Internet of Things begint met je dingen, de dingen die het belangrijkst zijn voor bedrijven. IoT kan een bedrijf geweldige waarde bieden door de kosten te verlagen, de omzet te verhogen en het bedrijf te transformeren. Het succes van deze transformatie hangt grotendeels af van het kiezen van de juiste IoT-software en serviceprovider. Dat betekent het vinden van een provider die deze transformatie niet alleen katalyseert door inzicht te krijgen in de behoeften en vereisten van bedrijven, maar ook services en software biedt die zijn gebouwd met beveiliging, privacy, transparantie en naleving als belangrijke ontwerpoverwegingen. Microsoft heeft uitgebreide ervaring met het ontwikkelen en implementeren van veilige software en services en blijft een leider in dit nieuwe tijdperk van Internet of Things.

De oplossingsversnellers bouwen beveiligingsmaatregelen door het ontwerp in, waardoor veilige monitoring van assets mogelijk is om de efficiëntie te verbeteren, operationele prestaties te stimuleren om innovatie mogelijk te maken en geavanceerde data-analyse te gebruiken om bedrijven te transformeren. Met zijn gelaagde benadering van beveiliging, meerdere beveiligingsfuncties en ontwerppatronen helpen de oplossingsversnellers bij het implementeren van een infrastructuur die kan worden vertrouwd om elk bedrijf te transformeren.

## <a name="additional-information"></a>Aanvullende informatie

Elke oplossingsversneller maakt exemplaren van Azure-services, zoals:

* [**Azure IoT Hub:**](https://azure.microsoft.com/services/iot-hub/)uw gateway die de cloud verbindt met apparaten. U per hub naar miljoenen verbindingen schalen en enorme hoeveelheden gegevens verwerken met verificatieondersteuning per apparaat, zodat u uw oplossing beveiligen.

* [**Azure Cosmos DB:**](https://azure.microsoft.com/services/cosmos-db/)een schaalbare, volledig geïndexeerde databaseservice voor semi-gestructureerde gegevens die metagegevens beheert voor de apparaten die u indient, zoals kenmerken, configuratie en beveiligingseigenschappen. Azure Cosmos DB biedt krachtige en krachtige verwerking, schema-agnostische indexering van gegevens en een uitgebreide SQL-queryinterface.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Real-time streamprocessing in de cloud waarmee u snel een low-cost analyseoplossing ontwikkelen en implementeren om realtime inzichten van apparaten, sensoren, infrastructuur en toepassingen te ontdekken. De gegevens van deze volledig beheerde service kunnen worden geschaald naar elk volume terwijl ze nog steeds een hoge doorvoer, lage latentie en tolerantie bereiken.

* [**Azure App Services:**](https://azure.microsoft.com/services/app-service/)een cloudplatform om krachtige web- en mobiele apps te bouwen die overal verbinding maken met gegevens; in de cloud of on-premises. Bouw aantrekkelijke mobiele apps voor iOS, Android en Windows. Integreer met uw Software as a Service (SaaS) en bedrijfsapplicaties met out-of-the-box connectiviteit met tientallen cloudservices en bedrijfsapplicaties. Code in uw favoriete taal en IDE—.NET, Node.js, PHP, Python of Java — om sneller dan ooit web-apps en API's te bouwen.

* [**Logic Apps:**](https://azure.microsoft.com/services/app-service/logic/)De logic apps-functie van Azure App Service helpt uw IoT-oplossing te integreren in uw bestaande bedrijfssystemen en workflowprocessen te automatiseren. Logic Apps stelt ontwikkelaars in staat om workflows te ontwerpen die uitgaan van een trigger en vervolgens een reeks stappen uit te voeren: regels en acties die krachtige connectors gebruiken om te integreren met uw bedrijfsprocessen. Logic Apps biedt kant-en-klare connectiviteit met een uitgebreid ecosysteem van SaaS- en cloudtoepassingen.

* [**Azure Blob-opslag:**](https://azure.microsoft.com/services/storage/)betrouwbare, economische cloudopslag voor de gegevens die uw apparaten naar de cloud verzenden.