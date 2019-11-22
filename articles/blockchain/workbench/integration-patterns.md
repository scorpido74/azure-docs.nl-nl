---
title: Integratie patronen voor Smart-contracten-Azure Block Chain workbench
description: Overzicht van de integratie patronen voor Smart-contracten in azure Block Chain Workbench preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 02789b2e4414af8503a655ea954b40031df8ccb7
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286679"
---
# <a name="smart-contract-integration-patterns"></a>Integratiepatronen voor slimme contracten

Slimme contracten vertegenwoordigen vaak een zakelijke werk stroom die moet worden geïntegreerd met externe systemen en apparaten.

De vereisten van deze werkstromen zijn onder meer de behoefte om transacties op een gedistribueerd grootboek met gegevens uit een extern systeem, service of een apparaat te starten. Ze moeten ook externe systemen laten reageren op gebeurtenissen die voortkomen uit slimme contracten op een gedistribueerd grootboek.

De integratie van REST API en berichten verzendt trans acties van externe systemen naar slimme contracten die zijn opgenomen in een Azure Block Chain Workbench-toepassing. Ook worden gebeurtenis meldingen naar externe systemen verzonden op basis van wijzigingen die in een toepassing plaatsvinden.

Voor scenario's voor gegevens integratie bevat Azure Block Chain Workbench een set database weergaven waarmee u een combi natie van transactionele gegevens uit de Block chain en meta gegevens over toepassingen en slimme contracten kunt samen voegen.

Sommige scenario's, bijvoorbeeld met betrekking tot de toeleveringsketen of media, kunnen ook de integratie van documenten vereisen. Hoewel Azure Blockchain Workbench geen API-aanroepen biedt voor het rechtstreeks verwerken van documenten, kunnen documenten worden opgenomen in een blockchain-toepassing. Deze sectie bevat ook dat patroon.

Deze sectie bevat de patronen die zijn geïdentificeerd voor de implementatie van elk van deze typen integraties in uw end-to-end-oplossingen.

## <a name="rest-api-based-integration"></a>Integratie op basis van REST API

Mogelijkheden binnen de door Azure Blockchain Workbench gegenereerde webtoepassing worden beschikbaar gemaakt via de REST API. Mogelijkheden zijn Azure Blockchain Workbench uploaden, configuratie en beheer van toepassingen, het verzenden van transacties naar een gedistribueerd grootboek en het opvragen van metagegevens en grootboekgegevens van toepassingen.

De REST-API wordt voornamelijk gebruikt voor interactieve clients, zoals web-, mobiele, en bot-toepassingen.

In deze sectie worden patronen besproken die zijn gericht op de aspecten van de REST-API die transacties verzenden naar een gedistribueerd grootboek en patronen die gegevens opvragen over transacties van de *off-chain* SQL-database van Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Het verzenden van transacties naar een gedistribueerd grootboek vanuit een extern systeem

Met Azure Block Chain Workbench REST API worden geverifieerde aanvragen verzonden voor het uitvoeren van trans acties in een gedistribueerd groot boek.

![Transacties verzenden naar een gedistribueerd grootboek](./media/integration-patterns/send-transactions-ledger.png)

Het uitvoeren van transacties gebeurt met behulp van het eerder afgebeelde proces, waarbij:

-   De externe toepassing wordt geverifieerd met de Azure Active Directory die is ingericht als onderdeel van de Azure Blockchain Workbench-implementatie.
-   Geautoriseerde gebruikers ontvangen een bearer-token dat samen met aanvragen naar de API kan worden verzonden.
-   Externe toepassingen doen aanroepen naar de REST-API met behulp van het bearer-token.
-   De REST API verpakt de aanvraag als een bericht en verzendt deze naar de Service Bus. Vanaf hier wordt het opgehaald, ondertekend en verzonden naar het juiste gedistribueerde grootboek.
-   De REST API vraagt een Azure Blockchain Workbench SQL DB om de aanvraag te registreren en de huidige inrichtingsstatus vast te stellen.
-   De SQL DB retourneert de inrichtingsstatus en de API-aanroep retourneert het ID naar de externe toepassing die deze heeft aangeroepen.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Query's uitvoeren op meta gegevens van Block Chain Workbench en gedistribueerde grootboek transacties

