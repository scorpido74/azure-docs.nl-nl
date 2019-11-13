---
title: Automatisch schalen in azure met behulp van een aangepaste metrische gegevens
description: Meer informatie over hoe u uw resource kunt schalen op basis van aangepaste metrische gegevens in Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: f83c68dd2f2ddbb73974a2ea4e91e746308945c6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958117"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Aan de slag met automatisch schalen op basis van aangepaste metrische gegevens in azure
In dit artikel wordt beschreven hoe u uw resource kunt schalen op basis van een aangepaste metriek in Azure Portal.

Azure Monitor automatisch schalen is alleen van toepassing op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Hiermee kunt u aan de slag
In dit artikel wordt ervan uitgegaan dat u een web-app met Application Insights hebt geconfigureerd. Als u er nog geen hebt, kunt u [Application Insights instellen voor uw ASP.net-website][1]

- [Azure Portal][2] openen
- Klik op Azure Monitor pictogram in het navigatie deel venster links.
  Azure Monitor][3] ![starten
- Klik op instelling voor automatisch schalen om alle resources weer te geven waarvoor automatische schaal baarheid van toepassing is, samen met de huidige status automatisch schalen ![automatische schaling in azure monitor][4]
- Open de Blade automatisch schalen in Azure Monitor en selecteer een resource die u wilt schalen
  > Opmerking: in de volgende stappen wordt een app service-plan gebruikt dat is gekoppeld aan een web-app die app Insights heeft geconfigureerd.
- In de Blade schaal instelling voor de resource ziet u dat het huidige aantal exemplaren 1 is. Klik op automatisch schalen inschakelen.
  ![schaal instelling voor nieuwe web-app][5]
- Geef een naam op voor de schaal instelling en klik op ' een regel toevoegen '. U ziet de opties voor de schaal regel die als context deel venster worden weer gegeven aan de rechter kant. Standaard wordt de optie voor het schalen van het aantal instanties door 1 ingesteld als het CPU-percentage van de resource groter is dan 70%. Wijzig de metrische bron boven aan ' Application Insights ', selecteer de app Insights-resource in de vervolg keuzelijst resource en selecteer vervolgens de aangepaste metriek op basis waarvan u de schaal wilt aanpassen.
  ![schalen op aangepaste meet][6]
- Net als bij de bovenstaande stap voegt u een schaal regel toe die wordt geschaald en verlaagt u het aantal schalen met 1 als de aangepaste metriek onder een drempel waarde ligt.
  Schaal ![op basis van CPU-][7]
- Stel de limieten van het exemplaar in. Als u bijvoorbeeld wilt schalen tussen 2-5-instanties, afhankelijk van de aangepaste fluctuaties van de metriek, stelt u ' minimum ' in ' 2 ', ' maximum ' op ' 5 ' en ' standaard ' in ' 2 '
  > Opmerking: als er een probleem is opgetreden bij het lezen van de metrische gegevens van de resource en de huidige capaciteit lager is dan de standaard capaciteit, moet automatisch schalen worden uitgebreid naar de standaard waarde om te zorgen voor de beschik baarheid van de resource. Als de huidige capaciteit al hoger is dan standaard capaciteit, kan automatisch schalen niet worden geschaald.
- Klik op opslaan

Gefeliciteerd! U hebt nu de schaal instelling gemaakt om uw web-app automatisch te schalen op basis van een aangepaste metriek.

> Opmerking: dezelfde stappen zijn van toepassing om aan de slag te gaan met een VMSS of Cloud service-rol.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

