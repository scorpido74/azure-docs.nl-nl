---
title: Gegevens opname van Azure Data Explorer
description: Meer informatie over de verschillende manieren waarop u gegevens kunt opnemen (laden) in azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 35d3451327a0ce7bcaf567f93c48d532842b4f25
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285911"
---
# <a name="azure-data-explorer-data-ingestion"></a>Gegevens opname van Azure Data Explorer

Gegevens opname is het proces dat wordt gebruikt om gegevens records uit een of meer bronnen te laden om een tabel in azure Data Explorer te maken of bij te werken. Zodra de gegevens zijn opgenomen, worden deze beschikbaar voor query's. In het onderstaande diagram ziet u de end-to-end-stroom voor het werken in azure Data Explorer, inclusief gegevens opname.

![Gegevens stroom](media/ingest-data-overview/data-flow.png)

De Azure Data Explorer Data Management-service, die verantwoordelijk is voor de opname van gegevens, biedt de volgende functionaliteit:

1. **Gegevens pull**: gegevens ophalen uit externe bronnen (Event hubs) of opname aanvragen van een Azure-wachtrij lezen.

1. **Batch**verwerking: batch gegevens stromen naar dezelfde data base en tabel om de door Voer van de opname te optimaliseren.

1. **Validatie**: voorlopige validatie en indelings conversie, indien nodig.

1. **Gegevens manipulatie**: overeenkomend schema, ordenen, indexeren, code ring en compressie van de gegevens.

1. **Persistentie punt in de opname stroom**: Hiermee beheert u de opname belasting voor de engine en voert u nieuwe pogingen uit bij tijdelijke fouten.

1. **De gegevensopname vastleggen**: Hiermee maakt u de gegevens beschikbaar voor de query.

## <a name="ingestion-methods"></a>Opname methoden

Azure Data Explorer ondersteunt verschillende opname methoden, elk met een eigen doel scenario's, voor delen en nadelen. Azure Data Explorer biedt pijp lijnen en connectors aan algemene services, programmatische opname met behulp van Sdk's en directe toegang tot de engine voor onderzoek doeleinden.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Opname met behulp van pijp lijnen, connectors en invoeg toepassingen

Azure Data Explorer ondersteunt momenteel:

* Event Grid pijp lijn, die kan worden beheerd met behulp van de wizard beheer in de Azure Portal. Zie [Azure-blobs opnemen in azure Data Explorer](ingest-data-event-grid.md)voor meer informatie.

* Event hub-pijp lijn, die kan worden beheerd met behulp van de wizard beheer in de Azure Portal. Zie [gegevens opnemen van Event hub in Azure Data Explorer](ingest-data-event-hub.md)voor meer informatie.

* Logstash-invoeg toepassing, Zie [opname gegevens van Logstash naar Azure Data Explorer](ingest-data-logstash.md).

