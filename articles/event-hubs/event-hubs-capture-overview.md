---
title: Streaminggebeurtenissen vastleggen - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de capture-functie waarmee u gebeurtenissen vastleggen die worden gestreamd via Azure Event Hubs.
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
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265010"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Gebeurtenissen vastleggen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage
Met Azure Event Hubs u de streaminggegevens automatisch vastleggen in Gebeurtenishubs in een [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of [Azure Data Lake Storage Gen 1- of Gen 2-account](https://azure.microsoft.com/services/data-lake-store/) naar keuze, met de extra flexibiliteit om een tijd- of grootteinterval op te geven. Het instellen van Capture is snel, er zijn geen administratieve kosten om het uit te voeren en het wordt automatisch geschaald met [overslageenheden](event-hubs-scalability.md#throughput-units)van Event Hubs. Event Hubs Capture is de eenvoudigste manier om streaminggegevens in Azure te laden en stelt u in staat om u te concentreren op gegevensverwerking in plaats van op het vastleggen van gegevens.

Event Hubs Capture stelt u in staat om real-time en batch-gebaseerde pijplijnen op dezelfde stream te verwerken. Dit betekent dat u oplossingen bouwen die in de loop van de tijd meegroeien met uw behoeften. Of u nu batchgebaseerde systemen bouwt met het oog op toekomstige real-time verwerking, of u wilt een efficiënt koud pad toevoegen aan een bestaande real-time oplossing, Event Hubs Capture maakt het werken met streaminggegevens eenvoudiger.


## <a name="how-event-hubs-capture-works"></a>Hoe Event Hubs Capture werkt

Event Hubs is een duurzame buffer voor het vasthouden van telemetrie, vergelijkbaar met een gedistribueerd logboek. De sleutel tot schalen in Event Hubs is het [verdeelde consumentenmodel.](event-hubs-scalability.md#partitions) Elke partitie is een onafhankelijk segment van gegevens en wordt onafhankelijk van elkaar verbruikt. Na verloop van tijd worden deze gegevens ouder, op basis van de configureerbare bewaarperiode. Als gevolg hiervan wordt een bepaalde gebeurtenishub nooit 'te vol'.

Met Event Hubs Capture u uw eigen Azure Blob-opslagaccount en -container opgeven, of Azure Data Lake Storage-account, die worden gebruikt om de vastgelegde gegevens op te slaan. Deze accounts kunnen zich in dezelfde regio bevinden als uw gebeurtenishub of in een andere regio, wat bijdraagt aan de flexibiliteit van de functie Gebeurtenishubs vastleggen.

Vastgelegde gegevens zijn geschreven in [Apache Avro-formaat:][Apache Avro] een compact, snel, binair formaat dat rijke gegevensstructuren voorziet van inline schema. Deze indeling wordt veel gebruikt in het Hadoop-ecosysteem, Stream Analytics en Azure Data Factory. Meer informatie over het werken met Avro is later in dit artikel beschikbaar.

### <a name="capture-windowing"></a>Venster vastleggen

Met Event Hubs Capture u een venster instellen om het vastleggen te regelen. Dit venster is een minimale grootte- en tijdconfiguratie met een beleid 'Eerste wint', wat betekent dat de eerste aangetroffen trigger een opnamebewerking veroorzaakt. Als u een opnamevenster van vijftien minuten en 100 MB hebt en 1 MB per seconde verzendt, wordt het venster voor de grootte vóór het tijdvenster geactiveerd. Elke partitie vangt onafhankelijk en schrijft een voltooide blokblob op het moment van vastleggen, vernoemd naar het moment waarop het opnameinterval werd aangetroffen. De conventie voor opslagnaamgeving is als volgt:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Houd er rekening mee dat de datumwaarden zijn opgevuld met nullen; een voorbeeld bestandsnaam kan zijn:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

In het geval dat uw Azure-opslagblob tijdelijk niet beschikbaar is, bewaart Event Hubs Capture uw gegevens voor de bewaarperiode voor gegevens die is geconfigureerd op uw gebeurtenishub en vult u de gegevens opnieuw op zodra uw opslagaccount weer beschikbaar is.

### <a name="scaling-to-throughput-units"></a>Schalen naar doorvoereenheden

Het verkeer van gebeurtenishubs wordt beheerd door [doorvoereenheden](event-hubs-scalability.md#throughput-units). Een enkele doorvoereenheid maakt 1 MB per seconde of 1000 gebeurtenissen per seconde van binnendringen en twee keer die hoeveelheid uitgang. Standaardgebeurtenishubs kunnen worden geconfigureerd met 1-20 doorvoereenheden en u meer kopen met een [ondersteuningsaanvraag voor][support request]quotaverhoging. Het gebruik buiten uw gekochte doorvoereenheden wordt beperkt. Event Hubs Capture kopieert gegevens rechtstreeks vanuit de interne opslag van Event Hubs, waardoor doorvoereenheden worden omzeild en uw uitgang wordt opgeslagen voor andere verwerkingslezers, zoals Stream Analytics of Spark.

Eenmaal geconfigureerd, wordt Event Hubs Capture automatisch uitgevoerd wanneer u uw eerste gebeurtenis verzendt en blijft worden uitgevoerd. Om het voor uw downstreamverwerking gemakkelijker te maken om te weten dat het proces werkt, schrijft Event Hubs lege bestanden wanneer er geen gegevens zijn. Dit proces biedt een voorspelbare cadans en markering die uw batchprocessors kan voeden.

## <a name="setting-up-event-hubs-capture"></a>Vastleggen van gebeurtenishubs instellen

U Vastleggen configureren op de tijd voor het maken van de gebeurtenishub met behulp van de [Azure-portal](https://portal.azure.com)of met Azure Resource Manager-sjablonen. Raadpleeg voor meer informatie de volgende artikelen:

- [Event Hubs Capture inschakelen met behulp van de Azure-portal](event-hubs-capture-enable-through-portal.md)
- [Een Event Hubs-naamruimte met een gebeurtenishub maken en Capture inschakelen met behulp van een Azure Resource Manager-sjabloon](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Het verkennen van de vastgelegde bestanden en het werken met Avro

Event Hubs Capture maakt bestanden in Avro-indeling, zoals opgegeven in het geconfigureerde tijdvenster. U deze bestanden weergeven in elk hulpprogramma, zoals [Azure Storage Explorer.][Azure Storage Explorer] U de bestanden lokaal downloaden om eraan te werken.

De bestanden die door Event Hubs Capture zijn geproduceerd, hebben het volgende Avro-schema:

![Avro-schema][3]

Een eenvoudige manier om Avro-bestanden te verkennen is door gebruik te maken van de [Avro Tools-pot][Avro Tools] van Apache. U [Apache Drill][Apache Drill] ook gebruiken voor een lichtgewicht SQL-gestuurde ervaring of [Apache Spark][Apache Spark] om complexe gedistribueerde verwerkingen uit te voeren op de ingenomen gegevens. 

### <a name="use-apache-drill"></a>Apache Drill gebruiken

[Apache Drill][Apache Drill] is een "open-source SQL-queryengine voor Big Data-verkenning" die gestructureerde en semi-gestructureerde gegevens kan opvragen, waar deze zich ook bevindt. De motor kan draaien als een standalone knooppunt of als een enorme cluster voor geweldige prestaties.

Er is een native ondersteuning voor Azure Blob-opslag beschikbaar, waardoor het eenvoudig is om gegevens op te vragen in een Avro-bestand, zoals beschreven in de documentatie:

[Apache Drill: Azure Blob Storage Plugin][Apache Drill: Azure Blob Storage Plugin]

Als u vastgelegde bestanden eenvoudig wilt opvragen, u een VM maken en uitvoeren met Apache Drill die via een container is ingeschakeld om toegang te krijgen tot Azure Blob-opslag:

https://github.com/yorek/apache-drill-azure-blob

Een volledig end-to-end sample is beschikbaar in de Streaming at Scale repository:

[Streaming op schaal: Event Hubs Capture]

### <a name="use-apache-spark"></a>Apache Spark gebruiken

[Apache Spark][Apache Spark] is een "unified analytics engine voor grootschalige gegevensverwerking." Het ondersteunt verschillende talen, waaronder SQL, en heeft eenvoudig toegang tot Azure Blob-opslag. Er zijn een paar opties om Apache Spark in Azure uit te voeren en elk biedt eenvoudige toegang tot Azure Blob-opslag:

- [HDInsight: Bestanden in Azure-opslag aanpakken][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob-opslag][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Avro-hulpprogramma's gebruiken

[Avro Tools][Avro Tools] zijn verkrijgbaar als potpakket. Nadat u het jar-bestand hebt gedownload, u het schema van een specifiek Avro-bestand bekijken door de volgende opdracht uit te voeren:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Deze opdracht keert terug

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

U Avro Tools ook gebruiken om het bestand om te zetten naar JSON-indeling en andere verwerkingen uit te voeren.

Om meer geavanceerde verwerking uit te voeren, downloaden en installeren Avro voor uw keuze van het platform. Op het moment van schrijven zijn er implementaties beschikbaar voor\#C, C++, C, Java, NodeJS, Perl, PHP, Python en Ruby.

Apache Avro heeft complete Getting Started gidsen voor [Java][Java] en [Python][Python]. U ook het artikel Aan de [slag met eventhubs vastleggen](event-hubs-capture-python.md) lezen.

## <a name="how-event-hubs-capture-is-charged"></a>Hoe eventhubs worden vastgelegd, wordt in rekening gebracht

Event Hubs Capture wordt gemeten op dezelfde manier als doorvoereenheden: als een uurlading. De kosten zijn rechtstreeks evenredig met het aantal doorvoereenheden dat voor de naamruimte is aangeschaft. Naarmate de doorvoereenheden worden verhoogd en verlaagd, nemen de meetmeters van Event Hubs toe om overeenkomende prestaties te bieden. De meters komen samen voor. Zie [Prijshubs voor](https://azure.microsoft.com/pricing/details/event-hubs/)prijsdetails . 

Houd er rekening mee dat Capture geen uitgaande quota verbruikt omdat het afzonderlijk wordt gefactureerd. 

## <a name="integration-with-event-grid"></a>Integratie met gebeurtenisraster 

U een Azure Event Grid-abonnement maken met een naamruimte voor gebeurtenishubs als bron. In de volgende zelfstudie ziet u hoe u een Event Grid-abonnement maakt met een gebeurtenishub als bron en een Azure Functions-app als een sink: [Verwerk en migreer captured Event Hubs-gegevens naar een SQL Data Warehouse met gebeurtenisraster en Azure-functies](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Volgende stappen
Event Hubs Capture is de eenvoudigste manier om gegevens in Azure te krijgen. Met Azure Data Lake, Azure Data Factory en Azure HDInsight u batchverwerking en andere analyses uitvoeren met behulp van vertrouwde hulpprogramma's en platforms van uw keuze, op elke schaal die u nodig hebt.

Meer informatie over het inschakelen van deze functie met behulp van de sjabloon Azure portal en Azure Resource Manager:

- [Azure Portal gebruiken om Event Hubs Capture in te schakelen](event-hubs-capture-enable-through-portal.md)
- [Een Azure Resource Manager-sjabloon gebruiken om gebeurtenishubs vast te leggen in te schakelen](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


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
[Streaming op schaal: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
