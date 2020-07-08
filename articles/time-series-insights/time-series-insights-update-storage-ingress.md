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
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: d3bfb589ec4c152b136e8e1f432864b719c97d58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509316"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Gegevens opslag en inkomend verkeer in Azure Time Series Insights preview

In dit artikel worden updates beschreven voor de gegevens opslag en het binnenkomen van Azure Time Series Insights preview. Hierin worden de onderliggende opslag structuur, bestands indeling en de time series-ID-eigenschap beschreven. Het onderliggende ingangs proces, aanbevolen procedures en de actuele preview-beperkingen worden ook beschreven.

## <a name="data-ingress"></a>Gegevens binnenkomend

Uw Azure Time Series Insights omgeving bevat een *opname-engine* voor het verzamelen, verwerken en opslaan van gegevens in de tijd reeks.

Er zijn enkele aandachtspunten om ervoor te zorgen dat alle binnenkomende gegevens worden verwerkt, om de schaal van een hoge ingang te vervolledigen en de *opname latentie* te minimaliseren (de tijd die nodig is time series Insights om gegevens van de gebeurtenis bron te lezen en te verwerken) bij [het plannen van uw omgeving](time-series-insights-update-plan.md).

Time Series Insights voor beeld van beleid voor gegevens inkomend verkeer bepalen waar gegevens kunnen worden gebrond en welke indeling de gegevens moeten hebben.

### <a name="ingress-policies"></a>Ingangs beleid

Bij inkomend *gegevens* verkeer wordt uitgelegd hoe gegevens worden verzonden naar een Azure time series Insights preview-omgeving.

Hieronder vindt u een overzicht van de belangrijkste configuratie, opmaak en aanbevolen procedures.

#### <a name="event-sources"></a>Gebeurtenisbronnen

Azure Time Series Insights preview ondersteunt de volgende gebeurtenis bronnen:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights preview ondersteunt Maxi maal twee gebeurtenis bronnen per instantie. Wanneer u verbinding maakt met een gebeurtenis bron, worden alle gebeurtenissen die momenteel zijn opgeslagen in uw IOT of event hub door de omgeving van de TSI gelezen, te beginnen met de oudste gebeurtenis.

> [!IMPORTANT]
>
> * U kunt een hoge initiële latentie ervaren wanneer u een gebeurtenis bron koppelt aan uw preview-omgeving.
> De gebeurtenis bron latentie is afhankelijk van het aantal gebeurtenissen dat zich momenteel in uw IoT Hub of event hub bevinden.
> * Hoge latentie wordt weer gegeven nadat de bron gegevens van de gebeurtenis voor het eerst zijn opgenomen. Verzend een ondersteunings ticket via de Azure Portal als u een voortdurende hoge latentie ondervindt.

#### <a name="supported-data-format-and-types"></a>Ondersteunde gegevens indeling en-typen

Azure Time Series Insights ondersteunt het JSON-bestand met UTF-8-code ring, verzonden vanuit Azure IoT Hub of Azure Event Hubs. 

De ondersteunde gegevens typen zijn:

