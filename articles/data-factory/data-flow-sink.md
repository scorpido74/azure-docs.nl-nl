---
title: Sinktransformatie in kaartgegevensstroom
description: Meer informatie over het configureren van een sinktransformatie in het toewijzen van gegevensstromen.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: a0b9d424c1995fba075c05ffe5058e297d764775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531257"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Sinktransformatie in kaartgegevensstroom

Nadat u uw gegevens hebt getransformeerd, u de gegevens in een doelgegevensset laten zinken. Elke gegevensstroom vereist ten minste één sinktransformatie, maar u naar zoveel putten schrijven als nodig is om uw transformatiestroom te voltooien. Als u naar extra putten wilt schrijven, maakt u nieuwe streams via nieuwe branches en voorwaardelijke splitsingen.

Elke sinktransformatie is gekoppeld aan precies één gegevensset gegevensfabriek van Data Factory. De gegevensset definieert de vorm en locatie van de gegevens waarnaar u wilt schrijven.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Ondersteunde sinkconnectors in kaartgegevensstroom

Momenteel kunnen de volgende gegevenssets worden gebruikt in een sinktransformatie:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parket)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parket)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parket)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

De specifieke instellingen voor deze connectors bevinden zich op het tabblad **Instellingen.** Informatie over deze instellingen bevindt zich in de connectordocumentatie. 

Azure Data Factory heeft toegang tot meer dan [90 native connectors.](connector-overview.md) Als u gegevens wilt schrijven naar die andere bronnen uit uw gegevensstroom, gebruikt u de activiteit kopiëren om die gegevens te laden uit een van de ondersteunde faseringsgebieden na voltooiing van uw gegevensstroom.

## <a name="sink-settings"></a>Sink-instellingen

Zodra u een gootsteen hebt toegevoegd, configureert u dit via het tabblad **Sink.** Hier u de gegevensset kiezen of maken waarnaar uw gootsteen schrijft 

![Sink-instellingen](media/data-flow/sink-settings.png "Sink-instellingen")

**Schemadrift:** [Schemadrift](concepts-data-flow-schema-drift.md) is de mogelijkheid van de gegevensfabriek om flexibele schema's in uw gegevensstromen native te verwerken zonder kolomwijzigingen expliciet te hoeven definiëren. **Schemadrift toestaan om** extra kolommen te schrijven bovenop wat is gedefinieerd in het schema voor sinkgegevens.

**Schema valideren:** Als het valideringsschema is geselecteerd, mislukt de gegevensstroom als een kolom van het binnenkomende bronschema niet wordt gevonden in de bronprojectie of als de gegevenstypen niet overeenkomen. Gebruik deze instelling om af te dwingen of de brongegevens voldoen aan het contract van uw gedefinieerde projectie. Het is zeer nuttig in databasebronscenario's om aan te geven dat kolomnamen of -typen zijn gewijzigd.

## <a name="field-mapping"></a>Veldtoewijzing

Net als bij een select-transformatie u in het tabblad **Toewijzing** van de gootsteen bepalen welke binnenkomende kolommen worden geschreven. Standaard worden alle invoerkolommen, inclusief zwevende kolommen, toegewezen. Dit staat bekend als **Auto-mapping**.

Wanneer u automatisch toewijzingen uitschakelt, u vaste toewijzingen op basis van kolommen of op regels gebaseerde toewijzingen toevoegen. Met regelgebaseerde toewijzingen u expressies schrijven met patroonmatching, terwijl vaste toewijzing logische en fysieke kolomnamen in kaart brengt. Zie [kolompatronen in de toewijzingsgegevensstroom voor](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)meer informatie over regelgebaseerde toewijzing.

## <a name="custom-sink-ordering"></a>Aangepaste gootsteen bestellen

Standaard worden gegevens naar meerdere sinks in een niet-deterministische volgorde geschreven. De uitvoeringsengine schrijft gegevens parallel naarmate de transformatielogica is voltooid en de gootsteenvolgorde elke run kan variëren. Als u het bestellen van een nauwkeurige gootsteen wilt opgeven en nauwkeurig wilt bestellen, schakelt u **Het bestellen van aangepaste gootsteen** in het algemene tabblad van de gegevensstroom in. Wanneer ingeschakeld, zal wastafels worden geschreven sequentieel in toenemende volgorde.

![Aangepaste gootsteen bestellen](media/data-flow/custom-sink-ordering.png "Aangepaste gootsteen bestellen")

## <a name="data-preview-in-sink"></a>Gegevensvoorbeeld in gootsteen

Wanneer u een voorbeeld van gegevens op een foutopsporingscluster ophaalt, worden er geen gegevens naar uw gootsteen geschreven. Een momentopname van hoe de gegevens eruit zien, wordt geretourneerd, maar er wordt niets naar uw bestemming geschreven. Als u het schrijven van gegevens in uw gootsteen wilt testen, voert u een foutopsporingsprogramma voor pijplijnuit het pijplijncanvas uit.

## <a name="next-steps"></a>Volgende stappen
Nu u uw gegevensstroom hebt gemaakt, voegt u een activiteit gegevensstroom toe [aan uw pijplijn.](concepts-data-flow-overview.md)
