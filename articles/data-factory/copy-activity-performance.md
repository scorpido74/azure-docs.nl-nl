---
title: Gids voor de prestaties en schaal baarheid van de Kopieer activiteit
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens in Azure Data Factory wanneer u de Kopieer activiteit gebruikt.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414179"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Gids voor de prestaties en schaal baarheid van de Kopieer activiteit

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-activity-performance.md)
> * [Huidige versie](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Of u nu een grootschalige gegevens migratie wilt uitvoeren van data Lake of ENTER prise Data Warehouse (EDW) naar Azure, of dat u gegevens wilt opnemen op schaal uit verschillende bronnen in azure voor big data Analytics, is essentieel voor optimale prestaties en schaal baarheid.  Azure Data Factory biedt een krachtige, robuuste en kosteneffectieve methode voor het opnemen van gegevens op schaal, waardoor het handig is voor gegevens technici die zeer krachtige en schaal bare gegevens opname pijplijnen willen bouwen.

Na het lezen van dit artikel kunt u de volgende vragen beantwoorden:

- Welk prestatie-en schaal niveau kan ik gebruiken voor het gebruik van ADF Copy-activiteiten voor gegevens migratie en gegevens opname scenario's?

- Welke stappen moet ik ondernemen om de prestaties van de ADF-Kopieer activiteit te optimaliseren?
- Wat kan ik doen met de methoden voor het optimaliseren van ADF? de prestaties voor een enkele Kopieer activiteit worden uitgevoerd?
- Wat andere factoren buiten ADF zijn om rekening mee te houden wanneer u de Kopieer prestaties optimaliseert?

> [!NOTE]
> Als u niet bekend bent met de Kopieer activiteit in het algemeen, raadpleegt u het overzicht van de [Kopieer activiteit](copy-activity-overview.md) voordat u dit artikel leest.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopieer prestaties en schaal baarheid die kan worden behaald met behulp van ADF

ADF biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt, zodat ontwikkel aars pijp lijnen kunnen bouwen om uw netwerk bandbreedte en opslag-IOPS en band breedte optimaal te benutten voor de door Voer van gegevens verplaatsing voor uw omgeving.  Dit betekent dat de door u gewenste door Voer kan worden geschat door de minimale door voer te meten die wordt aangeboden door de brongegevens opslag, het doel gegevens archief en de netwerk bandbreedte tussen de bron en de bestemming.  In de volgende tabel wordt de duur van de kopie berekend op basis van de gegevens grootte en de bandbreedte limiet voor uw omgeving. 

| Gegevens grootte/ <br/> BAP | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 minuten    | 1,4 minuten   | 0,3 minuten   | 0,1 minuten  | 0,03 minuten | 0,01 minuten | 0,0 minuten   |
| **10 GB**                   | 27,3 minuten   | 13,7 minuten  | 2,7 minuten   | 1,3 minuten  | 0,3 minuten  | 0,1 minuten  | 0,03 minuten  |
| **100 GB**                  | 4,6 uur    | 2,3 uur   | 0,5 uur   | 0,2 uur  | 0,05 uur | 0,02 uur | 0,0 uur   |
| **1 TB**                    | 46,6 uur   | 23,3 uur  | 4,7 uur   | 2,3 uur  | 0,5 uur  | 0,2 uur  | 0,05 uur  |
| **10 TB**                   | 19,4 dagen  | 9,7 dagen  | 1,9 dagen  | 0,9 dagen | 0,2 dagen | 0,1 dagen | 0,02 dagen |
| **100 TB**                  | 194,2 dagen | 97,1 dagen | 19,4 dagen | 9,7 dagen | 1,9 dagen | 1 dag    | 0,2 dagen  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

ADF-kopie is schaalbaar op verschillende niveaus:

![de schaal van ADF kopiëren](media/copy-activity-performance/adf-copy-scalability.png)

- De controle stroom van ADF kan meerdere Kopieer activiteiten parallel starten, bijvoorbeeld [voor elke lus](control-flow-for-each-activity.md).
- Eén Kopieer activiteit kan profiteren van schaal bare reken bronnen: wanneer u Azure Integration Runtime gebruikt, kunt u [Maxi maal 256 DIUs](#data-integration-units) voor elke Kopieer activiteit op serverloze wijze opgeven. Wanneer u zelf-hostende Integration Runtime gebruikt, kunt u de machine hand matig opschalen of uitschalen naar meerdere machines ([Maxi maal 4 knoop punten](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), en met één Kopieer activiteit wordt de bestands set gepartitioneerd op alle knoop punten.
- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads [parallel](#parallel-copy).

## <a name="performance-tuning-steps"></a>Stappen voor het afstemmen van prestaties

Voer de volgende stappen uit om de prestaties van uw Azure Data Factory-service af te stemmen met de Kopieer activiteit.

1. **Haal een test gegevensset op en stel een basis lijn in.** Test uw pijp lijn tijdens de ontwikkelings fase met behulp van de Kopieer activiteit op basis van een representatief gegevens voorbeeld. De gegevensset die u kiest, moet uw typische gegevens patronen vertegenwoordigen (mapstructuur, bestands patroon, gegevens schema, enzovoort) en is groot genoeg om de Kopieer prestaties te evalueren, bijvoorbeeld omdat het 10 minuten of langer duurt voordat de Kopieer activiteit is voltooid. Verzamel Details van de uitvoerings gegevens en prestatie kenmerken na het controleren van de [activiteit](copy-activity-monitoring.md).

2. **De prestaties van één Kopieer activiteit maximaliseren**:

   Om te beginnen, raden we u aan eerst de prestaties te maximaliseren met één Kopieer activiteit.

   - **Als de Kopieer activiteit wordt uitgevoerd op een Azure Integration runtime:** begin met standaard waarden voor de instellingen voor [gegevens integratie-eenheden (DIU)](#data-integration-units) en [parallelle kopieën](#parallel-copy) . 

   - **Als de Kopieer activiteit wordt uitgevoerd op een zelf-hostende Integration runtime:** u kunt het beste een toegewezen computer gebruiken die los staat van de server die als host fungeert voor de gegevens opslag voor de integratie-runtime. Begin met de standaard waarden voor de instelling voor [parallelle kopieën](#parallel-copy) en gebruik één knoop punt voor de zelf-hostende IR.  

   Voer een prestatie test uit en Bekijk de prestaties die zijn behaald, evenals de werkelijke waarden die worden gebruikt, zoals DIUs en parallelle kopieën. Raadpleeg de [controle activiteit](copy-activity-monitoring.md) voor het kopiëren van de resultaten en de gebruikte prestatie-instellingen en lees hoe u de prestaties van de [Kopieer activiteit kunt oplossen](copy-activity-performance-troubleshooting.md) om het knel punt op te sporen en op te lossen. 

   Herhaal de instructies voor het uitvoeren van extra prestatie tests die worden uitgevoerd na de richt lijnen voor probleem oplossing en afstemming. Wanneer de uitvoering van de single copy-activiteit geen betere door Voer heeft, kunt u overwegen om de geaggregeerde door voer te maximaliseren door meerdere exemplaren gelijktijdig te verwijzen naar stap 3.


3. **De geaggregeerde door Voer maximaliseren door meerdere exemplaren gelijktijdig uit te voeren:**

   Nu u de prestaties van één Kopieer activiteit hebt gemaximaliseerd, kunt u, als u nog niet de bovengrens voor door Voer van uw omgeving hebt bereikt: netwerk, brongegevens archief en doel gegevens opslag, meerdere Kopieer activiteiten tegelijk uitvoeren met de constructies van de ADF-controle stroom, zoals [voor elke lus](control-flow-for-each-activity.md). Raadpleeg het [kopiëren van bestanden uit meerdere containers](solution-template-copy-files-multiple-containers.md), [het migreren van gegevens van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md)of [bulksgewijs kopiëren met een controle tabel](solution-template-bulk-copy-with-control-table.md) oplossings sjablonen als algemeen voor beeld.

5. **Breid de configuratie uit naar uw volledige gegevensset.** Wanneer u tevreden bent met de resultaten en prestaties van de uitvoering, kunt u de definitie en pijp lijn uitvouwen om uw hele gegevensset te bedekken.

## <a name="troubleshoot-copy-activity-performance"></a>Prestaties van de Kopieer activiteit oplossen

Volg de [stappen voor het afstemmen](#performance-tuning-steps) van de prestaties om de prestatie test voor uw scenario te plannen en uit te voeren. Meer informatie over het oplossen van problemen met het prestatie probleem bij het uitvoeren van een Kopieer activiteit in Azure Data Factory van het [oplossen van problemen met de prestaties van Kopieer activiteiten](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Functies voor het optimaliseren van prestaties kopiëren

Azure Data Factory biedt de volgende functies voor prestatie optimalisatie:

- [Eenheden voor gegevensintegratie](#data-integration-units)
- [Schaal baarheid van zelf-hostende Integration runtime](#self-hosted-integration-runtime-scalability)
- [Parallelle kopie](#parallel-copy)
- [Gefaseerde kopie](#staged-copy)

### <a name="data-integration-units"></a>Eenheden voor gegevensintegratie

Een gegevens integratie-eenheid is een meting die de kracht vertegenwoordigt (een combi natie van CPU, geheugen en netwerk bron toewijzing) van één eenheid in Azure Data Factory. De gegevens integratie-eenheid is alleen van toepassing op [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), maar niet [zelf-hostende Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime). [Meer informatie](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Schaal baarheid van zelf-hostende Integration runtime

Als u de host wilt verg Roten gelijktijdige werk belasting of hogere prestaties wilt, kunt u de zelf-hostende Integration Runtime schalen of uitschalen. [Meer informatie](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Parallelle kopie

U kunt een parallelle kopie instellen om aan te geven in welke parallellisme u de Kopieer activiteit wilt gebruiken. U kunt deze eigenschap beschouwen als het maximum aantal threads in de Kopieer activiteit dat vanuit uw bron wordt gelezen of op parallelle gegevens opslag wordt geschreven. [Meer informatie](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Gefaseerde kopie

Wanneer u gegevens uit een brongegevens archief naar een Sink-gegevens archief kopieert, kunt u ervoor kiezen om Blob-opslag te gebruiken als een tijdelijke faserings opslag. [Meer informatie](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md)
- [Functies voor het optimaliseren van de activiteit prestaties](copy-activity-performance-features.md)
- [Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)
