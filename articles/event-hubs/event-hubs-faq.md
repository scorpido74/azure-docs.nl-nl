---
title: Veelgestelde vragen-Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u een lijst met veelgestelde vragen over Azure Event Hubs en de antwoorden hiervan.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: e8ae3cbbca926a97bf90f4ac1104d4f082b332fd
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835611"
---
# <a name="event-hubs-frequently-asked-questions"></a>Veelgestelde vragen over Event Hubs

## <a name="general"></a>Algemeen

### <a name="what-is-an-event-hubs-namespace"></a>Wat is een Event Hubs naam ruimte?
Een naam ruimte is een container voor Event hub/Kafka-onderwerpen. Het biedt u een unieke [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Een naam ruimte fungeert als een toepassings container die meerdere Event hub-Kafka onderwerpen kan bevatten. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Wanneer moet ik een nieuwe naam ruimte maken versus een bestaande naam ruimte gebruiken?
Capaciteits toewijzingen ([doorvoer eenheden (TUs)](#throughput-units)) worden gefactureerd op het niveau van de naam ruimte. Een naam ruimte is ook gekoppeld aan een regio.

U kunt een nieuwe naam ruimte maken in plaats van een bestaande te gebruiken in een van de volgende scenario's: 

- U hebt een event hub nodig die aan een nieuwe regio is gekoppeld.
- U hebt een event hub nodig die is gekoppeld aan een ander abonnement.
- U hebt een event hub met een afzonderlijke capaciteits toewijzing nodig (dat wil zeggen, de capaciteits behoefte voor de naam ruimte met de toegevoegde Event Hub zou de drempel waarde 40 TU overschrijden en u niet wilt voor het toegewezen cluster)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Wat is het verschil tussen Event Hubs Basic-en Standard-lagen?

De Standard-laag van Azure Event Hubs biedt functies die groter zijn dan in de Basic-laag. De volgende functies zijn opgenomen in de standaard:

* Langer bewaren van gebeurtenissen
* Extra brokered Connections, met een overschrijding kosten voor meer dan het aantal inbegrepen
* Meer dan één [consumenten groep](event-hubs-features.md#consumer-groups)
* [Opnames](event-hubs-capture-overview.md)
* [Kafka-integratie](event-hubs-for-kafka-ecosystem-overview.md)

Voor meer informatie over prijs categorieën, waaronder Event Hubs Dedicated, raadpleegt u de [Event hubs prijs informatie](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Waar is Azure Event Hubs beschikbaar?

Azure Event Hubs is beschikbaar in alle ondersteunde Azure-regio's. Ga voor een lijst naar de pagina [Azure-regio's](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan ik één AMQP-verbinding gebruiken voor het verzenden en ontvangen van meerdere Event hubs?

Ja, zolang alle Event hubs zich in dezelfde naam ruimte bevinden.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Wat is de maximale Bewaar periode voor gebeurtenissen?

Event Hubs Standard-laag ondersteunt momenteel een maximale Bewaar periode van zeven dagen. Event hubs is niet bedoeld als een permanent gegevens archief. Bewaar perioden van meer dan 24 uur zijn bedoeld voor scenario's waarin het handig is om een gebeurtenis stroom opnieuw te spelen in dezelfde systemen. bijvoorbeeld, om een nieuw machine learning model op bestaande gegevens te trainen of te controleren. Als u na zeven dagen een Bewaar periode van berichten nodig hebt, worden de gegevens van uw Event Hub naar het opslag account of het Azure Data Lake service account van uw keuze opgehaald als [Event hubs Capture](event-hubs-capture-overview.md) op uw event hub wordt ingeschakeld. Bij het inschakelen van Capture worden er kosten in rekening gebracht op basis van uw aangeschafte doorvoer eenheden.

U kunt de Bewaar periode voor de vastgelegde gegevens in uw opslag account configureren. De functie **levenscyclus beheer** van Azure Storage biedt een uitgebreid beleid op basis van regels voor algemeen gebruik v2-en Blob Storage-accounts. Gebruik het beleid om uw gegevens over te zetten naar de juiste toegangs lagen of verloopt aan het einde van de levens cyclus van de gegevens. Zie [de levens cyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)voor meer informatie. 

### <a name="how-do-i-monitor-my-event-hubs"></a>Mijn Event Hubs Hoe kan ik controleren?
Event Hubs levert uitgebreide metrische gegevens die de status van uw resources [Azure monitor](../azure-monitor/overview.md). Ze kunnen ook de algemene status van de Event Hubs-service alleen beoordelen op het niveau van de naam ruimte, maar ook op het niveau van de entiteit. Meer informatie over welke bewaking wordt aangeboden voor [Azure Event hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welke poorten moet ik op de firewall openen? 
U kunt de volgende protocollen gebruiken met Azure Service Bus voor het verzenden en ontvangen van berichten:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Zie de volgende tabel voor de uitgaande poorten die u moet openen om deze protocollen te gebruiken om te communiceren met Azure Event Hubs. 

| Protocol | Poorten | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 en 5672 | Zie [AMQP protocol Guide (Engelstalig](../service-bus-messaging/service-bus-amqp-protocol-guide.md) ) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Zie [Event hubs gebruiken in Kafka-toepassingen](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Welke IP-adressen moet ik white list?
Ga als volgt te werk om de juiste IP-adressen voor uw verbindingen te zoeken naar een witte lijst:

1. Voer de volgende opdracht uit vanaf een opdracht prompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer` . Als u de naam ruimte op een ander cluster herstelt, wordt de enige keer dat deze wordt gewijzigd.

Als u de zone redundantie voor uw naam ruimte gebruikt, moet u een aantal extra stappen uitvoeren: 

1. Eerst voert u Nslookup uit op de naam ruimte.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Noteer de naam in de sectie **niet-bindende antwoord** , die een van de volgende indelingen heeft: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Voer nslookup uit voor elk met achtervoegsels S1, S2 en S3 om de IP-adressen te verkrijgen van alle drie de instanties die worden uitgevoerd in drie beschikbaarheids zones, 

## <a name="apache-kafka-integration"></a>Integratie van Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hoe kan ik mijn bestaande Kafka-toepassing met Event Hubs integreren?
Event Hubs biedt een Kafka-eind punt dat kan worden gebruikt door uw bestaande op Apache Kafka gebaseerde toepassingen. Een configuratie wijziging is vereist voor de PaaS Kafka-ervaring. Het biedt een alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs ondersteunt Apache Kafka 1,0-en nieuwere client versies en werkt met uw bestaande Kafka-toepassingen,-hulpprogram ma's en-frameworks. Zie [Event hubs voor Kafka opslag plaats](https://github.com/Azure/azure-event-hubs-for-kafka)voor meer informatie.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Welke configuratie wijzigingen moeten worden uitgevoerd voor mijn bestaande toepassing om te praten met Event Hubs?
Als u verbinding wilt maken met een Event Hub, moet u de configuratie van de Kafka-client bijwerken. Dit doet u door een Event Hubs naam ruimte te maken en de [Connection String](event-hubs-get-connection-string.md)te verkrijgen. Wijzig de Boots trap. servers om de Event Hubs FQDN en de poort te laten verwijzen naar 9093. Werk de sasl. JAAS. config bij om de Kafka-client naar uw Event Hubs-eind punt te sturen (dit is de connection string die u hebt verkregen), met de juiste verificatie zoals hieronder wordt weer gegeven:

Boots trap. servers = {uw. Event hubs. FQDN}: 9093-aanvraag. time-out. MS = 60000 Security. Protocol = SASL_SSL sasl. mechanisme = sasl. JAAS. config = org. apache. Kafka. common. Security. Plains. PlainLoginModule vereist username = "$ConnectionString" Password = "{YOUR. Event hubs. Combi. TEKEN reeks} ";

Voorbeeld:

Boots trap. servers = dummynamespace. servicebus. Windows. net: 9093 Request. time-out. MS = 60000 Security. Protocol = SASL_SSL sasl. mechanisme = sasl. JAAS. config = org. apache. Kafka. common. Security. Plains. PlainLoginModule vereist username = "$ConnectionString" Password = "endpoint = SB://dummynamespace.servicebus.Windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Opmerking: als sasl. JAAS. config geen ondersteunde configuratie in uw Framework is, zoekt u de configuraties die worden gebruikt voor het instellen van de SASL gebruikers naam en het wacht woord en het gebruik daarvan. Stel de gebruikers naam in op $ConnectionString en het wacht woord voor uw Event Hubs connection string.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Wat is de bericht/gebeurtenis grootte voor Event Hubs?
De maximale toegestane bericht grootte voor Event Hubs is 1 MB.

## <a name="throughput-units"></a>Doorvoereenheden

### <a name="what-are-event-hubs-throughput-units"></a>Wat zijn Event Hubs doorvoer eenheden?
Door Voer in Event Hubs definieert de hoeveelheid gegevens in mega bytes of het aantal (in duizend tallen) gebeurtenissen van 1 KB die via Event Hubs binnenkomen en uitvallen. Deze door Voer wordt gemeten in doorvoer eenheden (TUs). Koop TUs voordat u de Event Hubs-service kunt gaan gebruiken. U kunt Event Hubs TUs expliciet selecteren met behulp van de portal of Event Hubs Resource Manager-sjablonen. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Worden doorvoer eenheden toegepast op alle Event hubs in een naam ruimte?
Ja, doorvoer eenheden (TUs) zijn van toepassing op alle Event hubs in een Event Hubs naam ruimte. Dit betekent dat u TUs op het niveau van de naam ruimte koopt en dat wordt gedeeld tussen de Event hubs onder die naam ruimte. Elke di geeft de naam ruimte aan de volgende mogelijkheden:

- Maxi maal 1 MB per seconde aan ingangs gebeurtenissen (gebeurtenissen die worden verzonden naar een Event Hub), maar niet meer dan 1000 ingangs gebeurtenissen, beheer bewerkingen of besturings-API-aanroepen per seconde.
- Maxi maal 2 MB per seconde voor afwijkings gebeurtenissen (gebeurtenissen die worden verbruikt van een Event Hub), maar niet meer dan 4096 uitwijkings gebeurtenissen.
- Maxi maal 84 GB gebeurtenis opslag (voldoende voor de standaard Bewaar periode van 24 uur).

### <a name="how-are-throughput-units-billed"></a>Hoe worden doorvoer eenheden in rekening gebracht?
Doorvoer eenheden (TUs) worden per uur gefactureerd. De facturering is gebaseerd op het maximum aantal eenheden dat tijdens het opgegeven uur is geselecteerd. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hoe kan ik het gebruik optimaliseren voor mijn doorvoer eenheden?
U kunt beginnen met één doorvoer eenheid (di) en [automatisch verg Roten](event-hubs-auto-inflate.md)inschakelen. Met de functie voor automatisch verg Roten kunt u uw TUs groeien naarmate uw verkeer/nettolading toeneemt. U kunt ook een bovengrens voor het aantal TUs instellen.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hoe werkt de functie voor het automatisch verg Roten van Event Hubs?
Met de functie voor automatisch verg Roten kunt u uw doorvoer eenheden (TUs) omhoog schalen. Dit betekent dat u kunt beginnen met het kopen van lage TUs en het automatisch verg Roten of uitbreiden van uw TUs wanneer de inkomende kracht toeneemt. Het biedt een voordelige optie en volledige controle over het aantal TUs dat u wilt beheren. Deze functie is een functie die alleen kan worden **geschaald** , en u kunt de schaal van het aantal TUs volledig regelen door deze bij te werken. 

Mogelijk wilt u beginnen met een lage doorvoer eenheid (TUs), bijvoorbeeld 2 TUs. Als u voor spelt dat uw verkeer kan toenemen tot 15 TUs, schakelt u de functie automatisch verg Roten in uw naam ruimte in en stelt u de maximum limiet in op 15 TUs. U kunt uw TUs nu automatisch laten groeien naarmate uw verkeer toeneemt.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Zijn er kosten verbonden aan het inschakelen van de functie voor automatisch verg Roten?
Er zijn **geen kosten** verbonden aan deze functie. 

### <a name="how-are-throughput-limits-enforced"></a>Hoe worden doorvoer limieten afgedwongen?
Als de totale **ingangs** doorvoer of het totale aantal ingangs gebeurtenissen voor alle Event hubs in een naam ruimte de cumulatieve doorvoer eenheid overschrijdt, worden afzenders beperkt en ontvangen ze fouten die aangeven dat het ingangs quotum is overschreden.

Als de totale **uitgangs** doorvoer of het totale percentage voor het oplopen van gebeurtenissen voor alle Event hubs in een naam ruimte de cumulatieve doorvoer eenheid overschrijdt, worden de ontvangers beperkt, maar worden er geen beperkings fouten gegenereerd. 

Ingangs-en uitgangs quota worden afzonderlijk afgedwongen, zodat de afzender geen gebeurtenis verbruik kan veroorzaken om te vertragen, en kan een ontvanger voor komen dat gebeurtenissen worden verzonden naar een Event Hub.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Is er een limiet voor het aantal doorvoer eenheden (TUs) dat kan worden gereserveerd of geselecteerd?
Bij een multi tenant-aanbieding kunnen doorvoer eenheden Maxi maal 40 TUs (u kunt Maxi maal 20 TUs selecteren in de portal en een ondersteunings ticket verhogen om dit te doen tot 40 TUs op dezelfde naam ruimte). Meer dan 40 TUs biedt Event Hubs het model op basis van resource/capaciteit dat de **Event hubs dedicated clusters**wordt genoemd. Toegewezen clusters worden verkocht in capaciteits eenheden (CUs).

## <a name="dedicated-clusters"></a>Toegewezen clusters

### <a name="what-are-event-hubs-dedicated-clusters"></a>Wat zijn toegewezen clusters van Event Hubs?
Event Hubs Dedicated-clusters bieden implementaties met één Tenant voor klanten met de meest veeleisende vereisten. Deze aanbieding bouwt voort op capaciteits clusters die niet zijn gebonden door doorvoer eenheden. Dit betekent dat u het cluster kunt gebruiken om uw gegevens op te nemen en te streamen, zoals bepaald door het CPU-en geheugen gebruik van het cluster. Zie [Event hubs dedicated clusters](event-hubs-dedicated-overview.md)voor meer informatie.

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Wat kan ik doen met één capaciteitseenheid?
Hoeveel u voor een toegewezen cluster kunt opnemen en streamen, is afhankelijk van verschillende factoren, zoals uw producenten, consumenten, de snelheid waarmee u opnamet en verwerkt, en nog veel meer. 

De volgende tabel toont de Bench Mark-resultaten die we hebben behaald tijdens onze tests:

| De shape Payload | Ontvangers | Ingangs bandbreedte| Berichten binnenkomend | Uitgangs band breedte | Uitstaande berichten | Totaal aantal TUs | TUs per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches van 100x1KB | 2 | 400 MB/sec. | 400k berichten per seconde | 800 MB/sec. | 800k berichten per seconde | 400 TUs | 100 TUs | 
| Batches van 10x10KB | 2 | 666 MB/sec. | 66.6 k-berichten/sec | 1,33 GB/sec. | 133k berichten per seconde | 666 TUs | 166 TUs |
| Batches van 6x32KB | 1 | 1,05 GB/sec. | 34k berichten per seconde | 1,05 GB/sec. | 34k berichten per seconde | 1000 TUs | 250 TUs |

Bij het testen zijn de volgende criteria gebruikt:

- Er is een toegewezen Event Hubs cluster met vier capaciteits eenheden (CUs) gebruikt. 
- De Event Hub die wordt gebruikt voor opname, had 200 partities. 
- De gegevens die zijn opgenomen, zijn ontvangen door twee receiver-toepassingen die van alle partities ontvangen.

De resultaten geven u een idee van wat er met een toegewezen Event Hubs cluster kan worden gerealiseerd. Daarnaast wordt een exclusief cluster geleverd met de Event Hubs Capture ingeschakeld voor uw scenario's voor de Bewaar periode van micro batch en lange termijn.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hoe kan ik een Event Hubs Dedicated cluster maken?
U maakt een Event Hubs toegewezen cluster door een [ondersteunings aanvraag voor quotum verhoging](https://portal.azure.com/#create/Microsoft.Support) in te dienen of door contact op te nemen met het [Event hubs team](mailto:askeventhubs@microsoft.com). Het duurt doorgaans ongeveer twee weken om het cluster op te halen en door te geven om door u te worden gebruikt. Dit proces is tijdelijk totdat een volledig zelfvoorzienings systeem beschikbaar is via de Azure Portal.

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="how-many-partitions-do-i-need"></a>Hoeveel partities heb ik nodig?
Het aantal partities wordt opgegeven bij het maken en moet tussen 2 en 32 liggen. Het aantal partities kan niet worden gewijzigd. Houd daarom rekening met de lange termijn bij het instellen van het aantal partities. Partities zijn een mechanisme voor gegevensordening. Ze hebben betrekking op de mate van downstreamparallelheid die is vereist bij het gebruik van toepassingen. Het aantal partities in een Event Hub houdt rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Zie [partities](event-hubs-features.md#partitions)voor meer informatie over partities.

Het is raadzaam om de waarde in te stellen op het hoogst mogelijke niveau: 32, op het moment dat deze wordt gemaakt. Houd er rekening mee dat er meer dan één partitie heeft als gevolg dat er gebeurtenissen worden verzonden naar meerdere partities zonder de volg orde te behouden, tenzij u afzenders configureert om alleen te verzenden naar één enkele partitie van de 32, waardoor de resterende 31 partities overbodig zijn. In het eerste geval moet u gebeurtenissen lezen in alle 32-partities. In het laatste geval zijn er geen extra kosten in rekening van de extra configuratie die u moet maken op de host van de gebeurtenis processor.

Event Hubs is ontworpen om één partitie lezer per Consumer groep toe te staan. In de meeste gevallen is de standaard instelling van vier partities voldoende. Als u van plan bent uw gebeurtenis verwerking te schalen, kunt u overwegen extra partities toe te voegen. Er is geen specifieke doorvoer limiet voor een partitie, maar de geaggregeerde door Voer in uw naam ruimte wordt beperkt door het aantal doorvoer eenheden. Als u het aantal doorvoer eenheden in uw naam ruimte verhoogt, wilt u mogelijk extra partities toestaan dat gelijktijdige lezers hun eigen maximale door Voer kunnen verzorgen.

Als u echter een model hebt waarin uw toepassing een affiniteit met een bepaalde partitie heeft, is het mogelijk dat het aantal partities niet van nut is voor u. Zie [Beschik baarheid en consistentie](event-hubs-availability-and-consistency.md)voor meer informatie.

## <a name="pricing"></a>Prijzen

### <a name="where-can-i-find-more-pricing-information"></a>Waar vind ik meer prijs informatie?

Zie de [Event hubs prijs informatie](https://azure.microsoft.com/pricing/details/event-hubs/)voor volledige informatie over Event hubs prijzen.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Worden er kosten in rekening gebracht voor het bewaren van Event Hubs gebeurtenissen gedurende meer dan 24 uur?

Met de laag Event Hubs standaard worden de Bewaar periode van een bericht langer dan 24 uur toegestaan, gedurende een maximum van zeven dagen. Als de grootte van het totale aantal opgeslagen gebeurtenissen de opslag limiet overschrijdt voor het aantal geselecteerde doorvoer eenheden (84 GB per doorvoer eenheid), wordt de grootte die de limiet overschrijdt, in rekening gebracht tegen de gepubliceerde Azure Blob-opslag snelheid. De opslag limiet in elke doorvoer eenheid omvat alle opslag kosten voor Bewaar perioden van 24 uur (de standaard instelling), zelfs als de doorvoer eenheid wordt gebruikt tot de maximale ingangs limiet.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hoe wordt de opslag grootte van Event Hubs berekend en in rekening gebracht?

De totale grootte van alle opgeslagen gebeurtenissen, inclusief interne overhead voor gebeurtenis koppen of opslag structuren op schijf in alle Event hubs, wordt gedurende de hele dag gemeten. Aan het einde van de dag wordt de grootte van de piek opslag berekend. De dagelijkse opslag limiet wordt berekend op basis van het minimum aantal doorvoer eenheden dat tijdens de dag is geselecteerd (elke doorvoer eenheid levert een limiet van 84 GB). Als de totale grootte de berekende dagelijkse opslag limiet overschrijdt, wordt de overmatige opslag gefactureerd met behulp van Azure Blob-opslag tarieven (tegen de **lokaal redundante opslag** snelheid).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hoe worden Event Hubs ingangs gebeurtenissen berekend?

Elke gebeurtenis die naar een Event Hub wordt verzonden, telt als een Factureerbaar bericht. Een *ingangs gebeurtenis* wordt gedefinieerd als een gegevens eenheid die kleiner is dan of gelijk is aan 64 kB. Een gebeurtenis die kleiner dan of gelijk is aan 64 KB groot is, wordt beschouwd als één factureer bare gebeurtenis. Als de gebeurtenis groter is dan 64 KB, wordt het aantal factureer bare gebeurtenissen berekend op basis van de grootte van de gebeurtenis, in veelvouden van 64 KB. Zo wordt een gebeurtenis met 8 KB die wordt verzonden naar de Event Hub, gefactureerd als één gebeurtenis, maar een 96-KB-bericht dat naar de Event Hub wordt verzonden, wordt gefactureerd als twee gebeurtenissen.

Gebeurtenissen die vanuit een Event Hub worden verbruikt, en beheer bewerkingen en besturings aanroepen zoals controle punten, worden niet meegeteld als factureer bare ingangs gebeurtenissen, maar toenemen tot de maximale doorvoer eenheid.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Zijn de kosten voor brokered Connections van toepassing op Event Hubs?

Verbindings kosten zijn alleen van toepassing wanneer het AMQP-protocol wordt gebruikt. Er worden geen verbindings kosten in rekening gebracht voor het verzenden van gebeurtenissen via HTTP, ongeacht het aantal verzend systemen of-apparaten. Als u van plan bent AMQP te gebruiken (bijvoorbeeld om efficiëntere gebeurtenis streaming te maken of om bidirectionele communicatie in IoT-opdracht-en controle scenario's in te scha kelen), raadpleegt u de pagina met [prijs informatie voor Event hubs](https://azure.microsoft.com/pricing/details/event-hubs/) voor meer informatie over hoeveel verbindingen in elke servicelaag zijn opgenomen.

### <a name="how-is-event-hubs-capture-billed"></a>Hoe wordt Event Hubs opgenomen?

Vastleggen is ingeschakeld wanneer voor een Event Hub in de naam ruimte de optie vastleggen is ingeschakeld. Event Hubs Capture wordt per uur gefactureerd op basis van de aangeschafte doorvoer eenheid. Naarmate het aantal doorvoer eenheden wordt verg root of verkleind, worden deze wijzigingen in de hele tijds duur van Event Hubs vastleg ging in rekening gebracht. Zie [Event hubs prijs informatie](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over het vastleggen van de facturering van Event hubs.

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Worden er kosten in rekening gebracht voor het opslag account dat ik Selecteer voor Event Hubs Capture?

Capture maakt gebruik van een opslag account dat u opgeeft wanneer het is ingeschakeld op een Event Hub. Als uw opslag account is, worden alle wijzigingen voor deze configuratie in rekening gebracht voor uw Azure-abonnement.

## <a name="quotas"></a>Quota

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Zijn er quota's gekoppeld aan Event Hubs?

Zie [quota's](event-hubs-quotas.md)voor een lijst met alle Event hubs quota's.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Waarom kan ik geen naam ruimte maken nadat ik deze heb verwijderd uit een ander abonnement? 
Wanneer u een naam ruimte uit een abonnement verwijdert, wacht u vier uur voordat u deze opnieuw maakt met dezelfde naam in een ander abonnement. Anders wordt het volgende fout bericht weer gegeven: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Wat zijn de uitzonde ringen die door Event Hubs worden gegenereerd en de voorgestelde acties?

Zie [overzicht van uitzonde ringen](event-hubs-messaging-exceptions.md)voor een lijst met mogelijke Event hubs uitzonde ringen.

### <a name="diagnostic-logs"></a>Diagnostische logboeken

Event Hubs ondersteunt twee typen [Diagnostische logboeken](event-hubs-diagnostic-logs.md) : fout logboeken en operationele logboeken vastleggen: beide worden weer gegeven in JSON en kunnen via de Azure Portal worden ingeschakeld.

### <a name="support-and-sla"></a>Ondersteuning en SLA

Technische ondersteuning voor Event Hubs is beschikbaar via de [pagina micro soft Q&een vraag voor Azure service bus](https://docs.microsoft.com/answers/topics/azure-service-bus.html). Ondersteuning bij facturering en abonnements beheer is gratis.

Voor meer informatie over onze SLA gaat u naar de pagina [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van Event Hubs](event-hubs-what-is-event-hubs.md)
* [Een event hub maken](event-hubs-create.md)
* [Event Hubs automatisch verg Roten](event-hubs-auto-inflate.md)
