---
title: Veelgestelde vragen - Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een lijst met veelgestelde vragen (FAQ) voor Azure Event Hubs en de antwoorden.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 3b46c574ea47622ec97e70c0d2f2cdc3aa54ec0d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264958"
---
# <a name="event-hubs-frequently-asked-questions"></a>Veelgestelde vragen over Eventhubs

## <a name="general"></a>Algemeen

### <a name="what-is-an-event-hubs-namespace"></a>Wat is een Event Hubs-naamruimte?
Een naamruimte is een scoping container voor Event Hub/Kafka-onderwerpen. Het biedt u een unieke [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Een naamruimte fungeert als een toepassingscontainer dat met meerdere Event Hub/Kafka-onderwerpen werken kan. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Wanneer moet ik een nieuwe naam ruimte maken versus een bestaande naam ruimte gebruiken?
Capaciteits toewijzingen ([doorvoer eenheden (TUs)](#throughput-units)) worden gefactureerd op het niveau van de naam ruimte. Een naam ruimte is ook gekoppeld aan een regio.

U kunt een nieuwe naam ruimte maken in plaats van een bestaande te gebruiken in een van de volgende scenario's: 

- U hebt een event hub nodig die aan een nieuwe regio is gekoppeld.
- U hebt een event hub nodig die is gekoppeld aan een ander abonnement.
- U hebt een event hub met een afzonderlijke capaciteits toewijzing nodig (dat wil zeggen, de capaciteits behoefte voor de naam ruimte met de toegevoegde Event Hub zou de drempel waarde 40 TU overschrijden en u niet wilt voor het toegewezen cluster)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Wat is het verschil tussen Event Hubs basis en standaard-laag?

De prijscategorie Standard van Azure Event Hubs biedt functies dan wat er beschikbaar is in de laag basis. De volgende functies zijn inbegrepen in Standard:

* Langere bewaartermijn van de gebeurtenis
* Extra brokered verbindingen met een vaste kosten voor meer dan het aantal opgenomen
* Meer dan één [consumenten groep](event-hubs-features.md#consumer-groups)
* [Opnames](event-hubs-capture-overview.md)
* [Kafka-integratie](event-hubs-for-kafka-ecosystem-overview.md)

Voor meer informatie over prijs categorieën, waaronder Event Hubs Dedicated, raadpleegt u de [Event hubs prijs informatie](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Waar is Azure Event Hubs beschikbaar?

Azure Event Hubs is beschikbaar in alle ondersteunde Azure-regio's. Ga voor een lijst naar de pagina [Azure-regio's](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan ik één AMQP-verbinding gebruiken om te verzenden en ontvangen van meerdere eventhubs?

Ja, zolang alle eventhubs in dezelfde naamruimte zijn.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Wat is de maximale bewaarperiode voor gebeurtenissen?

Event Hubs Standard-laag biedt momenteel ondersteuning voor een maximale bewaarperiode van zeven dagen. Event hubs is niet bedoeld als een permanent gegevens archief. Bewaar perioden van meer dan 24 uur zijn bedoeld voor scenario's waarin het handig is om een gebeurtenis stroom opnieuw te spelen in dezelfde systemen. bijvoorbeeld, om een nieuw machine learning model op bestaande gegevens te trainen of te controleren. Als u na zeven dagen een Bewaar periode van berichten nodig hebt, worden de gegevens van uw Event Hub naar het opslag account of het Azure Data Lake service account van uw keuze opgehaald als [Event hubs Capture](event-hubs-capture-overview.md) op uw event hub wordt ingeschakeld. Capture inschakelen leidt tot een kosten in rekening gebracht op basis van de aangekochte doorvoereenheden.

U kunt de Bewaar periode voor de vastgelegde gegevens in uw opslag account configureren. De functie **levenscyclus beheer** van Azure Storage biedt een uitgebreid beleid op basis van regels voor algemeen gebruik v2-en Blob Storage-accounts. Gebruik het beleid om uw gegevens over te zetten naar de juiste toegangs lagen of verloopt aan het einde van de levens cyclus van de gegevens. Zie [de levens cyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)voor meer informatie. 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hoe bewaak ik mijn Event Hubs?
Event Hubs levert uitgebreide metrische gegevens die de status van uw resources [Azure monitor](../azure-monitor/overview.md). Ook kunt u bij het bepalen van de algemene status van de Event Hubs-service niet alleen op het niveau van de naamruimte, maar ook op het entiteitsniveau van de. Meer informatie over welke bewaking wordt aangeboden voor [Azure Event hubs](event-hubs-metrics-azure-monitor.md).

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
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer`. Als u de naam ruimte op een ander cluster herstelt, wordt de enige keer dat deze wordt gewijzigd.

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

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hoe Integreer ik mijn bestaande Kafka-toepassing met Event Hubs?
Eventhubs biedt een Kafka-eindpunt dat kan worden gebruikt door uw bestaande Apache Kafka op basis van toepassingen. Een wijziging in de configuratie is het enige dat is vereist om de ervaring PaaS Kafka. Het biedt een alternatief voor het uitvoeren van uw eigen Kafka-cluster. Eventhubs biedt ondersteuning voor Apache Kafka 1.0 en nieuwere clientversies en werkt met uw bestaande toepassingen, hulpprogramma's en frameworks in Kafka. Zie [Event hubs voor Kafka opslag plaats](https://github.com/Azure/azure-event-hubs-for-kafka)voor meer informatie.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Welke wijzigingen in de configuratie moeten worden uitgevoerd voor mijn bestaande toepassingen kan communiceren met Event Hubs?
Voor verbinding met een Gebeurtenishub waarvoor Kafka is ingeschakeld, moet u de configuraties van Kafka-client bijwerken. Dit doet u door een Event Hubs naam ruimte te maken en de [Connection String](event-hubs-get-connection-string.md)te verkrijgen. De bootstrap.servers dat deze de FQDN-naam Event Hubs en de poort naar 9093 wijzigen. Werk de sasl. JAAS. config bij om de Kafka-client naar uw Kafka te sturen Event Hubs-eind punt (dit is de connection string die u hebt verkregen), met de juiste verificatie zoals hieronder wordt weer gegeven:

bootstrap.servers={Your. EVENT HUBS. FQDN-naam}: 9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule vereist gebruikersnaam = '$ConnectionString' wachtwoord = "{uw. EVENT HUBS. DE VERBINDING. TEKENREEKS} ";

Voorbeeld:

bootstrap.servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule vereist gebruikersnaam = "$ Password="Endpoint=sb://dummynamespace.servicebus.windows.net/ ConnectionString'; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Opmerking: als sasl. JAAS. config geen ondersteunde configuratie in uw Framework is, zoekt u de configuraties die worden gebruikt voor het instellen van de SASL gebruikers naam en het wacht woord en het gebruik daarvan. Stel de gebruikersnaam op $ConnectionString en het wachtwoord voor uw Event Hubs-verbindingsreeks.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Wat is de bericht-/ gebeurtenisgrootte voor Event Hubs waarvoor Kafka is ingeschakeld?
De maximale bericht grootte die is toegestaan voor Kafka-ingeschakelde Event Hubs is 1 MB.

## <a name="throughput-units"></a>Doorvoereenheden

### <a name="what-are-event-hubs-throughput-units"></a>Wat zijn Event hub-doorvoereenheden?
Doorvoer in Event Hubs definieert de hoeveelheid gegevens in bytes mega of het aantal (in duizendtallen) gebeurtenissen van 1 KB die inkomend en uitgaand verkeer via Event Hubs. Deze doorvoer wordt gemeten in doorvoereenheden (tabel). Doorvoereenheden aanschaffen voordat u kunt beginnen met behulp van de Event Hubs-service. U kunt expliciet Event Hubs-Doorvoereenheden selecteren met behulp van de portal of Event Hubs-Resource Manager-sjablonen. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Doorvoereenheden van toepassing op alle eventhubs in een naamruimte?
Ja, doorvoereenheden (Doorvoereenheden) van toepassing op alle eventhubs in een Event Hubs-naamruimte. Betekent dit dat u Doorvoereenheden op het niveau van de naamruimte aanschaft en worden gedeeld tussen de eventhubs in die naamruimte. Elke TU voorziet de naamruimte van de volgende mogelijkheden:

- Maximaal 1 MB per seconde aan ingangsgebeurtenissen (gebeurtenissen die worden verzonden naar een event hub), maar niet meer dan 1000 ingangsgebeurtenissen, beheerbewerkingen of besturingselement API-aanroepen per seconde.
- Maximaal 2 MB per seconde voor uitgaande gebeurtenissen (gebeurtenissen gebruikt vanaf een event hub), maar niet meer dan 4096 uitgaande gebeurtenissen.
- Maximaal 84 GB voor opslag van gebeurtenissen (voldoende voor de bewaartermijn voor 24 uur).

### <a name="how-are-throughput-units-billed"></a>Hoe worden doorvoereenheden gefactureerd?
Throughput units (Doorvoereenheden) worden gefactureerd op uurbasis. De facturering is gebaseerd op het maximum aantal eenheden dat tijdens het opgegeven uur is geselecteerd. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hoe kan ik het gebruik van mijn doorvoereenheden optimaliseren?
U kunt beginnen met één doorvoer eenheid (di) en [automatisch verg Roten](event-hubs-auto-inflate.md)inschakelen. Het automatisch vergroten functie kunt u uw Doorvoereenheden groeien als uw verkeer/nettolading toeneemt. U kunt ook een bovengrens instellen op het aantal Doorvoereenheden.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hoe werkt de functie voor automatisch vergroten van Event Hubs?
De functie kunt u uw doorvoereenheden (Doorvoereenheden opschalen) automatisch vergroten. Betekent dit dat u kunt beginnen door lage Doorvoereenheden aanschaffen en automatisch schalen van uw Doorvoereenheden vergroten als uw invoer toeneemt. Het biedt een voordelige optie zijn en de volledige controle over het aantal Doorvoereenheden te beheren. Deze functie is een functie die alleen kan worden **geschaald** , en u kunt de schaal van het aantal TUs volledig regelen door deze bij te werken. 

U wilt beginnen met lage doorvoereenheden (tabel), bijvoorbeeld 2 Doorvoereenheden. Als u dat uw verkeer tot 15 Doorvoereenheden uitbreiden kan voorspellen inschakelen de functie op uw naamruimte automatisch vergroten en de maximale limiet ingesteld op 15 Doorvoereenheden. U kunt uw Doorvoereenheden nu automatisch groeien als uw verkeer toeneemt.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Zijn er kosten wanneer ik inschakelen nadat de functie voor automatisch vergroten?
Er zijn **geen kosten** verbonden aan deze functie. 

### <a name="how-are-throughput-limits-enforced"></a>Hoe wordt de doorvoerlimieten toegepast?
Als de totale ingangsdoorvoer of de totale snelheid van ingangsgebeurtenissen voor alle eventhubs in een naamruimte groter is dan de cumulatieve throughput unit-limiet, worden de afzenders zijn beperkt en ontvangen ze foutberichten die aangeven dat het ingangsquotum is overschreden.

Als de totale uitgangsdoorvoer of de totale snelheid van uitgangsgebeurtenissen voor alle eventhubs in een naamruimte groter is dan de cumulatieve throughput unit-limiet, worden de ontvangers worden vertraagd en ontvangen ze foutberichten die aangeven dat het uitgangsquotum is overschreden. Inkomend en uitgaand verkeer worden afzonderlijk toegepast, waardoor afzenders geen vertraging voor gebeurtenisverbruik veroorzaken kan te vertragen, en niet kan een ontvanger te voorkomen dat gebeurtenissen worden verzonden naar een event hub.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Is er een limiet voor het aantal doorvoereenheden (tabel) die gereserveerd/geselecteerd worden kan?
Op een aanbieding met meerdere tenants, kunnen doorvoereenheden meegroeien, maximaal 40 Doorvoereenheden (u kunt maximaal 20 Doorvoereenheden selecteert in de portal en een ondersteuningsticket om te verhogen naar 40 Doorvoereenheden op dezelfde naamruimte verhogen). Meer dan 40 TUs biedt Event Hubs het model op basis van resource/capaciteit dat de **Event hubs dedicated clusters**wordt genoemd. Toegewezen clusters worden verkocht in capaciteitseenheden (Cu's).

## <a name="dedicated-clusters"></a>Toegewezen clusters

### <a name="what-are-event-hubs-dedicated-clusters"></a>Wat zijn Event Hubs Dedicated clusters?
Event Hubs Dedicated-clusters bieden één tenant-implementaties voor klanten met de meest veeleisende behoeften. Deze aanbieding bouwt een cluster op basis van capaciteit die niet door doorvoereenheden is gekoppeld. Dit betekent dat u het cluster kunt gebruiken om uw gegevens op te nemen en te streamen, zoals bepaald door het CPU-en geheugen gebruik van het cluster. Zie [Event hubs dedicated clusters](event-hubs-dedicated-overview.md)voor meer informatie.

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Hoeveel kiest een één capaciteitseenheid me bereiken?
Hoeveel u voor een toegewezen cluster kunt opnemen en streamen, is afhankelijk van verschillende factoren, zoals uw producenten, consumenten, de snelheid waarmee u opnamet en verwerkt, en nog veel meer. 

Onderstaande tabel ziet u de benchmarkresultaten dat we tijdens onze testen bereikt:

| Nettolading vorm | Ontvangers | Binnenkomende bandbreedte| Binnenkomende berichten | Uitgaande bandbreedte | Uitgaande berichten | Totaal aantal Doorvoereenheden | Doorvoereenheden per Capaciteitseenheid |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches van 100x1KB | 2 | 400 MB per seconde | 400k berichten per seconde | 800 MB per seconde | 800k berichten per seconde | 400 Doorvoereenheden | 100 Doorvoereenheden | 
| Batches van 10x10KB | 2 | 666 MB per seconde | 66.6 k-berichten/sec | 1.33 GB/sec | 133k berichten per seconde | 666 Doorvoereenheden | 166 Doorvoereenheden |
| Batches van 6x32KB | 1 | 1,05 GB/sec | 34k berichten per seconde | 1,05 GB/sec | 34k berichten per seconde | 1000 Doorvoereenheden | 250 Doorvoereenheden |

In het testen, is de volgende criteria gebruikt:

- Er is een specifieke Event Hubs-cluster met vier capaciteitseenheden (Cu's) gebruikt. 
- De event hub die wordt gebruikt voor gegevensopname heeft 200 partities. 
- De gegevens die is opgenomen is ontvangen door twee ontvanger toepassingen ontvangen van alle partities.

De resultaten geven u een idee van wat kan worden bereikt met een specifieke Event Hubs-cluster. Bovendien een cluster toewijzen wordt geleverd met de Event Hubs Capture ingeschakeld voor uw scenario's van microbatches als langdurige opslag.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hoe maak ik een Event Hubs Dedicated-cluster?
U maakt een Event Hubs toegewezen cluster door een [ondersteunings aanvraag voor quotum verhoging](https://portal.azure.com/#create/Microsoft.Support) in te dienen of door contact op te nemen met het [Event hubs team](mailto:askeventhubs@microsoft.com). Het duurt meestal ongeveer twee weken voor het cluster geïmplementeerd en afgegeven aan door u worden gebruikt. Dit proces is tijdelijk totdat een volledig zelfvoorzienings pakket beschikbaar is via de Azure Portal-of Azure Resource Manager sjablonen, die ongeveer twee uur duren om het cluster te implementeren.

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="how-many-partitions-do-i-need"></a>Het aantal partities heb ik nodig?
Het aantal partities wordt opgegeven bij het maken en moet tussen 2 en 32 liggen. Het aantal partities kan niet worden gewijzigd. Houd daarom rekening met de lange termijn bij het instellen van het aantal partities. Partities zijn een mechanisme voor gegevensordening. Ze hebben betrekking op de mate van downstreamparallelheid die is vereist bij het gebruik van toepassingen. Het aantal partities in een Event Hub houdt rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Zie [partities](event-hubs-features.md#partitions)voor meer informatie over partities.

Het is raadzaam om de waarde in te stellen op het hoogst mogelijke niveau: 32, op het moment dat deze wordt gemaakt. Houd er rekening mee dat er meer dan één partitie heeft als gevolg dat er gebeurtenissen worden verzonden naar meerdere partities zonder de volg orde te behouden, tenzij u afzenders configureert om alleen te verzenden naar één enkele partitie van de 32, waardoor de resterende 31 partities overbodig zijn. In het eerste geval moet u gebeurtenissen lezen in alle 32-partities. In het laatste geval zijn er geen extra kosten in rekening van de extra configuratie die u moet maken op de host van de gebeurtenis processor.

Eventhubs is ontworpen om toe te staan een lezer één partitie per consumergroep. Gebruik in de meeste gevallen is de standaardinstelling van vier partities voldoende. Als u van plan bent uw gebeurtenis verwerking te schalen, kunt u overwegen extra partities toe te voegen. Er is geen specifieke doorvoer limiet voor een partitie, maar de geaggregeerde door Voer in uw naam ruimte wordt beperkt door het aantal doorvoer eenheden. Als u het aantal throughput units in uw naamruimte verhoogt, kunt u extra partities om toe te staan van gelijktijdige lezers hun eigen maximale doorvoer te realiseren.

Echter, hebt u een model waarin uw toepassing een affiniteit met een bepaalde partitie heeft, waardoor het aantal partities mogelijk niet van een voordeel voor u. Zie [Beschik baarheid en consistentie](event-hubs-availability-and-consistency.md)voor meer informatie.

## <a name="pricing"></a>Prijzen

### <a name="where-can-i-find-more-pricing-information"></a>Waar vind ik meer informatie over de prijzen?

Zie de [Event hubs prijs informatie](https://azure.microsoft.com/pricing/details/event-hubs/)voor volledige informatie over Event hubs prijzen.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Is er een post voor die gebeurtenissen van Event Hubs bewaren gedurende meer dan 24 uur?

De Event Hubs Standard-laag is toegestaan voor bewaarperiode van bericht perioden van meer dan 24 uur, maximaal zeven dagen. Als de grootte van het totale aantal opgeslagen gebeurtenissen de opslaglimiet voor het aantal geselecteerde doorvoereenheden (84 GB per doorvoereenheid) overschrijdt, de maximaal toegestane limiet overschrijdt, wordt in rekening gebracht tegen de gepubliceerde tarief voor de Azure Blob-opslag. De opslaglimiet voor elke throughput unit omvat alle opslagkosten voor bewaarperioden van 24 uur (de standaardinstelling), zelfs als de throughput unit in de limiet voor de maximale ingangslimiet is verbruikt.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hoe wordt de Event Hubs-opslaggrootte berekend en in rekening gebracht?

De totale grootte van alle opgeslagen gebeurtenissen, inclusief interne overhead voor gebeurteniskoppen of opslagstructuren op schijf in alle eventhubs, wordt gedurende de hele dag gemeten. Aan het einde van de dag wordt de grootte van de piekopslag berekend. De dagelijkse opslaglimiet wordt berekend op basis van het minimum aantal doorvoereenhden dat gedurende de dag is geselecteerd (elke doorvoereenheid biedt een limiet van 84 GB). Als de totale grootte de berekende dagelijkse opslag limiet overschrijdt, wordt de overmatige opslag gefactureerd met behulp van Azure Blob-opslag tarieven (tegen de **lokaal redundante opslag** snelheid).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hoe worden ingangsgebeurtenissen voor Event Hubs berekend?

Elke gebeurtenis verzonden naar een event hub telt als een factureerbaar bericht. Een *ingangs gebeurtenis* wordt gedefinieerd als een gegevens eenheid die kleiner is dan of gelijk is aan 64 kB. Elke gebeurtenis kleiner dan of gelijk is aan 64 KB groot is, wordt beschouwd als één factureerbare gebeurtenis. Als de gebeurtenis groter dan 64 KB is, wordt het aantal factureerbare gebeurtenissen berekend op basis van de gebeurtenisgrootte in veelvouden van 64 KB. Bijvoorbeeld, een 8 KB gebeurtenis verzonden naar de event hub wordt gefactureerd als één gebeurtenis, maar een bericht van 96 KB-bericht verzonden naar de event hub wordt gefactureerd als twee gebeurtenissen.

Gebeurtenissen gebruikt vanaf een event hub, evenals beheerbewerkingen en controle-aanroepen, zoals controlepunten, worden niet gerekend als factureerbare ingangsgebeurtenissen, maar doorlopen tot de limiet voor doorvoereenheden.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gelden er kosten voor brokered Connections naar Event Hubs?

Verbindingskosten van toepassing alleen wanneer het AMQP-protocol wordt gebruikt. Er worden geen verbindingskosten in rekening gebracht voor het verzenden van gebeurtenissen via HTTP, ongeacht het aantal verzendsystemen/-apparaten. Als u van plan bent AMQP te gebruiken (bijvoorbeeld om efficiëntere gebeurtenis streaming te maken of om bidirectionele communicatie in IoT-opdracht-en controle scenario's in te scha kelen), raadpleegt u de pagina met [prijs informatie voor Event hubs](https://azure.microsoft.com/pricing/details/event-hubs/) voor meer informatie over hoeveel verbindingen in elke servicelaag zijn opgenomen.

### <a name="how-is-event-hubs-capture-billed"></a>Hoe wordt Event Hubs Capture gefactureerd?

Vastleggen is ingeschakeld wanneer een event hub in de naamruimte de optie vastleggen is ingeschakeld is. Event Hubs Capture wordt per uur gefactureerd per aangeschafte doorvoereenheid. Als het aantal doorvoereenheden toe- of afneemt, Event Hubs Capture facturering deze wijzigingen in stappen van hele uren weerspiegeld worden weergegeven. Zie [Event hubs prijs informatie](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over het vastleggen van de facturering van Event hubs.

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>In rekening gebracht voor het opslagaccount dat ik voor Event Hubs Capture Selecteer?

Vastleggen maakt gebruik van een opslagaccount dat die u verschaft wanneer dit is ingeschakeld op een event hub. Uw storage-account is, worden eventuele wijzigingen voor deze configuratie met uw Azure-abonnement gefactureerd.

## <a name="quotas"></a>Quota

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Zijn er geen quota's die zijn gekoppeld aan de Event Hubs?

Zie [quota's](event-hubs-quotas.md)voor een lijst met alle Event hubs quota's.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Waarom kan ik geen naam ruimte maken nadat ik deze heb verwijderd uit een ander abonnement? 
Wanneer u een naam ruimte uit een abonnement verwijdert, wacht u vier uur voordat u deze opnieuw maakt met dezelfde naam in een ander abonnement. Anders wordt het volgende fout bericht weer gegeven: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Wat zijn enkele van de uitzonderingen die worden gegenereerd door de Event Hubs en hun voorgestelde acties?

Zie [overzicht van uitzonde ringen](event-hubs-messaging-exceptions.md)voor een lijst met mogelijke Event hubs uitzonde ringen.

### <a name="diagnostic-logs"></a>Diagnostische logboeken

Event Hubs ondersteunt twee typen [Diagnostische logboeken](event-hubs-diagnostic-logs.md) : fout logboeken en operationele logboeken vastleggen: beide worden weer gegeven in JSON en kunnen via de Azure Portal worden ingeschakeld.

### <a name="support-and-sla"></a>Ondersteuning en SLA

Technische ondersteuning voor Event Hubs is beschikbaar via de [forums](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)van de community. Ondersteuning bij facturering en abonnementsbeheer is gratis.

Voor meer informatie over onze SLA gaat u naar de pagina [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
* [Event Hubs automatisch verg Roten](event-hubs-auto-inflate.md)
