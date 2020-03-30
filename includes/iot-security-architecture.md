---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73034543"
---
Bij het ontwerpen van een systeem is het belangrijk om de potentiële bedreigingen voor dat systeem te begrijpen en de juiste verdediging dienovereenkomstig toe te voegen, omdat het systeem is ontworpen en ontworpen. Het is belangrijk om het product vanaf het begin te ontwerpen met beveiliging in het achterhoofd, omdat het begrijpen van hoe een aanvaller een systeem kan compromitteren, ervoor zorgt dat de juiste oplossingen vanaf het begin zijn.

## <a name="security-starts-with-a-threat-model"></a>Beveiliging begint met een bedreigingsmodel

Microsoft heeft lang gebruik gemaakt van bedreigingsmodellen voor haar producten en heeft het proces van bedreigingsmodellering van het bedrijf openbaar gemaakt. De ervaring van het bedrijf toont aan dat de modellering onverwachte voordelen heeft die verder gaan dan het onmiddellijke begrip van welke bedreigingen het meest betrekking hebben. Het creëert bijvoorbeeld ook een weg voor een open discussie met anderen buiten het ontwikkelteam, wat kan leiden tot nieuwe ideeën en verbeteringen in het product.

Het doel van threat modeling is om te begrijpen hoe een aanvaller in staat zou kunnen zijn om een systeem te compromitteren en vervolgens ervoor te zorgen dat de juiste oplossingen zijn op zijn plaats. Bedreigingsmodellering dwingt het ontwerpteam om oplossingen te overwegen omdat het systeem is ontworpen in plaats van nadat een systeem is geïmplementeerd. Dit feit is van cruciaal belang, omdat het aanpassen van beveiligingssystemen aan een groot aantal apparaten in het veld onhaalbaar is, foutgevoelig en klanten in gevaar brengt.

Veel ontwikkelteams doen uitstekend werk met het vastleggen van de functionele vereisten voor het systeem dat klanten ten goede komt. Het identificeren van niet voor de hand liggende manieren waarop iemand het systeem zou kunnen misbruiken, is echter uitdagender. Bedreigingsmodellering kan ontwikkelingsteams helpen te begrijpen wat een aanvaller zou kunnen doen en waarom. Threat modeling is een gestructureerd proces dat een discussie over de beslissingen van het beveiligingsontwerp in het systeem creëert, evenals wijzigingen in het ontwerp die worden gemaakt langs de weg die de beveiliging beïnvloedt. Hoewel een bedreigingsmodel gewoon een document is, is deze documentatie ook een ideale manier om de continuïteit van kennis te waarborgen, de geleerde lessen te behouden en het nieuwe team snel aan boord te helpen. Ten slotte is een resultaat van threat modeling om u in staat te stellen andere aspecten van beveiliging te overwegen, zoals welke beveiligingsverplichtingen u aan uw klanten wilt verstrekken. Deze toezeggingen in combinatie met threat modeling informeren en stimuleren het testen van uw Internet of Things (IoT) oplossing.

### <a name="when-to-do-threat-modeling"></a>Wanneer te doen bedreiging modellering

[Threat modeling](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) biedt de grootste waarde wanneer u het opneemt in de ontwerpfase. Wanneer u ontwerpt, hebt u de grootste flexibiliteit om wijzigingen aan te brengen om bedreigingen te elimineren. Het elimineren van bedreigingen door het ontwerp is het gewenste resultaat. Het is veel gemakkelijker dan het toevoegen van mitigaties, het testen ervan, en ervoor te zorgen dat ze actueel blijven en bovendien, een dergelijke eliminatie is niet altijd mogelijk. Het wordt moeilijker om bedreigingen te elimineren als een product volwassener wordt, en op zijn beurt vereist uiteindelijk meer werk en een stuk hardere afwegingen dan bedreigingmodellering vroeg in de ontwikkeling.

### <a name="what-to-consider-for-threat-modeling"></a>Wat te overwegen voor bedreiging modellering

Je moet kijken naar de oplossing als geheel en ook richten op de volgende gebieden:

* De beveiligings- en privacyfuncties
* De functies waarvan de fouten beveiligingsrelevant zijn
* De functies die een vertrouwensgrens raken

### <a name="who-performs-threat-modeling"></a>Wie voert bedreigingsmodellering uit

Threat modeling is een proces als alle andere. Het is een goed idee om het dreigingsmodeldocument te behandelen als elk ander onderdeel van de oplossing en het te valideren. Veel ontwikkelteams doen uitstekend werk met het vastleggen van de functionele vereisten voor het systeem dat klanten ten goede komt. Het identificeren van niet voor de hand liggende manieren waarop iemand het systeem zou kunnen misbruiken, is echter uitdagender. Bedreigingsmodellering kan ontwikkelingsteams helpen te begrijpen wat een aanvaller zou kunnen doen en waarom.

### <a name="how-to-perform-threat-modeling"></a>Threat Modeling uitvoeren

