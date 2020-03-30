---
title: Gegevensopname in Azure Data Explorer
description: Meer informatie over de verschillende manieren waarop u gegevens innemen (laden) in Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246394"
---
# <a name="azure-data-explorer-data-ingestion"></a>Gegevensopname in Azure Data Explorer

Gegevensopname is het proces dat wordt gebruikt om gegevensrecords uit een of meer bronnen te laden om een tabel in Azure Data Explorer te maken of bij te werken. Eenmaal ingenomen, worden de gegevens beschikbaar voor query. Het onderstaande diagram toont de end-to-end stroom voor het werken in Azure Data Explorer, inclusief gegevensopname.

![Gegevensstroom](media/ingest-data-overview/data-flow.png)

De azure data explorer-gegevensbeheerservice, die verantwoordelijk is voor het innemen van gegevens, biedt de volgende functionaliteit:

1. **Gegevens ophalen:** gegevens ophalen uit externe bronnen (gebeurtenishubs) of innameaanvragen uit een Azure-wachtrij lezen.

1. **Batching**: Batchgegevens die naar dezelfde database en tabel stromen om de innamedoorvoer te optimaliseren.

1. **Validatie**: Preliminaire validatie en opmaakconversie indien nodig.

1. **Gegevensmanipulatie**: Overeenkomend schema, organiseren, indexeren, coderen en comprimeren van de gegevens.

1. **Persistentiepunt in de innamestroom**: Beheer de innamebelasting op de motor en behandel opnieuw pogingen bij tijdelijke storingen.

1. **De gegevens inname vastleggen**: Maakt de gegevens beschikbaar voor query.

## <a name="ingestion-methods"></a>Innamemethoden

Azure Data Explorer ondersteunt verschillende opnamemethoden, elk met zijn eigen doelscenario's, voordelen en nadelen. Azure Data Explorer biedt pijplijnen en connectors voor algemene services, programmatische opname met Behulp van SDK's en directe toegang tot de engine voor verkenningsdoeleinden.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Inname met behulp van pijplijnen, connectoren en plug-ins

Azure Data Explorer ondersteunt momenteel:

* Gebeurtenisrasterpijplijn, die kan worden beheerd met de beheerwizard in de Azure-portal. Zie [Azure Blobs opnemen in Azure Data Explorer](ingest-data-event-grid.md)voor meer informatie.

* Gebeurtenishubpijplijn, die kan worden beheerd met de beheerwizard in de Azure-portal. Zie [Gegevens opnemen van gebeurtenishub in Azure Data Explorer](ingest-data-event-hub.md)voor meer informatie.

* Logstash-plug-in, zie [Gegevens opnemen van Logstash naar Azure Data Explorer](ingest-data-logstash.md).

