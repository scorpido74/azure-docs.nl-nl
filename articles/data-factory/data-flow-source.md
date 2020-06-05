---
title: Bron transformatie in gegevens stroom toewijzen
description: Meer informatie over het instellen van een bron transformatie in het toewijzen van gegevens stroom.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 6da0c56e11b8531192ba77d8f0c27fa16eea5de2
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433299"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Bron transformatie in gegevens stroom toewijzen 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met een bron transformatie configureert u de gegevens bron voor de gegevens stroom. Bij het ontwerpen van gegevens stromen wordt de eerste stap altijd een bron transformatie configureren. Als u een bron wilt toevoegen, klikt u op het vak **bron toevoegen** in het canvas voor gegevens stromen.

Elke gegevens stroom vereist ten minste één bron transformatie, maar u kunt zoveel bronnen toevoegen als u nodig hebt om uw gegevens transformaties te volt ooien. U kunt deze bronnen samen voegen met een koppelings-, zoek-of een Union-trans formatie.

Elke bron transformatie is gekoppeld aan precies één Data Factory-gegevensset. De gegevensset definieert de vorm en locatie van de gegevens waarnaar u wilt schrijven of waaruit u wilt lezen. Als u een gegevensset op basis van een bestand gebruikt, kunt u Joker tekens en bestands lijsten in uw bron gebruiken om met meer dan één bestand tegelijk te werken.

## <a name="inline-datasets"></a>Inline gegevens sets

De eerste beslissing die u maakt bij het maken van een bron transformatie is of uw bron gegevens zijn gedefinieerd binnen een object dataset of binnen de bron transformatie. De meeste indelingen zijn alleen beschikbaar in een van beide. Raadpleeg het juiste connector document voor meer informatie over het gebruik van een specifieke connector.

Wanneer een indeling wordt ondersteund voor zowel inline als een object DataSet, zijn er voor delen van beide. DataSet-objecten zijn herbruikbare entiteiten die kunnen worden gebruikt in andere gegevens stromen en activiteiten zoals kopiëren. Dit is met name handig wanneer u een geharder schema gebruikt. Gegevens sets zijn niet gebaseerd op Spark en af en toe moet u mogelijk bepaalde instellingen of schema projectie in de bron transformatie overschrijven.

Inline gegevens sets worden aanbevolen bij het gebruik van flexibele schema's, eenmalige bron instanties of bronnen met para meters. Als uw bron zeer veel para meters bevat, kunt u met inline gegevens sets geen dummy-object maken. Inline gegevens sets zijn gebaseerd op Spark en hun eigenschappen zijn standaard voor de gegevens stroom.

Als u een inline gegevensset wilt gebruiken, selecteert u de gewenste indeling in de **bron type** kiezer. In plaats van een bron gegevensset te selecteren, selecteert u de gekoppelde service waarmee u verbinding wilt maken.

![Inline gegevensset](media/data-flow/inline-selector.png "Inline gegevensset")

### <a name="supported-inline-dataset-formats"></a>Ondersteunde indelingen voor inline-gegevensset

