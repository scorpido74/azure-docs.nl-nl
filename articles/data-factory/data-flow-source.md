---
title: Brontransformatie in kaartgegevensstroom
description: Meer informatie over het instellen van een brontransformatie in het toewijzen van gegevensstromen.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: b2f533e8bd9199025260aaca9cff587b13adce64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606308"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Brontransformatie in kaartgegevensstroom 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een brontransformatie configureert uw gegevensbron voor de gegevensstroom. Bij het ontwerpen van gegevensstromen wordt een brontransformatie altijd geconfigureerd bij het ontwerpen van gegevensstromen. Als u een bron wilt toevoegen, klikt u op het vak **Bron toevoegen** in het canvas voor gegevensstromen.

Elke gegevensstroom vereist ten minste één brontransformatie, maar u zoveel bronnen toevoegen als nodig is om uw gegevenstransformaties te voltooien. U deze bronnen samenvoegen met een join, lookup of een unietransformatie.

Elke brontransformatie is gekoppeld aan precies één gegevensset gegevensfabriek van Gegevensfabriek. De gegevensset definieert de vorm en locatie van de gegevens waarnaar u wilt schrijven of waaruit u wilt lezen. Als u een gegevensset op basis van bestanden gebruikt, u jokertekens en bestandslijsten in uw bron gebruiken om met meer dan één bestand tegelijk te werken.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Ondersteunde bronconnectors in kaartgegevensstroom

Mapping Data Flow volgt een extract-, load-, transform(ELT)-benadering en werkt met *faseringsgegevenssets* die allemaal in Azure zijn. Momenteel kunnen de volgende gegevenssets worden gebruikt in een brontransformatie:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parket)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parket)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parket)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

De specifieke instellingen voor deze connectors bevinden zich op het tabblad **Bronopties.** Informatie over deze instellingen bevindt zich in de connectordocumentatie. 

Azure Data Factory heeft toegang tot meer dan [90 native connectors.](connector-overview.md) Als u gegevens uit die andere bronnen in uw gegevensstroom wilt opnemen, gebruikt u de activiteit kopiëren om die gegevens in een van de ondersteunde faseringsgebieden te laden.

## <a name="source-settings"></a>Broninstellingen

Zodra u een bron hebt toegevoegd, configureert u dit via het tabblad **Broninstellingen.** Hier u de gegevensset kiezen of maken waarop uw bronpunten zijn gericht. U ook schema- en bemonsteringsopties voor uw gegevens selecteren.

![Tabblad Broninstellingen](media/data-flow/source1.png "Tabblad Broninstellingen")

**Testverbinding:** Test of de spark-service van de gegevensstroom al dan niet met succes verbinding kan maken met de gekoppelde service die wordt gebruikt in uw brongegevensset. De foutopsporingsmodus moet ingeschakeld zijn om deze functie in te schakelen.

**Schemadrift:** [Schemadrift](concepts-data-flow-schema-drift.md) is de mogelijkheid van de gegevensfabriek om flexibele schema's in uw gegevensstromen native te verwerken zonder kolomwijzigingen expliciet te hoeven definiëren.

* Schakel het **selectievakje Schemadrift toestaan** in als de bronkolommen vaak worden gewijzigd. Met deze instelling kunnen alle binnenkomende bronvelden door de transformaties naar de gootsteen stromen.

* Als u **Infer-kolomtypen kiest,** worden gegevensfabrieken geïnstrueerd om gegevenstypen voor elke nieuwe kolom te detecteren en te definiëren. Als deze functie is uitgeschakeld, zijn alle zwevende kolommen van het type tekenreeks.

**Schema valideren:** Als het validatieschema is geselecteerd, wordt de gegevensstroom niet uitgevoerd als de binnenkomende brongegevens niet overeenkomen met het gedefinieerde schema van de gegevensset.

**Aantal regeltellingen overslaan:** Het veld regeltelling overslaan geeft aan hoeveel regels u aan het begin van de gegevensset moet negeren.

**Bemonstering:** Schakel steekproeven in om het aantal rijen van uw bron te beperken. Gebruik deze instelling wanneer u gegevens uit uw bron test of voorbeeldt voor foutopsporingsdoeleinden.

