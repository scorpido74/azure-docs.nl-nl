---
title: Azure time series preview inrichten en beheren | Microsoft Docs
description: Meer informatie over het inrichten en beheren van Azure Time Series Insights preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a84c587a2666982e862f0640b30d2d6bef3b9a42
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744670"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights preview inrichten en beheren

In dit artikel wordt beschreven hoe u een Azure Time Series Insights voorbeeld omgeving maakt en beheert met behulp van de [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Overzicht

Azure Time Series Insights voorbeeld omgevingen zijn PAYG-omgevingen (betalen per gebruik).

Wanneer u een Azure Time Series Insights voorbeeld omgeving inricht, maakt u twee Azure-resources:

* Een Azure Time Series Insights preview-omgeving  
* Een Azure Storage v1-account voor algemeen gebruik
  
Meer informatie [over het plannen van uw omgeving](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Zorg ervoor dat u een Azure Storage-account voor algemeen gebruik (GPv1) gebruikt voor de preview-versie.

U kunt eventueel elke Azure Time Series Insights-voorbeeld omgeving koppelen aan een gebeurtenis bron. Lees [een event hub bron toevoegen](./time-series-insights-how-to-add-an-event-source-eventhub.md) en [een IOT-hub-bron toevoegen](./time-series-insights-how-to-add-an-event-source-iothub.md)voor meer informatie. Tijdens deze stap geeft u een time stamp-ID-eigenschap en een unieke gebruikers groep op. Dit zorgt ervoor dat de omgeving toegang heeft tot de juiste gebeurtenissen.

Nadat het inrichten is voltooid, kunt u uw toegangs beleid en andere omgevings kenmerken aanpassen aan uw bedrijfs vereisten.

## <a name="create-the-environment"></a>De omgeving maken

In de volgende stappen wordt beschreven hoe u een Azure Time Series Insights-voorbeeld omgeving maakt:

1. Selecteer de knop **payg** onder het menu **SKU** . Geef een omgevings naam op en kies welke abonnements groep en welke resource groep u wilt gebruiken. Selecteer vervolgens een ondersteunde locatie voor de omgeving die u wilt hosten.

   [![Maak een Azure Time Series Insights-exemplaar.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Voer een tijd reeks-ID in.

    >[!NOTE]
    > * De tijd reeks-ID is hoofdletter gevoelig en onveranderbaar. (Deze kan niet worden gewijzigd nadat deze is ingesteld.)
    > * Time Series-Id's kunnen Maxi maal drie sleutels hebben.
    > * Lees voor meer informatie over het selecteren van een tijd reeks-ID [een tijd reeks-id kiezen](./time-series-insights-update-how-to-id.md).

1. Maak een Azure Storage-account door de naam van een opslag account te selecteren en een replicatie keuze aan te wijzen. Als u dit doet, wordt er automatisch een Azure Storage algemeen v1-account gemaakt. Deze wordt gemaakt in dezelfde regio als de Azure Time Series Insights voorbeeld omgeving die u eerder hebt geselecteerd.

    [![Een Azure Storage-account maken voor uw exemplaar](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. U kunt desgewenst een gebeurtenis bron toevoegen.

   * Time Series Insights ondersteunt [azure IOT hub](./time-series-insights-how-to-add-an-event-source-iothub.md) en [Azure Event hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) als opties. Hoewel u slechts één gebeurtenis bron kunt toevoegen tijdens het maken van de omgeving, kunt u later een andere gebeurtenis bron toevoegen. Het is raadzaam om een unieke consumenten groep te maken om ervoor te zorgen dat alle gebeurtenissen zichtbaar zijn voor uw Azure Time Series Insights preview-exemplaar. U kunt een bestaande consumenten groep selecteren of een nieuwe Consumer groep maken bij het toevoegen van de gebeurtenis bron.

   * U moet ook de juiste tijds tempel eigenschap kiezen. Azure Time Series Insights gebruikt standaard het bericht tijd in gebruik voor elke bron van de gebeurtenis.

     > [!TIP]
     > De tijd waarop het bericht in gebruik is, is mogelijk niet de beste geconfigureerde instelling voor het gebruik van de scenario's voor het uploaden van gebeurtenissen in batch-of historische gegevens Zorg ervoor dat u uw beslissing hebt gecontroleerd of u in dergelijke gevallen geen tijds tempel eigenschap wilt gebruiken.

     [![Tabblad gebeurtenis bron](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Controleer of uw omgeving is ingericht met de gewenste instellingen.

    [![Tabblad controleren en maken](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>De omgeving beheren

U kunt uw Azure Time Series Insights-voorbeeld omgeving beheren door gebruik te maken van de Azure Portal. Dit zijn de belangrijkste verschillen bij het beheren van een PAYG-Azure Time Series Insights, in tegens telling tot een S1-of S2-omgeving, met behulp van de Azure Portal:

* De Blade **overzicht** van de Azure Portal is ongewijzigd in azure time series Insights, behalve op de volgende manieren:
  * De capaciteit wordt verwijderd omdat dit concept niet relevant is voor PAYG-omgevingen.
  * De ID-eigenschap van de tijd reeks is toegevoegd. Hiermee wordt bepaald hoe uw gegevens worden gepartitioneerd.
  * Verwijzings gegevens sets worden verwijderd.
  * Met de weer gegeven URL wordt u omgeleid naar de [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md).
  * De naam van uw Azure Storage-account wordt vermeld.

* De Blade **configureren** van de Azure Portal is in azure time series Insights preview verwijderd, omdat payg-omgevingen niet kunnen worden geconfigureerd.

* De Blade **referentie gegevens** van de Azure Portal is verwijderd in azure time series Insights preview omdat referentie gegevens geen onderdeel zijn van payg-omgevingen.

[![Time Series Insights voorbeeld omgeving in het Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- [Plan uw omgeving](./time-series-insights-update-plan.md).

- Meer informatie over het [toevoegen van een event hub bron](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- [Een IOT hub-bron](./time-series-insights-how-to-add-an-event-source-iothub.md)configureren.