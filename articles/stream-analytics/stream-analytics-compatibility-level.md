---
title: Compatibiliteitsniveaus van Azure Stream Analytics
description: Meer informatie over het instellen van een compatibiliteitsniveau voor een Azure Stream Analytics-taak en belangrijke wijzigingen in het nieuwste compatibiliteitsniveau
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087852"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Compatibiliteitsniveau voor Azure Stream Analytics-taken

In dit artikel wordt de optie compatibiliteitsniveau in Azure Stream Analytics beschreven. Stream Analytics is een beheerde service, met regelmatige functie-updates en prestatieverbeteringen. De meeste runtimes-updates van de service worden automatisch beschikbaar gesteld aan eindgebruikers. 

Sommige nieuwe functionaliteit in de service kan echter een belangrijke wijziging in de service introduceren, zoals een wijziging in het gedrag van een bestaande taak of een wijziging in de manier waarop gegevens worden verbruikt in lopende taken. U uw bestaande Stream Analytics-taken draaienzonder grote wijzigingen door de instelling voor compatibiliteitsniveau verlaagd te laten. Wanneer u klaar bent voor het nieuwste runtime-gedrag, u zich aanmelden door het compatibiliteitsniveau te verhogen. 

## <a name="choose-a-compatibility-level"></a>Een compatibiliteitsniveau kiezen

Compatibiliteitsniveau bepaalt het runtimegedrag van een streamanalysetaak. 

Azure Stream Analytics ondersteunt momenteel drie compatibiliteitsniveaus:

* 1.0 - Origineel compatibiliteitsniveau, enkele jaren geleden geïntroduceerd tijdens de algemene beschikbaarheid van Azure Stream Analytics.
* 1.1 - Vorig gedrag
* 1.2 - Nieuwste gedrag met meest recente verbeteringen

Wanneer u een nieuwe Stream Analytics-taak maakt, is het een goede manier om deze te maken met behulp van het nieuwste compatibiliteitsniveau. Start uw taakontwerp op basis van het nieuwste gedrag, om later extra verandering en complexiteit te voorkomen.

## <a name="set-the-compatibility-level"></a>Het compatibiliteitsniveau instellen

U het compatibiliteitsniveau instellen voor een Stream Analytics-taak in de Azure-portal of met behulp van de [API-aanroep voor taakREST maken.](/rest/api/streamanalytics/stream-analytics-job)

Ga als bedoeld als u het compatibiliteitsniveau van de taak in de Azure-portal bijwerkt:

