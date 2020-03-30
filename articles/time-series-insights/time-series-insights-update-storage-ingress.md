---
title: Gegevensopslag en -ingress in Preview - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over gegevensopslag en invallen in Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246185"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Gegevensopslag en -ingress in azure time series insights preview

In dit artikel worden updates voor gegevensopslag beschreven en wordt ingevallen voor Azure Time Series Insights Preview. Het beschrijft de onderliggende opslagstructuur, bestandsindeling en time series ID-eigenschap. Het onderliggende ingressproces, best practices en huidige previewbeperkingen worden ook beschreven.

## <a name="data-ingress"></a>Gegevens binnendringen

Uw Azure Time Series Insights-omgeving bevat een *opname-engine* voor het verzamelen, verwerken en opslaan van tijdreeksgegevens. 

Er zijn een aantal overwegingen om rekening mee te houden om ervoor te zorgen dat alle binnenkomende gegevens worden verwerkt, om een hoge inbinnendringenschaal te bereiken en *de innamelaten te* minimaliseren (de tijd die Time Series Insights nodig heeft om gegevens van de gebeurtenisbron te lezen en te verwerken) bij [het plannen van uw omgeving.](time-series-insights-update-plan.md)

Time Series Insights Preview-beleid voor gegevensinvallen bepaalt waar gegevens vandaan kunnen komen en welke indeling de gegevens moeten hebben.

### <a name="ingress-policies"></a>Invallend beleid

*Gegevensbinnendringen houdt* in hoe gegevens worden verzonden naar een Azure Time Series Insights Preview-omgeving. 

Belangrijke configuratie, opmaak en aanbevolen procedures worden hieronder samengevat.

#### <a name="event-sources"></a>Gebeurtenisbronnen

Azure Time Series Insights Preview ondersteunt de volgende gebeurtenisbronnen:

