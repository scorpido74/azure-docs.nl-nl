---
title: Een Azure Time Series Insights-omgeving maken | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Azure Portal gebruikt om een nieuwe Time Series Insights omgeving te maken.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 584172a9b248a9d151ba9a980bf4e52ed1e1b926
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141924"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Een nieuwe Time Series Insights-omgeving maken in Azure Portal

In dit artikel wordt beschreven hoe u een nieuwe Time Series Insights-omgeving maakt met behulp van de Azure Portal.

Met Time Series Insights kunt u aan de slag gaan met het visualiseren en opvragen van gegevens die in azure IoT hubs en Event Hubs in enkele minuten worden gestroomd, zodat u in een paar seconden een query kunt uitvoeren op grote volumes van tijdreeks gegevens.  Het is ontworpen voor de schaal van het Internet der dingen (IoT) en kan terabytes aan gegevens verwerken.

## <a name="steps-to-create-the-environment"></a>Stappen voor het maken van de omgeving

Volg deze stappen om een omgeving te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer de knop **+ een resource maken** .

1. Selecteer de categorie **Internet of Things** en selecteer **Time Series Insights**.

   De time series Insights omgeving maken) [ ![](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox)

1. Selecteer op de pagina **Time Series Insights** **maken**.

1. Vul de vereiste para meters in. De volgende tabel bevat uitleg over elke para meter:
   
   [![De Time Series Insights resource groep maken](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Instelling|Voorgestelde waarde|Description
   ---|---|---
   Naam van de omgeving | Een unieke naam | Deze naam vertegenwoordigt de omgeving in [Time Series Explorer](https://insights.timeseries.azure.com)
   Subscription | Uw abonnement | Als u meerdere abonnementen hebt, kiest u het abonnement dat uw gebeurtenis bron bevat bij voor keur. Time Series Insights kan Azure IoT Hub- en Event Hub-resources die in hetzelfde abonnement aanwezig zijn, automatisch detecteren.
   Resource group | Nieuwe maken of bestaande gebruiken | Een resourcegroep is een verzameling Azure-resources die samen worden gebruikt. U kunt een bestaande resource groep kiezen, bijvoorbeeld het item met uw event hub of IoT Hub. Of u kunt een nieuw item maken als deze bron niet is gerelateerd aan de andere resources.
   Location | Dichtstbijzijnde bron van uw gebeurtenis | Kies bij voor keur dezelfde Data Center-locatie die uw gebeurtenis brongegevens bevat, om te voor komen dat extra regio's en bandbreedte kosten voor meerdere zones worden toegevoegd en latentie wordt toegevoegd bij het verplaatsen van gegevens uit de regio.
   Prijscategorie | S1 | Kies de benodigde doorvoer. Voor de laagste kosten en de start capaciteit selecteert u S1.
   Capaciteit | 1 | Capaciteit is de vermenigvuldiger is van toepassing op het ingangs tempo, de opslag capaciteit en de kosten die zijn gekoppeld aan de geselecteerde SKU.  U kunt de capaciteit van een omgeving wijzigen nadat deze is gemaakt. Selecteer een capaciteit van 1 voor de laagste kosten. 
  
1. Selecteer **maken** om het inrichtings proces te starten. Het kan een paar minuten duren.

1. Als u het implementatie proces wilt bewaken , selecteert u het meldings symbool (pictogram Bell).

   [![De meldingen bekijken](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Wanneer de implementatie is voltooid, kunt u **Go to resource** selecteren om andere eigenschappen te configureren, beveiliging in te stellen met beleid voor gegevens toegang, gebeurtenis bronnen toevoegen en andere acties uit te voeren.

1. Selecteer in het resource **overzicht**het **speld pictogram** in de rechter bovenhoek om in de toekomst eenvoudig toegang te krijgen tot uw time series Insights omgeving.

   [![De Time Series Insights pincode voor het dash board maken](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* [Definieer het beleid voor gegevens toegang](time-series-insights-data-access.md) om uw omgeving te beveiligen.

* [Een event hub-gebeurtenis bron toevoegen](time-series-insights-how-to-add-an-event-source-eventhub.md) aan uw Azure time series Insights-omgeving.

* [Gebeurtenissen verzenden](time-series-insights-send-events.md) naar de gebeurtenisbron.

* Bekijk uw omgeving in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
