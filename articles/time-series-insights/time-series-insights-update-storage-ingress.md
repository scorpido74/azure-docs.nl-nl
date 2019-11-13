---
title: Gegevens opslag en inkomen in Preview-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over gegevens opslag en inkomend verkeer in Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 62ee248c06d2b26b935f72b3bb73cf708f949c72
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014713"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview

In dit artikel worden updates beschreven voor de gegevens opslag en het binnenkomen van Azure Time Series Insights preview. Hierin worden de onderliggende opslag structuur, bestands indeling en de time series-ID-eigenschap beschreven. Ook worden het onderliggende ingangs proces, de aanbevolen procedures en de actuele preview-beperkingen beschreven.

## <a name="data-ingress"></a>Gegevens binnenkomend

Uw Azure Time Series Insights omgeving bevat een opname-engine voor het verzamelen, verwerken en opslaan van gegevens in de tijd reeks. Bij het plannen van uw omgeving moet u rekening houden met een aantal aandachtspunten om ervoor te zorgen dat alle binnenkomende gegevens worden verwerkt, en om een hoge ingangs schaal te bewerkstelligen en de opname latentie te minimaliseren (de tijd die door de TSI wordt gebruikt om gegevens van de gebeurtenis te lezen en te verwerken) Bron). In Time Series Insights preview bepaalt het beleid voor gegevens inkomend verkeer welke gegevens kunnen worden gebrond en welke indeling de gegevens moeten hebben.

### <a name="ingress-policies"></a>Ingangs beleid

Time Series Insights preview ondersteunt de volgende gebeurtenis bronnen:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights preview ondersteunt Maxi maal twee gebeurtenis bronnen per instantie.
  
Azure Time Series Insights ondersteunt JSON die via Azure IoT Hub of Azure Event Hubs is verzonden.

> [!WARNING] 
> Wanneer u een nieuwe gebeurtenis bron aan uw Time Series Insights-voorbeeld omgeving koppelt, afhankelijk van het aantal gebeurtenissen dat zich momenteel in uw IoT Hub of event hub bevindt, kan er een hoge initiële opname latentie optreden. Wanneer gegevens worden opgenomen, moet u deze hoge latentie verwachten, maar als uw ervaring anders aangeeft, neemt u contact met ons op door een ondersteunings ticket in te dienen via de Azure Portal.

## <a name="ingress-best-practices"></a>Best practices voor binnenkomend verkeer

U wordt aangeraden de volgende aanbevolen procedures te gebruiken:

* Configureer Time Series Insights en een IoT-hub of Event Hub in dezelfde regio. Hiermee wordt de opname latentie verminderd die is ontstaan vanwege het netwerk.
* Plan uw schaal behoeften door uw verwachte opname frequentie te berekenen en te controleren of deze binnen de hieronder vermelde ondersteunde tarieven valt
* Meer informatie over het optimaliseren en vorm geven van uw JSON-gegevens, evenals de huidige beperkingen in de preview-versie, door [te lezen hoe u JSON voor ingress en query's kunt bepalen](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Inkomend schalen en beperkingen in Preview

Time Series Insights preview biedt standaard ondersteuning voor een eerste ingangs schaal van Maxi maal 1 MB per seconde (MB/s) per omgeving. Als dat nodig is, kunt u de door Voer van Maxi maal 16 MB/s beschikbaar maken door een ondersteunings ticket in te dienen in het Azure Portal als dit nodig is. Daarnaast is er een limiet van 0,5 MB/s per partitie. Dit heeft gevolgen voor klanten die IoT Hub met name, gezien de affiniteit tussen een IoT Hub apparaat een partitie. In scenario's waarbij één gateway apparaat berichten doorstuurt naar de hub met behulp van de eigen apparaat-ID en connection string, is er een risico dat de limiet van 0,5 MB/s wordt bereikt, omdat berichten in één partitie worden ontvangen, zelfs als de nettolading van de gebeurtenis verschillende TS bevat Id's. In het algemeen wordt ingangs snelheid weer gegeven als een factor van het aantal apparaten in uw organisatie, een gebeurtenis emissie frequentie en de grootte van een gebeurtenis. Bij het berekenen van opname frequentie moeten IoT Hub gebruikers het aantal hub-verbindingen gebruiken dat in gebruik is, in plaats van het totaal aantal apparaten in de organisatie. Verbeterde ondersteuning voor schalen is actief. Deze documentatie wordt bijgewerkt om deze verbeteringen weer te geven. 

> [!WARNING]
> Voor omgevingen met IoT Hub als gebeurtenis bron, berekent u de opname frequentie met het aantal Hub-apparaten dat in gebruik is.

Raadpleeg de volgende koppelingen voor meer informatie over doorvoer eenheden en partities:

* [IoT Hub schaal](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event hub-schaal](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event hub-partities](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

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
> De aanstaande release van de algemene Beschik baarheid (GA) van Time Series Insights zorgt ervoor dat gegevens in 60 seconden na het lezen van de gebeurtenis bron beschikbaar zijn. Tijdens de preview-periode kan het langer duren voordat gegevens beschikbaar zijn. Als u na 60 seconden een langere latentie ondervindt, kunt u een ondersteunings ticket indienen via de Azure Portal.

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