De REST-API van Azure Blockchain Workbench verzendt geverifieerde aanvragen om details met betrekking tot de uitvoering van een slim contract op een gedistribueerd grootboek te verkrijgen.

![Meta gegevens opvragen](./media/integration-patterns/querying-metadata.png)

Query's vinden plaats met behulp van het eerder afgebeelde proces, waarbij:

1. De externe toepassing wordt geverifieerd met de Azure Active Directory die is ingericht als onderdeel van de Azure Blockchain Workbench-implementatie.
2. Geautoriseerde gebruikers ontvangen een bearer-token dat samen met aanvragen naar de API kan worden verzonden.
3. Externe toepassingen doen aanroepen naar de REST-API met behulp van het bearer-token.
4. De REST-API haalt de gegevens van de aanvraag uit de SQL DB en retourneert deze naar de client.

## <a name="messaging-integration"></a>Integratie van berichten

Integratie met berichten vergemakkelijkt de interactie met systemen, services en apparaten waarbij een interactieve aanmelding niet mogelijk of wenselijk is. Integratie met berichten is gericht op twee typen berichten: berichten die vragen om uitvoering van transacties op een gedistribueerd grootboek en gebeurtenissen die worden weergegeven door het grootboek wanneer transacties hebben plaatsgevonden.

Integratie met berichten richt zich op het uitvoeren en monitoren van transacties met betrekking tot het maken van een gebruikersaccount, het maken van contracten en het uitvoeren van transacties op contracten en wordt voornamelijk gebruikt door *headless* back-end-systemen.

In deze sectie bekijken we patronen die zijn gericht op de aspecten van de op berichten gebaseerde API die transacties naar een gedistribueerd grootboek kan verzenden en patronen die gebeurtenisberichten vertegenwoordigen die worden weergegeven door het onderliggende gedistribueerde grootboek.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Eenzijdige gebeurtenisverzending van een slim contract naar een gebeurtenisconsumer 

In dit scenario vindt een gebeurtenis plaats binnen een slim contract, bijvoorbeeld een statuswijziging of de uitvoering van een specifiek type transactie. Deze gebeurtenis wordt verzonden via een Event Grid naar downstream-consumenten, en die consumenten nemen vervolgens passende acties.

Een voorbeeld van dit scenario is dat wanneer een transactie wordt uitgevoerd, een consument wordt gewaarschuwd en actie kan ondernemen, zoals het vastleggen van de informatie in een SQL DB of de Common Data Service. Dit scenario is hetzelfde patroon dat Workbench volgt om zijn *off-chain* SQL DB te vullen.

Een andere optie is als een smart contract overgaat naar een bepaalde status, bijvoorbeeld wanneer een contract wordt afgesloten met een *OutOfCompliance*. Wanneer deze statuswijziging plaatsvindt, kan dit een waarschuwing activeren voor verzending naar de mobiele telefoon van een beheerder.

![Eenrichtings gebeurtenis levering](./media/integration-patterns/one-way-event-delivery.png)

Dit scenario vindt plaats met behulp van het eerder afgebeelde proces, waarbij:

-   Het slimme contract gaat over naar een nieuwe status en verzendt een gebeurtenis naar het grootboek.
-   Het grootboek ontvangt en levert de gebeurtenis af aan Azure Blockchain Workbench.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit het grootboek en ontvangt de gebeurtenis.
-   Azure Blockchain Workbench publiceert de gebeurtenis voor abonnees op het Event Grid.
-   Externe systemen zijn geabonneerd op het Event Grid, nemen het bericht op en nemen de juiste acties.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Eenzijdige bezorging van gebeurtenissen van een bericht van een extern systeem op een slim contract

