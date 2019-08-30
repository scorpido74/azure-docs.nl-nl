---
title: Informatie over het compatibiliteitsniveau voor Azure Stream Analytics-taken
description: Informatie over het instellen van een compatibiliteitsniveau voor een Azure Stream Analytics-taak en belangrijke wijzigingen in de meest recente compatibiliteitsniveau
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: d6d31506a13656a954c48dfee00f14d8ab381fd5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173244"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Compatibiliteitsniveau voor Azure Stream Analytics-taken

In dit artikel wordt de optie compatibiliteits niveau in Azure Stream Analytics beschreven. Stream Analytics is een beheerde service, met normale onderdelen updates en prestatie verbeteringen. De meeste runtime-updates van de service worden automatisch beschikbaar gesteld aan eind gebruikers. 

Een aantal nieuwe functies in de service kan echter een grote wijziging veroorzaken, zoals een wijziging in het gedrag van een bestaande taak of een wijziging in de manier waarop gegevens worden gebruikt bij het uitvoeren van taken. U kunt ervoor zorgen dat uw bestaande Stream Analytics-taken worden uitgevoerd zonder grote wijzigingen door de instelling voor het compatibiliteits niveau te verlagen. Wanneer u klaar bent voor het laatste runtime-gedrag, kunt u zich aanmelden door het compatibiliteits niveau te verhogen. 

## <a name="choose-a-compatibility-level"></a>Een compatibiliteits niveau kiezen

Compatibiliteitsniveau bepaalt het runtimegedrag van een stream analytics-taak. 

Azure Stream Analytics ondersteunt momenteel drie compatibiliteits niveaus:

* 1,0-vorig gedrag
* 1,1-standaard gedrag
* 1,2 (preview): nieuwste gedrag met de meest recente verbeteringen bij de evaluatie

Het oorspronkelijke 1,0-compatibiliteits niveau werd geïntroduceerd tijdens de algemene Beschik baarheid van Azure Stream Analytics enkele jaren geleden.

Wanneer u een nieuwe Stream Analytics taak maakt, is dit een best practice om deze te maken met behulp van het meest recente compatibiliteits niveau. Start uw taak ontwerp dat afhankelijk is van het nieuwste gedrag, zodat u later extra wijzigingen en complexiteit kunt voor komen.

## <a name="set-the-compatibility-level"></a>Het compatibiliteits niveau instellen

U kunt het compatibiliteits niveau instellen voor een Stream Analytics taak in het Azure Portal of met behulp van de [aanroep taak maken rest API](/rest/api/streamanalytics/stream-analytics-job).

Het compatibiliteits niveau van de taak in het Azure Portal bijwerken:

