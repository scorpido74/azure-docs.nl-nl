---
title: Wat is Delta Lake
description: Overzicht van Delta Lake en hoe het werkt als onderdeel van Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429198"
---
# <a name="what-is-delta-lake"></a>Wat is Delta Lake?

Azure Synapse Analytics is compatibel met Linux Foundation Delta Lake. Delta Lake is een open-source opslaglaag die ACID-transacties (atomiciteit, consistentie, isolatie en duurzaamheid) naar Apache Spark en big data-workloads brengt.

## <a name="key-features"></a>Belangrijke functies

| Functie | Beschrijving |
| --- | --- |
| **ZUURtransacties** | Gegevensmeren worden meestal bevolkt via meerdere processen en pijplijnen, waarvan sommige gelijktijdig gegevens schrijven met reads. Voorafgaand aan Delta Lake en de toevoeging van transacties, moesten data engineers een handmatig foutgevoelig proces doorlopen om de integriteit van gegevens te waarborgen. Delta Lake brengt bekende ACID-transacties naar datalakes. Het biedt serializability, het sterkste niveau van isolatie niveau. Meer informatie bij [Diving into Delta Lake: Unpacking the Transaction Log](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Schaalbare verwerking met metagegevens** | In big data kunnen zelfs de metadata zelf "big data" zijn. Delta Lake behandelt metadata net als gegevens en maakt gebruik van de gedistribueerde verwerkingskracht van Spark om al zijn metadata te verwerken. Als gevolg hiervan kan Delta Lake op hun gemak tafels op petabyte-schaal verwerken met miljarden partities en bestanden. |
| **Tijdreizen (versiebeheer van gegevens)** | De mogelijkheid om een wijziging ongedaan te maken of terug te gaan naar een vorige versie is een van de belangrijkste kenmerken van transacties. Delta Lake biedt momentopnamen van gegevens waarmee u terugkeren naar eerdere versies van gegevens voor audits, terugdraaiingen of om experimenten te reproduceren. Meer informatie [over de introductie van Delta Lake Time Travel voor grootschalige datalakes.](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html) |
| **Open indeling** | Apache Parquet is de basislijnindeling voor Delta Lake, zodat u gebruik maken van de efficiënte compressie- en coderingsschema's die inheems zijn in het formaat. |
| **Unified Batch en Streaming Bron en Sink** | Een tabel in Delta Lake is zowel een batchtabel, als een streamingbron en gootsteen. Streaming gegevens inslikken, batch historische backfill, en interactieve query's allemaal gewoon werken uit de doos. |
| **Schemahandhaving** | Schemahandhaving helpt ervoor te zorgen dat de gegevenstypen correct zijn en dat vereiste kolommen aanwezig zijn, waardoor slechte gegevens geen gegevensinconsistentie kunnen veroorzaken. Zie [Duiken in Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) voor meer informatie |
| **Schema-evolutie** | Met Delta Lake u wijzigingen aanbrengen in een tabelschema dat automatisch kan worden toegepast, zonder dat u migratie DDL hoeft te schrijven. Zie [Duiken in Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) voor meer informatie |
| **Controlegeschiedenis** | Delta Lake transactielogboek registreert details over elke wijziging in gegevens die een volledig controlespoor van de wijzigingen bieden. |
| **Updates en verwijderingen** | Delta Lake ondersteunt Scala / Java / Python en SQL API's voor een verscheidenheid aan functionaliteit. Ondersteuning voor bewerkingen voor samenvoegen, bijwerken en verwijderen helpt u om aan de nalevingsvereisten te voldoen. Zie [De Delta Lake 0.4.0-release aankondigen](https://delta.io/news/delta-lake-0-4-0-released/) en [Eenvoudige, betrouwbare upserts en deletes op Delta Lake-tabellen met Python API's](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), die codefragmenten bevatten voor het samenvoegen, bijwerken en verwijderen van DML-opdrachten. |
| **100% compatibel met Apache Spark API** | Ontwikkelaars kunnen Delta Lake gebruiken met hun bestaande gegevenspijplijnen met minimale wijzigingen omdat het volledig compatibel is met bestaande Spark-implementaties. |

Zie de [documentatiepagina van Delta Lake](https://docs.delta.io/latest/delta-intro.html) voor volledige documentatie

Zie [Delta Lake Project](https://lfprojects.org)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [.NET voor Apache Spark-documentatie](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