Er is ook een scenario dat in de tegenovergestelde richting werkt. In dit geval wordt een gebeurtenis gegenereerd door een sensor of een extern systeem en moeten de gegevens van die gebeurtenis naar een slim contract worden verzonden.

Een bekend voorbeeld is de levering van gegevens van financiële markten, bijvoorbeeld prijzen van grondstoffen, aandelen of obligaties, aan een slim contract.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Directe levering van een Azure Blockchain Workbench-bericht in de verwachte indeling

Sommige toepassingen zijn gebouwd om te integreren met Azure Blockchain Workbench en genereren en verzenden direct berichten in de verwachte formaten.

![Directe levering](./media/integration-patterns/direct-delivery.png)

Deze levering vindt plaats met behulp van het eerder afgebeelde proces, waarbij:

-   In een extern systeem vindt een gebeurtenis plaats die de aanmaak van een bericht voor Azure Blockchain Workbench in gang zet.
-   Het externe systeem bevat code die is geschreven om dit bericht in een bekend formaat te maken en stuurt het rechtstreeks naar de Service Bus.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de servicebus en haalt het bericht op.
-   Azure Blockchain Workbench initieert een aanroep naar het grootboek, waarbij gegevens van het externe systeem naar een specifiek contract worden verzonden.
-   Na ontvangst van het bericht wordt het contract omgezet naar een nieuwe status.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Levering van een bericht in een formaat dat onbekend is bij Azure Blockchain Workbench

Sommige systemen kunnen niet worden aangepast om berichten weer te geven in de standaard-indelingen die worden gebruikt door Azure Blockchain Workbench. In deze gevallen kunnen vaak bestaande mechanismen en berichtformaten van deze systemen worden gebruikt. Met name kunnen de systeemeigen berichttypen van deze systemen worden getransformeerd naar een van de verwachte standaard berichtindelingen met behulp van Logic Apps, Azure Functions of andere aangepaste code.

![Onbekende bericht indeling](./media/integration-patterns/unknown-message-format.png)

Dit gebeurt met behulp van het eerder afgebeelde proces, waarbij:

-   In een extern systeem vindt een gebeurtenis plaats waardoor het maken van een bericht wordt geactiveerd.
-   Er wordt een logische app of aangepaste code gebruikt om dat bericht te ontvangen en het te transformeren naar een standaard Azure Blockchain Workbench-geformatteerd bericht.
-   De logische app stuurt het getransformeerde bericht rechtstreeks naar de Service Bus.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de servicebus en haalt het bericht op.
-   Azure Blockchain Workbench initieert een aanroep naar het grootboek en verzendt gegevens van het externe systeem naar een specifieke functie in het contract.
-   De functie wordt uitgevoerd en wijzigt meestal de status. De statuswijziging zorgt ervoor dat de bedrijfswerkstroom die wordt weergegeven in het slimme contract verdergaat, waardoor nu indien nodig andere functies kunnen worden uitgevoerd.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Besturing overdragen aan een extern proces en op voltooiing wachten

Er zijn scenario's waarbij een slim contract de interne uitvoering moet stoppen en de uitvoering overdragen aan een extern proces. Het externe proces wordt dan uitgevoerd en stuurt een bericht naar het slimme contract, waarna de uitvoering weer verdergaat binnen het slimme contract.

#### <a name="transition-to-the-external-process"></a>Overgang naar het externe proces

Dit patroon wordt gewoonlijk geïmplementeerd met de volgende methode:

-   Het slimme contract gaat over naar een specifieke status. In deze status kunnen geen of een beperkt aantal functies worden uitgevoerd totdat een extern systeem een gewenste actie uitvoert.
-   De statuswijziging komt naar voren als een gebeurtenis voor een downstream-consument.
-   De downstream-consument ontvangt de gebeurtenis en activeert de uitvoering van externe code.