Momenteel is de enige beschik bare indeling voor de inline-gegevensset het [common data model](format-common-data-model.md#source-properties) dat is gelezen van [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-source-datasets-in-mapping-data-flow"></a>Ondersteunde bron gegevens sets in de toewijzing van gegevens stroom

Toewijzing van gegevens stroom volgt een extractie benadering, Load, Transform (ELT) en werkt met *faserings* gegevens sets die allemaal in azure zijn. Momenteel kunnen de volgende gegevens sets worden gebruikt in een bron transformatie:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, AVRO, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

De instellingen die specifiek zijn voor deze connectors bevinden zich op het tabblad **bron opties** . informatie over deze instellingen vindt u in de documentatie van de connector. 

Azure Data Factory heeft toegang tot meer dan [90 systeemeigen connectors](connector-overview.md). Als u gegevens uit deze andere bronnen in uw gegevens stroom wilt toevoegen, gebruikt u de Kopieer activiteit om die gegevens te laden in een van de ondersteunde staging-gebieden.

## <a name="source-settings"></a>Broninstellingen

Nadat u een bron hebt toegevoegd, configureert u via het tabblad **bron instellingen** . Hier kunt u de gegevensset voor uw bron punten kiezen of maken. U kunt ook schema-en bemonsterings opties voor uw gegevens selecteren.

![Tabblad Bron instellingen](media/data-flow/source1.png "Tabblad Bron instellingen")

**Verbinding testen:** Testen of de Spark-service van de gegevens stroom verbinding kan maken met de gekoppelde service die in de bron-gegevensset wordt gebruikt. De foutopsporingsmodus moet zijn ingeschakeld om deze functie in te scha kelen.

**Schema-drift:** [schema-drift](concepts-data-flow-schema-drift.md) Data Factory is de mogelijkheid van een systeem eigen flexibele schema's in uw gegevens stromen zonder dat er expliciet kolom wijzigingen hoeven te worden gedefinieerd.

* Schakel het selectie vakje **schema-drift toestaan** in als de bron kolommen vaak worden gewijzigd. Met deze instelling kunnen alle binnenkomende bron velden door de trans formaties worden getransporteerd naar de sink.

* Bij het kiezen van een **afleidende kolom Type** wordt de instructie Data Factory om gegevens typen te detecteren en te definiëren voor elke nieuwe kolom die wordt gedetecteerd. Als deze functie is uitgeschakeld, zijn alle gedrijfde kolommen van het type teken reeks.

**Schema valideren:** Als validatie schema is geselecteerd, kan de gegevens stroom niet worden uitgevoerd als de binnenkomende bron gegevens niet overeenkomen met het gedefinieerde schema van de gegevensset.

**Aantal regels overs Laan:** In het veld aantal regel items overs Laan wordt aangegeven hoeveel regels aan het begin van de gegevensset moeten worden genegeerd.

**Steek proeven:** Schakel steek proeven in om het aantal rijen van de bron te beperken. Gebruik deze instelling wanneer u gegevens uit uw bron test of van uw source gebruikt voor fout opsporing.

**Meerregelige rijen:** Selecteer meerregelige rijen als uw bron tekst bestand teken reeks waarden bevat die meerdere rijen bevatten, dat wil zeggen een nieuwe regel in een waarde. Deze instelling is alleen beschikbaar in DelimitedText-gegevens sets.

Als u wilt valideren of uw bron juist is geconfigureerd, schakelt u de foutopsporingsmodus in en haalt u een voor beeld van de gegevens op. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

> [!NOTE]
> Wanneer de foutopsporingsmodus is ingeschakeld, wordt de sampling-instelling in de bron tijdens de preview-versie overschreven door de configuratie van het aantal rijen in de instellingen voor fout opsporing.

## <a name="projection"></a>Projectie

Net als schema's in gegevens sets definieert de projectie in een bron de gegevens kolommen, typen en indelingen van de bron gegevens. Voor de meeste typen gegevensset, zoals SQL en Parquet, wordt de projectie van een bron vastgesteld op basis van het schema dat is gedefinieerd in een gegevensset. Als uw bron bestanden niet sterk worden getypt (bijvoorbeeld platte CSV-bestanden in plaats van Parquet-bestanden), kunt u de gegevens typen voor elk veld in de bron transformatie definiëren.

![Instellingen op het tabblad projectie](media/data-flow/source3.png "Projectie")

Als uw tekst bestand geen gedefinieerd schema heeft, selecteert u **gegevens type detecteren** zodat Data Factory de gegevens typen worden gesampled en afgeleid. Selecteer **standaard indeling instellen** op automatische detectie van de standaard gegevens indelingen.

Als u het **schema opnieuw instelt** , wordt de projectie ingesteld op wat is gedefinieerd in de gegevensset waarnaar wordt verwezen.

U kunt de kolom gegevens typen wijzigen in een trans formatie die is afgeleid van een dalende stroom kolom. Gebruik een SELECT trans formatie om de kolom namen te wijzigen.

### <a name="import-schema"></a>Schema importeren

Met de knop **schema importeren** op het tabblad **projectie** kunt u een actief debug-cluster gebruiken om een schema projectie te maken. Bij het importeren van het schema dat in elk bron type beschikbaar is, wordt de projectie die in de gegevensset is gedefinieerd, overschreven. Het object dataset wordt niet gewijzigd.

Dit is handig in gegevens sets zoals Avro en CosmosDB die ondersteuning bieden voor complexe gegevens structuren waarvoor geen schema definities in de gegevensset bestaan.

## <a name="optimize-the-source-transformation"></a>De bron transformatie optimaliseren

Op het tabblad **optimaliseren** voor de bron transformatie ziet u mogelijk een type **bron** partitie. Deze optie is alleen beschikbaar als uw bron Azure SQL Database is. Dit komt omdat Data Factory verbinding probeert te maken met het parallel uitvoeren van grote query's op uw SQL Database bron.

![Instellingen van de bron partitie](media/data-flow/sourcepart3.png "partitioneren")

U hoeft geen gegevens op uw SQL Database bron te partitioneren, maar partities zijn handig voor grote query's. U kunt de partitie baseren op een kolom of een query.

### <a name="use-a-column-to-partition-data"></a>Een kolom gebruiken om gegevens te partitioneren

Selecteer in de bron tabel een kolom waarop moet worden gepartitioneerd. Stel ook het aantal partities in.

### <a name="use-a-query-to-partition-data"></a>Een query gebruiken om gegevens te partitioneren

U kunt ervoor kiezen om de verbindingen te partitioneren op basis van een query. Voer de inhoud van een WHERE-predikaat in. Voer bijvoorbeeld jaar > 1980 in.

Zie het [tabblad optimaliseren](concepts-data-flow-overview.md#optimize)voor meer informatie over optimalisatie binnen de toewijzing van gegevens stromen.

## <a name="next-steps"></a>Volgende stappen

Beginnen met het bouwen van een [afgeleide kolom transformatie](data-flow-derived-column.md) en een [geselecteerde trans formatie](data-flow-select.md).
