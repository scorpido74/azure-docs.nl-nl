---
title: Slimme contractintegratiepatronen - Azure Blockchain Workbench
description: Overzicht van slimme contractintegratiepatronen in Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325974"
---
# <a name="smart-contract-integration-patterns"></a>Integratiepatronen voor slimme contracten

Slimme contracten vertegenwoordigen vaak een zakelijke workflow die moet worden geïntegreerd met externe systemen en apparaten.

De vereisten van deze werkstromen omvatten de noodzaak om transacties te starten op een gedistribueerd grootboek met gegevens van een extern systeem, service of apparaat. Ze moeten ook externe systemen laten reageren op gebeurtenissen die voortkomen uit slimme contracten op een gedistribueerd grootboek.

De REST API- en messaging-integratie verzendt transacties van externe systemen naar slimme contracten die zijn opgenomen in een Azure Blockchain Workbench-toepassing. Het stuurt ook gebeurtenismeldingen naar externe systemen op basis van wijzigingen die plaatsvinden binnen een toepassing.

Voor scenario's voor gegevensintegratie bevat Azure Blockchain Workbench een reeks databaseweergaven die een combinatie van transactionele gegevens uit de blockchain en meta-gegevens over toepassingen en slimme contracten samenvoegen.

Bovendien kunnen sommige scenario's, zoals die met betrekking tot de toeleveringsketen of media, ook de integratie van documenten vereisen. Hoewel Azure Blockchain Workbench geen API-aanroepen biedt voor het rechtstreeks verwerken van documenten, kunnen documenten worden opgenomen in een blockchain-toepassing. Deze sectie bevat ook dat patroon.

Deze sectie bevat de patronen die zijn geïdentificeerd voor het implementeren van elk van deze typen integraties in uw end-to-end-oplossingen.

## <a name="rest-api-based-integration"></a>REST API-gebaseerde integratie

Mogelijkheden binnen de door Azure Blockchain Workbench gegenereerde webtoepassing worden blootgesteld via de REST API. Tot de mogelijkheden behoren het uploaden, configureren en beheren van toepassingen in Azure Blockchain Workbench, het verzenden van transacties naar een gedistribueerd grootboek en het opvragen van metagegevens en grootboekgegevens van toepassingen.

De REST API wordt voornamelijk gebruikt voor interactieve clients zoals web-, mobiele en bottoepassingen.

In deze sectie wordt gekeken naar patronen die zijn gericht op de aspecten van de REST API die transacties verzenden naar een gedistribueerd grootboek en patronen die gegevens over transacties uit de SQL-database van Azure Blockchain Workbench *opvragen.*

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Transacties verzenden naar een gedistribueerd grootboek vanuit een extern systeem

De Azure Blockchain Workbench REST API verzendt geverifieerde aanvragen om transacties uit te voeren op een gedistribueerd grootboek.

![Transacties verzenden naar een gedistribueerd grootboek](./media/integration-patterns/send-transactions-ledger.png)

Het uitvoeren van transacties vindt plaats met behulp van het eerder afgebeelde proces, waarbij:

-   De externe toepassing wordt geverifieerd naar de Azure Active Directory die is ingericht als onderdeel van de Azure Blockchain Workbench-implementatie.
-   Geautoriseerde gebruikers ontvangen een token aan toonder dat kan worden verzonden met aanvragen naar de API.
-   Externe toepassingen bellen naar de REST API met behulp van het token aan toonder.
-   De REST API verpakt de aanvraag als een bericht en stuurt deze naar de ServiceBus. Vanaf hier wordt het opgehaald, ondertekend en verzonden naar het juiste gedistribueerde grootboek.
-   De REST API doet een verzoek aan de Azure Blockchain Workbench SQL DB om de aanvraag vast te leggen en de huidige inrichtingsstatus vast te stellen.
-   De SQL DB retourneert de inrichtingsstatus en de API-aanroep retourneert de ID naar de externe toepassing die de id heeft genoemd.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Blockchain Workbench-metagegevens en gedistribueerde grootboektransacties opvragen

De Azure Blockchain Workbench REST API verzendt geverifieerde aanvragen naar querygegevens met betrekking tot slimme contractuitvoering op een gedistribueerd grootboek.

![Metagegevens opvragen](./media/integration-patterns/querying-metadata.png)

Query's vindt plaats met behulp van het eerder afgebeelde proces, waarbij:

