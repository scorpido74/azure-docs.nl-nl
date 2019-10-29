---
title: Azure time series preview inrichten en beheren | Microsoft Docs
description: Meer informatie over het inrichten en beheren van Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47c96cb14f2e466d02d57f73fb2e66bc1f44a311
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989903"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights preview inrichten en beheren

In dit artikel wordt beschreven hoe u een Azure Time Series Insights voorbeeld omgeving maakt en beheert met behulp van de [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Overzicht

Azure Time Series Insights voorbeeld omgevingen zijn PAYG-omgevingen (betalen per gebruik).

Wanneer u een Azure Time Series Insights preview-omgeving inricht, maakt u deze Azure-resources:

* Een Azure Time Series Insights preview-omgeving  
* Een Azure Storage v1-account voor algemeen gebruik
* Een optionele warme Store voor snellere en onbeperkte query's
  
Meer informatie [over het plannen van uw omgeving](./time-series-insights-update-plan.md).

Koppel elke Azure Time Series Insights-voorbeeld omgeving aan een gebeurtenis bron. Lees [een event hub bron toevoegen](./time-series-insights-how-to-add-an-event-source-eventhub.md) en [een IOT-hub-bron toevoegen](./time-series-insights-how-to-add-an-event-source-iothub.md)voor meer informatie. Tijdens deze stap geeft u een time stamp-ID-eigenschap en een unieke gebruikers groep op. Dit zorgt ervoor dat de omgeving toegang heeft tot de juiste gebeurtenissen.

> [!NOTE]
> De vorige stap is optioneel in de inrichtings werk stroom tijdens het maken van de preview-omgeving van de tijd reeks. U moet echter een gebeurtenis bron aan de omgeving koppelen zodat de gegevens in die omgeving kunnen worden gestart.

Nadat het inrichten is voltooid, kunt u uw toegangs beleid en andere omgevings kenmerken aanpassen aan uw bedrijfs vereisten.

## <a name="create-the-environment"></a>De omgeving maken

Een Azure Time Series Insights-voorbeeld omgeving maken:

1. Selecteer de knop **payg** in het menu **SKU** . Geef een omgevings naam op en kies de abonnements groep en de resource groep die u wilt gebruiken. Selecteer vervolgens een ondersteunde locatie voor het hosten van de omgeving.

   [![een Azure Time Series Insights-exemplaar maken.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Voer een tijd reeks-ID in.

    >[!NOTE]
    > * De tijd reeks-ID is hoofdletter gevoelig en onveranderbaar. (Deze kan niet worden gewijzigd nadat deze is ingesteld.)
    > * Time Series-Id's kunnen Maxi maal drie sleutels hebben.
    > * Lees voor meer informatie over het selecteren van een tijd reeks-ID [een tijd reeks-id kiezen](./time-series-insights-update-how-to-id.md).

1. Maak een Azure Storage-account door de naam van een opslag account te selecteren en een replicatie keuze aan te wijzen. Als u dit doet, wordt er automatisch een Azure Storage algemeen v1-account gemaakt. Het account wordt gemaakt in dezelfde regio als de Azure Time Series Insights-voorbeeld omgeving die u eerder hebt geselecteerd.

    [![een Azure Storage-account maken voor uw exemplaar](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Optioneel)** Schakel warme Store in voor uw omgeving als u snellere en onbeperkte query's wilt uitvoeren voor de meeste recente gegevens in uw omgeving. U kunt ook een warme archief maken of verwijderen via de optie **opslag configuratie** in het linkernavigatievenster, nadat u een time series Insights preview-omgeving hebt gemaakt.

1. **(Optioneel)** U kunt nu een gebeurtenis bron toevoegen. U kunt ook wachten totdat het exemplaar is ingericht.

   * Time Series Insights ondersteunt [azure IOT hub](./time-series-insights-how-to-add-an-event-source-iothub.md) en [Azure Event hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) als opties voor gebeurtenis bronnen. Hoewel u slechts één gebeurtenis bron kunt toevoegen wanneer u de omgeving maakt, kunt u later nog een gebeurtenis bron toevoegen. U kunt een bestaande consumenten groep selecteren of een nieuwe Consumer groep maken wanneer u de bron van de gebeurtenis toevoegt. Het is raadzaam om een unieke consumenten groep te maken om ervoor te zorgen dat alle gebeurtenissen zichtbaar zijn voor uw Azure Time Series Insights-voorbeeld omgeving.

   * Kies de juiste tijds tempel eigenschap. Azure Time Series Insights gebruikt standaard de time-out voor berichten in de wachtrij voor elke bron van de gebeurtenis.

     > [!TIP]
     > De time-outtijd van het bericht is mogelijk niet de beste geconfigureerde instelling voor gebruik in batch-gebeurtenis scenario's of scenario's voor het uploaden van historische gegevens. In dergelijke gevallen moet u controleren of u de beslissing hebt genomen of geen tijds tempel eigenschap wilt gebruiken.

     [tabblad![gebeurtenis bron](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Controleer of uw omgeving is ingericht met de instellingen die u wilt.

    [tabblad![bekijken + maken](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>De omgeving beheren

U kunt uw Azure Time Series Insights-voorbeeld omgeving beheren door gebruik te maken van de Azure Portal. Wanneer u via de Azure Portal beheert, ziet u enkele belang rijke verschillen tussen een betalen naar gebruik-Azure Time Series Insights voorbeeld omgeving en de algemeen beschik bare S1-of S2-omgevingen:

* De Blade **overzicht** van de Azure Portal is ongewijzigd in azure time series Insights, behalve op de volgende manieren:
  * De capaciteit wordt verwijderd omdat deze niet van toepassing is op omgevingen met betalen per gebruik.
  * De eigenschap time series ID wordt toegevoegd. Hiermee wordt bepaald hoe uw gegevens worden gepartitioneerd.
  * Verwijzings gegevens sets worden verwijderd.
  * Met de weer gegeven URL wordt u omgeleid naar de [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md).
  * De naam van uw Azure Storage-account wordt vermeld.

* De Blade **configureren** van de Azure portal wordt in azure time series Insights preview verwijderd, omdat payg-omgevingen niet kunnen worden geconfigureerd. U kunt echter **opslag configuratie** gebruiken om de zojuist geïntroduceerde warme Store te configureren.

* De Blade **referentie gegevens** van de Azure portal wordt verwijderd in azure time series Insights preview, omdat referentie gegevens geen deel uitmaken van betalen per gebruik-omgevingen.

[![Time Series Insights preview-omgeving in de Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Time Series Insights algemeen beschik bare omgevingen en preview-omgevingen door [de planning van uw omgeving](./time-series-insights-update-plan.md)te lezen.

- Meer informatie over het [toevoegen van een event hub bron](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Een [IOT hub-bron](./time-series-insights-how-to-add-an-event-source-iothub.md)configureren.
