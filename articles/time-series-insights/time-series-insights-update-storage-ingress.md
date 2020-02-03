---
title: Gegevens opslag en inkomen in Preview-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over gegevens opslag en inkomend verkeer in Azure Time Series Insights preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714283"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview

In dit artikel worden updates beschreven voor de gegevens opslag en het binnenkomen van Azure Time Series Insights preview. Hierin worden de onderliggende opslag structuur, bestands indeling en de time series-ID-eigenschap beschreven. Ook worden het onderliggende ingangs proces, de aanbevolen procedures en de actuele preview-beperkingen beschreven.

## <a name="data-ingress"></a>Gegevens binnenkomend

Uw Azure Time Series Insights omgeving bevat een opname-engine voor het verzamelen, verwerken en opslaan van gegevens in de tijd reeks. Bij het plannen van uw omgeving moet u rekening houden met een aantal aandachtspunten om ervoor te zorgen dat alle binnenkomende gegevens worden verwerkt, en om een hoge ingangs schaal te bewerkstelligen en de opname latentie te minimaliseren (de tijd die door de TSI wordt gebruikt om gegevens van de gebeurtenis te lezen en te verwerken) Bron). 

In Time Series Insights preview bepaalt het beleid voor gegevens inkomend verkeer welke gegevens kunnen worden gebrond en welke indeling de gegevens moeten hebben.

### <a name="ingress-policies"></a>Ingangs beleid

#### <a name="event-sources"></a>Gebeurtenis bronnen

Time Series Insights preview ondersteunt de volgende gebeurtenis bronnen:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights preview ondersteunt Maxi maal twee gebeurtenis bronnen per instantie.

> [!WARNING] 
> * U kunt een hoge initiële latentie ervaren wanneer u een gebeurtenis bron koppelt aan uw preview-omgeving. 
> De gebeurtenis bron latentie is afhankelijk van het aantal gebeurtenissen dat zich momenteel in uw IoT Hub of event hub bevinden.
> * Hoge latentie wordt weer gegeven nadat de bron gegevens van de gebeurtenis voor het eerst zijn opgenomen. Neem contact met ons op door een ondersteunings ticket in te dienen via de Azure Portal als u ervaring hebt met een hoge latentie.

#### <a name="supported-data-format-and-types"></a>Ondersteunde gegevens indeling en-typen

Azure Time Series Insights ondersteunt UTF8-gecodeerde JSON die via Azure IoT Hub of Azure Event Hubs is verzonden. 

Hieronder ziet u de lijst met ondersteunde gegevens typen.

| Gegevenstype | Beschrijving |
|-----------|------------------|-------------|
| bool      |   Een gegevens type met een van de twee statussen: True of false.       |
| Datum/tijd    |   Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum en tijd van de dag. Datum-en tijd notatie moeten de ISO 8601-indeling hebben.      |
| double    |   Een Double-Precision 64-bits IEEE 754 drijvende komma
| tekenreeks    |   Tekst waarden, bestaande uit Unicode-tekens.          |

#### <a name="objects-and-arrays"></a>Objecten en matrices

U kunt complexe typen zoals objecten en arrays verzenden als onderdeel van de nettolading van de gebeurtenis, maar uw gegevens worden op een afvlakkings proces ondergaan wanneer het wordt opgeslagen. Zie voor meer informatie over het vorm geven van de JSON-gebeurtenissen en Details over het complexe type en geneste object afvlakking de pagina voor de [vorm van JSON voor](./time-series-insights-update-how-to-shape-events.md)binnenkomend en query.


### <a name="ingress-best-practices"></a>Best practices voor binnenkomend verkeer

U wordt aangeraden de volgende aanbevolen procedures te gebruiken:

