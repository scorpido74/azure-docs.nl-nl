---
title: Veelgestelde vragen - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u een lijst met veelgestelde vragen (FAQ) voor Azure Event Hubs en hun antwoorden.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 7f6e1896c97c96cd484d15fb9e6a3056e5c5d6b2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086365"
---
# <a name="event-hubs-frequently-asked-questions"></a>Evenementenhubs veelgestelde vragen

## <a name="general"></a>Algemeen

### <a name="what-is-an-event-hubs-namespace"></a>Wat is de naamruimte van een Gebeurtenishubs?
Een naamruimte is een scopingcontainer voor Event Hub/Kafka-onderwerpen. Het geeft je een unieke [FQDN.](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) Een naamruimte dient als toepassingscontainer waarmee meerdere Event Hub/Kafka-onderwerpen kunnen worden huisvesten. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Wanneer maak ik een nieuwe naamruimte versus gebruik een bestaande naamruimte?
Capaciteitstoewijzingen[(doorvoereenheden ( US)](#throughput-units)worden gefactureerd op het naamruimteniveau. Een naamruimte is ook gekoppeld aan een regio.

U een nieuwe naamruimte maken in plaats van een bestaande ruimte te gebruiken in een van de volgende scenario's: 

- Je hebt een gebeurtenishub nodig die is gekoppeld aan een nieuwe regio.
- Je hebt een Event Hub nodig die is gekoppeld aan een ander abonnement.
- U hebt een eventhub nodig met een duidelijke capaciteitstoewijzing (dat wil zeggen dat de capaciteitsbehoefte voor de naamruimte met de toegevoegde gebeurtenishub de drempel van 40 TU overschrijdt en u niet voor het specifieke cluster wilt gaan)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Wat is het verschil tussen Event Hubs Basic en Standard tiers?

De standaardlaag azure-gebeurtenishubs biedt functies die verder gaan dan wat beschikbaar is in de laag Basis. De volgende functies zijn opgenomen met Standaard:

* Langere gebeurtenisretentie
* Aanvullende tussenpersoon verbindingen, met een overschrijding kosten voor meer dan het aantal inbegrepen
* Meer dan één [consumentengroep](event-hubs-features.md#consumer-groups)
* [Vangen](event-hubs-capture-overview.md)
* [Kafka-integratie](event-hubs-for-kafka-ecosystem-overview.md)

Zie de [prijsdetails van gebeurtenishubs](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over prijsniveaus, waaronder Dedicated Event Hubs.

### <a name="where-is-azure-event-hubs-available"></a>Waar zijn Azure Event Hubs beschikbaar?

Azure Event Hubs is beschikbaar in alle ondersteunde Azure-regio's. Ga voor een lijst naar de pagina [Azure-regio's.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan ik één AMQP-verbinding gebruiken om te verzenden en te ontvangen van meerdere gebeurtenishubs?

Ja, zolang alle gebeurtenishubs zich in dezelfde naamruimte bevinden.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Wat is de maximale bewaartermijn voor evenementen?

De standaardlaag gebeurtenishubs ondersteunt momenteel een maximale bewaartermijn van zeven dagen. Gebeurtenishubs zijn niet bedoeld als permanent gegevensarchief. Bewaarperioden van meer dan 24 uur zijn bedoeld voor scenario's waarin het handig is om een gebeurtenisstream opnieuw af te spelen in dezelfde systemen; bijvoorbeeld om een nieuw machine learning-model op bestaande gegevens te trainen of te verifiëren. Als u berichtbehoud langer dan zeven dagen nodig hebt, haalt het vastleggen van [gebeurtenishubs](event-hubs-capture-overview.md) op uw gebeurtenishub de gegevens uit uw gebeurtenishub naar het opslagaccount of het Azure Data Lake Service-account van uw keuze. Als u Capture inschakelt, worden kosten in rekening gebracht op basis van uw gekochte doorvoereenheden.

U de bewaartermijn voor de vastgelegde gegevens op uw opslagaccount configureren. De functie **levenscyclusbeheer** van Azure Storage biedt een uitgebreid, op regels gebaseerd beleid voor v2- en blobopslagaccounts voor algemene doeleinden. Gebruik het beleid om uw gegevens over te brengen naar de juiste toegangsniveaus of te verlopen aan het einde van de levenscyclus van de gegevens. Zie [De levenscyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)voor meer informatie. 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hoe houd ik mijn eventhubs in de gaten?
Event Hubs geeft uitgebreide statistieken die de status van uw resources bieden aan [Azure Monitor.](../azure-monitor/overview.md) Hiermee u ook de algehele status van de Gebeurtenishubs-service beoordelen, niet alleen op naamruimteniveau, maar ook op entiteitsniveau. Meer informatie over welke bewaking wordt aangeboden voor [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welke poorten heb ik nodig om te openen op de firewall? 
U de volgende protocollen met Azure Service Bus gebruiken om berichten te verzenden en te ontvangen:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Zie de volgende tabel voor de uitgaande poorten die u moet openen om deze protocollen te gebruiken om te communiceren met Azure Event Hubs. 

| Protocol | Poorten | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 en 5672 | Zie [AMQP-protocolgids](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Bekijk [Gebeurtenishubs gebruiken vanuit Kafka-toepassingen](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Welke IP-adressen moet ik op de witte lijst krijgen?
Voer de volgende stappen uit om de juiste IP-adressen voor uw verbindingen te vinden voor de juiste IP-adressen in de witte lijst voor uw verbindingen:

1. Voer de volgende opdracht uit vanuit een opdrachtprompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer`. De enige keer dat het zou veranderen is als u de naamruimte te herstellen op een ander cluster.

Als u de zoneredundantie voor uw naamruimte gebruikt, moet u een paar extra stappen uitvoeren: 

1. Eerst laat je nslookup op de naamruimte lopen.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Noteer de naam in de **niet-gezaghebbende antwoordsectie,** die zich in een van de volgende indelingen bevindt: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Voer nslookup uit voor elk met achtervoegsels s1, s2 en s3 om de IP-adressen van alle drie de instanties in drie beschikbaarheidszones te laten draaien, 

## <a name="apache-kafka-integration"></a>Apache Kafka integratie

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hoe integreer ik mijn bestaande Kafka-applicatie met Event Hubs?
Event Hubs biedt een Kafka-eindpunt dat kan worden gebruikt door uw bestaande Apache Kafka-gebaseerde toepassingen. Een configuratiewijziging is alles wat nodig is om de PaaS Kafka-ervaring te hebben. Het biedt een alternatief voor het runnen van uw eigen Kafka-cluster. Event Hubs ondersteunt Apache Kafka 1.0 en nieuwere clientversies en werkt met uw bestaande Kafka-toepassingen, -hulpprogramma's en frameworks. Zie [Gebeurtenishubs voor Kafka repo voor](https://github.com/Azure/azure-event-hubs-for-kafka)meer informatie.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Welke configuratiewijzigingen moeten worden uitgevoerd voor mijn bestaande toepassing om met Gebeurtenishubs te praten?
Als u verbinding wilt maken met een gebeurtenishub, moet u de configs van de Kafka-client bijwerken. Dit wordt gedaan door een naamruimte voor gebeurtenishubs te maken en de [verbindingstekenreeks te](event-hubs-get-connection-string.md)verkrijgen. Wijzig de bootstrap.servers om de Event Hubs FQDN en de poort naar 9093 te richten. Werk de sasl.jaas.config bij om de Kafka-client naar het eindpunt van uw Gebeurtenishubs (dat is de verbindingstekenreeks die u hebt verkregen) te leiden met de juiste verificatie zoals hieronder wordt weergegeven:

bootstrap.servers={YOUR. EVENTHUBS. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR. EVENTHUBS. Verbinding. STRING}";

Voorbeeld:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.common.security.plain.plainLoginModule required username="$ConnectionString password"="Endpoint="Endpoint="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKey=5dOntTTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Opmerking: Als sasl.jaas.config geen ondersteunde configuratie in uw framework is, zoekt u de configuraties die worden gebruikt om de SASL-gebruikersnaam en -wachtwoord in te stellen en deze in plaats daarvan te gebruiken. Stel de gebruikersnaam in op $ConnectionString en het wachtwoord voor de verbindingstekenreeks van Gebeurtenishubs.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Wat is de bericht-/gebeurtenisgrootte voor gebeurtenishubs?
De maximale berichtgrootte die is toegestaan voor gebeurtenishubs is 1 MB.

## <a name="throughput-units"></a>Doorvoereenheden

### <a name="what-are-event-hubs-throughput-units"></a>Wat zijn overslageenheden van Event Hubs?
Doorvoer in gebeurtenishubs definieert de hoeveelheid gegevens in megabytes of het aantal (in duizenden) gebeurtenissen van 1 KB die binnenkomen en via gebeurtenishubs binnendringen. Deze doorvoer wordt gemeten in doorvoereenheden (US's). Koop Amerikaanse tv's voordat u de service Event Hubs gebruiken. U gebeurtenishubs-amerikaanse gegevens expliciet selecteren met portal- of Event Hubs Resource Manager-sjablonen. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Zijn doorvoereenheden van toepassing op alle gebeurtenishubs in een naamruimte?
Ja, doorvoereenheden (US' s) zijn van toepassing op alle gebeurtenishubs in de naamruimte van gebeurtenishubs. Dit betekent dat u tv's koopt op naamruimteniveau en wordt gedeeld tussen de gebeurtenishubs onder die naamruimte. Elke TU geeft de naamruimte recht op de volgende mogelijkheden:

- Maximaal 1 MB per seconde van binnenkomende gebeurtenissen (gebeurtenissen die naar een gebeurtenishub worden verzonden), maar niet meer dan 1000 invallende gebeurtenissen, beheerbewerkingen of API-aanroepen per seconde.
- Maximaal 2 MB per seconde van uitgaande gebeurtenissen (gebeurtenissen die worden verbruikt vanaf een gebeurtenishub), maar niet meer dan 4096 uitgaande gebeurtenissen.
- Tot 84 GB aan gebeurtenisopslag (genoeg voor de standaard bewaarperiode van 24 uur).

### <a name="how-are-throughput-units-billed"></a>Hoe worden doorvoereenheden gefactureerd?
Doorvoereenheden (US' s) worden per uur gefactureerd. De facturering is gebaseerd op het maximum aantal eenheden dat tijdens het gegeven uur is geselecteerd. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hoe kan ik het gebruik van mijn doorvoereenheden optimaliseren?
U zo laag als één doorvoereenheid (TU) starten en [automatisch opblazen inschakelen.](event-hubs-auto-inflate.md) Met de functie automatisch opblazen u uw TV's laten groeien naarmate uw verkeer/laadvermogen toeneemt. U ook een bovengrens instellen voor het aantal tv's.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hoe werkt de functie Automatisch opblazen van gebeurtenishubs?
Met de functie automatisch opblazen u uw doorvoereenheden (US) opschalen. Het betekent dat u beginnen met de aankoop van lage TV's en auto-opblazen schalen van uw TV's als uw binnendringen toeneemt. Het geeft u een kosteneffectieve optie en volledige controle over het aantal TV's te beheren. Deze functie is alleen een **scale-up-functie** en u de afschaling van het aantal amerikaanse tv's volledig regelen door deze bij te werken. 

U beginnen met lage doorvoereenheden (US), bijvoorbeeld 2 TV's. Als u voorspelt dat uw verkeer kan uitgroeien tot 15 tv's, schakelt u de functie automatisch opblazen in uw naamruimte in en stelt u de maximumlimiet in op 15 tv's. U uw TV's nu automatisch laten groeien naarmate uw verkeer groeit.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Zijn er kosten verbonden wanneer ik de functie automatisch opblazen inschakel?
Er zijn **geen kosten** verbonden aan deze functie. 

### <a name="how-are-throughput-limits-enforced"></a>Hoe worden doorvoerlimieten afgedwongen?
Als de totale instroomdoorvoer of de totale instroomgebeurtenissnelheid voor alle gebeurtenishubs in een naamruimte groter is dan de totale doorvoereenheidsemissierechten, worden afzenders beperkt en worden fouten ontvangen die aangeven dat het invallende quotum is overschreden.

Als de totale uitgangsdoorvoer of de totale gebeurtenis-uitgangssnelheid voor alle gebeurtenishubs in een naamruimte groter is dan de totale doorvoereenheidsemissierechten, worden ontvangers beperkt en worden fouten ontvangen die erop wijzen dat het uitgangsquotum is overschreden. Binnenkomende en uitgaande quota worden afzonderlijk afgedwongen, zodat geen enkele afzender kan leiden tot vertraging van het gebeurtenisverbruik, noch kan een ontvanger voorkomen dat gebeurtenissen naar een gebeurtenishub worden verzonden.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Is er een limiet aan het aantal doorvoereenheden (US) dat kan worden gereserveerd/geselecteerd?
Op een multi-tenant-aanbod kunnen doorvoereenheden tot 40 tv's groeien (u maximaal 20 tv's selecteren in het portaal en een ondersteuningsticket verhogen om het te verhogen tot 40 tv's op dezelfde naamruimte). Naast 40 Amerikaanse tv's biedt Event Hubs het resource/capacity-gebaseerde model genaamd de **Event Hubs Dedicated clusters.** Speciale clusters worden verkocht in Capaciteitseenheden (C's).

## <a name="dedicated-clusters"></a>Speciale clusters

### <a name="what-are-event-hubs-dedicated-clusters"></a>Wat zijn toegewezen clusters van Event Hubs?
Event Hubs Dedicated clusters bieden implementaties met één tenant voor klanten met de meest veeleisende vereisten. Dit aanbod bouwt een op capaciteit gebaseerd cluster dat niet gebonden is aan doorvoereenheden. Dit betekent dat u het cluster gebruiken om uw gegevens in te nemen en te streamen, zoals bepaald door de CPU en het geheugengebruik van het cluster. Zie [Speciale clusters van gebeurtenishubs](event-hubs-dedicated-overview.md)voor meer informatie.

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Wat kan ik doen met één capaciteitseenheid?
Voor een speciaal cluster is hoeveel u innemen en streamen afhankelijk van verschillende factoren, zoals uw producenten, consumenten, de snelheid waarmee u inneemt en verwerkt, en nog veel meer. 

Onderstaande tabel toont de benchmarkresultaten die we tijdens onze tests hebben behaald:

| Payload-vorm | Ontvangers | Binnenvallende bandbreedte| Binnenkomende berichten | Bandbreedte uit de uitgang | Berichten uit de uitgang | Totaal amerikaanse tus | Amerikaanse sus's per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches van 100x1KB | 2 | 400 MB/s | 400k berichten per seconde | 800 MB/s | 800k berichten per seconde | 400 tUs | 100 tus | 
| Batches van 10x10KB | 2 | 666 MB/s | 66,6k berichten per seconde | 1,33 GB/s | 133k berichten per seconde | 666 tus's | 166 tUs |
| Batches van 6x32KB | 1 | 1,05 GB/s | 34k berichten / sec | 1,05 GB/s | 34k berichten per seconde | 1000 tus | 250 amerikaanse sus's |

Bij de tests werden de volgende criteria gebruikt:

- Er is gebruik gemaakt van een speciaal cluster van Event Hubs met vier capaciteitseenheden (CPU's). 
- De gebeurtenishub die voor inname wordt gebruikt had 200 partities. 
- De gegevens die werd ingenomen werd ontvangen door twee ontvanger toepassingen ontvangen van alle partities.

De resultaten geven u een idee van wat er bereikt kan worden met een speciaal Cluster Van Event Hubs. Daarnaast wordt een cluster met dedicated geleverd met de Event Hubs Capture die zijn ingeschakeld voor uw microbatch- en langetermijnretentiescenario's.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hoe maak ik een dedicated cluster voor eventhubs?
U maakt een speciaal cluster voor gebeurtenishubs door een [ondersteuningsaanvraag voor quotaverhoging](https://portal.azure.com/#create/Microsoft.Support) in te dienen of door contact op te nemen met het [team van Event Hubs.](mailto:askeventhubs@microsoft.com) Het duurt meestal ongeveer twee weken om het cluster geïmplementeerd en overhandigd te worden door u. Dit proces is tijdelijk totdat een volledige self-serve beschikbaar is gesteld via de Azure-portal.

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="how-many-partitions-do-i-need"></a>Hoeveel partities heb ik nodig?
Het aantal partities wordt opgegeven bij het maken en moet tussen 2 en 32 liggen. Het aantal partities is niet veranderlijk, dus u moet rekening houden met de langetermijnschaal bij het instellen van het aantal partities. Partities zijn een mechanisme voor gegevensordening. Ze hebben betrekking op de mate van downstreamparallelheid die is vereist bij het gebruik van toepassingen. Het aantal partities in een Event Hub houdt rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Zie Partities voor meer informatie over [partities.](event-hubs-features.md#partitions)

U instellen dat het de hoogst mogelijke waarde is, namelijk 32, op het moment van creatie. Vergeet niet dat het hebben van meer dan één partitie zal resulteren in gebeurtenissen die naar meerdere partities worden verzonden zonder de volgorde te behouden, tenzij u afzenders configureert om slechts naar één partitie van de 32 te verzenden en de resterende 31 partities overbodig te laten. In het eerste geval moet u gebeurtenissen lezen over alle 32 partities. In het laatste geval zijn er geen duidelijke extra kosten, afgezien van de extra configuratie die u moet maken op Event Processor Host.

Event Hubs is ontworpen om één partitielezer per consumentengroep mogelijk te maken. In de meeste gebruiksgevallen volstaat de standaardinstelling van vier partities. Als u uw gebeurtenisverwerking wilt schalen, u overwegen extra partities toe te voegen. Er is geen specifieke doorvoerlimiet voor een partitie, maar de totale doorvoer in uw naamruimte wordt beperkt door het aantal doorvoereenheden. Als u het aantal doorvoereenheden in uw naamruimte verhoogt, wilt u mogelijk extra partities zodat gelijktijdige lezers hun eigen maximale doorvoer kunnen bereiken.

Als u echter een model hebt waarin uw toepassing affiniteit heeft met een bepaalde partitie, kan het verhogen van het aantal partities u niet ten goede komen. Zie [beschikbaarheid en consistentie](event-hubs-availability-and-consistency.md)voor meer informatie.

## <a name="pricing"></a>Prijzen

### <a name="where-can-i-find-more-pricing-information"></a>Waar vind ik meer prijsinformatie?

Zie de [prijsgegevens van Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)voor volledige informatie over de prijzen van Event Hubs.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Zijn er kosten verbonden aan het langer dan 24 uur bewaren van evenementen met Event Hubs?

De standaardlaag Gebeurtenishubs staat de retentieperiode van berichten langer dan 24 uur toe, voor maximaal zeven dagen. Als de grootte van het totale aantal opgeslagen gebeurtenissen groter is dan de opslaghoeveelheid voor het aantal geselecteerde doorvoereenheden (84 GB per doorvoereenheid), wordt de grootte die de hoeveelheid overschrijdt, in rekening gebracht bij de gepubliceerde Azure Blob-opslagsnelheid. De opslagvergoeding in elke doorvoereenheid dekt alle opslagkosten voor bewaarperioden van 24 uur (de standaardwaarde), zelfs als de doorvoereenheid wordt gebruikt tot de maximale instroomvergoeding.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hoe wordt de opslaggrootte van De Gebeurtenishubs berekend en in rekening gebracht?

De totale grootte van alle opgeslagen gebeurtenissen, inclusief eventuele interne overhead voor gebeurteniskoppen of op schijfopslagstructuren in alle gebeurtenishubs, wordt gedurende de dag gemeten. Aan het einde van de dag wordt de piekopslaggrootte berekend. De dagelijkse opslaghoeveelheid wordt berekend op basis van het minimumaantal doorvoereenheden dat gedurende de dag is geselecteerd (elke doorvoereenheid biedt een vergoeding van 84 GB). Als de totale grootte hoger is dan de berekende dagelijkse opslaghoeveelheid, wordt de overtollige opslag gefactureerd met Azure Blob-opslagsnelheden (tegen de **locatie-redundante opslagsnelheid).**

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hoe worden gebeurtenishubs gebeurtenissen berekend?

Elke gebeurtenis die naar een gebeurtenishub wordt verzonden, geldt als een factureerbaar bericht. Een *invallende gebeurtenis* wordt gedefinieerd als een eenheid gegevens die kleiner is dan of gelijk is aan 64 KB. Elke gebeurtenis die kleiner is dan of gelijk is aan 64 KB in grootte wordt beschouwd als een factureerbare gebeurtenis. Als de gebeurtenis groter is dan 64 KB, wordt het aantal factureerbare gebeurtenissen berekend op basis van de gebeurtenisgrootte, in veelvouden van 64 KB. Een gebeurtenis van 8 KB die naar de gebeurtenishub wordt verzonden, wordt bijvoorbeeld gefactureerd als één gebeurtenis, maar een 96-KB-bericht dat naar de gebeurtenishub wordt verzonden, wordt gefactureerd als twee gebeurtenissen.

Gebeurtenissen die worden verbruikt vanuit een gebeurtenishub, evenals beheerbewerkingen en controleoproepen zoals controlepunten, worden niet meegeteld als factureerbare binnendringende gebeurtenissen, maar worden opgebouwd tot de doorvoereenheidsvergoeding.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Zijn er bemiddelingskosten van toepassing op gebeurtenishubs?

Verbindingskosten zijn alleen van toepassing wanneer het AMQP-protocol wordt gebruikt. Er zijn geen verbindingskosten verbonden aan het verzenden van gebeurtenissen via HTTP, ongeacht het aantal verzendsystemen of -apparaten. Als u van plan bent AMQP te gebruiken (bijvoorbeeld om efficiëntere gebeurtenisstreaming te bereiken of om bidirectionele communicatie in Te schakelen in IoT-opdracht- en controlescenario's), raadpleegt u de [prijsinformatiepagina van Gebeurtenishubs](https://azure.microsoft.com/pricing/details/event-hubs/) voor meer informatie over het aantal verbindingen dat in elke servicelaag is opgenomen.

### <a name="how-is-event-hubs-capture-billed"></a>Hoe wordt Event Hubs Capture gefactureerd?

Vastleggen is ingeschakeld wanneer een gebeurtenishub in de naamruimte de optie Vastleggen heeft ingeschakeld. Event Hubs Capture wordt per uur gefactureerd per gekochte doorvoereenheid. Als het aantal doorvoereenheden wordt verhoogd of verlaagd, weerspiegelt Event Hubs Capture-facturering deze wijzigingen in stappen van hele uren. Zie Prijsgegevens van [gebeurtenishubs](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over facturering via gebeurtenishubs.

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Krijg ik kosten in rekening voor het opslagaccount dat ik selecteer voor Event Hubs Capture?

Capture maakt gebruik van een opslagaccount dat u opgeeft wanneer het is ingeschakeld op een gebeurtenishub. Omdat het uw opslagaccount is, worden eventuele wijzigingen voor deze configuratie in rekening gebracht in uw Azure-abonnement.

## <a name="quotas"></a>Quota

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Zijn er quota gekoppeld aan gebeurtenishubs?

Zie [quota](event-hubs-quotas.md)voor een lijst met alle quota voor gebeurtenishubs .

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Waarom kan ik geen naamruimte maken nadat ik deze uit een ander abonnement heb verwijderd? 
Wanneer u een naamruimte uit een abonnement verwijdert, wacht u 4 uur voordat u deze opnieuw maakt met dezelfde naam in een ander abonnement. Anders ontvangt u mogelijk het `Namespace already exists`volgende foutbericht: . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Wat zijn enkele uitzonderingen die worden gegenereerd door gebeurtenishubs en hun voorgestelde acties?

Zie [Uitzonderingenoverzicht](event-hubs-messaging-exceptions.md)voor een lijst met mogelijke uitzonderingen op gebeurtenishubs.

### <a name="diagnostic-logs"></a>Diagnostische logboeken

Event Hubs ondersteunt twee soorten [diagnostische logboeken](event-hubs-diagnostic-logs.md) - Leg foutlogboeken en operationele logboeken vast - beide zijn vertegenwoordigd in json en kunnen worden ingeschakeld via de Azure-portal.

### <a name="support-and-sla"></a>Ondersteuning en SLA

Technische ondersteuning voor Event Hubs is beschikbaar via de [communityforums.](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus) Ondersteuning voor facturering en abonnementsbeheer wordt gratis aangeboden.

Zie de pagina [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) voor meer informatie over onze SLA.

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van gebeurtenishubs](event-hubs-what-is-event-hubs.md)
* [Een gebeurtenishub maken](event-hubs-create.md)
* [Gebeurtenishubs automatisch opblazen](event-hubs-auto-inflate.md)
