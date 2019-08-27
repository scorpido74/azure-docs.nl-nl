---
title: Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview | Microsoft Docs
description: Informatie over gegevens opslag en inkomend verkeer in Azure Time Series Insights preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 98baa8d3f951a8922bcd1f40449fa26840f3a3c4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051469"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview

In dit artikel worden wijzigingen in de gegevens opslag en inkomend verkeer van Azure Time Series Insights preview beschreven. Hierin worden de onderliggende opslag structuur, bestands indeling en de time series-ID-eigenschap beschreven. In het artikel worden ook het onderliggende ingangs proces, de door Voer en de beperkingen beschreven.

## <a name="data-ingress"></a>Gegevens binnenkomend

Azure Time Series Insights beleid voor het inkomen van gegevens bepaalt u waar gegevens kunnen worden gebrond en in welke indeling.

[![Overzicht van Time Series-model](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Ingangs beleid

De preview-versie van Time Series Insights ondersteunt dezelfde gebeurtenis bronnen en bestands typen die Time Series Insights momenteel ondersteunt:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights ondersteunt JSON die via Azure IoT Hub of Azure Event Hubs is verzonden. Meer informatie over het optimaliseren van uw IoT JSON-gegevens vindt u in de [vorm van JSON](./time-series-insights-send-events.md#json).

### <a name="data-storage"></a>Gegevensopslag

Wanneer u een Time Series Insights preview-versie van betalen per gebruik-SKU maakt, maakt u twee resources:

* Een Time Series Insights omgeving.
* Een Azure Storage v1-account voor algemeen gebruik waarop de gegevens worden opgeslagen.

Het Time Series Insights-voor beeld maakt gebruik van Azure Blob Storage met het bestands type Parquet. Time Series Insights beheert alle gegevens bewerkingen, zoals het maken van blobs, het indexeren en partitioneren van de gegevens in het Azure-opslag account. U maakt deze blobs met behulp van een Azure-opslag account.

Net als andere Azure Storage-blobs, kunt u met Time Series Insights gemaakte blobs ze lezen en ernaar schrijven om verschillende integratie scenario's te ondersteunen.

### <a name="data-availability"></a>Beschik baarheid van gegevens

De Time Series Insights preview indexeert gegevens met behulp van een optimalisatie strategie voor de grootte van de blob. Gegevens zijn beschikbaar voor query's na het indexeren, die zijn gebaseerd op de hoeveelheid gegevens die binnenkomen in en op welke snelheid.

> [!IMPORTANT]
> * Met de Time Series Insights algemene Beschik baarheid (GA) worden gegevens binnen 60 seconden na het aankomen van een gebeurtenis bron beschikbaar gemaakt.
> * Tijdens de preview periode wordt een langere termijn verwacht voordat de gegevens beschikbaar worden gemaakt.
> * Neem contact met ons op als u een belang rijke latentie ondervindt.

### <a name="scale"></a>Schalen

De preview-versie van Time Series Insights biedt ondersteuning voor een eerste ingangs schaal van Maxi maal 1 Mega byte per seconde (Mbps) per omgeving. Verbeterde ondersteuning voor schalen is actief. We gaan onze documentatie bijwerken om deze verbeteringen weer te geven.

## <a name="parquet-file-format"></a>Parquet-bestands indeling

Parquet is een kolom gerichte indeling voor gegevens bestanden die is ontworpen voor:

* Interoperabiliteit
* Efficiënte ruimte
* Query efficiëntie

Time Series Insights hebt gekozen voor Parquet omdat het efficiënte gegevens compressie en coderings schema's biedt, met verbeterde prestaties waarmee complexe gegevens in bulk kunnen worden verwerkt.

Raadpleeg de [Parquet-documentatie](https://parquet.apache.org/documentation/latest/)voor meer informatie over het bestands type Parquet.

Zie [ondersteunde bestands typen in azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format)voor meer informatie over de bestands indeling Parquet in Azure.

### <a name="event-structure-in-parquet"></a>Structuur van gebeurtenissen in Parquet

Met Time Series Insights worden kopieën van blobs gemaakt en opgeslagen in de volgende twee indelingen:

1. De eerste, eerste kopie wordt gepartitioneerd op aankomst tijd:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Aanmaak tijd van BLOB voor blobs gepartitioneerd door aankomst tijd.

1. De tweede, opnieuw gepartitioneerde kopie wordt gepartitioneerd door een dynamische groepering van de tijd reeks-ID:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Minimale gebeurtenis-tijds tempel in een BLOB voor blobs gepartitioneerd door de time series-ID.

> [!NOTE]
> * `<YYYY>`wordt toegewezen aan een jaar representatie van vier cijfers.
> * `<MM>`wordt toegewezen aan een maand weergave van twee cijfers.
> * `<YYYYMMDDHHMMSSfff>`wordt toegewezen aan een tijds tempel weergave met`YYYY`een jaar van vier cijfers (), een maand van 2 cijfers (`MM`), 2`DD`-cijferig dag (), 2`HH`-cijferig uur (), 2`MM`-cijferig minuut (), 2`SS`-cijfer seconde () en 3-cijfer milliseconde`fff`().

Time Series Insights gebeurtenissen worden als volgt toegewezen aan de Parquet-bestands inhoud:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Ingebouwde **Time Stamp** -kolom met een gebeurtenis tijds tempel. De tijds tempel eigenschap is nooit null. Standaard wordt de **gebeurtenis bron** in de wachtrij geplaatst als de time stamp-eigenschap niet is opgegeven in de bron van de gebeurtenis. De tijds tempel is UTC. 
* Alle andere eigenschappen die worden toegewezen aan kolommen end with `_string` (String), `_bool` (Booleaans), `_datetime` (datetime) en `_double` (double), afhankelijk van het type eigenschap.
* Dat is het toewijzings schema voor de eerste versie van de bestands indeling, waarnaar wordt verwezen als **V = 1**. Wanneer deze functie wordt ontwikkeld, wordt de naam verhoogd naar **V = 2**, **v = 3**, enzovoort.

## <a name="azure-storage"></a>Azure Storage

In deze sectie worden Azure Storage gegevens beschreven die relevant zijn voor Azure Time Series Insights.

Lees de [Inleiding tot opslag](../storage/blobs/storage-blobs-introduction.md)-blobs voor een uitgebreide beschrijving van de Azure Blob Storage-service.

### <a name="your-storage-account"></a>Uw opslag account

Wanneer u een Time Series Insights betalen per gebruik-omgeving maakt, maakt u twee resources: een Time Series Insights omgeving en een Azure Storage v1-account voor algemeen gebruik waarop de gegevens worden opgeslagen. We hebben ervoor gekozen om Azure Storage algemeen v1 de standaard bron te maken vanwege de interoperabiliteit, prijs en prestaties.

Time Series Insights publiceert Maxi maal twee exemplaren van elke gebeurtenis in uw Azure Storage-account. De eerste kopie blijft altijd behouden, zodat u deze snel kunt opvragen met behulp van andere services. U kunt eenvoudig Spark-, Hadoop-en andere vertrouwde hulpprogram ma's gebruiken in time series-Id's via onbewerkte Parquet-bestanden, omdat deze engines basis filters voor bestands namen ondersteunen. Groepeer-blobs per jaar en maand is een handige manier om blobs binnen een specifiek tijds bestek voor een aangepaste taak weer te geven.

Daarnaast Time Series Insights de Parquet-bestanden opnieuw partitioneren om te optimaliseren voor de Time Series Insights-Api's. Het bestand dat het laatst opnieuw is gepartitioneerd, wordt ook opgeslagen.

Tijdens de open bare preview worden gegevens voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights-blobs schrijven en bewerken

Bewerk of verwijder geen blobs die zijn gemaakt door Time Series Insights om de query prestaties en de beschik baarheid van gegevens te garanderen.

> [!TIP]
> Time Series Insights prestaties kunnen nadelig worden beïnvloed als u te vaak uw blobs leest of schrijft.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Toegang tot en exporteren van gegevens vanuit Time Series Insights preview

U kunt toegang krijgen tot gegevens die zijn opgeslagen in de Time Series Insights preview Explorer om te gebruiken in combi natie met andere services. U kunt bijvoorbeeld uw gegevens gebruiken om te rapporteren in Power BI, machine learning uit te voeren met behulp van Azure Machine Learning Studio, of om te gebruiken in een notebook-toepassing met Jupyter-notebooks.

U kunt op drie manieren toegang krijgen tot uw gegevens:

* Vanuit de Time Series Insights preview Explorer: u kunt gegevens exporteren als een CSV-bestand vanuit de Time Series Insights preview Explorer. Zie [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md)voor meer informatie.
* Van de preview-Api's van Time Series Insights: het API-eind punt `/getRecorded`kan worden bereikt op. Zie [Time Series query](./time-series-insights-update-tsq.md)(Engelstalig) voor meer informatie over deze API.
* Rechtstreeks vanuit een Azure-opslag account (hieronder).

#### <a name="from-an-azure-storage-account"></a>Vanuit een Azure-opslag account

* U hebt lees toegang nodig tot het account dat u gebruikt voor toegang tot uw Time Series Insights gegevens. Zie [toegang tot de resources van uw opslag account beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.
* Zie [een Azure-oplossing kiezen voor gegevens overdracht](../storage/common/storage-choose-data-transfer-solution.md)voor meer informatie over directe manieren om gegevens te lezen uit Azure Blob-opslag.
* Gegevens exporteren uit een Azure-opslag account:
    * Zorg er eerst voor dat uw account voldoet aan de vereiste vereisten voor het exporteren van gegevens. Zie [vereisten voor opslag importeren en exporteren](../storage/common/storage-import-export-requirements.md)voor meer informatie.
    * Zie [gegevens uit blobs importeren en exporteren](../storage/common/storage-import-export-data-from-blobs.md)voor meer informatie over andere manieren om gegevens te exporteren uit uw Azure-opslag account.

### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder geen blobs. Het is niet alleen handig om een record van uw gegevens te controleren en te onderhouden, maar de Time Series Insights preview beheert BLOB-meta gegevens binnen elke blob.

## <a name="partitions"></a>Partities

Elke Time Series Insights-voorbeeld omgeving moet een **tijd reeks-ID-** eigenschap hebben en een **Time Stamp** -eigenschap die deze uniek kan identificeren. Uw tijd reeks-ID fungeert als een logische partitie voor uw gegevens en geeft de Time Series Insights voorbeeld omgeving een natuurlijke grens voor het distribueren van gegevens over fysieke partities. Fysieke partities worden beheerd door Time Series Insights preview in een Azure Storage-account.

Time Series Insights gebruikt dynamische partitionering om opslag-en query prestaties te optimaliseren door partities te verwijderen en opnieuw te maken. De Time Series Insights preview-algoritme voor dynamische partitionering probeert te voor komen dat één fysieke partitie gegevens voor meerdere, afzonderlijke logische partities kan hebben. Met andere woorden: het partitioneren houdt alle gegevens bij die specifiek zijn voor een enkele tijd reeks-ID die uitsluitend in Parquet-bestanden aanwezig is zonder te worden geverlaat met andere time series-Id's. Het algoritme voor dynamische partitionering probeert ook de oorspronkelijke volg orde van gebeurtenissen binnen één tijd reeks-ID te bewaren.

In eerste instantie worden gegevens gepartitioneerd door de tijds tempel zodat één logische partitie binnen een bepaald tijds bereik kan worden verdeeld over meerdere fysieke partities. Eén fysieke partitie kan ook veel of alle logische partities bevatten. Vanwege beperkingen van de Blob-grootte, zelfs bij een optimale partitionering, kan één logische partitie meerdere fysieke partities in beslag nemen.

> [!NOTE]
> De time stamp-waarde is standaard het bericht dat in uw geconfigureerde gebeurtenis bron in de *wachtrij* is geplaatst.

Als u historische gegevens of batch berichten uploadt, wijst u de waarde die u wilt opslaan met uw gegevens toe aan de time stamp-eigenschap die is toegewezen aan de juiste tijds tempel. De tijds tempel eigenschap is hoofdletter gevoelig. Zie [Time Series model](./time-series-insights-update-tsm.md)voor meer informatie.

### <a name="physical-partitions"></a>Fysieke partities

Een fysieke partitie is een blok-blob die is opgeslagen in uw opslag account. De werkelijke grootte van de blobs kan variëren omdat de grootte afhankelijk is van het push tempo. We verwachten echter dat blobs ongeveer 20 MB tot 50 MB groot zijn. Deze verwachting leidde het Time Series Insights team om 20 MB te selecteren als de grootte om de query prestaties te optimaliseren. Deze omvang kan in de loop van de tijd veranderen, afhankelijk van de grootte van het bestand en de snelheid van het inkomen van gegevens.

> [!NOTE]
> * Blobs hebben een grootte van 20 MB.
> * Azure-blobs worden af en toe opnieuw gepartitioneerd voor betere prestaties door te verwijderen en opnieuw te maken.
> * Ook kunnen dezelfde Time Series Insights gegevens aanwezig zijn in twee of meer blobs.

### <a name="logical-partitions"></a>Logische partities

Een logische partitie is een partitie binnen een fysieke partitie waarin alle gegevens worden opgeslagen die zijn gekoppeld aan één partitie sleutel waarde. De Time Series Insights preview logisch partitioneert elke Blob op basis van twee eigenschappen:

* **Tijd reeks-id**: De partitie sleutel voor alle Time Series Insights gegevens in de gebeurtenis stroom en het model.
* **Tijds tempel**: De tijd op basis van de eerste ingang.

Het Time Series Insights preview biedt query's op basis van deze twee eigenschappen. Deze twee eigenschappen bieden ook de meest efficiënte methode voor het snel leveren van Time Series Insights gegevens.

Het is belang rijk dat u een geschikte tijd reeks-ID selecteert, omdat dit een onveranderbare eigenschap is. Zie [Time Series-Id's kiezen](./time-series-insights-update-how-to-id.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Lees de [Azure time series Insights preview-opslag en-ingang](./time-series-insights-update-storage-ingress.md).

- Meer informatie over de nieuwe [gegevens modellering](./time-series-insights-update-tsm.md).