* Configureer Time Series Insights en uw IoT Hub of event hub in dezelfde regio om de netwerk opname latentie te verminderen.
* Plan uw schaal behoeften door uw verwachte opname frequentie te berekenen en te controleren of deze binnen de hieronder vermelde ondersteunde tarieven valt
* Meer informatie over het optimaliseren en vorm geven van uw JSON-gegevens, evenals de huidige beperkingen in de preview-versie, door [te lezen hoe u JSON voor ingress en query's kunt bepalen](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Inkomend schalen en beperkingen in Preview

#### <a name="per-environment-limitations"></a>Beperkingen per omgeving

Over het algemeen worden de ingangs snelheden weer gegeven als de factor van het aantal apparaten in uw organisatie, de gebeurtenis emissie frequentie en de grootte van elke gebeurtenis:

*  **Aantal apparaten** × **gebeurtenis emissie frequentie** × **grootte van elke gebeurtenis**.

Time Series Insights preview kan standaard binnenkomende gegevens opnemen met een snelheid van Maxi maal 1 MB per seconde (MBps) **per TSI-omgeving**. Neem contact met ons op als deze niet voldoet aan uw vereisten, kunnen we Maxi maal 16 MBps voor een omgeving ondersteunen door een ondersteunings ticket in te dienen in het Azure Portal.
 
Voor beeld 1: contoso-verzen ding heeft 100.000 apparaten die drie keer per minuut een gebeurtenis verzenden. De grootte van een gebeurtenis is 200 bytes. Ze gebruiken een event hub met 4 partities als de TSI gebeurtenis bron.
De opname frequentie voor de omgeving van de TSI is: 100.000 apparaten * 200 bytes/gebeurtenis * (3/60 gebeurtenis/sec) = 1 MBps.
De opname frequentie per partitie zou 0,25 MBps zijn.
De opname snelheid van Contoso-verzen ding valt binnen de limiet voor de schaal van de preview-versie.
 
Voor beeld 2: contoso vloot Analytics heeft 60.000 apparaten die elke seconde een gebeurtenis verzenden. Ze gebruiken een IoT Hub 24 aantal partities van 4 als de TSI gebeurtenis bron. De grootte van een gebeurtenis is 200 bytes.
Het opname tempo van de omgeving is: 20.000 apparaten * 200 bytes/gebeurtenis * 1 gebeurtenis/SEC = 4 MBps.
Het per partitie aantal zou 1 MBps zijn.
Contoso vloot Analytics moet een aanvraag indienen bij een TSI via de Azure Portal voor een specifieke omgeving om deze schaal te verzorgen.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hub-partities en limieten per partitie

Bij het plannen van uw TSI-omgeving is het belang rijk dat u rekening houdt met de configuratie van de bron (nen) van de gebeurtenis die u wilt verbinden met de TSI. Zowel Azure IoT Hub als Event Hubs gebruiken partities om een horizontale schaal in te scha kelen voor gebeurtenis verwerking.  Een partitie is een geordende reeks gebeurtenissen die in een hub wordt bewaard. Het aantal partities wordt ingesteld tijdens het maken van de IoT-of Event Hubs-fase en kan niet worden gewijzigd. Meer informatie over het bepalen van het aantal partities vindt u in de veelgestelde vragen over de Event Hubs? Voor TSI-omgevingen die gebruikmaken van IoT Hub, hebben meestal de meeste IoT-hubs 4 partities nodig. Of u nu een nieuwe hub wilt maken voor uw omgeving met de TSI of een bestaand wilt gebruiken, moet u de opname frequentie per partitie berekenen om te bepalen of deze zich binnen de preview-limieten bevindt. De TSI preview heeft momenteel een limiet van 0,5 MB/s **per partitie** . Gebruik de voor beelden hieronder als referentie en houd rekening met de volgende IoT Hub-specifieke overweging als u een IoT Hub gebruiker bent.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifieke overwegingen

Wanneer een apparaat wordt gemaakt in IoT Hub wordt het toegewezen aan een partitie en wordt de partitie toewijzing niet gewijzigd. Op die manier kan IoT Hub de volg orde van gebeurtenissen garanderen. Dit heeft echter gevolgen voor de TSI als een stroomafwaartse lezer in bepaalde scenario's. Wanneer berichten van meerdere apparaten worden doorgestuurd naar de hub met behulp van dezelfde gateway apparaat-ID, zullen ze in dezelfde partitie arriveren, waardoor de limiet voor de schaal per partitie mogelijk wordt overschreden. 

**Impact**: als een enkele partitie een aanhoudende snelheid van opname in de preview-beperking heeft, is het mogelijk dat de TSI-lezer niet meer kan worden onderschept voordat de IOT hub gegevens Bewaar periode is overschreden. Dit leidt tot verlies van gegevens.

We raden het volgende aan: 

* Bereken de opname frequentie per omgeving en per partitie voordat u de oplossing implementeert
* Zorg ervoor dat uw IoT Hub-apparaten (en dus partities) gelijkmatig zijn verdeeld over de uitbrei ding die mogelijk is

> [!WARNING]
> Voor omgevingen die IoT Hub als gebeurtenis bron gebruiken, berekent u de opname snelheid met het aantal Hub-apparaten dat wordt gebruikt om ervoor te zorgen dat de snelheid lager is dan de limiet van 0,5 MBps per partitie in de preview-periode.

  ![IoT Hub partitie diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

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
> Tijdens de preview-periode kan het tot 60 seconden duren voordat gegevens beschikbaar worden. Als u na 60 seconden een langere latentie ondervindt, kunt u een ondersteunings ticket indienen via de Azure Portal.

## <a name="azure-storage"></a>Azure Storage

In deze sectie worden Azure Storage informatie beschreven die relevant is voor Azure Time Series Insights preview.

Lees de [Inleiding tot opslag-blobs](../storage/blobs/storage-blobs-introduction.md)voor een uitgebreide beschrijving van Azure Blob-opslag.

### <a name="your-storage-account"></a>Uw opslag account

Wanneer u een Time Series Insights preview-functie voor betalen per gebruik maakt, wordt een Azure Storage algemeen v1-BLOB-account gemaakt als uw langlopende koel winkel.  

Time Series Insights preview publiceert Maxi maal twee exemplaren van elke gebeurtenis in uw Azure Storage-account. De eerste kopie heeft gebeurtenissen die zijn geordend op opname tijd en is altijd behouden, zodat u andere services kunt gebruiken om deze te openen. U kunt Spark, Hadoop en andere vertrouwde hulpprogram ma's gebruiken om de onbewerkte Parquet-bestanden te verwerken. 

Time Series Insights Preview opnieuw partitioneert de Parquet-bestanden om te optimaliseren voor de Time Series Insights-query. Deze opnieuw gepartitioneerde kopie van de gegevens wordt ook opgeslagen.

Tijdens de open bare preview worden gegevens voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights-blobs schrijven en bewerken

Om de query prestaties en de beschik baarheid van gegevens te garanderen, moet u geen blobs bewerken of verwijderen die Time Series Insights preview maakt.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Toegang tot en exporteren van gegevens vanuit Time Series Insights preview

U kunt toegang krijgen tot gegevens die worden weer gegeven in de Time Series Insights preview Explorer om te gebruiken in combi natie met andere services. U kunt uw gegevens bijvoorbeeld gebruiken om een rapport te maken in Power BI of om een machine learning model te trainen met behulp van Azure Machine Learning Studio. U kunt uw gegevens ook gebruiken om uw Jupyter-notebooks te transformeren, te visualiseren en te model leren.

U kunt op drie manieren toegang krijgen tot uw gegevens:

* Vanuit de Time Series Insights preview Explorer. U kunt gegevens exporteren als een CSV-bestand vanuit de Explorer. Lees [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md)voor meer informatie.
* Vanuit de Time Series Insights preview-API met behulp van de query Get Events. Lees voor meer informatie over deze API de [Time Series-query](./time-series-insights-update-tsq.md).
* Rechtstreeks vanuit een Azure Storage-account. U hebt lees toegang nodig tot het account dat u gebruikt voor toegang tot uw Time Series Insights preview-gegevens. Lees [toegang tot de resources van uw opslag account beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.

#### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder de Time Series Insights Preview-bestanden niet. Gerelateerde gegevens alleen beheren vanuit Time Series Insights preview.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet bestands indeling en mapstructuur

Parquet is een open-source bestands indeling die is ontworpen voor efficiënte opslag en prestaties. Time Series Insights preview gebruikt Parquet om de volgende redenen. Het partitioneert gegevens op tijd reeks-ID voor query prestaties op schaal.  

Lees de [Parquet-documentatie](https://parquet.apache.org/documentation/latest/)voor meer informatie over het bestands type Parquet.

In Time Series Insights preview worden kopieën van uw gegevens als volgt opgeslagen:

* De eerste, eerste kopie wordt gepartitioneerd op basis van de opname tijd en slaat ruwweg gegevens op in volg orde van aankomst. De gegevens bevinden zich in de map `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw gepartitioneerde kopie wordt gepartitioneerd door een groepering van Time Series-Id's en bevindt zich in de map `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

In beide gevallen komen de tijd waarden overeen met de aanmaak tijd van de blob. De gegevens in de map `PT=Time` blijven bewaard. Gegevens in de map `PT=TsId` worden gedurende een bepaalde periode geoptimaliseerd voor query's en blijven niet statisch.

> [!NOTE]
> * `<YYYY>` is toegewezen aan een jaar representatie van vier cijfers.
> * `<MM>` is toegewezen aan een maand weergave met twee cijfers.
> * `<YYYYMMDDHHMMSSfff>` is toegewezen aan een tijds tempel aanduiding met een jaar van vier cijfers (`YYYY`), een maand van twee cijfers (`MM`), een tweecijferige dag (`DD`), een uur van twee cijfers (`HH`), een minuut van twee cijfers (`MM`), een tweede cijfer (`SS`) en een milliseconde van drie cijfers (`fff`).

Time Series Insights preview-gebeurtenissen worden als volgt toegewezen aan de Parquet-bestands inhoud:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Elke rij bevat de **Time Stamp** -kolom met een tijds tempel van de gebeurtenis. De eigenschap time stamp is nooit null. De standaard waarde is een time-outwaarde voor een **gebeurtenis** als de eigenschap time stamp niet is opgegeven in de bron van de gebeurtenis. De tijds tempel is altijd in UTC.
* Elke rij bevat de time series-ID-kolom (men) zoals deze is gedefinieerd wanneer de Time Series Insights omgeving wordt gemaakt. De naam van de eigenschap bevat het `_string` achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden verzonden, worden toegewezen aan kolom namen die eindigen op `_string` (teken reeks), `_bool` (Boolean), `_datetime` (datetime) of `_double` (double), afhankelijk van het type eigenschap.
* Dit toewijzings schema is van toepassing op de eerste versie van de bestands indeling, waarnaar wordt verwezen als **V = 1**. Als deze functie zich ontwikkelt, kan de naam worden verhoogd.

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u JSON voor ingangen en query's kunt vorm](./time-series-insights-update-how-to-shape-events.md)geven.

- Meer informatie over de nieuwe [gegevens modellering](./time-series-insights-update-tsm.md).