![Overgang naar extern proces](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Controle terugkrijgen van het slimme contract

Afhankelijk van de mogelijkheid om het externe systeem aan te passen, kan het al dan niet in staat zijn om berichten af te leveren in een van de standaardindelingen die Azure Blockchain Workbench verwacht. Op basis van de mogelijkheid van het externe systeem om een van deze berichten te genereren, wordt bepaald welke van de volgende twee retourpaden wordt genomen.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Directe levering van een Azure Blockchain Workbench-bericht in de verwachte indeling

![](./media/integration-patterns/direct-delivery.png)

In dit model vindt de communicatie naar het contract en de daaropvolgende statuswijziging plaats na het vorige proces waarbij:

-   Nadat de voltooiing of een specifieke mijlpaal in de uitvoering van de externe code is bereikt, wordt een gebeurtenis verzonden naar de Service Bus die is verbonden met Azure Blockchain Workbench.

-   Voor systemen die niet direct kunnen worden aangepast om een bericht te schrijven dat voldoet aan de verwachtingen van de API, wordt het getransformeerd.

-   De inhoud van het bericht wordt verpakt en verzonden naar een specifieke functie in het smart contract. Deze levering gebeurt namens de gebruiker die is gekoppeld aan het externe systeem.

-   De functie wordt uitgevoerd en wijzigt meestal de status. De statuswijziging zorgt ervoor dat de bedrijfswerkstroom die wordt weergegeven in het slimme contract verdergaat, waardoor nu indien nodig andere functies kunnen worden uitgevoerd.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Levering van een bericht in een formaat dat onbekend is bij Azure Blockchain Workbench

![Onbekende bericht indeling](./media/integration-patterns/unknown-message-format.png)

In dit model waarbij een bericht in een standaardindeling niet rechtstreeks kan worden verzonden, vindt de communicatie naar het contract en de daaropvolgende statuswijziging plaats na het vorige proces waarbij:

1.  Nadat de voltooiing of een specifieke mijlpaal in de uitvoering van de externe code is bereikt, wordt een gebeurtenis verzonden naar de Service Bus die is verbonden met Azure Blockchain Workbench.
2.  Er wordt een logische app of aangepaste code gebruikt om dat bericht te ontvangen en het te transformeren naar een standaard Azure Blockchain Workbench-geformatteerd bericht.
3.  De logische app stuurt het getransformeerde bericht rechtstreeks naar de Service Bus.
4.  Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de servicebus en haalt het bericht op.
5.  Azure Blockchain Workbench initieert een aanroep naar het grootboek, waarbij gegevens van het externe systeem naar een specifiek contract worden verzonden.
6. De inhoud van het bericht wordt verpakt en verzonden naar een specifieke functie in het smart contract. Deze levering gebeurt namens de gebruiker die is gekoppeld aan het externe systeem.
7.  De functie wordt uitgevoerd en wijzigt meestal de status. De statuswijziging zorgt ervoor dat de bedrijfswerkstroom die wordt weergegeven in het slimme contract verdergaat, waardoor nu indien nodig andere functies kunnen worden uitgevoerd.

## <a name="iot-integration"></a>IoT-integratie

Een gebruikelijk integratiescenario is het opnemen van telemetrische gegevens afkomstig van sensoren in een slim contract. Op basis van door sensoren geleverde gegevens kunnen slimme contracten gepaste acties ondernemen en de status van het contract wijzigen.

Als de temperatuur van een vrachtwagen die geneesmiddelen bezorgt, bijvoorbeeld boven de 45 graden komt, zou dat de effectiviteit van het geneesmiddel kunnen verminderen, en zou er een probleem  voor de openbare veiligheid kunnen ontstaan als dit niet wordt gedetecteerd en het geneesmiddel niet uit de toeleveringsketen wordt verwijderd. Als een stuur programma zijn auto versnelt naar 100 mijl per uur, zou de resulterende sensor informatie een annulering van de verzekering door hun verzekerings provider kunnen activeren. Als de auto een huurauto was, zouden GPS-gegevens kunnen aangeven dat de bestuurder buiten het gebied van de huurovereenkomst is gegaan, en zou het verhuurbedrijf een boete kunnen berekenen.

De uitdaging is dat deze sensoren op constante basis gegevens kunnen leveren en het niet gepast is om al deze gegevens naar een slim contract te sturen. Een typische benadering is om het aantal berichten dat naar de blockchain wordt verzonden te beperken terwijl alle berichten aan een secundaire opslag worden bezorgd. Lever bijvoorbeeld ontvangen berichten alleen met een vast interval, bijvoorbeeld eenmaal per uur, en wanneer een ingesloten waarde buiten een overeengekomen bereik voor een slim contract valt. Het controleren van waarden die buiten de toleranties vallen, zorgt ervoor dat de gegevens die relevant zijn voor de zakelijke logica van de contracten worden ontvangen en uitgevoerd. Door de waarde op het interval te controleren, wordt bevestigd dat de sensor nog steeds rapporteert. Alle gegevens worden verzonden naar een secundaire rapportageopslag om een bredere rapportering, analyse en machine-learning mogelijk te maken. Het is bijvoorbeeld mogelijk dat het voor een slim contract niet nodig is om elke minuut sensormetingen voor GPS te ontvangen, maar het kan interessante gegevens opleveren die in rapporten of mappingroutes kunnen worden gebruikt.

Op het Azure-platform wordt integratie met apparaten meestal uitgevoerd met IoT Hub. IoT Hub biedt routering van berichten op basis van inhoud en maakt het eerder beschreven type functionaliteit mogelijk.

![IoT-berichten](./media/integration-patterns/iot.png)

Het proces toont een patroon:

-   Een apparaat communiceert rechtstreeks of via een veld Gateway naar IoT Hub.
-   IoT Hub ontvangt de berichten en evalueert de berichten bijvoorbeeld op basis van routes die de inhoud van het bericht controleren. *Geeft de sensor een temperatuur van meer dan 50 graden aan?*
-   De IoT Hub verzendt berichten die voldoen aan de criteria voor een gedefinieerde Service Bus voor de route.
-   Een Logic App of andere code luistert naar de Service Bus die IoT Hub voor de route heeft ingesteld.
-   De logische app of andere code haalt het bericht op en transformeert het naar een bekend formaat.
-   Het getransformeerde bericht, nu in een standaardformaat, wordt naar de Service Bus voor Azure Blockchain Workbench verzonden.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de servicebus en haalt het bericht op.
-   Azure Blockchain Workbench initieert een aanroep naar het grootboek, waarbij gegevens van het externe systeem naar een specifiek contract worden verzonden.
-   Na ontvangst van het bericht evalueert het contract de gegevens en kan het de status wijzigen op basis van de uitkomst van die evaluatie, en bijvoorbeeld bij een hoge temperatuur de status wijzigen in *niet conform*.

## <a name="data-integration"></a>Gegevensintegratie

Naast REST en berichtgebaseerde API biedt Azure Blockchain Workbench ook toegang tot een SQL-database die is gevuld met applicatie- en contractmetagegevens, evenals transactiegegevens van gedistribueerde grootboeken.

![Gegevensintegratie](./media/integration-patterns/data-integration.png)

De gegegevensintegratie is bekend:

-   Azure Blockchain Workbench slaat metagegevens over toepassingen, workflows, contracten en transacties op als onderdeel van zijn normale uitvoeringsgedrag.
-   Externe systemen of hulpmiddelen bieden een of meer dialoogvensters om het verzamelen van informatie over de database te vergemakkelijken, zoals databaseservernaam, databasenaam, type authenticatie, aanmeldingsreferenties en welke database-views moeten worden gebruikt.
-   Query's worden geschreven tegen SQL-databaseweergaven om downstream consumptie door externe systemen, services, rapportage, ontwikkelaarstools en productiviteitstools voor bedrijven te vergemakkelijken.

## <a name="storage-integration"></a>Opslagintegratie

In veel scenario's kan het nodig zijn attesteerbare bestanden op te nemen. Om meerdere redenen is het onverstandig om bestanden in een blockchain te plaatsen. In plaats daarvan is een veelgebruikte benadering om een cryptografische hash (bijvoorbeeld SHA-256) op een bestand uit te voeren en die hash op een gedistribueerd grootboek te delen. Als de hash op een later tijdstip opnieuw wordt uitgevoerd, moet hetzelfde resultaat worden geretourneerd. Als het bestand wordt gewijzigd, resulteert de hash in een andere waarde, zelfs als slechts één pixel in een afbeelding is gewijzigd.

![Opslagintegratie](./media/integration-patterns/storage-integration.png)

Het patroon kan worden geïmplementeerd, waarbij:

-   Een extern systeem een bestand bewaart in een opslagmechanisme, zoals Azure Storage.
-   Er een hash wordt gegenereerd met het bestand of het bestand en bijbehorende metagegevens, zoals een id voor de eigenaar, de URL waar het bestand zich bevindt, enzovoort.
-   De hash en alle metagegevens worden verzonden naar een functie in een slim contract, zoals *FileAdded*.
-   In de toekomst kunnen het bestand en de metagegevens opnieuw worden gehasht en vergeleken met de waarden die in het grootboek zijn opgeslagen.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Vereisten voor het implementeren van integratiepatronen met behulp van de REST- en bericht-API's

Om het een extern systeem of apparaat mogelijk te maken met het slimme contract te communiceren met behulp van de REST- of bericht-API, moet het volgende gebeuren:

1. In de Azure Active Directory voor het consortium wordt een account gemaakt dat het externe systeem of apparaat vertegenwoordigt.
2. Een of meer geschikte slimme contracten voor uw Azure Blockchain Workbench-applicatie hebben functies die zijn gedefinieerd om de gebeurtenissen van uw externe systeem of apparaat te accepteren.
3. Het configuratiebestand van de toepassing voor uw slimme contract bevat de rol die aan het systeem of het apparaat is toegewezen.
4. Het configuratie bestand van de toepassing voor uw slimme contract identificeert in welke status deze functie wordt aangeroepen door de gedefinieerde rol.
5. Het configuratie bestand van de toepassing en de bijbehorende slimme contracten worden geüpload naar Azure Block Chain Workbench.

Nadat de toepassing is geüpload, wordt het Azure Active Directory-account voor het externe systeem toegewezen aan het contract en de bijbehorende rol.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Het testen van externe systeemintegratiestromen voorafgaand aan het schrijven van de integratiecode 

Integratie met externe systemen is een essentiële vereiste voor veel scenario's. Het is wenselijk om een slimcontractontwerp vóór of parallel aan de ontwikkeling van de code voor de integratie met externe systemen te kunnen valideren.

Het gebruik van Azure Active Directory (Azure AD) kan de ontwikkelaarproductiviteit en time-to-value enorm versnellen. Met name de code-integratie met een extern systeem kan een niet-triviale hoeveelheid tijd in beslag nemen. Door Azure AD te gebruiken en de automatische generatie van UX door Azure Blockchain Workbench, kunt u ontwikkelaars toestaan zich aan te melden bij Blockchain Workbench als het externe systeem en waarden van het externe systeem via de UX te vullen. U kunt ideeën snel ontwikkelen en valideren in een proof of concept-omgeving voordat de integratiecode voor de externe systemen wordt geschreven.
