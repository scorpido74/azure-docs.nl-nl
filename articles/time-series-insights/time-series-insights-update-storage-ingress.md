---
title: Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview | Microsoft Docs
description: Informatie over gegevens opslag en inkomend verkeer in Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989695"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview

In dit artikel worden updates beschreven voor de gegevens opslag en het binnenkomen van Azure Time Series Insights preview. Hierin worden de onderliggende opslag structuur, bestands indeling en de time series-ID-eigenschap beschreven. Ook worden het onderliggende ingangs proces, de door Voer en de beperkingen beschreven.

## <a name="data-ingress"></a>Gegevens binnenkomend

In Time Series Insights preview bepaalt het beleid voor gegevens inkomend verkeer welke gegevens kunnen worden gebrond en welke indeling de gegevens moeten hebben.

[overzicht van![time series-model](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Ingangs beleid

Time Series Insights preview ondersteunt dezelfde gebeurtenis bronnen die momenteel door Time Series Insights worden ondersteund:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights preview ondersteunt Maxi maal twee gebeurtenis bronnen per instantie.
  
Azure Time Series Insights ondersteunt JSON die via Azure IoT Hub of Azure Event Hubs is verzonden. Meer informatie over het optimaliseren van uw IoT JSON-gegevens vindt u in de [vorm van JSON](./time-series-insights-send-events.md#supported-json-shapes).

### <a name="data-storage"></a>Gegevensopslag

Wanneer u een Time Series Insights preview-versie van betalen per gebruik-SKU maakt, maakt u twee Azure-resources:

* Een Time Series Insights-voorbeeld omgeving die eventueel warme opslag mogelijkheden kan bevatten.
* Een Azure Storage v1 BLOB-account voor algemeen gebruik voor koude gegevens opslag.

Gegevens in uw warme archief zijn alleen beschikbaar via de [Time Series-query](./time-series-insights-update-tsq.md) en de [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md). 

Met Time Series Insights preview worden uw koude Store-gegevens opgeslagen in Azure Blob-opslag in de [Parquet-bestands indeling](#parquet-file-format-and-folder-structure). Time Series Insights preview beheert deze koude Store-gegevens uitsluitend, maar u kunt deze rechtstreeks als standaard Parquet-bestanden lezen.

> [!WARNING]
> Als eigenaar van het Azure Blob Storage-account waar koud opgeslagen gegevens zich bevinden, hebt u volledige toegang tot alle gegevens in het account. Deze toegang omvat machtigingen voor schrijven en verwijderen. Bewerk of verwijder geen gegevens die Time Series Insights preview-schrijf bewerkingen, omdat dat gegevens verlies kan veroorzaken.

### <a name="data-availability"></a>Beschik baarheid van gegevens
Time Series Insights preview-partities en gegevens indexeren voor optimale query prestaties. Er worden gegevens beschikbaar voor query's na de index. De hoeveelheid gegevens die wordt opgenomen, kan van invloed zijn op deze Beschik baarheid.

> [!IMPORTANT]
> De release van de algemene Beschik baarheid (GA) van Time Series Insights zorgt ervoor dat gegevens in 60 seconden na het lezen van de gebeurtenis bron beschikbaar zijn. Tijdens de preview-periode kan het langer duren voordat de gegevens beschikbaar zijn. Neem contact met ons op als u na 60 seconden een belang rijke latentie ondervindt.

### <a name="scale"></a>Schaal

Time Series Insights preview biedt standaard ondersteuning voor een eerste ingangs schaal van Maxi maal 1 MB per seconde (MB/s) per omgeving. Een door Voer van Maxi maal 16 MB/s is beschikbaar als u dat nodig hebt. Als u ondersteuning voor uitgebreid schalen nodig hebt, kunt u contact met ons opnemen.

U kunt aanvullende mogelijkheden voor binnenkomend en schalen voor de gebeurtenis bron krijgen:

* [IoT Hub](../iot-hub/iot-hub-scaling.md)
* [Event Hubs](../event-hubs/event-hubs-scalability.md)

## <a name="azure-storage"></a>Azure Storage

In deze sectie worden Azure Storage informatie beschreven die relevant is voor Azure Time Series Insights preview.

Lees de [Inleiding tot opslag-blobs](../storage/blobs/storage-blobs-introduction.md)voor een uitgebreide beschrijving van Azure Blob-opslag.

### <a name="your-storage-account"></a>Uw opslag account

Wanneer u een Time Series Insights preview-functie voor betalen per gebruik maakt, wordt een Azure Storage algemeen v1-BLOB-account gemaakt als uw langlopende koel winkel.  

Time Series Insights preview publiceert Maxi maal twee exemplaren van elke gebeurtenis in uw Azure Storage-account. De eerste kopie heeft gebeurtenissen die zijn geordend op opname tijd en is altijd behouden, zodat u andere services kunt gebruiken om deze te openen. U kunt Spark, Hadoop en andere vertrouwde hulpprogram ma's gebruiken om de onbewerkte Parquet-bestanden te verwerken. 

Time Series Insights Preview opnieuw partitioneert de Parquet-bestanden om te optimaliseren voor de Time Series Insights-query. Deze opnieuw gepartitioneerde kopie van de gegevens wordt ook opgeslagen.

Tijdens de open bare preview worden gegevens voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights-blobs schrijven en bewerken

Om de query prestaties en de beschik baarheid van gegevens te garanderen, moet u geen blobs bewerken of verwijderen die Time Series Insights preview maakt.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Toegang tot en exporteren van gegevens vanuit Time Series Insights preview

U kunt toegang krijgen tot gegevens die worden weer gegeven in de Time Series Insights preview Explorer om te gebruiken in combi natie met andere services. U kunt uw gegevens bijvoorbeeld gebruiken om een rapport te maken in Power BI of om een machine learning model te trainen met behulp van Azure Machine Learning Studio. U kunt uw gegevens ook gebruiken om uw Jupyter-notebooks te transformeren, te visualiseren en te model leren.

U kunt op drie manieren toegang krijgen tot uw gegevens:

* Vanuit de Time Series Insights preview Explorer. U kunt gegevens exporteren als een CSV-bestand vanuit de Explorer. Zie [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md)voor meer informatie.
* Van de Time Series Insights preview-API. U kunt het API-eind punt bereiken op `/getRecorded`. Zie [Time Series query](./time-series-insights-update-tsq.md)(Engelstalig) voor meer informatie over deze API.
* Rechtstreeks vanuit een Azure-opslag account. U hebt lees toegang nodig tot het account dat u gebruikt voor toegang tot uw Time Series Insights preview-gegevens. Zie [toegang tot de resources van uw opslag account beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.

### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder de Time Series Insights Preview-bestanden niet. U moet gerelateerde gegevens alleen beheren vanuit Time Series Insights preview.

## <a name="parquet-file-format-and-folder-structure"></a>Parquet bestands indeling en mapstructuur

Parquet is een open-source bestands indeling die is ontworpen voor efficiënte opslag en prestaties. Time Series Insights preview gebruikt Parquet om de volgende redenen. Het partitioneert gegevens op tijd reeks-ID voor query prestaties op schaal.  

Zie de [Parquet-documentatie](https://parquet.apache.org/documentation/latest/)voor meer informatie over het bestands type Parquet.

In Time Series Insights preview worden kopieën van uw gegevens als volgt opgeslagen:

* De eerste, eerste kopie wordt gepartitioneerd op basis van de opname tijd en slaat ruwweg gegevens op in volg orde van aankomst. De gegevens bevinden zich in de map `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw gepartitioneerde kopie wordt gepartitioneerd door een groepering van Time Series-Id's en bevindt zich in de map `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

In beide gevallen komen de tijd waarden overeen met de aanmaak tijd van de blob. De gegevens in de map `PT=Time` blijven bewaard. Gegevens in de map `PT=TsId` worden gedurende een bepaalde periode geoptimaliseerd voor query's en blijven niet statisch.

> [!NOTE]
> * `<YYYY>` is toegewezen aan een jaar representatie van vier cijfers.
> * `<MM>` is toegewezen aan een maand weergave met twee cijfers.
> * `<YYYYMMDDHHMMSSfff>` is toegewezen aan een tijds tempel weergave met vier cijfers per jaar (`YYYY`), een maand van twee cijfers (`MM`), een tweecijferige dag (`DD`), een uur van twee cijfers (`HH`), een minuut (`MM`) van twee cijfers, een tweede cijfer (`SS`) en een milliseconde van drie cijfers (`fff`).

Time Series Insights preview-gebeurtenissen worden als volgt toegewezen aan de Parquet-bestands inhoud:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Elke rij bevat de **Time Stamp** -kolom met een tijds tempel van de gebeurtenis. De eigenschap time stamp is nooit null. De standaard waarde is een time-outwaarde voor een **gebeurtenis** als de eigenschap time stamp niet is opgegeven in de bron van de gebeurtenis. De tijds tempel is altijd in UTC.
* Elke rij bevat de kolom tijd reeks-ID zoals gedefinieerd wanneer de Time Series Insights omgeving wordt gemaakt. De naam van de eigenschap bevat het `_string` achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden verzonden, worden toegewezen aan kolom namen die eindigen op `_string` (teken reeks), `_bool` (Boolean), `_datetime` (datetime) of `_double` (double), afhankelijk van het type eigenschap.
* Dit toewijzings schema is van toepassing op de eerste versie van de bestands indeling, waarnaar wordt verwezen als **V = 1**. Als deze functie zich ontwikkelt, kan de naam worden verhoogd.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure time series Insights voor beeld-opslag en](./time-series-insights-update-storage-ingress.md)-inkomend verkeer.

- Meer informatie over de nieuwe [gegevens modellering](./time-series-insights-update-tsm.md).
