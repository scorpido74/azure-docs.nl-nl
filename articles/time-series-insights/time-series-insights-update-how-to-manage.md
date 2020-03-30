---
title: Een voorbeeldomgeving inrichten en beheren - Azure Time Series | Microsoft Documenten
description: Meer informatie over het inrichten en beheren van een Azure Time Series Insights Preview-omgeving.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087209"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights Preview inrichten en beheren

In dit artikel wordt beschreven hoe u een Azure Time Series Insights Preview-omgeving maakt en beheert met behulp van de [Azure-portal.](https://portal.azure.com/)

## <a name="overview"></a>Overzicht

Azure Time Series Insights Preview-omgevingen zijn *pay-as-you-go* (PAYG)-omgevingen.

Wanneer u een Azure Time Series Insights Preview-omgeving indient, maakt u deze Azure-bronnen:

* Een Azure Time Series Insights Preview-omgeving  
* Een V1-account voor algemene doeleinden van Azure Storage
* Een optionele warme winkel voor snellere en onbeperkte query's

> [!TIP]
> * Meer informatie over [het plannen van uw omgeving.](./time-series-insights-update-plan.md)
> * Lees meer over het [toevoegen van een gebeurtenishubbron](./time-series-insights-how-to-add-an-event-source-eventhub.md) of hoe [u een IoT-hubbron toevoegt.](./time-series-insights-how-to-add-an-event-source-iothub.md)

U leert het volgende:

1. **(Optioneel)** Koppel elke Azure Time Series Insights Preview-omgeving aan een gebeurtenisbron. U verstrekt ook een Timestamp ID-eigenschap en een unieke consumentengroep om ervoor te zorgen dat de omgeving toegang heeft tot de juiste evenementen.

   > [!NOTE]
   > De vorige stap is optioneel bij het inrichten van een omgeving. Als u deze stap overslaat, moet u later een gebeurtenisbron aan de omgeving koppelen, zodat gegevens in de omgeving kunnen worden geopend.

1. Nadat de inrichting is voltooid, u uw toegangsbeleid en andere omgevingskenmerken aanpassen aan uw bedrijfsvereisten.

## <a name="create-the-environment"></a>De omgeving maken

Ga als het gaat om een Azure Time Series Insights Preview-omgeving:

1. Selecteer **PAYG** als **laag**. Geef een omgevingsnaam op en kies de abonnementsgroep en resourcegroep die u wilt gebruiken. Selecteer vervolgens een ondersteunde locatie om de omgeving te hosten.

   [![Maak een azure time series Insights-exemplaar.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Voer een tijdserie-id in.

    > [!NOTE]
    > * De Time Series ID is *case-sensitive* en *onveranderlijk.* (Het kan niet worden gewijzigd nadat het is ingesteld.)
    > * Time Series ID's kunnen maximaal *drie* toetsen zijn.
    > * Lees meer over [Hoe kies je een Time Series ID](time-series-insights-update-how-to-id.md)

1. Maak een Azure Storage-account door een naam van een opslagaccount te selecteren en een replicatiekeuze aan te wijzen. Als u dit doet, wordt automatisch een V1-account voor algemene doeleinden van Azure Storage gemaakt. Het account wordt gemaakt in dezelfde regio als de Azure Time Series Insights Preview-omgeving die u eerder hebt geselecteerd.

    [![Configuratie van koude opslag](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Optioneel)** Schakel warme winkel voor uw omgeving in als u sneller en onbeperkt wilt vragen over de meest recente gegevens in uw omgeving. U ook een warme winkel maken of verwijderen via de optie **Opslagconfiguratie** in het linkernavigatiedeelvenster, nadat u een time series Insights Preview-omgeving hebt gemaakt.

    [![Configuratie van warme opslag](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Optioneel)** U nu een gebeurtenisbron toevoegen. U ook wachten tot nadat de instantie is ingericht.

   * Time Series Insights ondersteunt [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) en [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) als gebeurtenisbronopties. Hoewel u slechts één gebeurtenisbron toevoegen wanneer u de omgeving maakt, u later een andere gebeurtenisbron toevoegen. 
   
     U een bestaande consumentengroep selecteren of een nieuwe consumentengroep maken wanneer u de gebeurtenisbron toevoegt. U het beste een unieke consumentengroep maken om ervoor te zorgen dat alle gebeurtenissen zichtbaar zijn voor uw Azure Time Series Insights Preview-omgeving.

   * Kies de juiste eigenschap Timestamp. Azure Time Series Insights gebruikt standaard de tijd die in het bericht is geplaatst voor elke gebeurtenisbron.

     > [!TIP]
     > De tijd die in het bericht is geplaatst, is mogelijk niet de best geconfigureerde instelling die u gebruiken in scenario's voor batchgebeurtenissen of scenario's voor het uploaden van historische gegevens. Zorg er in dergelijke gevallen voor dat u uw beslissing om een eigenschap Timestamp te gebruiken of niet gebruikt, controleert.

     [![Tabblad Configuratie van gebeurtenisbron](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Controleer of uw omgeving is ingericht en geconfigureerd zoals u dat wilt.

    [![Controleren + tabblad Maken](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>De omgeving beheren

U uw Azure Time Series Insights Preview-omgeving beheren met behulp van de Azure-portal. Er zijn enkele belangrijke verschillen tussen een PAYG Azure Time Series Insights Preview-omgeving en de algemeen beschikbare S1- of S2-omgevingen om rekening mee te houden wanneer u uw omgeving beheert via de Azure-portal:

* Het **overzichtsblad** van azure portal Preview heeft de volgende wijzigingen:

  * Capaciteit wordt verwijderd omdat deze niet van toepassing is op PAYG-omgevingen.
  * De eigenschap **Tijdreeks-ID** wordt toegevoegd. Het bepaalt hoe uw gegevens worden verdeeld.
  * Referentiegegevenssets worden verwijderd.
  * De weergegeven URL leidt u naar de [Azure Time Series Insights Preview-verkenner](./time-series-insights-update-explorer.md).
  * De naam van uw Azure Storage-account wordt weergegeven.

* Het **beheer van de Azure-portal wordt** verwijderd in Azure Time Series Insights Preview omdat PAYG-omgevingen niet configureerbaar zijn. U echter **opslagconfiguratie** gebruiken om de nieuw geïntroduceerde warme winkel te configureren.

* Het **referentiegegevensblad** van de Azure-portal wordt verwijderd in Azure Time Series Insights Preview omdat referentiegegevens geen deel uitmaken van PAYG-omgevingen.

[![Time Series Insights Preview-omgeving in de Azure-portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over time series Insights algemeen beschikbare omgevingen en preview-omgevingen door [Uw omgeving plannen te](./time-series-insights-update-plan.md)lezen.

- Meer informatie over het [toevoegen van een gebeurtenishubbron](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Een [IoT-hubbron](./time-series-insights-how-to-add-an-event-source-iothub.md)configureren .
