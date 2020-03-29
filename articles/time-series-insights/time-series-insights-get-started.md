---
title: Een omgeving maken - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het gebruik van de Azure-portal om een nieuwe Time Series Insights-omgeving te maken.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: 2c946c49884ef0de6843028976d4ec00ccfbcdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934853"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Een nieuwe Time Series Insights-omgeving maken in Azure Portal

In dit artikel wordt beschreven hoe u een nieuwe Time Series Insights-omgeving maakt met behulp van de Azure-portal.

Met Time Series Insights u binnen enkele minuten aan de slag gaan met het visualiseren en opvragen van gegevens die in Azure IoT-hubs en gebeurtenishubs worden weergegeven, zodat u binnen enkele seconden grote hoeveelheden tijdreeksgegevens opvragen.  Het is ontworpen voor de internet-of-things (IoT) schaal en kan terabytes aan gegevens verwerken.

## <a name="steps-to-create-the-environment"></a>Stappen voor het maken van de omgeving

Volg de volgende stappen om een omgeving te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer de **knop + Een resource** maken.

1. Selecteer de categorie **Internet of Things** en selecteer Inzichten uit de **tijdreeks**.

   [![De Time Series Insights-omgeving maken](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Selecteer op de pagina Inzichten in de **tijdreeks** de optie **Maken**.

1. Vul de vereiste parameters in. In de volgende tabel wordt elke parameter uitgelegd:
   
   [![De Time Series Insights-resourcegroep maken](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)
   
   Instelling|Voorgestelde waarde|Beschrijving
   ---|---|---
   Omgevingsnaam | Een unieke naam | Deze naam vertegenwoordigt de omgeving in [de tijdreeksontdekkingsreiziger](https://insights.timeseries.azure.com)
   Abonnement | Uw abonnement | Als u meerdere abonnementen hebt, kiest u bij voorkeur het abonnement met uw gebeurtenisbron. Time Series Insights kan automatisch azure IoT Hub- en Event Hub-bronnen detecteren die in hetzelfde abonnement bestaan.
   Resourcegroep | Een nieuw maken of bestaande gebruiken | Een resourcegroep is een verzameling Azure-resources die samen worden gebruikt. U een bestaande resourcegroep kiezen, bijvoorbeeld die met uw Event Hub of IoT Hub. U ook een nieuwe bron maken als deze bron niet gerelateerd is aan de andere bronnen.
   Locatie | Dichtstbijzijnde gebeurtenisbron | Kies bij voorkeur dezelfde datacenterlocatie die uw gebeurtenisbrongegevens bevat, in de poging om extra bandbreedtekosten voor verschillende regio's en bandbreedte tussen verschillende zones en extra latentie te voorkomen bij het verplaatsen van gegevens uit de regio.
   Prijscategorie | S1 | Kies de benodigde doorvoer. Selecteer S1 voor de laagste kosten en startcapaciteit.
   Capaciteit | 1 | Capaciteit is de multiplier die van toepassing is op het invallende tarief, de opslagcapaciteit en de kosten die zijn gekoppeld aan de geselecteerde SKU.  U kunt de capaciteit van een omgeving wijzigen nadat deze is gemaakt. Voor de laagste kosten selecteert u een capaciteit van 1. 
  
1. Selecteer **Maken** om het inrichtingsproces te starten. Dit kan een paar minuten duren.

1. Als u het implementatieproces wilt controleren, selecteert u het **symbool Meldingen** (belpictogram).

   [![Bekijk de meldingen](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Controleer in het **resourceoverzicht**de instellingen voor implementatieconfiguratie.

   [![Time Series Insights vastmaken aan het dashboard](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Optioneel)** Selecteer het **pinpictogram** in de rechterbovenhoek om in de toekomst eenvoudig toegang te krijgen tot uw Time Series Insights-omgeving.

## <a name="next-steps"></a>Volgende stappen

* [Definieer beleid voor gegevenstoegang](time-series-insights-data-access.md) om uw omgeving te beveiligen.

* [Voeg een gebeurtenisbron voor eventhub toe](time-series-insights-how-to-add-an-event-source-eventhub.md) aan uw Azure Time Series Insights-omgeving.

* [Gebeurtenissen naar](time-series-insights-send-events.md) de gebeurtenisbron verzenden.

* Bekijk uw omgeving in [Time Series Insights explorer.](https://insights.timeseries.azure.com)
