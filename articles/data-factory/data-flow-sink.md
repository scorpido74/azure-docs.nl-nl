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
ms.date: 12/12/2019
ms.openlocfilehash: 3b631c068d1a444691345e054219208c4c8b0b8c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020043"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trans formatie sinken bij toewijzing van gegevens stroom

Nadat u uw gegevens hebt getransformeerd, kunt u de gegevens in een doel-gegevensset opvangen. Elke gegevens stroom vereist ten minste één sink-trans formatie, maar u kunt indien nodig naar zoveel sinks schrijven om uw transformatie stroom te volt ooien. Als u naar extra sinks wilt schrijven, maakt u nieuwe streams via nieuwe vertakkingen en voorwaardelijke splitsingen.

Elke Sink-trans formatie is aan precies één Data Factory-gegevensset gekoppeld. De gegevensset definieert de vorm en locatie van de gegevens waarnaar u wilt schrijven.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Ondersteunde Sink-connectors in gegevens stroom toewijzen

Momenteel kunnen de volgende gegevens sets worden gebruikt in een Sink-trans formatie:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

De instellingen die specifiek zijn voor deze connectors bevinden zich op het tabblad **instellingen** . informatie over deze instellingen vindt u in de documentatie van de connector. 

Azure Data Factory heeft toegang tot meer dan [90 systeem eigen connectors](connector-overview.md). Als u gegevens wilt schrijven naar deze andere bronnen uit uw gegevens stroom, gebruikt u de Kopieer activiteit om die gegevens te laden uit een van de ondersteunde tijdelijke gebieden na voltooiing van de gegevens stroom.

## <a name="sink-settings"></a>Sink-instellingen

Nadat u een Sink hebt toegevoegd, configureert u via het tabblad **sink** . Hier kunt u de gegevensset voor uw sinks selecteren of maken 

![Sink-instellingen](media/data-flow/sink-settings.png "Sink-instellingen")

**Schema-drift:** [schema-drift](concepts-data-flow-schema-drift.md) Data Factory is de mogelijkheid van een systeem eigen flexibele schema's in uw gegevens stromen zonder dat er expliciet kolom wijzigingen hoeven te worden gedefinieerd. Schakel **schema-drift toestaan** in voor het schrijven van aanvullende kolommen boven op wat is gedefinieerd in het sink data-schema.

**Schema valideren:** Als validate schema is geselecteerd, mislukt de gegevens stroom als een van de kolommen in het gedefinieerde schema van de gegevensset niet is gevonden.

## <a name="field-mapping"></a>Veld toewijzing

Net als bij een trans formatie selecteren kunt u op het tabblad **toewijzing** van de Sink bepalen welke binnenkomende kolommen worden geschreven. Standaard worden alle invoer kolommen, inclusief gedrijfde kolommen, toegewezen. Dit wordt **automatische toewijzing**genoemd.

Wanneer u automatische toewijzing uitschakelt, hebt u de mogelijkheid om vaste toewijzingen op basis van een kolom of op basis van een regel toe te voegen. Met op regels gebaseerde toewijzingen kunt u expressies met patroon overeenkomsten schrijven, terwijl vaste toewijzing logische en fysieke kolom namen toewijst. Zie voor meer informatie over op regels gebaseerde toewijzing [kolom patronen in gegevens stroom toewijzen](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Aangepaste Sink-ordening

Standaard worden gegevens in een niet-deterministische volg orde naar meerdere sinks geschreven. De uitvoerings engine schrijft gegevens parallel als de transformatie logica is voltooid en de Sink-bestelling kan elke uitvoering variëren. Als u een exacte Sink-bestelling wilt opgeven, schakelt u **aangepaste Sink-ordening** in op het tabblad Algemeen van de gegevens stroom. Als deze functie is ingeschakeld, worden de sinks opeenvolgend geschreven in een oplopende volg orde.

![Aangepaste Sink-ordening](media/data-flow/custom-sink-ordering.png "Aangepaste Sink-ordening")

## <a name="data-preview-in-sink"></a>Voor beeld van gegevens in Sink

Wanneer u een voor beeld van een gegevens ophaalt op een cluster voor fout opsporing, worden er geen gegevens naar uw Sink geschreven. Een moment opname van de gegevens die worden weer gegeven, maar er wordt niets naar uw bestemming geschreven. Als u het schrijven van gegevens naar uw Sink wilt testen, voert u een pijplijn fout opsporing uit vanaf het pijp lijn papier.

## <a name="next-steps"></a>Volgende stappen
Nu u de gegevens stroom hebt gemaakt, voegt u een [gegevens stroom activiteit toe aan uw pijp lijn](concepts-data-flow-overview.md).
