---
title: Trans formatie sinken bij toewijzing van gegevens stroom
description: Meer informatie over het configureren van een Sink-trans formatie in het toewijzen van gegevens stroom.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 2c57ddd88046044cccd13b0ade23144cd5649455
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433318"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trans formatie sinken bij toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nadat u uw gegevens hebt getransformeerd, kunt u de gegevens in een doel-gegevensset opvangen. Elke gegevens stroom vereist ten minste één sink-trans formatie, maar u kunt indien nodig naar zoveel sinks schrijven om uw transformatie stroom te volt ooien. Als u naar extra sinks wilt schrijven, maakt u nieuwe streams via nieuwe vertakkingen en voorwaardelijke splitsingen.

Elke Sink-trans formatie is aan precies één Data Factory-gegevensset gekoppeld. De gegevensset definieert de vorm en locatie van de gegevens waarnaar u wilt schrijven.

## <a name="inline-datasets"></a>Inline gegevens sets

Wanneer u een Sink-trans formatie maakt, kiest u of uw Sink-informatie is gedefinieerd binnen een DataSet-object of binnen de Sink-trans formatie. De meeste indelingen zijn alleen beschikbaar in een van beide. Raadpleeg het juiste connector document voor meer informatie over het gebruik van een specifieke connector.

Wanneer een indeling wordt ondersteund voor zowel inline als een object DataSet, zijn er voor delen van beide. DataSet-objecten zijn herbruikbare entiteiten die kunnen worden gebruikt in andere gegevens stromen en activiteiten zoals kopiëren. Dit is met name handig wanneer u een geharder schema gebruikt. Gegevens sets zijn niet gebaseerd op Spark en af en toe moet u mogelijk bepaalde instellingen of schema projectie in de Sink-trans formatie overschrijven.

Inline gegevens sets worden aanbevolen bij het gebruik van flexibele schema's, eenmalige Sink-instanties of sinks met para meters. Als uw Sink met hoge para meters is gefilterd, kunt u in de regel gegevens sets geen dummy-object maken. Inline gegevens sets zijn gebaseerd op Spark en hun eigenschappen zijn standaard voor de gegevens stroom.

Als u een inline gegevensset wilt gebruiken, selecteert u de gewenste indeling in de selector voor het **type Sink** . In plaats van een Sink-gegevensset te selecteren, selecteert u de gekoppelde service waarmee u verbinding wilt maken.

![Inline gegevensset](media/data-flow/inline-selector.png "Inline gegevensset")

### <a name="supported-inline-dataset-formats"></a>Ondersteunde indelingen voor inline-gegevensset

Momenteel is de enige beschik bare indeling voor de inline-gegevensset het [common data model](format-common-data-model.md#sink-properties) dat is gelezen van [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Ondersteunde Sink-connectors in gegevens stroom toewijzen

Momenteel kunnen de volgende gegevens sets worden gebruikt in een Sink-trans formatie:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

De instellingen die specifiek zijn voor deze connectors bevinden zich op het tabblad **instellingen** . informatie over deze instellingen vindt u in de documentatie van de connector. 

Azure Data Factory heeft toegang tot meer dan [90 systeemeigen connectors](connector-overview.md). Als u gegevens naar deze andere Connect oren uit uw gegevens stroom wilt schrijven, gebruikt u de Kopieer activiteit om die gegevens uit een van de ondersteunde tijdelijke gebieden te laden nadat de gegevens stroom is voltooid.

## <a name="sink-settings"></a>Sink-instellingen

Nadat u een Sink hebt toegevoegd, configureert u via het tabblad **sink** . Hier kunt u de gegevensset voor uw sinks selecteren of maken. Hieronder ziet u een video met een aantal verschillende Sink-opties voor door tekst gescheiden bestands typen:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Sink-instellingen](media/data-flow/sink-settings.png "Sink-instellingen")

**Schema-drift:** [schema-drift](concepts-data-flow-schema-drift.md) Data Factory is de mogelijkheid van een systeem eigen flexibele schema's in uw gegevens stromen zonder dat er expliciet kolom wijzigingen hoeven te worden gedefinieerd. Schakel **schema-drift toestaan** in voor het schrijven van aanvullende kolommen boven op wat is gedefinieerd in het sink data-schema.

**Schema valideren:** Als validate schema is geselecteerd, mislukt de gegevens stroom als een van de kolommen van het binnenkomende bron schema niet wordt gevonden in de bron projectie of als de gegevens typen niet overeenkomen. Gebruik deze instelling om af te dwingen dat de bron gegevens voldoen aan het contract van uw gedefinieerde projectie. Het is zeer nuttig in database bron scenario's om te signaleren dat kolom namen of typen zijn gewijzigd.

## <a name="field-mapping"></a>Veldtoewijzing

Net als bij een trans formatie selecteren kunt u op het tabblad **toewijzing** van de Sink bepalen welke binnenkomende kolommen worden geschreven. Standaard worden alle invoer kolommen, inclusief gedrijfde kolommen, toegewezen. Dit wordt **automatische toewijzing**genoemd.

Wanneer u automatische toewijzing uitschakelt, hebt u de mogelijkheid om vaste toewijzingen op basis van een kolom of op basis van een regel toe te voegen. Met op regels gebaseerde toewijzingen kunt u expressies met patroon overeenkomsten schrijven, terwijl vaste toewijzing logische en fysieke kolom namen toewijst. Zie voor meer informatie over op regels gebaseerde toewijzing [kolom patronen in gegevens stroom toewijzen](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Aangepaste Sink-ordening

Standaard worden gegevens in een niet-deterministische volg orde naar meerdere sinks geschreven. De uitvoerings engine schrijft gegevens parallel als de transformatie logica is voltooid en de Sink-bestelling kan elke uitvoering variëren. Als u een exacte Sink-bestelling wilt opgeven, schakelt u **aangepaste Sink-ordening** in op het tabblad Algemeen van de gegevens stroom. Als deze functie is ingeschakeld, worden de sinks opeenvolgend geschreven in een oplopende volg orde.

![Aangepaste Sink-ordening](media/data-flow/custom-sink-ordering.png "Aangepaste Sink-ordening")

## <a name="data-preview-in-sink"></a>Voor beeld van gegevens in Sink

Wanneer u een voor beeld van een gegevens ophaalt op een cluster voor fout opsporing, worden er geen gegevens naar uw Sink geschreven. Een moment opname van de gegevens die worden weer gegeven, maar er wordt niets naar uw bestemming geschreven. Als u het schrijven van gegevens naar uw Sink wilt testen, voert u een pijplijn fout opsporing uit vanaf het pijp lijn papier.

## <a name="next-steps"></a>Volgende stappen
Nu u de gegevens stroom hebt gemaakt, voegt u een [gegevens stroom activiteit toe aan uw pijp lijn](concepts-data-flow-overview.md).