Het proces voor het modelleren van bedreigingen bestaat uit vier stappen; de stappen zijn:

* De toepassing modelleren
* Bedreigingen opsommen
* Bedreigingen beperken
* De beperkende maatregelen valideren

#### <a name="the-process-steps"></a>De processtappen

Drie vuistregels om in gedachten te houden bij het bouwen van een bedreigingsmodel:

1. Maak een diagram op basis van referentiearchitectuur.

2. Begin eerst met de breedte. Krijg een overzicht en begrijp het systeem als geheel, voordat je diepduikt. Deze aanpak zorgt ervoor dat u diep duikt op de juiste plaatsen.

3. Het proces aansturen, laat het proces u niet rijden. Als u een probleem in de modellering fase en wilt verkennen, go for it! Heb niet het gevoel dat je nodig hebt om deze stappen slaafs te volgen.

#### <a name="threats"></a>Bedreigingen

De vier kernelementen van een dreigingsmodel zijn:

* Processen zoals webservices, Win32-services en *nix daemons. Sommige complexe entiteiten (bijvoorbeeld veldgateways en sensoren) kunnen worden geabstraheerd als een proces wanneer een technische inzoom in deze gebieden niet mogelijk is.

* Gegevensopslag (overal waar gegevens worden opgeslagen, zoals een configuratiebestand of database)

* Gegevensstroom (waarbij gegevens worden verplaatst tussen andere elementen in de toepassing)

* Externe entiteiten (alles wat interageert met het systeem, maar niet onder de controle van de toepassing staat, voorbeelden zijn gebruikers en satellietfeeds)

Alle elementen in het architectonisch diagram zijn onderhevig aan verschillende bedreigingen; dit artikel de STRIDE ezelsbruggetje. Lees [Threat Modeling Again, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) om meer te weten te komen over de STRIDE-elementen.

Verschillende elementen van het toepassingsdiagram zijn onderhevig aan bepaalde STRIDE-bedreigingen:

* Processen zijn onderhevig aan STRIDE
* Gegevensstromen zijn onderworpen aan TID
* Gegevensopslag is onderworpen aan TID, en soms R, wanneer de gegevensopslag logboekdossiers zijn.
* Externe entiteiten zijn onderworpen aan SRD

## <a name="security-in-iot"></a>Beveiliging in IoT

Verbonden apparaten voor speciale doeleinden hebben een aanzienlijk aantal potentiële interactieoppervlaktes en interactiepatronen, die allemaal moeten worden beschouwd als een kader voor het beveiligen van digitale toegang tot die apparaten. De term "digitale toegang" wordt hier gebruikt om onderscheid te maken van alle bewerkingen die worden uitgevoerd door middel van directe apparaatinteractie waarbij toegangsbeveiliging wordt geboden via fysieke toegangscontrole. Bijvoorbeeld, het apparaat in een kamer met een slot op de deur. Hoewel fysieke toegang niet kan worden geweigerd met behulp van software en hardware, kunnen maatregelen worden genomen om te voorkomen dat fysieke toegang leidt tot systeeminterferentie.

Terwijl u de interactiepatronen verkent, kijkt u met dezelfde aandacht naar 'apparaatbesturing' en 'apparaatgegevens'. "Apparaatbesturing" kan worden geclassificeerd als alle informatie die door een partij aan een apparaat wordt verstrekt met als doel zijn gedrag ten opzichte van de toestand of de toestand van zijn omgeving te veranderen of te beïnvloeden. "Apparaatgegevens" kunnen worden geclassificeerd als alle informatie die een apparaat aan een andere partij uitstraalt over de toestand en de waargenomen toestand van zijn omgeving.

Om de best practices voor beveiliging te optimaliseren, wordt aanbevolen dat een typische IoT-architectuur is onderverdeeld in verschillende componenten/zones als onderdeel van de oefening voor bedreigingsmodellering. Deze zones worden volledig beschreven in deze sectie en omvatten:

* Apparaat
* Veldgateway,
* Cloudgateways en
* Diensten.

Zones zijn een brede manier om een oplossing te segmenteren; elke zone heeft vaak zijn eigen gegevens- en verificatie- en autorisatievereisten. Zones kunnen ook worden gebruikt om schade te isoleren en de impact van lage vertrouwenszones op hogere vertrouwenszones te beperken.

Elke zone wordt gescheiden door een vertrouwensgrens, die wordt aangeduid als de gestippelde rode lijn in het volgende diagram. Het vertegenwoordigt een overgang van gegevens/informatie van de ene bron naar de andere. Tijdens deze overgang kunnen de gegevens/informatie onderhevig zijn aan Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service en Elevation of Privilege (STRIDE).