1. De externe toepassing wordt geverifieerd naar de Azure Active Directory die is ingericht als onderdeel van de Azure Blockchain Workbench-implementatie.
2. Geautoriseerde gebruikers ontvangen een token aan toonder dat kan worden verzonden met aanvragen naar de API.
3. Externe toepassingen bellen naar de REST API met behulp van het token aan toonder.
4. De REST API query's de gegevens voor de aanvraag van de SQL DB en retourneert deze naar de client.

## <a name="messaging-integration"></a>Integratie van berichten

Messaging-integratie vergemakkelijkt de interactie met systemen, services en apparaten waarbij een interactieve aanmelding niet mogelijk of wenselijk is. Messaging-integratie richt zich op twee soorten berichten: berichten waarin om transacties worden gevraagd, worden uitgevoerd op een gedistribueerd grootboek en gebeurtenissen die door dat grootboek worden weergegeven wanneer transacties hebben plaatsgevonden.

Messaging-integratie richt zich op de uitvoering en monitoring van transacties met betrekking tot het maken van gebruikers, het maken van contracten en de uitvoering van transacties op contracten en wordt voornamelijk gebruikt door *headless* back-end systemen.

In deze sectie wordt gekeken naar patronen die zijn gericht op de aspecten van de op berichten gebaseerde API die transacties verzenden naar een gedistribueerd grootboek en patronen die gebeurtenisberichten weergeven die worden blootgesteld door het onderliggende gedistribueerde grootboek.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>One-way event delivery from a smart contract to a event consumer 

In dit scenario vindt een gebeurtenis plaats binnen een slim contract, bijvoorbeeld een statuswijziging of de uitvoering van een specifiek type transactie. Dit evenement wordt via een Event Grid uitgezonden naar downstream consumenten, en die consumenten nemen vervolgens passende maatregelen.

Een voorbeeld van dit scenario is dat wanneer een transactie plaatsvindt, een consument wordt gewaarschuwd en actie kan ondernemen, zoals het opnemen van de informatie in een SQL DB of de Common Data Service. Dit scenario is hetzelfde patroon dat Workbench volgt om de SQL DB *van de off-chain* te vullen.

Een ander zou zijn als een slim contract overgaat naar een bepaalde staat, bijvoorbeeld wanneer een contract gaat in een *OutOfCompliance*. Wanneer deze statuswijziging plaatsvindt, kan deze een waarschuwing activeren die naar de mobiele telefoon van een beheerder moet worden verzonden.

![Levering van eenenkele gebeurtenis](./media/integration-patterns/one-way-event-delivery.png)

Dit scenario vindt plaats met behulp van het eerder afgebeelde proces, waarbij:

-   Het slimme contract wordt overgezet naar een nieuwe status en stuurt een gebeurtenis naar het grootboek.
-   Het grootboek ontvangt en levert het evenement aan Azure Blockchain Workbench.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit het grootboek en ontvangt de gebeurtenis.
-   Azure Blockchain Workbench publiceert de gebeurtenis aan abonnees op het Event Grid.
-   Externe systemen zijn geabonneerd op het gebeurtenisraster, verbruiken het bericht en nemen de juiste acties.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>One-way event levering van een bericht van een extern systeem naar een slim contract

Er is ook een scenario dat uit de tegenovergestelde richting stroomt. In dit geval wordt een gebeurtenis gegenereerd door een sensor of een extern systeem en moeten de gegevens van die gebeurtenis naar een slim contract worden verzonden.

Een veelvoorkomend voorbeeld is de levering van gegevens van financiële markten, bijvoorbeeld prijzen van grondstoffen, aandelen of obligaties, aan een slim contract.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Directe levering van een Azure Blockchain Workbench in de verwachte indeling

Sommige toepassingen zijn gebouwd om te integreren met Azure Blockchain Workbench en genereert en verzendt berichten rechtstreeks in de verwachte indelingen.

![Directe levering](./media/integration-patterns/direct-delivery.png)

Deze levering vindt plaats met behulp van het proces dat eerder is afgebeeld, waarbij:

-   Een gebeurtenis vindt plaats in een extern systeem dat het maken van een bericht voor Azure Blockchain Workbench activeert.
-   Het externe systeem heeft code geschreven om dit bericht in een bekende indeling te maken en stuurt het rechtstreeks naar de Service Bus.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de ServiceBus en haalt het bericht op.
-   Azure Blockchain Workbench initieert een oproep naar het grootboek en verzendt gegevens van het externe systeem naar een specifiek contract.
-   Na ontvangst van het bericht wordt het contract overgezet naar een nieuwe staat.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Levering van een bericht in een indeling die onbekend is met Azure Blockchain Workbench