- [Azure IoT-hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights Preview ondersteunt maximaal twee gebeurtenisbronnen per exemplaar.

> [!IMPORTANT] 
> * U een hoge initiële latentie ervaren wanneer u een gebeurtenisbron aan uw Preview-omgeving koppelt. 
> De latentie van gebeurtenisbronnen is afhankelijk van het aantal gebeurtenissen dat momenteel in uw IoT-hub of gebeurtenishub wordt geplaatst.
> * Hoge latentie zal verdwijnen nadat gebeurtenisbrongegevens voor het eerst zijn ingenomen. Verzend een ondersteuningsticket via de Azure-portal als u een aanhoudende hoge latentie ervaart.

#### <a name="supported-data-format-and-types"></a>Ondersteunde gegevensindeling en -typen

Azure Time Series Insights ondersteunt UTF-8 gecodeerde JSON verzonden vanuit Azure IoT Hub of Azure Event Hubs. 

De ondersteunde gegevenstypen zijn:

| Gegevenstype | Beschrijving |
|---|---|
| **bool** | Een gegevenstype met een `true` van `false`de twee statussen: of . |
| **Datetime** | Vertegenwoordigt een moment in de tijd, meestal uitgedrukt als een datum en tijd van de dag. Uitgedrukt in [ISO 8601-formaat.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **double** | Een dubbel-precisie 64-bits [IEEE 754](https://ieeexplore.ieee.org/document/8766229) zwevend punt. |
| **tekenreeks** | Tekstwaarden, bestaande uit Unicode-tekens.          |

#### <a name="objects-and-arrays"></a>Objecten en arrays

U complexe typen zoals objecten en arrays verzenden als onderdeel van uw gebeurtenispayload, maar uw gegevens ondergaan een afvlakingsproces wanneer ze worden opgeslagen. 

Gedetailleerde informatie over hoe u uw JSON-gebeurtenissen vormgeven, complexe tekst verzenden en geneste objectafvlaking uitvoeren, is beschikbaar in [Hoe JSON vorm te geven aan binnendringen en query](./time-series-insights-update-how-to-shape-events.md) om te helpen bij planning en optimalisatie.

### <a name="ingress-best-practices"></a>Invallen de beste praktijken

We raden u aan de volgende best practices te gebruiken:

* Configureer Azure Time Series Insights en elke IoT Hub of Event Hub in dezelfde regio om de potentiële latentie te verminderen.

* [Plan uw schaalbehoeften](time-series-insights-update-plan.md) door uw verwachte opnamepercentage te berekenen en te controleren of deze binnen het ondersteunde tarief valt dat hieronder wordt vermeld.

* Begrijp hoe u uw JSON-gegevens optimaliseren en vormgeven, evenals de huidige beperkingen in preview, door te lezen [hoe JSON wordt gevormd voor binnendringen en query's.](./time-series-insights-update-how-to-shape-events.md)

### <a name="ingress-scale-and-preview-limitations"></a>Beperkingen voor invallenschaal en Voorbeeld 

Azure Time Series Insights Preview-beperkingen worden hieronder beschreven.

> [!TIP]
> Lees [Uw preview-omgeving plannen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) voor een uitgebreide lijst met alle preview-limieten.

#### <a name="per-environment-limitations"></a>Per omgevingsbeperkingen

In het algemeen worden invallende percentages beschouwd als de factor van het aantal apparaten dat zich in uw organisatie bevindt, de frequentie van de evenementemissie en de grootte van elke gebeurtenis:

*  **Aantal apparaten** × **Frequentie van gebeurtenis** × Grootte van elke **gebeurtenis**.

Standaard kan time series Insights preview binnenkomende gegevens opnemen met een snelheid van **maximaal 1 megabyte per seconde (MBps) per Time Series Insights-omgeving.**

> [!TIP] 
> * Ondersteuning voor het innemen van omgevingen tot 16 MBps kan op aanvraag worden geboden.
> * Neem contact met ons op als u een hogere doorvoer nodig hebt door een ondersteuningsticket in te dienen via Azure-portal.
 
* **Voorbeeld 1:**

    Contoso Shipping heeft 100.000 apparaten die een gebeurtenis drie keer per minuut uitzenden. De grootte van een gebeurtenis is 200 bytes. Ze gebruiken een Event Hub met vier partities als gebeurtenisbron time series Insights.

    * De opnamesnelheid voor hun Time Series Insights-omgeving zou zijn: **100.000 apparaten * 200 bytes/gebeurtenis * (3/60 event/sec) = 1 MBps**.
    * De opnamesnelheid per partitie zou 0,25 MBps zijn.
    * Contoso Shipping's inname tarief zou binnen de preview schaal beperking.

* **Voorbeeld 2:**

    Contoso Fleet Analytics heeft 60.000 apparaten die elke seconde een evenement uitzenden. Ze gebruiken een IoT Hub 24-partitietelling van 4 als de gebeurtenisbron Time Series Insights. De grootte van een gebeurtenis is 200 bytes.

    * De milieuopnamesnelheid zou zijn: **20.000 apparaten * 200 bytes/gebeurtenis * 1 gebeurtenis/sec = 4 MBps**.
    * De per partitiesnelheid zou 1 MBps zijn.
    * Contoso Fleet Analytics kan via Azure-portal een aanvraag indienen bij Time Series Insights om de opnamesnelheid voor hun omgeving te verhogen.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hubpartities en per partitielimieten

Bij het plannen van uw Time Series Insights-omgeving is het belangrijk om rekening te houden met de configuratie van de gebeurtenisbron(s) die u verbinding maakt met Time Series Insights. Zowel Azure IoT Hub als Event Hubs maken gebruik van partities om horizontale schaal voor gebeurtenisverwerking mogelijk te maken. 

Een *partitie* is een geordende volgorde van gebeurtenissen die in een hub worden gehouden. Het aantal partities wordt ingesteld tijdens de hubcreatiefase en kan niet worden gewijzigd. 

Bekijk [hoeveel partities ik nodig heb](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need) voor best practices voor gebeurtenishubs voor het partitioneren van aanbevolen procedures?

> [!NOTE]
> De meeste IoT-hubs die worden gebruikt met Azure Time Series Insights hebben slechts vier partities nodig.

Of u nu een nieuwe hub maakt voor uw Time Series Insights-omgeving of een bestaande hub gebruikt, u moet uw opnamesnelheid per partitie berekenen om te bepalen of deze binnen de preview-limieten valt. 

Azure Time Series Insights Preview heeft momenteel een algemene **limiet per partitielimiet van 0,5 MBps.**

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifieke overwegingen

Wanneer een apparaat wordt gemaakt in IoT Hub, wordt het permanent toegewezen aan een partitie. Hierdoor kan IoT Hub het bestellen van evenementen garanderen (omdat de toewijzing nooit verandert).

Een vaste partitietoewijzing heeft ook invloed op Time Series Insights-exemplaren die gegevens opnemen die vanuit IoT Hub downstream worden verzonden. Wanneer berichten van meerdere apparaten met dezelfde gateway-apparaat-ID naar de hub worden doorgestuurd, kunnen ze tegelijkertijd in dezelfde partitie aankomen die mogelijk de limieten per partitieschaal overschrijdt. 

**Gevolgen**:

* Als een enkele partitie een aanhoudende opnamesnelheid ervaart boven de preview-limiet, is het mogelijk dat Time Series Insights niet alle telemetrie van het apparaat synchroniseert voordat de bewaarperiode van IoT Hub-gegevens is overschreden. Als gevolg hiervan kunnen verzonden gegevens verloren gaan als de innamelimieten consequent worden overschreden.

Om die omstandigheid te beperken, raden we de volgende aanbevolen procedures aan:

* Bereken uw per omgeving en per partitieopnamepercentages voordat u uw oplossing implementeert.
* Zorg ervoor dat uw IoT Hub-apparaten zo vers mogelijk in balans zijn.

> [!IMPORTANT]
> Voor omgevingen die IoT Hub als gebeurtenisbron gebruiken, berekent u de opnamesnelheid met behulp van het aantal hubapparaten dat wordt gebruikt om er zeker van te zijn dat de snelheid onder de 0,5 MBps per partitiebeperking in preview daalt.
> * Zelfs als meerdere gebeurtenissen tegelijk binnenkomen, wordt de preview-limiet niet overschreden.

  ![IoT-hubpartitiediagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Raadpleeg de volgende bronnen voor meer informatie over het optimaliseren van hubdoorvoer en partities:

* [IoT-hubschaal](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Schaal gebeurtenishub](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Gebeurtenishubpartities](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Gegevensopslag

Wanneer u een *Pay-as-you-go-SKU-omgeving* (Time Series Insights Preview) maakt, maakt u twee Azure-bronnen:

* Een Azure Time Series Insights Preview-omgeving die kan worden geconfigureerd voor warme gegevensopslag.
* Een V1-blobaccount voor algemene doeleinden voor azure storage voor koude gegevensopslag.

Gegevens in uw warme winkel zijn alleen beschikbaar via [Time Series Query](./time-series-insights-update-tsq.md) en de Azure Time Series Insights Preview [explorer.](./time-series-insights-update-explorer.md) Uw warme winkel bevat recente gegevens binnen de [bewaarperiode](./time-series-insights-update-plan.md#the-preview-environment) die is geselecteerd bij het maken van de Time Series Insights-omgeving.

Time Series Insights Preview slaat uw koelhuisgegevens op in Azure Blob-opslag in de [indeling van het parketbestand.](#parquet-file-format-and-folder-structure) Time Series Insights Preview beheert deze koelhuisgegevens exclusief, maar is beschikbaar om direct als standaard parketbestanden te lezen.

> [!WARNING]
> Als eigenaar van het Azure Blob-opslagaccount waar koelopslaggegevens zich bevinden, hebt u volledige toegang tot alle gegevens in het account. Deze toegang omvat schrijf- en verwijdermachtigingen. Bewerk of verwijder de gegevens die Time Series Insights Preview schrijft niet, omdat dit kan leiden tot gegevensverlies.

### <a name="data-availability"></a>Beschikbaarheid van gegevens

Azure Time Series Insights Preview partities en indexeert gegevens voor optimale queryprestaties. Gegevens worden beschikbaar voor query van zowel warm (indien ingeschakeld) en koelhuis nadat deze is geïndexeerd. De hoeveelheid gegevens die wordt ingenomen, kan van invloed zijn op deze beschikbaarheid.

> [!IMPORTANT]
> Tijdens de preview u een periode van maximaal 60 seconden ervaren voordat gegevens beschikbaar zijn. Als u meer dan 60 seconden aanzienlijke latentie ervaart, dient u een ondersteuningsticket in via de Azure-portal.

## <a name="azure-storage"></a>Azure Storage

In deze sectie worden azure-opslaggegevens beschreven die relevant zijn voor azure time series insights preview.

Lees de [inleiding opslagblobs voor](../storage/blobs/storage-blobs-introduction.md)een grondige beschrijving van Azure Blob-opslag .

### <a name="your-storage-account"></a>Uw opslagaccount

Wanneer u een PayG-omgeving voor Azure Time Series Insights Preview maakt, wordt een V1-blobaccount voor algemene doeleinden van Azure Storage gemaakt als uw cold store voor de lange termijn.  

Azure Time Series Insights Preview behoudt maximaal twee exemplaren van elke gebeurtenis in uw Azure Storage-account. Eén kopie slaat gebeurtenissen op die zijn besteld op basis van innametijd, waardoor altijd toegang wordt gegeven tot gebeurtenissen in een tijdsvolgorde. Na verloop van tijd maakt Time Series Insights Preview ook een opnieuw gepartitioneerde kopie van de gegevens om te optimaliseren voor performante Time Series Insights-query. 

Tijdens openbare preview worden gegevens voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Voorbeelden van timeseries schrijven en bewerken

Als u de prestaties van query's en gegevens wilt garanderen, bewerkt of verwijdert u geen blobs die door Time Series Insights Preview worden gemaakt.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Toegang tot time-series Insights Preview cold store-gegevens 

Naast de toegang tot uw gegevens uit de [Time Series Insights Preview explorer](./time-series-insights-update-explorer.md) en Time Series [Query,](./time-series-insights-update-tsq.md)u ook rechtstreeks toegang krijgen tot uw gegevens vanuit de parketbestanden die in het koelhuis zijn opgeslagen. U bijvoorbeeld gegevens lezen, transformeren en reinigen in een Jupyter-notitieblok en deze vervolgens gebruiken om uw Azure Machine Learning-model in dezelfde Spark-werkstroom te trainen.

Als u rechtstreeks toegang wilt krijgen tot gegevens vanuit uw Azure Storage-account, hebt u leestoegang nodig tot het account dat wordt gebruikt om uw Time Series Insights Preview-gegevens op te slaan. U vervolgens geselecteerde gegevens lezen op basis van de `PT=Time` aanmaaktijd van het parketbestand in de onderstaande map in de sectie [Parketbestandsindeling.](#parquet-file-format-and-folder-structure)  Zie [Toegang tot uw opslagaccountresources beheren voor](../storage/blobs/storage-manage-access-to-resources.md)meer informatie over het inschakelen van leestoegang tot uw opslagaccount.

#### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder uw Time Series Insights Preview-bestanden niet. Beheer alleen gerelateerde gegevens vanuit Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Parketbestandsindeling en mapstructuur

Parket is een open-source columnar bestandsformaat ontworpen voor efficiënte opslag en prestaties. Time Series Insights Preview maakt gebruik van Parket om op time series gebaseerde queryprestaties op schaal in te schakelen.  

Lees voor meer informatie over het parketbestandtype de [parketdocumentatie.](https://parquet.apache.org/documentation/latest/)

Time Series Insights Preview slaat kopieën van uw gegevens als volgt op:

* De eerste, eerste kopie wordt verdeeld door innametijd en slaat gegevens ruwweg op in volgorde van aankomst. Deze gegevens bevinden `PT=Time` zich in de map:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw verdeelde kopie wordt gegroepeerd op time-series-iD's en bevindt zich in de `PT=TsId` map:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

In beide gevallen komt de eigenschap tijd van het parketbestand overeen met de tijd voor het maken van blobs. Gegevens in `PT=Time` de map worden bewaard zonder wijzigingen zodra deze naar het bestand zijn geschreven. Gegevens in `PT=TsId` de map worden in de loop van de tijd geoptimaliseerd voor query's en zijn niet statisch.

> [!NOTE]
> * `<YYYY>`kaarten aan een viercijferige jaarvertegenwoordiging.
> * `<MM>`kaarten aan een tweecijferige maandvertegenwoordiging.
> * `<YYYYMMDDHHMMSSfff>`kaarten aan een tijdstempelrepresentatie met`YYYY`een jaar van`MM`vier cijfers (`DD`), tweecijferige`HH`maand ( ),`MM`tweecijferige dag`SS`( ), tweecijferig`fff`uur ( ), tweecijferige minuten ( ), tweecijferige seconde ( ) en drie-cijferige milliseconde ( ).

Time Series Insights Preview-gebeurtenissen worden als volgt toegewezen aan de inhoud van het parketbestand:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Elke rij bevat de **tijdstempelkolom** met een gebeurtenistijdstempel. De tijdstempel eigenschap is nooit null. De gebeurtenis in de **wachtrij staat** standaard als de eigenschap tijdstempel niet is opgegeven in de gebeurtenisbron. De opgeslagen tijdstempel is altijd in UTC.
* Elke rij bevat de kolom (TSID)-kolom(en) van de Time Series ID zoals gedefinieerd wanneer de Time Series Insights-omgeving wordt gemaakt. De eigenschapnaam TSID `_string` bevat het achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden `_string` verzonden, `_bool` worden toegewezen `_datetime` aan kolomnamen `_double` die eindigen met (tekenreeks), (Booleaan), (datumtijd) of (dubbel), afhankelijk van het eigenschapstype.
* Dit toewijzingsschema is van toepassing op de eerste versie van de bestandsindeling, die wordt aangeduid als **V=1** en is opgeslagen in de basismap met dezelfde naam. Naarmate deze functie evolueert, kan dit toewijzingsschema worden gewijzigd en wordt de referentienaam verhoogd.

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u JSON vormgeven voor binnendringen en query's](./time-series-insights-update-how-to-shape-events.md).

- Lees meer over de nieuwe [gegevensmodellering](./time-series-insights-update-tsm.md).