![IoT-beveiligingszones](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De componenten die binnen elke grens worden afgebeeld, worden ook onderworpen aan STRIDE, waardoor een volledige 360-dreigingsmodelleringsweergave van de oplossing mogelijk is. In de volgende secties wordt dieper ingegaan op elk van de componenten en specifieke beveiligingsproblemen en oplossingen die moeten worden ingevoerd.

In de volgende secties worden standaardcomponenten besproken die doorgaans in deze zones worden gevonden.

### <a name="the-device-zone"></a>De apparaatzone

De apparaatomgeving is de directe fysieke ruimte rond het apparaat waar fysieke toegang en/of "lokaal netwerk" peer-to-peer digitale toegang tot het apparaat mogelijk is. Een "lokaal netwerk" wordt verondersteld om een netwerk te zijn dat van - maar potentieel overbrugd aan - het openbare Internet onderscheidt en wordt geïsoleerd, en om het even welke draadloze radiotechnologie op korte afstand omvat die peer-to-peer communicatie van apparaten toestaat. Het omvat *geen* netwerkvirtualisatietechnologie die de illusie van een dergelijk lokaal netwerk creëert en omvat ook geen openbare operatornetwerken die twee apparaten vereisen om te communiceren in de openbare netwerkruimte als ze een peer-to-peer-communicatierelatie zouden aangaan.

### <a name="the-field-gateway-zone"></a>De veldgatewayzone

Field gateway is een apparaat/toestel of een algemene servercomputersoftware die fungeert als communicatie-enabler en, mogelijk, als apparaatbesturingssysteem en apparaatgegevensverwerkingshub. De veldgatewayzone bevat de veldgateway zelf en alle apparaten die eraan zijn gekoppeld. Zoals de naam al aangeeft, veld gateways handelen buiten speciale gegevensverwerkingsfaciliteiten, zijn meestal locatie gebonden, zijn mogelijk onderhevig aan fysieke inbraak, en heeft beperkte operationele redundantie. Alles om te zeggen dat een veld gateway is meestal een ding dat men kan aanraken en saboteren terwijl u weet wat de functie ervan is.

Een veldgateway is anders dan een netwerkrouter in die zin dat deze een actieve rol heeft gehad bij het beheren van toegang en informatiestroom, wat betekent dat het een toepassing is die is geadresseerd aan entiteit en netwerkverbinding of sessieterminal. Een NAT-apparaat of firewall daarentegen wordt niet aangemerkt als veldgateways, omdat het geen expliciete verbindings- of sessieterminals zijn, maar eerder een route (of blok) verbindingen of sessies die via deze gateways worden gemaakt. De veldgateway heeft twee verschillende oppervlakken. De ene kijkt uit op de apparaten die eraan zijn gekoppeld en vertegenwoordigt de binnenkant van de zone, en de andere staat tegenover alle externe partijen en is de rand van de zone.

### <a name="the-cloud-gateway-zone"></a>De cloudgatewayzone

Een cloudgateway is een systeem dat communicatie op afstand van en naar apparaten of veldgateways van verschillende sites in de openbare netwerkruimte mogelijk maakt, meestal naar een cloudgebaseerd controle- en gegevensanalysesysteem, een federatie van dergelijke systemen. In sommige gevallen kan een cloudgateway de toegang tot apparaten voor speciale doeleinden onmiddellijk vergemakkelijken vanaf terminals zoals tablets of telefoons. In de hier besproken context is "cloud" bedoeld om te verwijzen naar een speciaal gegevensverwerkingssysteem dat niet gebonden is aan dezelfde site als de aangesloten apparaten of veldgateways. Ook in een cloudzone voorkomen operationele maatregelen gerichte fysieke toegang en worden ze niet noodzakelijkerwijs blootgesteld aan een "public cloud"-infrastructuur.  

Een cloudgateway kan mogelijk worden toegewezen aan een netwerkvirtualisatieoverlay om de cloudgateway en alle aangesloten apparaten of veldgateways van ander netwerkverkeer te isoleren. De cloudgateway zelf is geen apparaatbeheersysteem of verwerkings- of opslagfaciliteit voor apparaatgegevens; deze faciliteiten interface met de cloud gateway. De cloudgatewayzone bevat de cloudgateway zelf, samen met alle veldgateways en apparaten die er direct of indirect aan zijn gekoppeld. De rand van de zone is een aparte oppervlakte waar alle externe partijen communiceren door.

### <a name="the-services-zone"></a>De dienstenzone

Een "service" wordt voor deze context gedefinieerd als elke softwarecomponent of -module die wordt verbonden met apparaten via een veld- of cloudgateway voor het verzamelen en analyseren van gegevens, evenals voor opdracht en controle. Diensten zijn bemiddelaars. Ze handelen onder hun identiteit ten opzichte van gateways en andere subsystemen, slaan en analyseren gegevens op, geven autonoom opdrachten uit aan apparaten op basis van gegevensinzichten of -schema's en stellen informatie- en controlemogelijkheden bloot aan geautoriseerde eindgebruikers.

### <a name="information-devices-versus-special-purpose-devices"></a>Informatieapparaten versus apparaten voor speciale doeleinden

Pc's, telefoons en tablets zijn voornamelijk interactieve informatieapparaten. Telefoons en tablets zijn expliciet geoptimaliseerd rond het maximaliseren van de levensduur van de batterij. Ze worden bij voorkeur gedeeltelijk uitgeschakeld wanneer ze niet onmiddellijk interactie hebben met een persoon, of wanneer ze geen diensten verlenen, zoals het afspelen van muziek of het begeleiden van hun eigenaar naar een bepaalde locatie. Vanuit een systeemperspectief werken deze informatietechnologie-apparaten voornamelijk als volmachten naar mensen toe. Ze zijn "mensen actuatoren" suggereren acties en "mensen sensoren" verzamelen input.

Apparaten voor speciale doeleinden, van eenvoudige temperatuursensoren tot complexe productielijnen in de fabriek met duizenden componenten erin, zijn verschillend. Deze apparaten zijn veel meer scoped in doel en zelfs als ze een aantal gebruikersinterface, ze zijn grotendeels scoped om interfacing met of worden geïntegreerd in activa in de fysieke wereld. Ze meten en rapporteren milieuomstandigheden, draaien kleppen, controle servo's, alarmen, schakellichten, en doen vele andere taken. Ze helpen om werk te doen waarvoor een informatieapparaat ofwel te generiek, te duur, te groot of te broos is. Het concrete doel dicteert onmiddellijk hun technisch ontwerp en het beschikbare monetaire budget voor hun productie en geplande levensduur operatie. De combinatie van deze twee belangrijke factoren beperkt het beschikbare operationele energiebudget, de fysieke voetafdruk en dus de beschikbare opslag-, reken- en beveiligingsmogelijkheden.

Als er iets "misgaat" met geautomatiseerde of op afstand bestuurbare apparaten, bijvoorbeeld fysieke defecten of controle logica gebreken aan opzettelijke onbevoegde inbraak en manipulatie. De productie partijen kunnen worden vernietigd, gebouwen kunnen worden geplunderd of afgebrand, en mensen kunnen gewond raken of zelfs sterven. Dit is een heel andere klasse van schade dan iemand maxing uit een gestolen credit card limiet. De beveiligingsbalk voor apparaten die dingen laten bewegen, en ook voor sensorgegevens die uiteindelijk resulteren in opdrachten die ervoor zorgen dat dingen bewegen, moet hoger zijn dan in een e-commerce- of bankscenario.

### <a name="device-control-and-device-data-interactions"></a>Apparaatbesturing en apparaatgegevensinteracties

Verbonden apparaten voor speciale doeleinden hebben een aanzienlijk aantal potentiële interactieoppervlaktes en interactiepatronen, die allemaal moeten worden beschouwd als een kader voor het beveiligen van digitale toegang tot die apparaten. De term "digitale toegang" wordt hier gebruikt om onderscheid te maken van alle bewerkingen die worden uitgevoerd door middel van directe apparaatinteractie waarbij toegangsbeveiliging wordt geboden via fysieke toegangscontrole. Bijvoorbeeld, het apparaat in een kamer met een slot op de deur. Hoewel fysieke toegang niet kan worden geweigerd met behulp van software en hardware, kunnen maatregelen worden genomen om te voorkomen dat fysieke toegang leidt tot systeeminterferentie.

Terwijl u de interactiepatronen verkent, kijkt u naar 'apparaatbesturing' en 'apparaatgegevens' met hetzelfde niveau van aandacht terwijl u bedreigingen modelleert. "Apparaatbesturing" kan worden geclassificeerd als alle informatie die door een partij aan een apparaat wordt verstrekt met als doel zijn gedrag ten opzichte van de toestand of de toestand van zijn omgeving te veranderen of te beïnvloeden. "Apparaatgegevens" kunnen worden geclassificeerd als alle informatie die een apparaat aan een andere partij uitstraalt over de toestand en de waargenomen toestand van zijn omgeving.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Bedreigingsmodellering uitvoeren voor de Azure IoT-referentiearchitectuur

Microsoft gebruikt het eerder geschetste framework om bedreigingsmodellering voor Azure IoT uit te voeren. In de volgende sectie wordt het concrete voorbeeld van Azure IoT Reference Architecture gebruikt om aan te tonen hoe u moet nadenken over bedreigingsmodellering voor IoT en hoe u de geïdentificeerde bedreigingen aanpakken. In dit voorbeeld worden vier belangrijke aandachtsgebieden geïdentificeerd:

* Apparaten en gegevensbronnen,
* Gegevenstransport,
* Apparaat- en gebeurtenisverwerking, en
* Stijl

![Bedreigingsmodellering voor Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Het volgende diagram biedt een vereenvoudigde weergave van de IoT-architectuur van Microsoft met behulp van een gegevensstroomdiagrammodel dat wordt gebruikt door het Microsoft Threat Modeling Tool:

![Bedreigingsmodellering voor Azure IoT met MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Het is belangrijk op te merken dat de architectuur het apparaat en de gatewaymogelijkheden scheidt. Deze aanpak stelt de gebruiker in staat om gebruik te maken van gateway-apparaten die veiliger zijn: ze zijn in staat om te communiceren met de cloudgateway met behulp van beveiligde protocollen, wat doorgaans een grotere verwerkingsoverhead vereist die een native apparaat - zoals een thermostaat - zou kunnen op zichzelf te verstrekken. Ga er in de Azure-serviceszone van uit dat de Cloud Gateway wordt vertegenwoordigd door de Azure IoT Hub-service.

### <a name="device-and-data-sourcesdata-transport"></a>Apparaat- en gegevensbronnen/gegevenstransport

Deze sectie verkent de architectuur die eerder beschreven door de lens van bedreiging modellering en geeft een overzicht van hoe een aantal van de inherente problemen aan te pakken. Dit voorbeeld richt zich op de kernelementen van een bedreigingsmodel:

* Processen (zowel onder uw controle als externe items)
* Communicatie (ook wel datastromen genoemd)
* Opslag (ook wel gegevensopslag genoemd)

#### <a name="processes"></a>Processen

In elk van de categorieën die in de Azure IoT-architectuur worden beschreven, probeert dit voorbeeld een aantal verschillende bedreigingen in de verschillende fasen te beperken waarin gegevens/informatie bestaat: proces, communicatie en opslag. Hieronder volgt een overzicht van de meest voorkomende voor de categorie "proces", gevolgd door een overzicht van hoe deze bedreigingen het best kunnen worden beperkt:

**Spoofing (S):** Een aanvaller kan cryptografisch sleutelmateriaal uit een apparaat halen, hetzij op software- of hardwareniveau, en vervolgens toegang krijgen tot het systeem met een ander fysiek of virtueel apparaat onder de identiteit van het apparaat waarvan het sleutelmateriaal is verwijderd. Een goede illustratie is afstandsbedieningen die elke TV kan draaien en dat zijn populaire prankster tools.

**Denial of Service (D):** Een apparaat kan niet meer functioneren of communiceren door storende radiofrequenties of het snijden van draden. Een bewakingscamera met opzettelijk uitgeschakelde stroom- of netwerkverbinding kan bijvoorbeeld helemaal geen gegevens rapporteren.

**Knoeien (T):** Een aanvaller kan de software die op het apparaat draait geheel of gedeeltelijk vervangen, waardoor de vervangen software mogelijk gebruik kan maken van de echte identiteit van het apparaat als het sleutelmateriaal of de cryptografische faciliteiten met sleutelmateriaal beschikbaar waren voor het illegale programma. Een aanvaller kan bijvoorbeeld extraheerssleutelmateriaal gebruiken om gegevens van het apparaat op het communicatiepad te onderscheppen en te onderdrukken en te vervangen door valse gegevens die zijn geverifieerd met het gestolen sleutelmateriaal.

**Information Disclosure (I)**: Als het apparaat gemanipuleerde software draait, kan dergelijke gemanipuleerde software mogelijk gegevens lekken naar onbevoegde partijen. Een aanvaller kan bijvoorbeeld geëxtraheerd sleutelmateriaal gebruiken om zichzelf in het communicatiepad tussen het apparaat en een controller- of veldgateway of cloudgateway te injecteren om informatie af te toveren.

**Verhoging van privilege (E)**: Een apparaat dat specifieke functie doet kan worden gedwongen om iets anders te doen. Bijvoorbeeld, een klep die is geprogrammeerd om halverwege te openen kan worden misleid om helemaal te openen.

| **Component** | **Bedreiging** | **Oplossing** | **Risico** | **Uitvoering** |
| --- | --- | --- | --- | --- |
| Apparaat |S |Identiteit toewijzen aan het apparaat en het apparaat verifiëren |Het vervangen van het apparaat of een deel van het apparaat door een ander apparaat. Hoe weet je dat je tegen het juiste apparaat praat? |Het apparaat verifiëren met TLS (Transport Layer Security) of IPSec. Infrastructuur moet ondersteuning bieden voor het gebruik van pre-shared key (PSK) op apparaten die volledige asymmetrische cryptografie niet aankunnen. Azure AD, [OAuth gebruiken](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID (TRID) |Pas tamperproof mechanismen toe op het apparaat, bijvoorbeeld door het moeilijk tot onmogelijk te maken om sleutels en ander cryptografisch materiaal uit het apparaat te halen. |Het risico is als iemand het apparaat knoeit (fysieke interferentie). Hoe weet je zeker, dat apparaat is niet geknoeid. |De meest effectieve mitigatie is een vertrouwde platformmodule (TPM) mogelijkheid waarmee sleutels kunnen worden opgeslagen in speciale on-chip circuits van waaruit de sleutels niet kunnen worden gelezen, maar alleen kan worden gebruikt voor cryptografische bewerkingen die de sleutel gebruiken, maar nooit de sleutel onthullen. Geheugenversleuteling van het apparaat. Sleutelbeheer voor het apparaat. De code ondertekenen. |
|| E |Het hebben van toegangscontrole van het apparaat. Vergunningsregeling. |Als het apparaat het mogelijk maakt om afzonderlijke acties uit te voeren op basis van opdrachten van een externe bron of zelfs gecompromitteerde sensoren, kan de aanval bewerkingen uitvoeren die anders niet toegankelijk zijn. |Een vergunningsregeling voor het apparaat |
| Veldgateway |S |De veldgateway naar Cloud Gateway verifiëren (zoals op basis van cert, PSK of Claim gebaseerd.) |Als iemand Field Gateway kan spoofen, kan deze zich presenteren als elk apparaat. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Allemaal dezelfde key storage en attest betreft van apparaten in het algemeen - het beste geval is gebruik TPM. 6LowPAN-extensie voor IPSec ter ondersteuning van Wireless Sensor Networks (WSN). |
|| TRID (TRID) |Bescherm de veldgateway tegen manipulatie (TPM?) |Spoofing-aanvallen die de cloudgateway misleiden en denken dat het met de veldgateway praat, kunnen leiden tot openbaarmaking van informatie en het knoeien met gegevens |Geheugenversleuteling, TPM's, authenticatie. |
|| E |Toegangscontrolemechanisme voor veldgateway | | |

Hier volgen enkele voorbeelden van bedreigingen in deze categorie:

**Spoofing**: Een aanvaller kan cryptografisch sleutelmateriaal uit een apparaat halen, hetzij op software- of hardwareniveau, en vervolgens toegang krijgen tot het systeem met een ander fysiek of virtueel apparaat onder de identiteit van het apparaat waarvan het sleutelmateriaal is verwijderd.

**Denial of Service**: Een apparaat kan niet meer functioneren of communiceren door storende radiofrequenties of het doorsnijden van draden. Een bewakingscamera met opzettelijk uitgeschakelde stroom- of netwerkverbinding kan bijvoorbeeld helemaal geen gegevens rapporteren.

**Knoeien**: Een aanvaller kan de software die op het apparaat draait geheel of gedeeltelijk vervangen, waardoor de vervangen software mogelijk gebruik kan maken van de echte identiteit van het apparaat als het sleutelmateriaal of de cryptografische faciliteiten met sleutelmaterialen beschikbaar waren voor het illegale programma.

**Knoeien**: Een bewakingscamera die een zichtbaar spectrumbeeld van een lege gang toont zou op een foto van zulk een gang kunnen worden gericht. Een rook- of brandsensor kan iemand melden die er onder een aansteker onder zit. In beide gevallen kan het apparaat technisch volledig betrouwbaar zijn ten opzichte van het systeem, maar het rapporteert gemanipuleerde informatie.

**Knoeien**: Een aanvaller kan extraheersbelangrijk materiaal gebruiken om gegevens van het apparaat op het communicatiepad te onderscheppen en te onderdrukken en te vervangen door valse gegevens die zijn geverifieerd door het gestolen sleutelmateriaal.

**Knoeien**: Een aanvaller kan de software die op het apparaat draait geheel of gedeeltelijk vervangen, waardoor de vervangen software mogelijk gebruik kan maken van de echte identiteit van het apparaat als het sleutelmateriaal of de cryptografische faciliteiten met sleutelmaterialen beschikbaar waren voor het illegale programma.

**Informatie openbaarmaking:** Als het apparaat draait gemanipuleerde software, dergelijke gemanipuleerde software kan mogelijk lekken gegevens aan onbevoegde partijen.

**Informatieinformatie:** Een aanvaller kan geëxtraheerd sleutelmateriaal gebruiken om zichzelf in het communicatiepad tussen het apparaat en een controller- of veldgateway of cloudgateway te injecteren om informatie te overhevelen.

**Denial of Service**: Het apparaat kan worden uitgeschakeld of omgezet in een modus waarin communicatie niet mogelijk is (wat opzettelijk is in veel industriële machines).

**Knoeien**: Het apparaat kan opnieuw worden geconfigureerd om te werken in een toestand die onbekend is met het besturingssysteem (buiten bekende kalibratieparameters) en zo gegevens verstrekken die verkeerd kunnen worden geïnterpreteerd

**Verhoging van privilege**: Een apparaat dat specifieke functie doet kan worden gedwongen om iets anders te doen. Bijvoorbeeld, een klep die is geprogrammeerd om halverwege te openen kan worden misleid om helemaal te openen.

**Denial of Service**: Het apparaat kan worden omgezet in een status waarin communicatie niet mogelijk is.

**Knoeien**: Het apparaat kan opnieuw worden geconfigureerd om te werken in een staat die onbekend is met het besturingssysteem (buiten bekende kalibratieparameters) en zo gegevens verstrekken die verkeerd kunnen worden geïnterpreteerd.

**Spoofing/tampering/repudiation**: Als niet beveiligd (wat zelden het geval is met de afstandsbedieningen van de consument), kan een aanvaller de toestand van een apparaat anoniem manipuleren. Een goede illustratie is afstandsbedieningen die elke TV kan draaien en dat zijn populaire prankster tools.

#### <a name="communication"></a>Communicatie

Bedreigingen rond communicatiepad tussen apparaten, apparaten en veldgateways en apparaat- en cloudgateway. De volgende tabel heeft enkele richtlijnen rond open sockets op het apparaat/VPN:

| **Component** | **Bedreiging** | **Oplossing** | **Risico** | **Uitvoering** |
| --- | --- | --- | --- | --- |
| Apparaat IoT-hub |Tid |(D) TLS (PSK/RSA) om het verkeer te versleutelen |Het afluisteren of verstoren van de communicatie tussen het apparaat en de gateway |Beveiliging op protocolniveau. Met aangepaste protocollen moet u uitzoeken hoe u ze beschermen. In de meeste gevallen vindt de communicatie plaats van het apparaat naar de IoT Hub (apparaat initieert de verbinding). |
| Apparaat naar apparaat |Tid |(D) TLS (PSK/RSA) om het verkeer te versleutelen. |Het lezen van gegevens in doorvoer tussen apparaten. Knoeien met de gegevens. Overbelasting van het apparaat met nieuwe aansluitingen |Beveiliging op protocolniveau (MQTT/AMQP/HTTP/CoAP. Met aangepaste protocollen moet u uitzoeken hoe u ze beschermen. De beperking voor de DoS-bedreiging is om apparaten te peeren via een cloud- of veldgateway en deze alleen als clients naar het netwerk te laten fungeren. Het peering kan resulteren in een directe verbinding tussen de peers na te zijn bemiddeld door de gateway |
| Extern entiteitsapparaat |Tid |Sterke koppeling van de externe entiteit met het apparaat |Afluisteren van de verbinding met het apparaat. De communicatie met het apparaat verstoren |De externe entiteit veilig koppelen aan het apparaat NFC/Bluetooth LE. Het operationele paneel van het apparaat bedienen (Fysiek) |
| Field Gateway Cloud Gateway |Tid |TLS (PSK/RSA) om het verkeer te versleutelen. |Het afluisteren of verstoren van de communicatie tussen het apparaat en de gateway |Beveiliging op protocolniveau (MQTT/AMQP/HTTP/CoAP). Met aangepaste protocollen moet u uitzoeken hoe u ze beschermen. |
| Cloudgateway voor apparaten |Tid |TLS (PSK/RSA) om het verkeer te versleutelen. |Het afluisteren of verstoren van de communicatie tussen het apparaat en de gateway |Beveiliging op protocolniveau (MQTT/AMQP/HTTP/CoAP). Met aangepaste protocollen moet u uitzoeken hoe u ze beschermen. |

Hier volgen enkele voorbeelden van bedreigingen in deze categorie:

**Denial of Service**: Beperkte apparaten staan over het algemeen onder DoS-dreiging wanneer ze actief luisteren naar binnenkomende verbindingen of ongevraagde datagrammen op een netwerk, omdat een aanvaller veel verbindingen parallel kan openen en ze niet kan onderhouden of ze langzaam service, of het apparaat kan worden overspoeld met ongevraagd verkeer. In beide gevallen kan het apparaat effectief onbruikbaar worden gemaakt op het netwerk.

**Spoofing, Information Disclosure**: Beperkte apparaten en apparaten voor speciale doeleinden hebben vaak een-voor-alle beveiligingsvoorzieningen zoals wachtwoord- of pincodebeveiliging, of ze vertrouwen volledig op het vertrouwen van het netwerk, wat betekent dat ze toegang verlenen tot informatie wanneer een apparaat zich op hetzelfde netwerk bevindt, en dat netwerk vaak alleen wordt beschermd door een gedeelde sleutel. Dat betekent dat wanneer het gedeelde geheim van apparaat of netwerk wordt bekendgemaakt, het mogelijk is om het apparaat te bedienen of gegevens te observeren die van het apparaat worden uitgezonden.  

**Spoofing**: een aanvaller kan de uitzending onderscheppen of gedeeltelijk overschrijven en de initiator spoofen (man in het midden)

**Knoeien:** een aanvaller kan de uitzending onderscheppen of gedeeltelijk overschrijven en valse informatie verzenden 

**Information Disclosure:** een aanvaller kan een uitzending afluisteren en informatie verkrijgen zonder autorisatie **Denial of Service:** een aanvaller kan het uitzendsignaal blokkeren en informatiedistributie weigeren

#### <a name="storage"></a>Storage

Elk apparaat en veld gateway heeft een vorm van opslag (tijdelijk voor het in de rij staan van de gegevens, besturingssysteem (OS) beeldopslag).

| **Component** | **Bedreiging** | **Oplossing** | **Risico** | **Uitvoering** |
| --- | --- | --- | --- | --- |
| Apparaatopslag |TRID (TRID) |Opslagversleuteling, het ondertekenen van de logboeken |Gegevens uit de opslag (PII-gegevens) lezen, knoeien met telemetriegegevens. Knoeien met gegevens over opdrachtbeheer in de wachtrij of in de cache. Knoeien met configuratie- of firmware-updatepakketten terwijl ze in de cache staan of lokaal in de wachtrij staan, kan ertoe leiden dat os- en/of systeemonderdelen worden gecompromitteerd |Versleuteling, berichtverificatiecode (MAC) of digitale handtekening. Waar mogelijk, sterk toegangscontrole via resource access control lijsten (ACL's) of machtigingen. |
| Afbeelding van apparaatbesturingssysteem |TRID (TRID) | |Knoeien met os /vervangen van de OS-componenten |Alleen-lezen OS-partitie, ondertekende OS-afbeelding, Versleuteling |
| Field Gateway-opslag (in de rij staan van de gegevens) |TRID (TRID) |Opslagversleuteling, het ondertekenen van de logboeken |Gegevens uit de opslag (PII-gegevens) lezen, knoeien met telemetriegegevens, knoeien met gegevens over opdrachtbeheer in de wachtrij of in de cache. Knoeien met configuratie- of firmware-updatepakketten (bestemd voor apparaten of veldgateway), terwijl in de cache of lokaal in de wachtrij staan, kan ertoe leiden dat OS- en/of systeemonderdelen worden gecompromitteerd |BitLocker |
| Afbeelding van veldgateway-besturingssysteem |TRID (TRID) | |Knoeien met os /vervangen van de OS-componenten |Alleen-lezen OS-partitie, ondertekende OS-afbeelding, Versleuteling |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Apparaat- en gebeurtenisverwerking/cloudgatewayzone

Een cloudgateway is een systeem dat communicatie op afstand van en naar apparaten of veldgateways van verschillende sites in de openbare netwerkruimte mogelijk maakt, meestal naar een cloudgebaseerd controle- en gegevensanalysesysteem, een federatie van dergelijke systemen. In sommige gevallen kan een cloudgateway de toegang tot apparaten voor speciale doeleinden onmiddellijk vergemakkelijken vanaf terminals zoals tablets of telefoons. In de hier besproken context is "cloud" bedoeld om te verwijzen naar een speciaal gegevensverwerkingssysteem dat niet gebonden is aan dezelfde site als de aangesloten apparaten of veldgateways, en waar operationele maatregelen gerichte fysieke toegang verhinderen, maar niet noodzakelijkerwijs naar een " infrastructuur voor openbare cloud.public cloud". Een cloudgateway kan mogelijk worden toegewezen aan een netwerkvirtualisatieoverlay om de cloudgateway en alle aangesloten apparaten of veldgateways van ander netwerkverkeer te isoleren. De cloudgateway zelf is geen apparaatbeheersysteem of verwerkings- of opslagfaciliteit voor apparaatgegevens; deze faciliteiten interface met de cloud gateway. De cloudgatewayzone bevat de cloudgateway zelf, samen met alle veldgateways en apparaten die er direct of indirect aan zijn gekoppeld.

Cloud gateway is meestal op maat gebouwd stukje software dat wordt uitgevoerd als een service met blootgestelde eindpunten waarop veldgateway en apparaten verbinding maken. Als zodanig moet worden ontworpen met de veiligheid in het achterhoofd. Volg [het SDL-proces](https://www.microsoft.com/sdl) voor het ontwerpen en bouwen van deze service.

#### <a name="services-zone"></a>Dienstenzone

Een besturingssysteem (of controller) is een softwareoplossing die interfaces met een apparaat, een veldgateway of cloudgateway met het oog op het besturen van een of meerdere apparaten en/of het verzamelen en/of opslaan en/of analyseren van apparaatgegevens voor presentatie- of daaropvolgende besturingsdoeleinden. Controlesystemen zijn de enige entiteiten in het kader van deze discussie die de interactie met mensen onmiddellijk kunnen vergemakkelijken. De uitzonderingen zijn tussenliggende fysieke controleoppervlakken op apparaten, zoals een schakelaar waarmee een persoon het apparaat kan uitschakelen of andere eigenschappen kan wijzigen, en waarvoor er geen functioneel equivalent is dat digitaal kan worden geopend.

Intermediaire fysieke controleoppervlakken zijn die waar de regerende logica de functie van het fysieke controleoppervlak zodanig beperkt dat een gelijkwaardige functie op afstand kan worden gestart of invoerconflicten met externe invoer kunnen worden vermeden – dergelijke bedieningsoppervlakken zijn conceptueel gekoppeld aan een lokaal besturingssysteem dat gebruik maakt van dezelfde onderliggende functionaliteit als elk ander afstandsbedieningssysteem waaraan het apparaat parallel kan worden bevestigd. De belangrijkste bedreigingen voor de cloud computing zijn te lezen op de pagina [Cloud Security Alliance (CSA).](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/)

## <a name="additional-resources"></a>Aanvullende bronnen

Raadpleeg voor meer informatie de volgende artikelen:

* [SDL Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT-referentiearchitectuur](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