Sommige systemen kunnen niet worden gewijzigd om berichten af te leveren in de standaardindelingen die worden gebruikt door Azure Blockchain Workbench. In deze gevallen kunnen vaak bestaande mechanismen en berichtformaten van deze systemen worden gebruikt. In het bijzonder kunnen de native berichttypen van deze systemen worden getransformeerd met Behulp van Logische Apps, Azure-functies of andere aangepaste code om toe te leiden naar een van de verwachte standaardberichtenindelingen.

![Onbekende berichtindeling](./media/integration-patterns/unknown-message-format.png)

Dit gebeurt met behulp van het eerder afgebeelde proces, waarbij:

-   Een gebeurtenis vindt plaats in een extern systeem dat het maken van een bericht activeert.
-   Een Logic App of aangepaste code wordt gebruikt om dat bericht te ontvangen en om te zetten in een standaard Azure Blockchain Workbench-opgemaakte bericht.
-   De Logic App stuurt het getransformeerde bericht rechtstreeks naar de ServiceBus.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de ServiceBus en haalt het bericht op.
-   Azure Blockchain Workbench initieert een aanroep naar het grootboek en verzendt gegevens van het externe systeem naar een specifieke functie op het contract.
-   De functie wordt uitgevoerd en wijzigt doorgaans de status. De statuswijziging wordt doordeverplaatsingen van de bedrijfsworkflow die in het slimme contract wordt weerspiegeld, zodat andere functies nu naar gelang van het geval kunnen worden uitgevoerd.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Overgang controle naar een extern proces en wachten op voltooiing

Er zijn scenario's waarin een slim contract de interne uitvoering moet stoppen en moet overdragen aan een extern proces. Dat externe proces zou dan voltooien, een bericht sturen naar het slimme contract, en de uitvoering zou dan doorgaan binnen het slimme contract.

#### <a name="transition-to-the-external-process"></a>Overgang naar het externe proces

Dit patroon wordt meestal geïmplementeerd met de volgende aanpak:

-   Het slimme contract wordt overgezet naar een specifieke status. In deze toestand kunnen geen of een beperkt aantal functies worden uitgevoerd totdat een extern systeem de gewenste actie onderneemt.
-   De verandering van staat wordt opgedoken als een gebeurtenis aan een downstream consument.
-   De downstream-consument ontvangt de gebeurtenis en activeert externe codeuitvoering.

