---
title: Gegevensindelingen die worden ondersteund door Azure Data Explorer voor inname.
description: Meer informatie over de verschillende gegevens- en compressieindelingen die worden ondersteund door Azure Data Explorer voor opname.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235306"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Gegevensindelingen die worden ondersteund door Azure Data Explorer voor inname

Gegevensopname is het proces waarbij gegevens aan een tabel worden toegevoegd en beschikbaar worden gesteld voor query's in Azure Data Explorer. Voor alle innamemethoden, met elkaar inplaats van query, moeten de gegevens zich in een van de ondersteunde indelingen begeven. In de volgende tabel worden de indelingen beschreven die Azure Data Explorer ondersteunt voor het innemen van gegevens.

|Indeling   |Toestelnummer   |Beschrijving|
|---------|------------|-----------|
|Avro     |`.avro`     |Een [Avro containerbestand.](https://avro.apache.org/docs/current/) De volgende codes worden `null` `deflate` ondersteund: , (`snappy` wordt momenteel niet ondersteund).|
|CSV      |`.csv`      |Een tekstbestand met door komma's gescheiden waarden (`,`). Zie [RFC 4180: _Common Format and MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |Een tekstbestand met JSON-objecten die zijn afgebakend door `\n` of `\r\n`. Zie [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON (MultiJSON)|`.multijson`|Een tekstbestand met een JSON-array van eigenschapsassen (die elk een record `\n` vertegenwoordigen), of een willekeurig aantal eigenschapsassen die zijn afgebakend door witruimte, of `\r\n`. Elke woning zak kan worden verspreid over meerdere lijnen. Dit formaat heeft `JSON`de voorkeur boven , tenzij de gegevens niet-eigendom zakken.|
|Orc      |`.orc`      |Een [Orc-bestand](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parket  |`.parquet`  |Een [parketdossier.](https://en.wikipedia.org/wiki/Apache_Parquet)|
|Psv      |`.psv`      |Een tekstbestand met door<code>&#124;</code>de pijp gescheiden waarden ( ).|
|Raw      |`.raw`      |Een tekstbestand waarvan de volledige inhoud één tekenreekswaarde is.|
|SCsv (Scsv)     |`.scsv`     |Een tekstbestand met door puntkomma's gescheiden waarden (`;`).|
|SOHSV SOHSV    |`.sohsv`    |Een tekstbestand met soh-gescheiden waarden. (SOH is ASCII codepoint 1; dit formaat wordt gebruikt door Hive op HDInsight.)|
|Tsv      |`.tsv`      |Een tekstbestand met door`\t`tabbladen gescheiden waarden ( ).|
|TSVE TSVE     |`.tsv`      |Een tekstbestand met door`\t`tabbladen gescheiden waarden ( ). Een backslash-teken (`\`) wordt gebruikt om te ontsnappen.|
|TXT      |`.txt`      |Een tekstbestand met regels `\n`die worden afgebakend door . Lege lijnen worden overgeslagen.|

## <a name="supported-data-compression-formats"></a>Ondersteunde gegevenscompressie-indelingen

Blobs en bestanden kunnen worden gecomprimeerd via een van de volgende compressiealgoritmen:

|Compressie|Toestelnummer|
|-----------|---------|
|Gzip       |.gz      |
|Zip        |.zip     |

Geef compressie aan door de extensie toe te voegen aan de naam van de blob of het bestand.

Bijvoorbeeld:
* `MyData.csv.zip`geeft een blob of een bestand aan dat is opgemaakt als CSV, gecomprimeerd met ZIP (archief of één bestand)
* `MyData.csv.gz`geeft een blob of een bestand aan dat is opgemaakt als CSV, gecomprimeerd met GZip

Blob- of bestandsnamen die de indelingsextensies niet bevatten, maar alleen compressie (bijvoorbeeld ) worden ook ondersteund. In dit geval moet de bestandsindeling worden opgegeven als een eigenschap voor inname, omdat deze niet kan worden afgeleid.

> [!NOTE]
> Sommige compressieformaten houden de oorspronkelijke bestandsextensie bij als onderdeel van de gecomprimeerde stream. Deze extensie wordt over het algemeen genegeerd voor het bepalen van de bestandsindeling. Als de bestandsindeling niet kan worden bepaald aan de hand van de (gecomprimeerde) blob of bestandsnaam, moet deze worden opgegeven via de `format` eigenschap inname.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het innemen van gegevens](/azure/data-explorer/ingest-data-overview)
* Meer informatie over de eigenschappen van [Azure Data Explorer-gegevensopname](ingestion-properties.md)