1. Gebruik de [Azure-portal](https://portal.azure.com) om uw Stream Analytics-taak te zoeken.
2. **Stop** de taak voordat u het compatibiliteitsniveau bijwerkt. U het compatibiliteitsniveau niet bijwerken als uw taak in een status is.
3. Selecteer onder de kop **Configureren** de optie **Compatibiliteitsniveau**.
4. Kies de gewenste waarde op compatibiliteitsniveau.
5. Selecteer **Opslaan** onder aan de pagina.

![Compatibiliteitsniveau voor Stream Analytics in Azure-portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Wanneer u het compatibiliteitsniveau bijwerkt, valideert de T-compiler de taak met de syntaxis die overeenkomt met het geselecteerde compatibiliteitsniveau.

## <a name="compatibility-level-12"></a>Compatibiliteitsniveau 1.2

De volgende belangrijke wijzigingen worden doorgevoerd in compatibiliteitsniveau 1.2:

###  <a name="amqp-messaging-protocol"></a>AMQP-berichtenprotocol

**1.2-niveau**: Azure Stream Analytics gebruikt [het ADQP-berichtenprotocol (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) om naar wachtrijen en onderwerpen van servicebussen te schrijven. AMQP stelt u in staat om cross-platform, hybride applicaties te bouwen met behulp van een open standaard protocol.

### <a name="geospatial-functions"></a>Georuimtelijke functies

**Eerdere niveaus:** Azure Stream Analytics heeft aardrijkskundeberekeningen gebruikt.

**1.2 niveau:** Met Azure Stream Analytics u geometrische geprojecteerde geocoördinaten berekenen. Er is geen verandering in de handtekening van de georuimtelijke functies. Hun semantiek is echter iets anders, waardoor een preciezere berekening mogelijk is dan voorheen.

Azure Stream Analytics ondersteunt georuimtelijke referentiegegevensindexering. Referentiegegevens die georuimtelijke elementen bevatten, kunnen worden geïndexeerd voor een snellere joinberekening.

De bijgewerkte georuimtelijke functies brengen de volledige expressiviteit van welbekende tekst (WKT) geospatiale formaat. U andere georuimtelijke componenten opgeven die voorheen niet met GeoJson zijn ondersteund.

Zie [Updates voor georuimtelijke functies in Azure Stream Analytics – Cloud en IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)voor meer informatie.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallelle query-uitvoering voor invoerbronnen met meerdere partities

**Eerdere niveaus:** Azure Stream Analytics-query's vereisten het gebruik van partitie per clausule om queryverwerking te paralleleren tussen invoerbronpartities.

**1.2 niveau:** Als querylogica kan worden parallellopen tussen invoerbronpartities, maakt Azure Stream Analytics afzonderlijke query-exemplaren en voert berekeningen parallel uit.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Native Bulk API-integratie met CosmosDB-uitvoer

**Eerdere niveaus:** Het upsert-gedrag is *ingevoegd of samengevoegd*.

**1.2 niveau:** Native Bulk API-integratie met CosmosDB-uitvoer maximaliseert de doorvoer en verwerkt op efficiënte wijze throttling-aanvragen. Zie [de Azure Stream Analytics-uitvoer naar azure cosmos DB-pagina](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)voor meer informatie.

Het upsert-gedrag is *invoegen of vervangen.*

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** [DateTimeOffset-typen](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) werden aangepast aan UTC.

**1.2 niveau:** DateTimeOffset wordt niet meer aangepast.

### <a name="long-when-writing-to-sql-output"></a>Lang bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** Waarden zijn afgekapt op basis van het doeltype.

**1.2 niveau:** Waarden die niet in het doeltype passen, worden verwerkt volgens het uitvoerfoutbeleid.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serialisatie opnemen en arrayen bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** Records werden geschreven als "Record" en arrays werden geschreven als "Array".

**1.2 niveau:** Records en arrays worden geserialiseerd in JSON-indeling.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikte validatie van voorvoegsel van functies

**Eerdere niveaus:** Er was geen strikte validatie van functievoorvoegsels.

**1.2 niveau:** Azure Stream Analytics heeft een strikte validatie van functievoorvoegsels. Het toevoegen van een voorvoegsel aan een ingebouwde functie veroorzaakt een fout. Wordt bijvoorbeeld`myprefix.ABS(…)` niet ondersteund.

Het toevoegen van een voorvoegsel aan ingebouwde aggregaten resulteert ook in fouten. Wordt bijvoorbeeld `myprefix.SUM(…)` niet ondersteund.

Het gebruik van het voorvoegsel "systeem" voor elke door de gebruiker gedefinieerde functies resulteert in fouten.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Array en object niet toestaan als sleuteleigenschappen in Cosmos DB-uitvoeradapter

**Eerdere niveaus:** Array- en objecttypen werden ondersteund als een belangrijke eigenschap.

**1.2 niveau:** Array- en objecttypen worden niet langer ondersteund als een belangrijke eigenschap.

## <a name="compatibility-level-11"></a>Compatibiliteitsniveau 1.1

De volgende belangrijke wijzigingen worden doorgevoerd in compatibiliteitsniveau 1.1:

### <a name="service-bus-xml-format"></a>XML-indeling servicebus

**1.0 niveau:** Azure Stream Analytics gebruikte DataContractSerializer, dus de berichtinhoud bevatte XML-tags. Bijvoorbeeld:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 niveau:** De inhoud van het bericht bevat de stream direct zonder extra tags. Bijvoorbeeld: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Aanhoudende hoofdlettergevoeligheid voor veldnamen

**1.0 niveau:** Veldnamen zijn gewijzigd in kleine letters wanneer deze zijn verwerkt door de Azure Stream Analytics-engine.

**1.1 niveau:** case-sensitivity blijft bestaan voor veldnamen wanneer deze worden verwerkt door de Azure Stream Analytics-engine.

> [!NOTE]
> Aanhoudende hoofdlettergevoeligheid is nog niet beschikbaar voor Stream Analytic-taken die worden gehost met behulp van de Edge-omgeving. Als gevolg hiervan worden alle veldnamen geconverteerd naar kleine letters als uw taak wordt gehost op Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 niveau:** De opdracht TABEL MAKEN filterde geen gebeurtenissen met NaN (Geen getal. Bijvoorbeeld Infinity, -Infinity) in een FLOAT-kolomtype omdat ze buiten het gedocumenteerde bereik voor deze getallen vallen.

**1.1 niveau:** Met CREATE TABLE u een sterk schema opgeven. De Stream Analytics-engine valideert dat de gegevens aan dit schema voldoen. Met dit model kan de opdracht gebeurtenissen filteren met NaN-waarden.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Automatische upcast uitschakelen voor datumtekenreeksen in JSON

**1.0 niveau:** De JSON-parser zou tekenreekswaarden met datum-/tijd-/zone-informatie automatisch upcasten naar DateTime-type en deze vervolgens converteren naar UTC. Dit gedrag resulteerde in het verliezen van de tijdzone informatie.

**1.1 niveau:** Er is niet meer automatisch upcast van tekenreekswaarden met datum/tijd/streekinformatie aan type DateTime. Als gevolg hiervan wordt tijdzone-informatie bewaard.

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-invoer oplossen](stream-analytics-troubleshoot-input.md)
* [Stream Analytics-bronstatus](stream-analytics-resource-health.md)
