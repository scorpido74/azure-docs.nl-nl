---
title: Ondersteunde gegevens typen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over de ondersteunde gegevens typen vindt u in Azure Time Series Insights preview.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049951"
---
# <a name="supported-data-types"></a>Ondersteunde gegevenstypen

De volgende tabel geeft een lijst van de gegevens typen die worden ondersteund door Time Series Insights

| Gegevenstype | Beschrijving | Voorbeeld | Naam van eigenschaps kolom in Parquet
|---|---|---|---|
| **booleaans** | Een gegevens type met een van de twee statussen: `true` of `false` . | "isQuestionable": True | isQuestionable_bool
| **datum/tijd** | Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum en tijd van de dag. Uitgedrukt in [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -indeling. Datetime-eigenschappen worden altijd opgeslagen in UTC-indeling. Tijd zone-offsets worden toegepast en vervolgens wordt de waarde opgeslagen in UTC. Zie [deze](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sectie voor meer informatie over de omgeving tijds tempel eigenschap en datum/tijd verschuivingen | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | Een 64-bits getal met dubbele precisie  | "waarde": 31,0482941 | value_double
| **long** | Een ondertekend 64-bits geheel getal  | "waarde": 31 | value_long
| **tekenreeks** | Tekst waarden moeten bestaan uit geldige UTF-8. |  "site": "DIM_MLGGG" | site_string
| **dynamisch** | Een complex (niet-primitieve) type dat bestaat uit een matrix of eigenschappen verzameling (Dictionary). Momenteel worden alleen stringified JSON-matrices van primitieven of matrices met objecten die de TS-ID of de juiste tijds tempel (en) bevatten, opgeslagen als dynamisch. Lees dit [artikel](./concepts-json-flattening-escaping-rules.md) voor meer informatie over hoe objecten worden afgevlakt en matrices kunnen niet worden getotaliseerd |  "waarden": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * De omgeving van de TSI is sterk getypeerd. Als apparaten of Tags zowel integrale als niet-integrale gegevens verzenden, worden de waarden van de apparaatinstellingen opgeslagen in twee gescheiden en lange kolommen en moet de [functie Coalesce ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) worden gebruikt bij het maken van API-aanroepen en het definiëren van variabele expressies van uw tijd reeks model.

#### <a name="objects-and-arrays"></a>Objecten en matrices

U kunt complexe typen zoals objecten en arrays verzenden als onderdeel van de nettolading van de gebeurtenis. Geneste objecten worden afgevlakt en matrices worden opgeslagen als `dynamic` of afgevlakt om meerdere gebeurtenissen te maken, afhankelijk van uw omgevings configuratie en JSON-vorm. Meer informatie over de [JSON afvlakking en Escape-regels](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Volgende stappen

* Lees de [regels voor json-afvlakking en-Escapes](./concepts-json-flattening-escaping-rules.md) om te begrijpen hoe gebeurtenissen worden opgeslagen. 

* Inzicht in de [doorvoer beperkingen](concepts-streaming-throughput-limitations.md) van uw omgeving

* Meer informatie over [gebeurtenis bronnen](concepts-streaming-ingestion-event-sources.md) voor het opnemen van streaming-gegevens.
