---
title: Handleiding voor activiteitsprestaties en schaalbaarheid kopiëren
description: Meer informatie over belangrijke factoren die van invloed zijn op de prestaties van gegevensverplaatsing in Azure Data Factory wanneer u de kopieeractiviteit gebruikt.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414179"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Handleiding voor activiteitsprestaties en schaalbaarheid kopiëren

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-activity-performance.md)
> * [Huidige versie](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Of u nu een grootschalige gegevensmigratie van data lake of enterprise data warehouse (EDW) naar Azure wilt uitvoeren, of dat u gegevens op schaal wilt opnemen van verschillende bronnen in Azure voor big data analytics, het is essentieel om optimale prestaties en schaalbaarheid te bereiken.  Azure Data Factory biedt een performant, veerkrachtig en kosteneffectief mechanisme om gegevens op schaal in te nemen, waardoor het een geweldige pasvorm is voor gegevenstechnici die zeer performante en schaalbare gegevensopnamepijplijnen willen bouwen.

Na het lezen van dit artikel u de volgende vragen beantwoorden:

- Welk prestatieniveau en schaalbaarheid kan ik bereiken met ADF-kopieeractiviteit voor gegevensmigratie en gegevensopnamescenario's?

- Welke stappen moet ik nemen om de prestaties van ADF-kopieeractiviteiten af te stemmen?
- Welke ADF perf optimalisatie knoppen kan ik gebruiken om de prestaties te optimaliseren voor een enkele kopie activiteit draaien?
- Welke andere factoren buiten ADF om rekening mee te houden bij het optimaliseren van de kopieerprestaties?

> [!NOTE]
> Als u niet bekend bent met de kopieeractiviteit in het algemeen, raadpleegt u het [overzicht van kopieeractiviteiten](copy-activity-overview.md) voordat u dit artikel leest.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Prestaties en schaalbaarheid kopiëren die haalbaar zijn met ADF

ADF biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt, waardoor ontwikkelaars pijplijnen kunnen bouwen om uw netwerkbandbreedte volledig te benutten, evenals opslag-IOPS en bandbreedte om de doorvoer van gegevensverkeer voor uw omgeving te maximaliseren.  Dit betekent dat de doorvoer die u bereiken kan worden geschat door het meten van de minimale doorvoer aangeboden door de bron gegevens archief, de bestemming gegevens op te slaan, en netwerk bandbreedte tussen de bron en bestemming.  In de onderstaande tabel wordt de kopieerduur berekend op basis van de gegevensgrootte en de bandbreedtelimiet voor uw omgeving. 

| Gegevensgrootte / <br/> Bandbreedte | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0.0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 uur    | 2,3 uur   | 0,5 uur   | 0,2 uur  | 0.05 uur | 0.02 uur | 0.0 uur   |
| **1 TB**                    | 46.6 uur   | 23.3 uur  | 4.7 uur   | 2,3 uur  | 0,5 uur  | 0,2 uur  | 0.05 uur  |
| **10 TB**                   | 19,4 dagen  | 9,7 dagen  | 1,9 dagen  | 0,9 dagen | 0,2 dagen | 0,1 dagen | 0,02 dagen |
| **100 TB**                  | 194,2 dagen | 97,1 dagen | 19,4 dagen | 9,7 dagen | 1,9 dagen | 1 dag    | 0,2 dagen  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3.2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3.2 mo   | 0,6 mo    |

ADF-kopie is schaalbaar op verschillende niveaus:

![hoe ADF-kopie schaalt](media/copy-activity-performance/adf-copy-scalability.png)

- ADF-besturingsstroom kan meerdere kopieeractiviteiten parallel starten, bijvoorbeeld met [Voor Elke lus](control-flow-for-each-activity.md).
- Eén kopieeractiviteit kan profiteren van schaalbare rekenbronnen: wanneer u Azure Integration Runtime gebruikt, u [maximaal 256 DIU's](#data-integration-units) voor elke kopieeractiviteit op een serverloze manier opgeven; wanneer u zelf gehoste Implementatieruntime gebruikt, u de machine handmatig opschalen of uitschalen naar meerdere machines[(maximaal 4 knooppunten)](create-self-hosted-integration-runtime.md#high-availability-and-scalability)en wordt de bestandsset met één kopieactiviteit verdeeld over alle knooppunten.
- Een enkele kopie activiteit leest van en schrijft naar het gegevensarchief met behulp van meerdere threads [in parallel](#parallel-copy).

## <a name="performance-tuning-steps"></a>Stappen voor prestatieafstemming

Neem deze stappen om de prestaties van uw Azure Data Factory-service af te stemmen op de kopieeractiviteit.

1. **Pak een testgegevensset en stel een basislijn vast.** Test tijdens de ontwikkelingsfase uw pijplijn met behulp van de kopieeractiviteit aan de hand van een representatieve gegevenssteekproef. De gegevensset die u kiest, moet uw typische gegevenspatronen weergeven (mapstructuur, bestandspatroon, gegevensschema, enzovoort), en is groot genoeg om de kopieerprestaties te evalueren, bijvoorbeeld het duurt 10 minuten of verder voordat kopieeractiviteit is voltooid. Verzamel uitvoeringsgegevens en prestatiekenmerken na [controle van kopieeractiviteiten](copy-activity-monitoring.md).

2. **Hoe de prestaties van een enkele kopieeractiviteit te maximaliseren:**

   Om te beginnen raden we je aan om eerst de prestaties te maximaliseren met één kopieeractiviteit.

   - **Als de kopieeractiviteit wordt uitgevoerd op een Azure Integration Runtime:** begin met standaardwaarden voor [DIU (Data Integration Units)](#data-integration-units) en [parallelle kopieerinstellingen.](#parallel-copy) 

   - **Als de kopieeractiviteit wordt uitgevoerd op een zelf gehoste Integratieruntime:** we raden u aan een speciale machine te gebruiken die losstaat van de server die het gegevensarchief host om de runtime voor integratie te hosten. Begin met standaardwaarden voor [parallelle kopieerinstellingen](#parallel-copy) en gebruik één knooppunt voor de zelfgehoste IR.  

   Voer een prestatietest uit en noteer de behaalde prestaties en de werkelijke waarden die worden gebruikt, zoals DIU's en parallelle kopieën. Raadpleeg [de controle van kopieeractiviteiten](copy-activity-monitoring.md) over het verzamelen van gebruikte runresultaten en prestatie-instellingen en hoe u [de prestaties van kopieeractiviteiten oplossen](copy-activity-performance-troubleshooting.md) om het knelpunt te identificeren en op te lossen. 

   Herhalen om aanvullende prestatietestruns uit te voeren volgens de richtlijnen voor probleemoplossing en tuning. Zodra één kopieeractiviteit geen betere doorvoer kan bereiken, u overwegen de totale doorvoer te maximaliseren door meerdere kopieën uit te voeren die gelijktijdig verwijzen naar stap 3.


3. **De totale doorvoer maximaliseren door meerdere kopieën tegelijk uit te voeren:**

   Nu u de prestaties van één exemplaaractiviteit hebt gemaximaliseerd, u, als u de doorvoerbovengrenzen van uw omgeving - netwerk, brongegevensarchief en bestemmingsgegevensarchief - nog niet hebt bereikt, meerdere kopieeractiviteiten parallel uitvoeren met ADF-regelstroomconstructies zoals [For Each loop.](control-flow-for-each-activity.md) Raadpleeg [Bestanden uit meerdere containers](solution-template-copy-files-multiple-containers.md)kopiëren, [Gegevens migreren van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md)of [Bulkkopiëren met een configuratietabeloplossingssjablonen](solution-template-bulk-copy-with-control-table.md) als algemeen voorbeeld.

5. **Vouw de configuratie uit naar uw volledige gegevensset.** Wanneer u tevreden bent met de uitvoeringsresultaten en -prestaties, u de definitie en pijplijn uitbreiden naar uw volledige gegevensset.

## <a name="troubleshoot-copy-activity-performance"></a>Problemen met de prestaties van kopieeractiviteiten oplossen

Volg de [prestatieafstemmingsstappen](#performance-tuning-steps) om prestatietests voor uw scenario te plannen en uit te voeren. En lees hoe u het prestatieprobleem van elke kopieeractiviteit oplossen in Azure Data Factory door [problemen met de prestaties van kopieeractiviteiten op te lossen.](copy-activity-performance-troubleshooting.md)

## <a name="copy-performance-optimization-features"></a>Functies voor prestatieoptimalisatie kopiëren

Azure Data Factory biedt de volgende functies voor prestatieoptimalisatie:

- [Eenheden voor gegevensintegratie](#data-integration-units)
- [Schaalbaarheid van zelfgehoste integratieruntime](#self-hosted-integration-runtime-scalability)
- [Parallelle kopie](#parallel-copy)
- [Gefaseerde kopie](#staged-copy)

### <a name="data-integration-units"></a>Eenheden voor gegevensintegratie

Een data-integratie-eenheid is een maat die de kracht (een combinatie van CPU, geheugen en toewijzing van netwerkbronnen) van één eenheid in Azure Data Factory vertegenwoordigt. Data Integration Unit is alleen van toepassing op [Azure-integratieruntime,](concepts-integration-runtime.md#azure-integration-runtime)maar niet [op zelf gehoste runtime voor integratie.](concepts-integration-runtime.md#self-hosted-integration-runtime) [Meer informatie](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Schaalbaarheid van zelfgehoste integratieruntime

Als u de toenemende gelijktijdige werkbelasting wilt hosten of hogere prestaties wilt bereiken, u de runtime voor zelfgehoste integratie opschalen of opschalen. [Meer informatie](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Parallelle kopie

U parallelle tekst instellen om het parallellisme aan te geven dat u wilt dat de kopieeractiviteit gebruikt. U deze eigenschap zien als het maximum aantal threads binnen de kopieeractiviteit die uit uw bron leest of parallel naar uw sink data stores schrijft. [Meer informatie](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Gefaseerde kopie

Wanneer u gegevens uit een brongegevensarchief kopieert naar een sink-gegevensarchief, u ervoor kiezen blobopslag te gebruiken als een tijdelijke faseringsopslag. [Meer informatie](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen van de exemplaaractiviteit:

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Problemen met de prestaties van kopieeractiviteiten oplossen](copy-activity-performance-troubleshooting.md)
- [Functies voor optimalisatie van activiteitsprestaties kopiëren](copy-activity-performance-features.md)
- [Azure Data Factory gebruiken om gegevens uit uw gegevensmeer of gegevensmagazijn naar Azure te migreren](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)