**Rijen met meerdere lijnen:** Selecteer rijen met meerdere regels als het brontekstbestand tekenreekswaarden bevat die meerdere rijen omvatten, d.w.z. nieuwe lijnen binnen een waarde. Deze instelling is alleen beschikbaar in de setgegevens van DelimitedText.

Als u wilt dat uw bron correct is geconfigureerd, schakelt u de foutopsporingsmodus in en haalt u een gegevensvoorbeeld op. Zie [Foutopsporingsmodus](concepts-data-flow-debug-mode.md)voor meer informatie .

> [!NOTE]
> Wanneer de foutopsporingsmodus is ingeschakeld, overschrijft de configuratie van de rijlimiet in foutopsporingsinstellingen de bemonsteringsinstelling in de bron tijdens het voorbeeld van gegevens.

## <a name="projection"></a>Projectie

Net als schema's in gegevenssets definieert de projectie in een bron de gegevenskolommen, -typen en -indelingen uit de brongegevens. Voor de meeste gegevenssettypen zoals SQL en Parket wordt de projectie in een bron bevestigd om het schema weer te geven dat in een gegevensset is gedefinieerd. Wanneer uw bronbestanden niet sterk zijn getypt (bijvoorbeeld platte csv-bestanden in plaats van Parketbestanden), u de gegevenstypen voor elk veld in de brontransformatie definiëren.

![Instellingen op het tabblad Projectie](media/data-flow/source3.png "Projectie")

Als uw tekstbestand geen gedefinieerd schema heeft, selecteert u **Gegevenstype detecteren** zodat Gegevensfabriek de gegevenstypen bemonstert en afstelt. Selecteer **Standaardnotatie definiëren** om de standaardgegevensindelingen automatisch op te sporen.

**Met het resetschema** wordt de projectie opnieuw ingesteld op wat is gedefinieerd in de gegevensset waarnaar wordt verwezen.

U de kolomgegevenstypen wijzigen in een down-stream afgeleide kolomtransformatie. Gebruik een selecte transformatie om de kolomnamen te wijzigen.

### <a name="import-schema"></a>Importschema

Met de knop **Schema importeren** op het tabblad **Projectie** u een actief foutopsporingscluster gebruiken om een schemaprojectie te maken. Als u in elk brontype beschikbaar bent, overschrijft het importeren van het schema hier de projectie die in de gegevensset is gedefinieerd. Het gegevenssetobject wordt niet gewijzigd.

Dit is handig in datasets zoals Avro en CosmosDB die complexe gegevensstructuren ondersteunen, vereisen geen schemadefinities die in de gegevensset moeten bestaan.

## <a name="optimize-the-source-transformation"></a>De brontransformatie optimaliseren

Op het tabblad **Optimaliseren** voor de brontransformatie ziet u mogelijk een **bronpartitietype.** Deze optie is alleen beschikbaar wanneer uw bron Azure SQL Database is. Dit komt omdat Data Factory probeert verbindingen parallel te maken om grote query's uit te voeren tegen uw SQL Database-bron.

![Instellingen voor bronpartitie](media/data-flow/sourcepart3.png "Partitioneren")

U hoeft geen gegevens te partitioneren op uw SQL Database-bron, maar partities zijn handig voor grote query's. U uw partitie baseren op een kolom of een query.

### <a name="use-a-column-to-partition-data"></a>Een kolom gebruiken om gegevens te partitioneren

Selecteer in de brontabel een kolom om op te partitioneren. Stel ook het aantal partities in.

### <a name="use-a-query-to-partition-data"></a>Een query gebruiken om gegevens te partitioneren

U ervoor kiezen om de verbindingen te partitioneren op basis van een query. Voer de inhoud van een WHERE-predicaat in. Voer bijvoorbeeld het jaar > 1980 in.

Zie het [tabblad Optimaliseren](concepts-data-flow-overview.md#optimize)voor meer informatie over optimalisatie binnen de toewijzingsgegevensstroom.

## <a name="next-steps"></a>Volgende stappen

Begin met het bouwen van een [transformatie met afgeleide kolommen](data-flow-derived-column.md) en een geselecteerde [transformatie](data-flow-select.md).
