---
title: Streaming-gebeurtenissen - Azure Event Hubs Capture | Microsoft Docs
description: Dit artikel bevat een overzicht van de Capture-functie waarmee u vast te leggen gebeurtenissen streamen via Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 9b69feef7c6587f7356648e6a6828277ba500aea
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460072"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Vastleggen van gebeurtenissen tot en met Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage
Met Azure Event Hubs kunt u automatisch de streaminggegevens vastleggen in Event Hubs in een [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of [Azure data Lake Storage gen 1-of gen 2](https://azure.microsoft.com/services/data-lake-store/) -account van uw keuze, met extra flexibiliteit voor het opgeven van een tijd-of grootte-interval. Het instellen van vastleggen is snel, er zijn geen administratieve kosten om deze uit te voeren en deze worden automatisch geschaald met Event Hubs [doorvoer eenheden](event-hubs-scalability.md#throughput-units). Event Hubs Capture is de eenvoudigste manier om het streaming-gegevens laden in Azure, en kunt u zich richten op gegevensverwerking in plaats van gegevensregistratie.

Event Hubs Capture kunt u realtime en op basis van een batch-pijplijnen in dezelfde stroom verwerken. Dit betekent dat u oplossingen die meegroeien met uw behoeften na verloop van tijd kunt bouwen. Of u op basis van een batch-systemen vandaag nog met het oog voor toekomstige verwerking in realtime bouwt, of u wilt een efficiënte koude pad toevoegen aan een bestaande oplossing voor realtime, maakt Event Hubs Capture werken met het streamen van gegevens gemakkelijker.


## <a name="how-event-hubs-capture-works"></a>Event Hubs Capture werking

Eventhubs is een duurzame buffer retentietijd voor telemetrie inkomend verkeer, vergelijkbaar met een gedistribueerde logboek. De te schalen sleutel in Event Hubs is het [gepartitioneerde consument model](event-hubs-scalability.md#partitions). Elke partitie is een onafhankelijke segment van de gegevens en onafhankelijk van elkaar worden verbruikt. Na verloop van tijd leeftijden deze gegevens op uit, op basis van de configureerbare bewaarperiode. "Als gevolg hiervan een bepaalde event hub nooit te vol raakt."

Met Event Hubs Capture kunt u uw eigen Azure Blob-opslag account en-container of Azure Data Lake Storage-account opgeven dat wordt gebruikt voor het opslaan van de vastgelegde gegevens. Deze accounts kunnen zich in dezelfde regio als uw event hub of in een andere regio toe te voegen aan de flexibiliteit van de Event Hubs Capture-functie.

Vastgelegde gegevens worden geschreven in [Apache Avro][Apache Avro] -indeling: een compacte, snelle en binaire indeling die voorziet in uitgebreide gegevens structuren met inline-schema. Deze indeling wordt veel gebruikt in de Hadoop-ecosysteem, Stream Analytics en Azure Data Factory. Meer informatie over het werken met Avro vindt u verderop in dit artikel.

### <a name="capture-windowing"></a>Windowing vastleggen

Event Hubs Capture kunt u het instellen van een venster voor het beheren van vast te leggen. Dit venster is een minimale grootte en de configuratie van de tijd met 'eerste WINS-beleid,' wat betekent dat een capture-bewerking zorgt ervoor dat de eerste trigger is aangetroffen. Als u een vijftien minuten hebt, wordt 100 MB venster vastleggen en verzenden van 1 MB per seconde, de grootte van venster triggers voor het tijdvenster. Elke partitie afzonderlijk vastgelegd en schrijft een voltooide blok-blob op het moment van opname, met de naam van de tijd waarbinnen de capture-interval is opgetreden. De naamconventie voor opslag is als volgt:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Houd er rekening mee dat de date-waarden worden opgevuld met nullen; een voorbeeld van de bestandsnaam is mogelijk:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

In het geval dat uw Azure Storage-BLOB tijdelijk niet beschikbaar is, behoudt Event Hubs Capture uw gegevens voor de Bewaar periode voor gegevens die op uw Event Hub is geconfigureerd en vult de gegevens terug wanneer uw opslag account weer beschikbaar is.

### <a name="scaling-to-throughput-units"></a>Schalen naar doorvoereenheden

Event Hubs verkeer wordt bepaald door [doorvoer eenheden](event-hubs-scalability.md#throughput-units). Eén doorvoereenheid toestaat 1 MB per seconde of 1000 gebeurtenissen per seconde van inkomend verkeer en twee keer de omvang van uitgaand verkeer. Standard Event Hubs kunnen worden geconfigureerd met 1-20 doorvoer eenheden en u kunt meer kopen met een [ondersteunings aanvraag][support request]voor quotum verhoging. Gebruik bij meer dan uw aangeschafte doorvoereenheden wordt vertraagd. Event Hubs Capture kopieert gegevens rechtstreeks van de interne opslag van de Event Hubs voor het overslaan van doorvoer eenheid uitgaande quota en opslaan van uw uitgaand verkeer voor andere lezers van de verwerking, zoals Stream Analytics of Apache Spark.

Wanneer geconfigureerd, Event Hubs Capture wordt automatisch uitgevoerd wanneer u uw eerste gebeurtenis verzenden en verder wordt uitgevoerd. Event Hubs schrijft lege bestanden te vereenvoudigen voor uw downstream-verwerkingen weten of het proces werkt, als er geen gegevens. Deze procedure biedt een voorspelbare uitgebracht en markering waarin uw batch-processors.

## <a name="setting-up-event-hubs-capture"></a>Instellen van Event Hubs Capture

U kunt vastleggen configureren op het moment van Event Hub aanmaak met behulp van de [Azure Portal](https://portal.azure.com)of Azure Resource Manager sjablonen gebruiken. Raadpleeg voor meer informatie de volgende artikelen:

- [Event Hubs Capture inschakelen met behulp van de Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Een Event Hubs naam ruimte met een Event Hub maken en vastleggen inschakelen met een Azure Resource Manager-sjabloon](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>De vastgelegde bestanden verkennen en werken met Avro

Event Hubs Capture, maakt bestanden in de Avro-indeling, zoals opgegeven op de geconfigureerde periode. U kunt deze bestanden weer geven in elk hulp programma, zoals [Azure Storage Explorer][Azure Storage Explorer]. U kunt downloaden de bestanden lokaal te werken.

De bestanden die worden geproduceerd door Event Hubs Capture hebben de volgende Avro-schema:

![Avro-schema][3]

Een eenvoudige manier om Avro-bestanden te verkennen, is met behulp van de [Avro-Hulpprogram ma's][Avro Tools] jar van Apache. U kunt Apache- [analyse][Apache Drill] ook gebruiken voor een lichte, op SQL gebaseerde ervaring of [Apache Spark][Apache Spark] voor het uitvoeren van complexe gedistribueerde verwerking op de opgenomen gegevens. 

### <a name="use-apache-drill"></a>Apache-boren gebruiken

[Apache Drill][Apache Drill] is een open-source SQL-query-engine voor het verkennen van Big Data, waarmee u op elke gewenste manier gestructureerde en semi-gestructureerde gegevens kunt opvragen. De engine kan worden uitgevoerd als een zelfstandig knoop punt of als een enorm cluster voor geweldige prestaties.

Er is een systeem eigen ondersteuning beschikbaar voor Azure Blob Storage, waarmee u eenvoudig gegevens kunt opvragen in een AVRO-bestand, zoals beschreven in de documentatie:

[Apache-detail weergave: Azure Blob Storage-invoeg toepassing][Apache Drill: Azure Blob Storage Plugin]

Als u snel vastgelegde bestanden wilt doorzoeken, kunt u een virtuele machine maken en uitvoeren met Apache-Drill ingeschakeld via een container om toegang te krijgen tot Azure Blob-opslag:

https://github.com/yorek/apache-drill-azure-blob

Een volledig end-to-end-voor beeld is beschikbaar in het streamen in de opslag plaats op schaal:

[Streaming op schaal: Event Hubs Capture]

### <a name="use-apache-spark"></a>Apache Spark gebruiken

[Apache Spark][Apache Spark] is een geïntegreerde analyse-engine voor grootschalige gegevens verwerking. Het ondersteunt verschillende talen, waaronder SQL, en kan eenvoudig toegang krijgen tot Azure Blob-opslag. Er zijn twee opties om Apache Spark in azure uit te voeren en beide bieden eenvoudige toegang tot Azure Blob-opslag:

- [HDInsight: adres bestanden in azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob-opslag][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Avro-Hulpprogram Ma's gebruiken

[Avro-Hulpprogram ma's][Avro Tools] zijn beschikbaar als jar-pakket. Nadat u het jar-bestand hebt gedownload, kunt u het schema van een specifiek Avro-bestand weer geven door de volgende opdracht uit te voeren:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Deze opdracht wordt geretourneerd

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

U kunt ook de Avro-hulpprogramma's gebruiken voor het bestand converteren naar JSON-indeling en andere bewerkingen uitvoeren.

Als u meer geavanceerde verwerken, downloaden en installeren van Avro naar keuze van platform. Op het moment van schrijven zijn er implementaties beschikbaar voor C, C++, C\#, Java, NodeJS, perl, PHP, python en Ruby.

Apache Avro is volledig aan de slag met hand leidingen voor [Java][Java] en [python][Python]. U kunt ook het artikel [aan de slag met Event hubs vastleggen](event-hubs-capture-python.md) lezen.

## <a name="how-event-hubs-capture-is-charged"></a>Hoe Event Hubs Capture wordt in rekening gebracht

Op dezelfde manier naar Event Hubs Capture is gebruik in eenheden gegevensdoorvoer: als een uurtarief. De kosten is rechtstreeks in verhouding met het aantal doorvoereenheden hebt aangeschaft voor de naamruimte. Als doorvoereenheden worden verhoogd of verlaagd, wordt Event Hubs Capture meters verhogen en verlagen om overeenkomende prestaties te bieden. De meters die zich voordoen in combinatie. Zie [Event hubs prijzen](https://azure.microsoft.com/pricing/details/event-hubs/)voor prijs informatie. 

Houd er rekening mee dat bij het vastleggen geen quotum voor uitgaand verkeer wordt verbruikt. 

## <a name="integration-with-event-grid"></a>Integratie met Event Grid 

U kunt een Azure Event Grid-abonnement maken met een Event Hubs-naamruimte als de bron. In de volgende zelf studie ziet u hoe u een Event Grid-abonnement met een Event Hub als een bron en een Azure Functions-app als een sink maakt, waarbij [vastgelegde Event hubs gegevens worden verwerkt en gemigreerd naar een SQL data warehouse met behulp van Event grid en Azure functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Volgende stappen
Event Hubs Capture is de eenvoudigste manier om gegevens in Azure. Met Azure Data Lake, Azure Data Factory en Azure HDInsight, kunt u batchverwerking uitvoeren en andere analyses met vertrouwde hulpprogramma's en platforms van uw keuze, op elke schaal u nodig hebt.

Meer informatie over het inschakelen van deze functie met behulp van de Azure Portal en Azure Resource Manager sjabloon:

- [Azure Portal gebruiken om Event Hubs Capture in te schakelen](event-hubs-capture-enable-through-portal.md)
- [Een Azure Resource Manager sjabloon gebruiken om Event Hubs vastleggen in te scha kelen](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming op schaal: Event Hubs Capture]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