* Kafka-connector, Zie [opname gegevens van Kafka in Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Opname met behulp van integratie Services

* Azure Data Factory (ADF), een volledig beheerde gegevens integratie service voor analytische workloads in azure, om gegevens te kopiëren van en naar Azure Data Explorer met behulp van [ondersteunde gegevens archieven en-indelingen](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Zie [gegevens kopiëren van Azure Data Factory naar Azure Data Explorer](/azure/data-explorer/data-factory-load-data)voor meer informatie.

### <a name="programmatic-ingestion"></a>Programma opname

Azure Data Explorer biedt Sdk's die kunnen worden gebruikt voor query's en gegevens opname. Programmatische opname wordt geoptimaliseerd voor het verlagen van de kosten voor opname, door opslag transacties te minimaliseren tijdens en na het opname proces.

**Beschik bare sdk's en open-source projecten**:

Kusto biedt client-SDK die kan worden gebruikt voor het opnemen en opvragen van gegevens met:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET-SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node-SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Technieken voor opname op programmeer niveau**:

* Gegevens opnemen via de Azure Data Explorer Data Management-service (hoge door Voer en betrouw bare opname):

    [**Batch opname**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (door SDK): de-client uploadt de gegevens naar Azure Blob Storage (aangegeven door de Azure Data Explorer Data Management-service) en plaatst een melding in een Azure-wachtrij. Batch opname is de aanbevolen techniek voor het opnemen van hoge volumes, betrouw bare en goedkope gegevens opname.

* Gegevens rechtstreeks opnemen in de Azure Data Explorer-Engine (geschikt voor verkennen en prototypen):

  * **Inline opname**: besturings opdracht (. inline opnemen) met in-band-gegevens is bedoeld voor ad-hoc test doeleinden.

  * Opnemen **vanuit query**: besturings opdracht (. set,. set-of-Append,. set-of-replace) die verwijst naar query resultaten wordt gebruikt voor het genereren van rapporten of kleine tijdelijke tabellen.

  * Opnemen **uit opslag**: besturings opdracht (. opnemen in) met gegevens die extern zijn opgeslagen (bijvoorbeeld Azure Blob Storage), zodat gegevens efficiënt kunnen worden opgenomen.

**Latentie van verschillende methoden**:

| Methode | Latentie |
| --- | --- |
| **Inline opname** | Onmiddellijk |
| **Opnemen uit query** | Query tijd + verwerkings tijd |
| **Opnemen uit opslag** | Download tijd + verwerkings tijd |
| **Opname in wachtrij** | Batch verwerking en verwerkings tijd |
| |

De verwerkings tijd is afhankelijk van de gegevens grootte, kleiner dan een paar seconden. Batch-tijd wordt standaard ingesteld op 5 minuten.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>De meest geschikte opname methode kiezen

Voordat u begint met het opnemen van gegevens, moet u de volgende vragen stellen.

* Waar worden mijn gegevens opgeslagen? 
* Wat is de gegevens indeling en kan worden gewijzigd? 
* Wat zijn de vereiste velden waarvoor een query moet worden uitgevoerd? 
* Wat is de verwachte hoeveelheid gegevens en snelheid? 
* Hoeveel gebeurtenis typen worden er verwacht (weer gegeven als het aantal tabellen)? 
* Hoe vaak wordt het gebeurtenis schema naar verwachting gewijzigd? 
* Hoeveel knoop punten genereren de gegevens? 
* Wat is het bron besturingssysteem? 
* Wat zijn de latentie vereisten? 
* Kan een van de bestaande pijp lijnen voor beheerde opname worden gebruikt? 

Voor organisaties met een bestaande infra structuur die is gebaseerd op een berichten service zoals Event hub en IoT Hub, is het waarschijnlijk de meest geschikte oplossing. Opname in de wachtrij is geschikt voor grote gegevens volumes.

## <a name="supported-data-formats"></a>Ondersteunde gegevens indelingen

Voor alle opname methoden behalve opnemen vanuit query moet u de gegevens opmaken zodat deze door Azure Data Explorer kunnen worden geparseerd. De ondersteunde gegevens indelingen zijn:

* TXT, CSV, TSV, TSVE, PSV, SCSV, SOH
* JSON (lijn-gescheiden, meerdere regels), AVRO
* ZIP en GZIP 

> [!NOTE]
> Wanneer gegevens worden opgenomen, worden gegevens typen afgeleid op basis van de kolommen van de doel tabel. Als een record onvolledig is of als een veld niet kan worden geparseerd als het vereiste gegevens type, worden de bijbehorende tabel kolommen gevuld met Null-waarden.

## <a name="ingestion-recommendations-and-limitations"></a>Aanbevelingen en beperkingen van opname

* Het efficiënte Bewaar beleid van opgenomen gegevens wordt afgeleid van het Bewaar beleid van de data base. Zie het [Bewaar beleid](/azure/kusto/concepts/retentionpolicy) voor meer informatie. Het opnemen van gegevens vereist een **tabel opname** of de machtigingen voor de **Data Base-inslikken** .
* Opname ondersteunt een maximale bestands grootte van 5 GB. De aanbeveling is het opnemen van bestanden tussen 100 MB en 1 GB.

## <a name="schema-mapping"></a>Schema toewijzing

Schema toewijzing helpt bij het binden van bron gegevens velden aan doel tabel kolommen.

* [CSV-toewijzing](/azure/kusto/management/mappings?branch=master#csv-mapping) (optioneel) werkt met alle notaties op basis van een rang telwoord. Dit kan worden uitgevoerd met behulp van de opname opdracht parameter of [vooraf gemaakte in de tabel](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) en waarnaar wordt verwezen vanuit de opdracht parameter opnemen.
* [JSON-toewijzing](/azure/kusto/management/mappings?branch=master#json-mapping) (verplicht) en [Avro-toewijzing](/azure/kusto/management/mappings?branch=master#avro-mapping) (verplicht) kunnen worden uitgevoerd met behulp van de opdracht parameter opnemen. Ze kunnen ook [vooraf worden gemaakt in de tabel](/azure/kusto/management/tables#create-ingestion-mapping) en worden verwezen vanuit de opdracht parameter opnemen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens opnemen vanuit de Event hub in azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met Event Grid-abonnement in azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen van Kafka in azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met behulp van de Azure Data Explorer python-bibliotheek](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met behulp van de knoop punt bibliotheek van Azure Data Explorer](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen met behulp van de Azure Data Explorer .NET Standard SDK (preview)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Gegevens opnemen van Logstash naar Azure Data Explorer](ingest-data-logstash.md)
