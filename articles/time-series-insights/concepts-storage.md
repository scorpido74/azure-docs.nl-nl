---
title: Overzicht van opslag-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over gegevens opslag in Azure Time Series Insights.
author: esung22
ms.author: elsung
manager: diegoviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: fc6b6b42293b4f2028f1a12af950e96e28febc87
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085562"
---
# <a name="data-storage"></a>Gegevensopslag

Wanneer u Time Series Insights een payg-SKU-omgeving ( *betalen per gebruik* ) maakt, maakt u twee Azure-resources:

* Een Azure Time Series Insights omgeving die kan worden geconfigureerd voor warme gegevens opslag.
* Een Azure Storage account voor koude gegevens opslag.

Gegevens in uw warme archief zijn alleen beschikbaar via de [Time Series-query](./time-series-insights-update-tsq.md) en de [Azure time series Insights Explorer](./time-series-insights-update-explorer.md). Uw warme archief bevat recente gegevens binnen de [Bewaar periode](./time-series-insights-update-plan.md#the-preview-environment) die is geselecteerd bij het maken van de time series Insights omgeving.

Azure Time Series Insights slaat uw koude Store-gegevens op in de Parquet- [bestands indeling](#parquet-file-format-and-folder-structure)voor Azure Blob Storage. Time Series Insights beheert deze koude Store-gegevens uitsluitend, maar u kunt deze rechtstreeks als standaard Parquet-bestanden lezen.

> [!WARNING]
> Als eigenaar van het Azure Blob Storage-account waar koud opgeslagen gegevens zich bevinden, hebt u volledige toegang tot alle gegevens in het account. Deze toegang omvat machtigingen voor schrijven en verwijderen. Bewerk of verwijder de gegevens die Azure Time Series Insights schrijft, omdat dat gegevens verlies kan veroorzaken.

## <a name="data-availability"></a>Beschikbaarheid van gegevens

Azure Time Series Insights partities en index gegevens voor optimale query prestaties. Gegevens worden beschikbaar gesteld voor het uitvoeren van een query vanuit zowel warme (indien ingeschakeld) als koud opgeslagen na de index. De hoeveelheid gegevens die wordt opgenomen, kan van invloed zijn op deze Beschik baarheid.

> [!IMPORTANT]
> Het kan een periode van Maxi maal 60 seconden duren voordat de gegevens beschikbaar zijn. Als u na 60 seconden een langere latentie ondervindt, kunt u een ondersteunings ticket indienen via de Azure Portal.

## <a name="azure-storage"></a>Azure Storage

In deze sectie worden Azure Storage gegevens beschreven die relevant zijn voor Azure Time Series Insights.

Lees de [Inleiding tot opslag-blobs](../storage/blobs/storage-blobs-introduction.md)voor een uitgebreide beschrijving van Azure Blob-opslag.

### <a name="your-storage-account"></a>Uw opslag account

Wanneer u een Azure Time Series Insights PAYG-omgeving maakt, wordt er een Azure Storage-account gemaakt als uw lange termijn koel huis.  

Azure Time Series Insights bewaard Maxi maal twee exemplaren van elke gebeurtenis in uw Azure Storage-account. Een kopie slaat gebeurtenissen op die zijn besteld door opname tijd, waarbij altijd toegang tot gebeurtenissen in een geordende reeks wordt toegestaan. Time Series Insights maakt ook een opnieuw gepartitioneerde kopie van de gegevens in de loop van de tijd om te worden geoptimaliseerd voor het uitvoeren van Time Series Insights query's.

Al uw gegevens worden voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights-blobs schrijven en bewerken

Om de query prestaties en de beschik baarheid van gegevens te garanderen, moet u geen blobs bewerken of verwijderen die Time Series Insights maken.

#### <a name="accessing-time-series-insights-cold-store-data"></a>Toegang tot Time Series Insights koude Store-gegevens

Behalve dat u toegang hebt tot uw gegevens vanuit de [Time Series Insights Explorer](./time-series-insights-update-explorer.md) en de [Time Series-query](./time-series-insights-update-tsq.md), kunt u ook rechtstreeks toegang krijgen tot uw gegevens vanuit de Parquet-bestanden die zijn opgeslagen in het koude-archief. U kunt bijvoorbeeld gegevens in een Jupyter-notebook lezen, transformeren en opschonen en deze vervolgens gebruiken om uw Azure Machine Learning model te trainen in dezelfde Spark-werk stroom.

Als u gegevens rechtstreeks vanuit uw Azure Storage-account wilt openen, moet u lees toegang hebben tot het account dat wordt gebruikt voor het opslaan van uw Time Series Insights preview-gegevens. U kunt vervolgens geselecteerde gegevens lezen op basis van de aanmaak tijd van het Parquet-bestand dat zich bevindt in de `PT=Time` map die hieronder wordt beschreven in de sectie [Parquet-bestands indeling](#parquet-file-format-and-folder-structure) .  Zie [toegang tot de resources van uw opslag account beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie over het inschakelen van lees toegang tot uw opslag account.

#### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder uw Azure Time Series Insights-bestanden niet. Het beheren van gerelateerde gegevens alleen vanuit Azure Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet bestands indeling en mapstructuur

Parquet is een open-source kolom indeling die is ontworpen voor efficiënte opslag en prestaties. Azure Time Series Insights gebruikt Parquet om query prestaties op basis van de tijd reeks op schaal in te scha kelen.  

Lees de [Parquet-documentatie](https://parquet.apache.org/documentation/latest/)voor meer informatie over het bestands type Parquet.

Azure Time Series Insights worden kopieën van uw gegevens als volgt opgeslagen:

* De eerste, eerste kopie wordt gepartitioneerd op basis van de opname tijd en slaat ruwweg gegevens op in volg orde van aankomst. Deze gegevens bevinden zich in de `PT=Time` map:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw gepartitioneerde kopie wordt gegroepeerd op Time Series-Id's en bevindt zich in de `PT=TsId` map:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

De tijds tempel in de BLOB-namen in de `PT=Time` map komt overeen met de aankomst tijd van de gegevens in azure time series Insights en niet de tijds tempel van de gebeurtenissen.

Gegevens in de `PT=TsId` map worden gedurende een bepaalde periode geoptimaliseerd en zijn niet statisch. Tijdens het opnieuw partitioneren zijn er mogelijk enkele gebeurtenissen aanwezig in meerdere blobs. De naamgeving van de blobs in deze map is niet gegarandeerd dat deze hetzelfde blijft. 

In het algemeen kunt u de map gebruiken als u rechtstreeks toegang moet hebben tot gegevens via Parquet-bestanden `PT=Time` .  De toekomstige functionaliteit zorgt ervoor dat de map efficiënt wordt geopend `PT=TsId` . 

> [!NOTE]
>
> * `<YYYY>`wordt toegewezen aan een jaar representatie van vier cijfers.
> * `<MM>`wordt toegewezen aan een maand weergave met twee cijfers.
> * `<YYYYMMDDHHMMSSfff>`is gekoppeld aan een tijds tempel weergave met vier cijfers per jaar ( `YYYY` ), een maand van twee cijfers (), een dag van twee cijfers (), een uur van twee cijfers (), een minuut van twee `MM` `DD` `HH` cijfers ( `MM` ), een tweede cijfer seconde ( `SS` ) en een milliseconde van drie cijfers `fff` .

Time Series Insights preview-gebeurtenissen worden als volgt toegewezen aan de Parquet-bestands inhoud:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Elke rij bevat de **Time Stamp** -kolom met een tijds tempel van de gebeurtenis. De eigenschap time stamp is nooit null. De standaard instelling is dat de gebeurtenis in de **wachtrij** wordt geplaatst als de eigenschap time stamp niet is opgegeven in de bron van de gebeurtenis. De opgeslagen tijds tempel is altijd in UTC.
* Elke rij bevat de tijdreeks-ID (TSID) kolom (men) zoals deze is gedefinieerd wanneer de Time Series Insights omgeving wordt gemaakt. De TSID-eigenschaps naam bevat het `_string` achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden verzonden, worden toegewezen aan kolom namen die eindigen `_bool` op (Booleaans), `_datetime` (tijds tempel), `_long` (Long), `_double` (dubbele), `_string` (teken reeks) of `dynamic` (dynamisch), afhankelijk van het eigenschaps type.  Meer informatie over [ondersteunde gegevens typen](./concepts-supported-data-types.md)vindt u in.
* Dit toewijzings schema is van toepassing op de eerste versie van de bestands indeling, waarnaar wordt verwezen als **V = 1** en wordt opgeslagen in de map base met dezelfde naam. Als deze functie zich ontwikkelt, kan dit toewijzings schema worden gewijzigd en wordt de referentie naam verhoogd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [gegevens modellering](./time-series-insights-update-tsm.md).

* Plan uw [Azure time series Insights preview-omgeving](./time-series-insights-update-plan.md).