| Gegevenstype | Description |
|---|---|
| **booleaans** | Een gegevens type met een van de twee statussen: `true` of `false` . |
| **dateTime** | Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum en tijd van de dag. Uitgedrukt in [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -indeling. |
| **long** | Een ondertekend 64-bits geheel getal  |
| **double** | Een Double-Precision 64-bits [IEEE 754](https://ieeexplore.ieee.org/document/8766229) drijvende komma. |
| **tekenreeks** | Tekst waarden, bestaande uit Unicode-tekens.          |

> [!IMPORTANT]
>
> * De omgeving van de TSI is sterk getypeerd. Als apparaten of Tags zowel integrale als niet-integrale gegevens verzenden, worden de waarden van de apparaatinstellingen opgeslagen in twee gescheiden en lange kolommen en moet de [functie Coalesce ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) worden gebruikt bij het maken van API-aanroepen en het definiëren van variabele expressies van uw tijd reeks model.

#### <a name="objects-and-arrays"></a>Objecten en matrices

U kunt complexe typen zoals objecten en arrays verzenden als onderdeel van de nettolading van de gebeurtenis, maar uw gegevens worden op een afvlakkings proces ondergaan wanneer het wordt opgeslagen.

Gedetailleerde informatie over het maken van een shape van JSON-gebeurtenissen, het complex type verzenden en het afvlakken van geneste objecten is beschikbaar in de [vorm van JSON voor](./time-series-insights-update-how-to-shape-events.md) inkomend verkeer en query's om te helpen bij het plannen en optimaliseren.

### <a name="ingress-best-practices"></a>Best practices voor binnenkomend verkeer

U wordt aangeraden de volgende aanbevolen procedures te gebruiken:

* Configureer Azure Time Series Insights en alle IoT Hub of event hub in dezelfde regio om mogelijke latentie te verminderen.

* [Plan uw schaal behoeften](time-series-insights-update-plan.md) door uw verwachte opname frequentie te berekenen en te controleren of deze binnen de hieronder vermelde ondersteunde frequentie valt.

* Meer informatie over het optimaliseren en vorm geven van uw JSON-gegevens, evenals de huidige beperkingen in de preview-versie, door [te lezen hoe u JSON voor ingress en query's kunt bepalen](./time-series-insights-update-how-to-shape-events.md).

* Gebruik Streaming-opname alleen voor bijna realtime-en recente gegevens, en het streamen van historische gegevens wordt niet ondersteund.

#### <a name="historical-data-ingestion"></a>Historische gegevens opname

Het gebruik van de streaming-pijp lijn voor het importeren van historische gegevens wordt momenteel niet ondersteund in Azure Time Series Insights preview. Als u gegevens uit het verleden in uw omgeving wilt importeren, volgt u de onderstaande richt lijnen:

* Streamt geen Live en historische gegevens parallel. Het opnemen van gegevens uit de volg orde resulteert in gedegradeerde query prestaties.
* Neem historische gegevens op in een tijdgebonden manier om de beste prestaties te leveren.
* Blijf binnen de limieten voor de doorvoer snelheid van de opname hieronder.
* Schakel warme Store uit als de gegevens ouder zijn dan de Bewaar periode voor uw warme winkel.

### <a name="ingress-scale-and-preview-limitations"></a>Inkomend schalen en preview-beperkingen

De beperkingen voor Azure Time Series Insights preview-ingang worden hieronder beschreven.

> [!TIP]
> [Plan uw voorbeeld omgeving](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) voor een uitgebreide lijst met alle preview-limieten.

#### <a name="per-environment-limitations"></a>Beperkingen per omgeving

Over het algemeen worden de ingangs snelheden weer gegeven als de factor van het aantal apparaten in uw organisatie, de gebeurtenis emissie frequentie en de grootte van elke gebeurtenis:

*  **Aantal apparaten** × **gebeurtenis emissie frequentie** × **grootte van elke gebeurtenis**.

Standaard kan met Time Series Insights preview inkomende gegevens worden opgenomen met een snelheid van **Maxi maal 1 MB per seconde (Mbps) per time series Insights omgeving**. Er zijn extra beperkingen [per hub-partitie](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Omgevings ondersteuning voor het opnemen van snelheden tot 16 MBps kan worden geboden door een aanvraag.
> * Neem contact met ons op als u een hogere door voer nodig hebt door een ondersteunings ticket in te dienen via Azure Portal.
 
* **Voor beeld 1:**

    Contoso-verzen ding heeft 100.000 apparaten die drie keer per minuut een gebeurtenis verzenden. De grootte van een gebeurtenis is 200 bytes. Ze gebruiken een IoT Hub met vier partities als de bron van de Time Series Insights gebeurtenis.

    * De opname frequentie voor de Time Series Insights omgeving zou zijn: **100.000 apparaten * 200 bytes/gebeurtenis * (3/60 gebeurtenis/sec) = 1 Mbps**.
    * De opname frequentie per partitie zou 0,25 MBps zijn.
    * De opname snelheid van Contoso-verzen ding valt binnen de limiet voor de schaal van de preview-versie.

* **Voor beeld 2:**

    Contoso vloot Analytics heeft 60.000 apparaten die elke seconde een gebeurtenis verzenden. Ze gebruiken een event hub met een aantal partities van 4 als de bron van de Time Series Insights gebeurtenis. De grootte van een gebeurtenis is 200 bytes.

    * Het opname tempo van de omgeving is: **60.000 apparaten * 200 bytes/gebeurtenis * 1 gebeurtenis/SEC = 12 Mbps**.
    * Het per partitie aantal zou 3 MBps zijn.
    * De opname frequentie van Contoso vloot Analytics ligt boven de omgeving en de partitie limieten. Ze kunnen een aanvraag indienen bij Time Series Insights via Azure Portal om de opname frequentie voor hun omgeving te verhogen en een event hub te maken met meer partities die binnen de preview-grenzen vallen.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hub-partities en limieten per partitie

Bij het plannen van uw Time Series Insights-omgeving is het belang rijk dat u rekening houdt met de configuratie van de bron (nen) van de gebeurtenis die u wilt verbinden met Time Series Insights. Zowel Azure IoT Hub als Event Hubs gebruiken partities om een horizontale schaal in te scha kelen voor gebeurtenis verwerking. 

Een *partitie* is een geordende reeks gebeurtenissen die in een hub worden bewaard. Het aantal partities wordt ingesteld tijdens het maken van de hub en kan niet worden gewijzigd.

Bekijk voor Event Hubs aanbevolen procedures voor het partitioneren van [het aantal partities dat ik nodig heb?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> De meeste IoT-hubs die worden gebruikt met Azure Time Series Insights hebben slechts vier partities nodig.

Of u nu een nieuwe hub voor uw Time Series Insights omgeving maakt of een bestaand gebruikt, u moet de opname frequentie per partitie berekenen om te bepalen of deze zich binnen de preview-limieten bevindt. 

Azure Time Series Insights preview heeft momenteel een **limiet van 0,5 Mbps voor algemeen per partitie**.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifieke overwegingen

Wanneer een apparaat wordt gemaakt in IoT Hub, wordt het permanent toegewezen aan een partitie. In dat geval kan IoT Hub de volg orde van gebeurtenissen garanderen (omdat de toewijzing nooit wordt gewijzigd).

Een vaste partitie toewijzing heeft ook invloed op Time Series Insights instanties die gegevens opnemen die vanuit IoT Hub downstream worden verzonden. Wanneer berichten van meerdere apparaten worden doorgestuurd naar de hub met behulp van dezelfde gateway apparaat-ID, kunnen ze in dezelfde partitie worden ontvangen op hetzelfde moment mogelijk de schaal limieten per partitie overschrijden.

**Impact**:

* Als een enkele partitie een onderhouds frequentie voor de preview-limiet heeft, is het mogelijk dat Time Series Insights niet alle telemetrie van het apparaat synchroniseert voordat de IoT Hub gegevens Bewaar periode is overschreden. Als gevolg hiervan kunnen verzonden gegevens verloren gaan als de opname limieten consistent zijn overschreden.

Om dat te beperken, raden we u aan de volgende aanbevolen procedures uit te voeren:

* Bereken uw per omgeving en de opname snelheid per partitie voordat u de oplossing implementeert.
* Zorg ervoor dat uw IoT Hub-apparaten zoveel mogelijk gelijkmatig zijn verdeeld.

> [!IMPORTANT]
> Voor omgevingen die IoT Hub als gebeurtenis bron gebruiken, berekent u de opname snelheid met het aantal Hub-apparaten dat wordt gebruikt om ervoor te zorgen dat de snelheid lager is dan de limiet van 0,5 MBps per partitie in de preview-periode.
>
> * Zelfs als er meerdere gebeurtenissen tegelijk arriveren, wordt de limiet voor de preview-versie niet overschreden.

  ![IoT Hub partitie diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Raadpleeg de volgende bronnen voor meer informatie over het optimaliseren van hub-door Voer en-partities:

* [IoT Hub schaal](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event hub-schaal](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event hub-partities](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Gegevensopslag

Wanneer u een Time Series Insights preview-SKU voor *betalen per gebruik* (PAYG) maakt, maakt u twee Azure-resources:

* Een Azure Time Series Insights voorbeeld omgeving die kan worden geconfigureerd voor warme gegevens opslag.
* Een Azure Storage v1 BLOB-account voor algemeen gebruik voor koude gegevens opslag.

Gegevens in uw warme archief zijn alleen beschikbaar via de [Time Series-query](./time-series-insights-update-tsq.md) en de [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md). Uw warme archief bevat recente gegevens binnen de [Bewaar periode](./time-series-insights-update-plan.md#the-preview-environment) die is geselecteerd bij het maken van de time series Insights omgeving.

Met Time Series Insights preview worden uw koude Store-gegevens opgeslagen in Azure Blob-opslag in de [Parquet-bestands indeling](#parquet-file-format-and-folder-structure). Time Series Insights preview beheert deze koude Store-gegevens uitsluitend, maar u kunt deze rechtstreeks als standaard Parquet-bestanden lezen.

> [!WARNING]
> Als eigenaar van het Azure Blob Storage-account waar koud opgeslagen gegevens zich bevinden, hebt u volledige toegang tot alle gegevens in het account. Deze toegang omvat machtigingen voor schrijven en verwijderen. Bewerk of verwijder geen gegevens die Time Series Insights preview-schrijf bewerkingen, omdat dat gegevens verlies kan veroorzaken.

### <a name="data-availability"></a>Beschikbaarheid van gegevens

Azure Time Series Insights preview-partities en gegevens indexeren voor optimale query prestaties. Gegevens worden beschikbaar gesteld voor het uitvoeren van een query vanuit zowel warme (indien ingeschakeld) als koud opgeslagen na de index. De hoeveelheid gegevens die wordt opgenomen, kan van invloed zijn op deze Beschik baarheid.

> [!IMPORTANT]
> Tijdens de preview-periode kan het tot 60 seconden duren voordat gegevens beschikbaar worden. Als u na 60 seconden een langere latentie ondervindt, kunt u een ondersteunings ticket indienen via de Azure Portal.

## <a name="azure-storage"></a>Azure Storage

In deze sectie worden Azure Storage informatie beschreven die relevant is voor Azure Time Series Insights preview.

Lees de [Inleiding tot opslag-blobs](../storage/blobs/storage-blobs-introduction.md)voor een uitgebreide beschrijving van Azure Blob-opslag.

### <a name="your-storage-account"></a>Uw opslag account

Wanneer u een Azure Time Series Insights preview-PAYG-omgeving maakt, wordt er een Azure Storage General-Purpose v1 BLOB-account gemaakt als uw langlopende koel winkel.  

Met Azure Time Series Insights preview worden Maxi maal twee exemplaren van elke gebeurtenis in uw Azure Storage-account bewaard. Een kopie slaat gebeurtenissen op die zijn besteld door opname tijd, waarbij altijd toegang tot gebeurtenissen in een geordende reeks wordt toegestaan. Time Series Insights preview maakt na verloop van tijd ook een opnieuw gepartitioneerde kopie van de gegevens die u kunt optimaliseren voor een uitvoerende Time Series Insights query.

Tijdens de open bare preview worden gegevens voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights-blobs schrijven en bewerken

Om de query prestaties en de beschik baarheid van gegevens te garanderen, moet u geen blobs bewerken of verwijderen die Time Series Insights preview maakt.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Time Series Insights voor beeld van koude Store-gegevens openen

Behalve dat u toegang hebt tot uw gegevens vanuit de [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md) en de [Time Series-query](./time-series-insights-update-tsq.md), kunt u ook rechtstreeks toegang krijgen tot uw gegevens vanuit de Parquet-bestanden die zijn opgeslagen in het koude-archief. U kunt bijvoorbeeld gegevens in een Jupyter-notebook lezen, transformeren en opschonen en deze vervolgens gebruiken om uw Azure Machine Learning model te trainen in dezelfde Spark-werk stroom.

Als u gegevens rechtstreeks vanuit uw Azure Storage-account wilt openen, moet u lees toegang hebben tot het account dat wordt gebruikt voor het opslaan van uw Time Series Insights preview-gegevens. U kunt vervolgens geselecteerde gegevens lezen op basis van de aanmaak tijd van het Parquet-bestand dat zich bevindt in de `PT=Time` map die hieronder wordt beschreven in de sectie [Parquet-bestands indeling](#parquet-file-format-and-folder-structure) .  Zie [toegang tot de resources van uw opslag account beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie over het inschakelen van lees toegang tot uw opslag account.

#### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder de Time Series Insights Preview-bestanden niet. Gerelateerde gegevens alleen beheren vanuit Time Series Insights preview.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet bestands indeling en mapstructuur

Parquet is een open-source kolom indeling die is ontworpen voor efficiënte opslag en prestaties. Time Series Insights preview gebruikt Parquet om query prestaties op basis van tijd reeksen op schaal in te scha kelen.  

Lees de [Parquet-documentatie](https://parquet.apache.org/documentation/latest/)voor meer informatie over het bestands type Parquet.

In Time Series Insights preview worden kopieën van uw gegevens als volgt opgeslagen:

* De eerste, eerste kopie wordt gepartitioneerd op basis van de opname tijd en slaat ruwweg gegevens op in volg orde van aankomst. Deze gegevens bevinden zich in de `PT=Time` map:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw gepartitioneerde kopie wordt gegroepeerd op Time Series-Id's en bevindt zich in de `PT=TsId` map:

  `V=1/PT=TsId/<TSI_INTERNAL_STRUCTURE>/<TSI_INTERNAL_NAME>.parquet`

Tijds tempel in de blobs namen in de `PT=Time` map komt overeen met de aankomst tijd van de gegevens naar de TSI (niet de tijds tempel van de gebeurtenissen).

Gegevens in de `PT=TsId` map worden gedurende een bepaalde periode geoptimaliseerd en zijn niet statisch. Tijdens het opnieuw partitioneren zijn er mogelijk dezelfde gebeurtenissen aanwezig in meerdere blobs. De naam van de blobs kan ook in de toekomst worden gewijzigd.

> [!NOTE]
>
> * `<YYYY>`wordt toegewezen aan een jaar representatie van vier cijfers.
> * `<MM>`wordt toegewezen aan een maand weergave met twee cijfers.
> * `<YYYYMMDDHHMMSSfff>`is gekoppeld aan een tijds tempel weergave met vier cijfers per jaar ( `YYYY` ), een maand van twee cijfers (), een dag van twee cijfers (), een uur van twee cijfers (), een minuut van twee `MM` `DD` `HH` cijfers ( `MM` ), een tweede cijfer seconde ( `SS` ) en een milliseconde van drie cijfers `fff` .

Time Series Insights preview-gebeurtenissen worden als volgt toegewezen aan de Parquet-bestands inhoud:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Elke rij bevat de **Time Stamp** -kolom met een tijds tempel van de gebeurtenis. De eigenschap time stamp is nooit null. De standaard instelling is dat de gebeurtenis in de **wachtrij** wordt geplaatst als de eigenschap time stamp niet is opgegeven in de bron van de gebeurtenis. De opgeslagen tijds tempel is altijd in UTC.
* Elke rij bevat de tijdreeks-ID (TSID) kolom (men) zoals deze is gedefinieerd wanneer de Time Series Insights omgeving wordt gemaakt. De TSID-eigenschaps naam bevat het `_string` achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden verzonden, worden toegewezen aan kolom namen die eindigen `_string` op (String), `_bool` (Booleaans), `_datetime` (datetime) of `_double` (double), afhankelijk van het eigenschaps type.
* Dit toewijzings schema is van toepassing op de eerste versie van de bestands indeling, waarnaar wordt verwezen als **V = 1** en wordt opgeslagen in de map base met dezelfde naam. Als deze functie zich ontwikkelt, kan dit toewijzings schema worden gewijzigd en wordt de referentie naam verhoogd.

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u JSON voor ingangen en query's kunt vorm](./time-series-insights-update-how-to-shape-events.md)geven.

- Meer informatie over de nieuwe [gegevens modellering](./time-series-insights-update-tsm.md).