1. Gebruik de [Azure Portal](https://portal.azure.com) om naar uw stream Analytics-taak te zoeken.
2. **Stop** de taak voordat u het compatibiliteits niveau bijwerkt. U kunt het compatibiliteitsniveau niet bijwerken als de taak actief is is.
3. Onder de kop **configureren** selecteert u **compatibiliteits niveau**.
4. Kies de gewenste waarde voor het compatibiliteits niveau.
5. Selecteer onder aan de pagina **Opslaan** .

![Compatibiliteitsniveau van Stream Analytics in Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Wanneer u het compatibiliteitsniveau bijwerkt, wordt de taak met de syntaxis die overeenkomt met het geselecteerde compatibiliteitsniveau gevalideerd door de T-SQL-compiler.

## <a name="compatibility-level-12-preview"></a>Compatibiliteits niveau 1,2 (preview-versie)

De volgende belang rijke wijzigingen worden geïntroduceerd in compatibiliteits niveau 1,2:

### <a name="geospatial-functions"></a>Georuimtelijke functies

**Eerdere niveaus:** Geografie berekeningen Azure Stream Analytics gebruikt.

**niveau van 1,2:** Met Azure Stream Analytics kunt u geometrische geprojecteerde geo-coördinaten berekenen. Er is geen wijziging in de hand tekening van de georuimtelijke functies. De semantiek is echter iets anders, waardoor nauw keuriger kan worden gerekend dan voorheen.

Azure Stream Analytics ondersteunt het indexeren van georuimtelijke referentie gegevens. Referentie gegevens die georuimtelijke elementen bevatten, kunnen worden geïndexeerd voor een snellere samenvoegings berekening.

Met de bijgewerkte georuimtelijke functies wordt de WKT-indeling (volledig herkende tekst) met een georuimtelijke notatie. U kunt andere georuimtelijke onderdelen opgeven die niet eerder met geojson worden ondersteund.

Zie [updates voor georuimtelijke functies in azure stream Analytics-Cloud en IOT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)voor meer informatie.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallelle query uitvoering voor invoer bronnen met meerdere partities

**Eerdere niveaus:** Voor Azure Stream Analytics query's is het gebruik van de component PARTITION BY vereist voor het verwerken van parallelliseren-query's tussen de verschillende invoer bron partities.

**niveau van 1,2:** Als query logica kan worden geparallelleerd tussen de verschillende invoer bron partities, maakt Azure Stream Analytics afzonderlijke query-instanties en worden berekeningen parallel uitgevoerd.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Systeem eigen bulk-API-integratie met CosmosDB-uitvoer

**Eerdere niveaus:** Het upsert-gedrag is *invoegen of samen voegen*.

**niveau van 1,2:** Systeem eigen bulk-API-integratie met CosmosDB-uitvoer maximaliseert de door Voer en efficiënt afhandelen van bandbreedte aanvragen. Zie [de pagina Azure stream Analytics uitvoer naar Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)voor meer informatie.

Het gedrag van de upsert wordt *ingevoegd of vervangen*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>Date time offset bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** [Date Time offset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) -typen zijn aangepast aan UTC.

**niveau van 1,2:** Date time offset wordt niet meer aangepast.

### <a name="long-when-writing-to-sql-output"></a>Lang bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** De waarden zijn afgekapt op basis van het doel type.

**niveau van 1,2:** Waarden die niet in het doel type passen, worden afgehandeld op basis van het uitvoer fout beleid.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serialisatie van records en matrices bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** Records zijn geschreven als ' record ' en matrices zijn geschreven als ' matrix '.

**niveau van 1,2:** Records en matrices worden geserialiseerd in JSON-indeling.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikte validatie van het voor voegsel van functies

**Eerdere niveaus:** Er is geen strikte validatie van functie voorvoegsels.

**niveau van 1,2:** Azure Stream Analytics heeft een strikte validatie van functie voorvoegsels. Als u een voor voegsel toevoegt aan een ingebouwde functie, treedt er een fout op. Wordt bijvoorbeeld`myprefix.ABS(…)` niet ondersteund.

Het toevoegen van een voor voegsel aan ingebouwde aggregaties resulteert ook in een fout. Wordt bijvoorbeeld `myprefix.SUM(…)` niet ondersteund.

Als u het voor voegsel ' System ' gebruikt voor door de gebruiker gedefinieerde functies, treedt er een fout op.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Matrix en object niet toestaan als sleutel eigenschappen in Cosmos DB uitvoer adapter

**Eerdere niveaus:** Matrix-en object typen worden ondersteund als een sleutel eigenschap.

**niveau van 1,2:** Matrix-en object typen worden niet meer ondersteund als een sleutel eigenschap.

## <a name="compatibility-level-11"></a>Compatibiliteits niveau 1,1

De volgende belangrijke wijzigingen zijn geïntroduceerd in compatibiliteitsniveau 1.1 van:

### <a name="service-bus-xml-format"></a>Service Bus XML-indeling

**niveau van 1,0:** Azure Stream Analytics gebruikgemaakt van DataContractSerializer, waardoor de inhoud van het bericht XML-tags bevat. Bijvoorbeeld:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**niveau van 1,1:** De inhoud van het bericht bevat de stream direct zonder extra tags. Bijvoorbeeld: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Hoofdletter gevoeligheid voor veld namen persistent maken

**niveau van 1,0:** Veld namen zijn in kleine letters gewijzigd wanneer ze worden verwerkt door de Azure Stream Analytics-engine.

**niveau 1,1:** hoofdletter gevoeligheid is persistent voor veld namen wanneer deze worden verwerkt door de Azure stream Analytics-engine.

> [!NOTE]
> Permanente hoofdlettergevoeligheid is nog niet beschikbaar voor Stream Analytics-taken die worden gehost met behulp van Edge-omgeving. Als gevolg hiervan, worden alle veldnamen geconverteerd naar kleine letters, als de taak voor edge-apparaten wordt gehost.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**niveau van 1,0:** CREATE TABLE opdracht heeft geen gebeurtenissen gefilterd met NaN (geen-a-number). Bijvoorbeeld oneindig, -oneindig) in een FLOAT-kolom typt, omdat deze buiten het bereik beschreven voor deze getallen zijn.

**niveau van 1,1:** Met CREATE TABLE kunt u een sterk schema opgeven. De Stream Analytics-engine valideert dat de gegevens aan dit schema voldoen. De opdracht kunt u gebeurtenissen met NaN waarden filteren met dit model.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Automatische conversie voor datetime-teken reeksen uitschakelen in JSON

**niveau van 1,0:** Met de JSON-parser worden teken reeks waarden met datum/tijd/zone-informatie automatisch geconverteerd naar DateTime-type en vervolgens geconverteerd naar UTC. Dit gedrag heeft geleid tot verlies van informatie over de tijd zone.

**niveau van 1,1:** Er zijn niet meer automatisch conversie van teken reeks waarden met datum/tijd/zone-informatie naar het type DateTime. Als gevolg hiervan, wordt informatie over de tijdzone bewaard.

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-invoer oplossen](stream-analytics-troubleshoot-input.md)
* [Resource status Stream Analytics](stream-analytics-resource-health.md)