* Kafka-connector, zie [Inname van gegevens van Kafka in Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Inname met behulp van integratieservices

* Azure Data Factory (ADF), een volledig beheerde service voor gegevensintegratie voor analytische workloads in Azure, om gegevens van en naar Azure Data Explorer te kopiëren met behulp van [ondersteunde gegevensopslag en -indelingen.](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Zie [Gegevens uit Azure Data Factory kopiëren naar Azure Data Explorer](/azure/data-explorer/data-factory-load-data)voor meer informatie.

### <a name="programmatic-ingestion"></a>Programmatische inname

Azure Data Explorer biedt SDK's die kunnen worden gebruikt voor het innemen van query's en gegevens. Programmatische inname is geoptimaliseerd voor het verlagen van de innamekosten (COGs), door opslagtransacties tijdens en na het innameproces te minimaliseren.

**Beschikbare SDK's en open-source projecten:**

Kusto biedt client SDK die kan worden gebruikt om gegevens in te nemen en op te vragen met:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST-API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programmatische innametechnieken:**

* Gegevens opnemen via de Azure Data Explorer-gegevensbeheerservice (hoge doorvoer en betrouwbare opname):

    [**Batch-opname**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (geleverd door SDK): de client uploadt de gegevens naar Azure Blob-opslag (aangewezen door de Azure Data Explorer-gegevensbeheerservice) en plaatst een melding naar een Azure Queue. Batch inname is de aanbevolen techniek voor high-volume, betrouwbare en goedkope gegevens inname.

* Gegevens rechtstreeks opnemen in de Azure Data Explorer-engine (het meest geschikt voor verkenning en prototypen):

  * **Inline ingestion**: besturingsopdracht (.inline) met in-bandgegevens is bedoeld voor ad hoc testdoeleinden.

  * **Inname van query:** de opdracht besturingselementen (.set, .set-or-append, .set-or-replace) die verwijst naar queryresultaten, wordt gebruikt voor het genereren van rapporten of kleine tijdelijke tabellen.

  * **Inname vanuit opslag:** besturingsopdracht (.innemen) met gegevens die extern zijn opgeslagen (bijvoorbeeld Azure Blob Storage) maakt een efficiënte bulkopname van gegevens mogelijk.

**Latentie van verschillende methoden:**

| Methode | Latentie |
| --- | --- |
| **Inline inname** | Onmiddellijke |
| **Inname van query** | Querytijd + verwerkingstijd |
| **Inname uit opslag** | Downloadtijd + verwerkingstijd |
| **Inname in de wachtrij** | Batchtijd + verwerkingstijd |
| |

De verwerkingstijd is afhankelijk van de gegevensgrootte, minder dan een paar seconden. Batchtijd is standaard 5 minuten.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>De meest geschikte innamemethode kiezen

Voordat u begint met het innemen van gegevens, moet u zich de volgende vragen stellen.

* Waar bevinden mijn gegevens zich? 
* Wat is de gegevensindeling en kan deze worden gewijzigd? 
* Wat zijn de vereiste velden die moeten worden opgevraagd? 
* Wat is het verwachte datavolume en de snelheid? 
* Hoeveel gebeurtenistypen worden verwacht (weergegeven als het aantal tabellen)? 
* Hoe vaak wordt verwacht dat het gebeurtenisschema wordt gewijzigd? 
* Hoeveel knooppunten genereren de gegevens? 
* Wat is het bron-besturingssysteem? 
* Wat zijn de latentievereisten? 
* Kan een van de bestaande beheerde innamepijplijnen worden gebruikt? 

Voor organisaties met een bestaande infrastructuur die zijn gebaseerd op een berichtenservice zoals Event Hub en IoT Hub, is het gebruik van een connector waarschijnlijk de meest geschikte oplossing. Inname in de wachtrij is geschikt voor grote gegevensvolumes.

## <a name="supported-data-formats"></a>Ondersteunde gegevensindelingen

Voor alle andere innamemethoden dan inname uit query, maakt u de gegevens op zodat Azure Data Explorer deze kan ontleeden. 
* De ondersteunde dataformaten zijn: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (line-separated, multi-line), Avro, Orc en Parquet. 
* Ondersteunt ZIP- en GZIP-compressie.

> [!NOTE]
> Wanneer gegevens worden ingenomen, worden gegevenstypen afgeleid op basis van de doeltabelkolommen. Als een record onvolledig is of als een veld niet kan worden ontleed als het vereiste gegevenstype, worden de bijbehorende tabelkolommen gevuld met null-waarden.

## <a name="ingestion-recommendations-and-limitations"></a>Innameaanbevelingen en beperkingen

* Het effectieve bewaarbeleid van ingenomen gegevens is afgeleid van het bewaarbeleid van de database. Zie [bewaarbeleid](/azure/kusto/concepts/retentionpolicy) voor meer informatie. Voor het innemen van gegevens zijn **tabelinname of** **database-innamemachtigingen** vereist.
* Inname ondersteunt een maximale bestandsgrootte van 5 GB. De aanbeveling is om bestanden tussen 100 MB en 1 GB in te nemen.

## <a name="schema-mapping"></a>Schematoewijzing

Met schematoewijzing u brongegevensvelden binden aan doeltabelkolommen.

* [CSV Mapping](/azure/kusto/management/mappings?branch=master#csv-mapping) (optioneel) werkt met alle ordinale formaten. Het kan worden uitgevoerd met behulp van de inname opdracht parameter of [vooraf gemaakt op de tabel](/azure/kusto/management/create-ingestion-mapping-command) en verwezen uit de inname command parameter.
* [JSON Mapping](/azure/kusto/management/mappings?branch=master#json-mapping) (verplicht) en [Avro mapping](/azure/kusto/management/mappings?branch=master#avro-mapping) (verplicht) kan worden uitgevoerd met behulp van de inname opdrachtparameter. Ze kunnen ook vooraf worden [gemaakt op de tabel](/azure/kusto/management/create-ingestion-mapping-command) en waarnaar wordt verwezen vanuit de innameopdrachtparameter.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [gegevens uit Event Hub opnemen in Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met eventgrid-abonnement in Azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Gegevens uit Kafka opnemen in Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met behulp van de Python-bibliotheek voor Azure Data Explorer](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met behulp van de Node-bibliotheek voor Azure Data Explorer](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met de Azure Data Explorer .NET Standard SDK (Preview)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Gegevens uit Logstash opnemen in Azure Data Explorer](ingest-data-logstash.md)