![Overgangscontrole naar extern proces](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Terugkeer van de controle van het slimme contract

Afhankelijk van de mogelijkheid om het externe systeem aan te passen, kan het al dan niet in staat zijn om berichten te leveren in een van de standaardindelingen die Azure Blockchain Workbench verwacht. Op basis van de externe systemen mogelijkheid om een van deze berichten te genereren bepalen welke van de volgende twee terugkeer paden wordt genomen.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Directe levering van een Azure Blockchain Workbench in de verwachte indeling

![](./media/integration-patterns/direct-delivery.png)

In dit model vindt de communicatie naar het contract en de daaropvolgende statuswijziging plaats na het vorige proces waarbij -

-   Bij het bereiken van de voltooiing of een specifieke mijlpaal in de uitvoering van de externe code wordt een gebeurtenis verzonden naar de ServiceBus die is verbonden met Azure Blockchain Workbench.

-   Voor systemen die niet direct kunnen worden aangepast om een bericht te schrijven dat voldoet aan de verwachtingen van de API, wordt het getransformeerd.

-   De inhoud van het bericht wordt verpakt en verzonden naar een specifieke functie op het slimme contract. Deze levering gebeurt namens de gebruiker die is gekoppeld aan het externe systeem.

-   De functie wordt uitgevoerd en wijzigt doorgaans de status. De statuswijziging wordt doordeverplaatsingen van de bedrijfsworkflow die in het slimme contract wordt weerspiegeld, zodat andere functies nu naar gelang van het geval kunnen worden uitgevoerd.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Levering van een bericht in een indeling die onbekend is met Azure Blockchain Workbench

![Onbekende berichtindeling](./media/integration-patterns/unknown-message-format.png)

In dit model waarin een bericht in een standaardformaat niet rechtstreeks kan worden verzonden, vindt de communicatie naar het contract en de daaropvolgende statuswijziging plaats na het vorige proces wanneer:

1.  Bij het bereiken van de voltooiing of een specifieke mijlpaal in de uitvoering van de externe code wordt een gebeurtenis verzonden naar de ServiceBus die is verbonden met Azure Blockchain Workbench.
2.  Een Logic App of aangepaste code wordt gebruikt om dat bericht te ontvangen en om te zetten in een standaard Azure Blockchain Workbench-opgemaakte bericht.
3.  De Logic App stuurt het getransformeerde bericht rechtstreeks naar de ServiceBus.
4.  Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de ServiceBus en haalt het bericht op.
5.  Azure Blockchain Workbench initieert een oproep naar het grootboek en verzendt gegevens van het externe systeem naar een specifiek contract.
6. De inhoud van het bericht wordt verpakt en verzonden naar een specifieke functie op het slimme contract. Deze levering gebeurt namens de gebruiker die is gekoppeld aan het externe systeem.
7.  De functie wordt uitgevoerd en wijzigt doorgaans de status. De statuswijziging wordt doordeverplaatsingen van de bedrijfsworkflow die in het slimme contract wordt weerspiegeld, zodat andere functies nu naar gelang van het geval kunnen worden uitgevoerd.

## <a name="iot-integration"></a>IoT-integratie

Een veelvoorkomend integratiescenario is het opnemen van telemetriegegevens die zijn opgehaald van sensoren in een slim contract. Op basis van gegevens die door sensoren worden geleverd, kunnen slimme contracten geïnformeerde acties ondernemen en de staat van het contract wijzigen.

Als een vrachtwagen die medicijnen levert bijvoorbeeld zijn temperatuur tot 110 graden heeft laten stijgen, kan dit de effectiviteit van het geneesmiddel beïnvloeden en kan dit een probleem met de openbare veiligheid veroorzaken als het niet wordt gedetecteerd en uit de toeleveringsketen wordt verwijderd. Als een bestuurder zijn auto naar 100 mijl per uur heeft versneld, kan de resulterende sensorinformatie leiden tot een annulering van de verzekering door zijn verzekeringsmaatschappij. Als de auto een huurauto was, konden GPS-gegevens aangeven wanneer de bestuurder buiten een geografie ging die onder hun huurovereenkomst viel en een boete in rekening brengt.

De uitdaging is dat deze sensoren constant data kunnen leveren en het is niet gepast om al deze gegevens naar een slim contract te sturen. Een typische aanpak is om het aantal berichten dat naar de blockchain wordt verzonden te beperken terwijl u alle berichten naar een secundaire winkel levert. Lever bijvoorbeeld berichten die met een vast interval worden ontvangen, bijvoorbeeld één keer per uur, en wanneer een opgenomen waarde buiten een overeengekomen bereik valt voor een slim contract. Het controleren van waarden die buiten toleranties vallen, zorgt ervoor dat de gegevens die relevant zijn voor de bedrijfslogica van contracten worden ontvangen en uitgevoerd. Het controleren van de waarde op het interval bevestigt dat de sensor nog steeds rapporteert. Alle gegevens worden verzonden naar een secundair rapportagearchief om bredere rapportage, analyse en machine learning mogelijk te maken. Bijvoorbeeld, terwijl het krijgen van sensor lezingen voor GPS kan niet elke minuut nodig zijn voor een slim contract, kunnen ze interessante gegevens worden gebruikt in rapporten of mapping routes.

Op het Azure-platform wordt de integratie met apparaten meestal uitgevoerd met IoT Hub. IoT Hub biedt routering van berichten op basis van inhoud en maakt het type functionaliteit dat eerder wordt beschreven, mogelijk.

![IoT-berichten](./media/integration-patterns/iot.png)

Het proces toont een patroon:

-   Een apparaat communiceert rechtstreeks of via een veldgateway naar IoT Hub.
-   IoT Hub ontvangt de berichten en evalueert de berichten op routes die bijvoorbeeld de inhoud van het bericht controleren. *Meldt de sensor een temperatuur van meer dan 50 graden?*
-   De IoT Hub stuurt berichten die voldoen aan de criteria naar een gedefinieerde servicebus voor de route.
-   Een Logische App of een andere code luistert naar de ServiceBus die IoT Hub voor de route heeft ingesteld.
-   De Logic App of andere code haalt het bericht op en transformeert het bericht naar een bekende indeling.
-   Het getransformeerde bericht, dat nu in een standaardformaat is, wordt verzonden naar de ServiceBus voor Azure Blockchain Workbench.
-   Azure Blockchain Workbench is geabonneerd op gebeurtenissen uit de ServiceBus en haalt het bericht op.
-   Azure Blockchain Workbench initieert een oproep naar het grootboek en verzendt gegevens van het externe systeem naar een specifiek contract.
-   Na ontvangst van het bericht evalueert het contract de gegevens en kan de status veranderen op basis van de uitkomst van die evaluatie, bijvoorbeeld voor een hoge temperatuur, de status wijzigen in *Out of Compliance.*

## <a name="data-integration"></a>Gegevensintegratie

Naast REST en message-based API biedt Azure Blockchain Workbench ook toegang tot een SQL DB gevuld met toepassings- en contractmetagegevens en transactionele gegevens uit gedistribueerde grootboeken.

![Gegevensintegratie](./media/integration-patterns/data-integration.png)

De data-integratie is bekend:

-   Azure Blockchain Workbench slaat metadata op over toepassingen, workflows, contracten en transacties als onderdeel van het normale bedrijfsgedrag.
-   Externe systemen of hulpprogramma's bieden een of meer dialogen om het verzamelen van informatie over de database te vergemakkelijken, zoals de naam van de databaseserver, de naam van de database, het type verificatie, inloggegevens en welke databaseweergaven moeten worden gebruikt.
-   Query's zijn geschreven tegen SQL-databaseweergaven om downstream-verbruik te vergemakkelijken door externe systemen, services, rapportage, hulpprogramma's voor ontwikkelaars en productiviteitstools voor bedrijven.

## <a name="storage-integration"></a>Opslagintegratie

Veel scenario's vereisen mogelijk de noodzaak om attestable-bestanden op te nemen. Om meerdere redenen is het ongepast om bestanden op een blockchain te plaatsen. In plaats daarvan is een gemeenschappelijke aanpak het uitvoeren van een cryptografische hash (bijvoorbeeld SHA-256) tegen een bestand en die hash delen op een gedistribueerd grootboek. Als u de hash in de toekomst opnieuw uitvoert, moet dit hetzelfde resultaat opleveren. Als het bestand wordt gewijzigd, zelfs als slechts één pixel in een afbeelding wordt gewijzigd, geeft de hash een andere waarde als resultaat.

![Opslagintegratie](./media/integration-patterns/storage-integration.png)

Het patroon kan worden toegepast wanneer:

-   Een extern systeem blijft bestaan in een bestand in een opslagmechanisme, zoals Azure Storage.
-   Er wordt een hash gegenereerd met het bestand of het bestand en bijbehorende metagegevens, zoals een id voor de eigenaar, de URL waar het bestand zich bevindt, enz.
-   De hash en alle metagegevens worden verzonden naar een functie op een slim contract, zoals *FileAdded*
-   In de toekomst kunnen het bestand en meta-gegevens opnieuw worden gehasht en vergeleken met de waarden die op het grootboek zijn opgeslagen.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Voorwaarden voor het implementeren van integratiepatronen met behulp van de REST- en bericht-API's

Om de mogelijkheid voor een extern systeem of apparaat om te communiceren met het slimme contract met behulp van de REST of message API te vergemakkelijken, moet het volgende optreden -

1. In de Azure Active Directory voor het consortium wordt een account gemaakt dat het externe systeem of apparaat vertegenwoordigt.
2. Een of meer geschikte slimme contracten voor uw Azure Blockchain Workbench-toepassing hebben functies die zijn gedefinieerd om de gebeurtenissen van uw externe systeem of apparaat te accepteren.
3. Het toepassingsconfiguratiebestand voor uw slimme contract bevat de rol, waaraan het systeem of apparaat is toegewezen.
4. Het toepassingsconfiguratiebestand voor uw slimme contract identificeert in welke toestanden deze functie wordt aangeroepen door de gedefinieerde rol.
5. Het configuratiebestand van de toepassing en de slimme contracten worden geüpload naar Azure Blockchain Workbench.

Zodra de toepassing is geüpload, wordt het Azure Active Directory-account voor het externe systeem toegewezen aan het contract en de bijbehorende rol.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Externe systeemintegratiestromen testen voordat integratiecode wordt geschreven 

Integratie met externe systemen is een belangrijke vereiste van vele scenario's. Het is wenselijk om slim contractontwerp te kunnen valideren voorafgaand aan of parallel aan de ontwikkeling van code om te integreren met externe systemen.

Het gebruik van Azure Active Directory (Azure AD) kan de productiviteit van ontwikkelaars en de time-to-value sterk versnellen. In het bijzonder kan de code-integratie met een extern systeem een niet-triviale hoeveelheid tijd in beslag nemen. Door Azure AD te gebruiken en de automatische generatie UX door Azure Blockchain Workbench te gebruiken, u ontwikkelaars toestaan zich aan te melden bij Blockchain Workbench als het externe systeem en waarden van het externe systeem via de UX te vullen. U ideeën snel ontwikkelen en valideren in een proof of concept omgeving voordat integratiecode wordt geschreven voor de externe systemen.
