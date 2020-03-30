---
title: Automatisch schalen in Azure met behulp van een aangepaste statistiek
description: Meer informatie over het schalen van uw resource op aangepaste statistiek in Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425116"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Aan de slag met automatische schaal op aangepaste statistiek in Azure
In dit artikel wordt beschreven hoe u uw resource schalen met een aangepaste statistiek in azure-portal.

Azure Monitor autoscale is alleen van toepassing op [Virtual Machine Scale Sets,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Cloud Services](https://azure.microsoft.com/services/cloud-services/), App Service - [Web Apps](https://azure.microsoft.com/services/app-service/web/), Azure Data [Explorer Cluster](https://azure.microsoft.com/services/data-explorer/) ,   
Integratieserviceomgeving en [API-beheerservices.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

## <a name="lets-get-started"></a>Laat aan de slag
In dit artikel wordt ervan uitgegaan dat u een web-app hebt met toepassingsinzichten geconfigureerd. Als u er nog geen hebt, u [Application Insights instellen voor uw ASP.NET website][1]

- [Azure-portal openen][2]
- Klik op het azure-monitorpictogram in het linkernavigatiedeelvenster.
  ![Azure-monitor starten][3]
- Klik op de instelling Automatisch schalen om alle resources weer te geven waarvoor ![automatische schaal van toepassing is, samen met de huidige status Automatisch schalen ontdekken in Azure-monitor][4]
- Open het blad 'Automatisch schalen' in Azure Monitor en selecteer een resource die u wilt schalen
  > Opmerking: in de onderstaande stappen wordt een app-serviceplan gebruikt dat is gekoppeld aan een web-app waarin app-inzichten zijn geconfigureerd.
- Let er in het schaalinstellingsblad voor de resource op dat het huidige aantal instantie1 is. Klik op 'Autoscale inschakelen'.
  ![Schaalinstelling voor nieuwe web-app][5]
- Geef een naam op voor de schaalinstelling en klik op 'Een regel toevoegen'. Let op de opties voor schaalregels die worden geopend als een contextvenster aan de rechterkant. Standaard stelt het de optie in om het aantal instanceen met 1 te schalen als het CPU-percentage van de resource meer dan 70% bedraagt. Wijzig de metrische bron bovenaan in 'Application Insights', selecteer de bron voor app-inzichten in de vervolgkeuzelijst 'Resource' en selecteer vervolgens de aangepaste statistiek op basis waarvan u wilt schalen.
  ![Schalen op aangepaste statistiek][6]
- Voeg, net als de bovenstaande stap, een schaalregel toe die het aantal schaalwaarden met 1 schaalt en verlaagt als de aangepaste statistiek onder een drempelwaarde ligt.
  ![Schaal op basis van cpu][7]
- Stel de instantielimieten in. Als u bijvoorbeeld wilt schalen tussen 2-5 instanties, afhankelijk van de aangepaste metrische schommelingen, stelt u 'minimum' in op '2', 'maximaal' op '5' en 'standaard' op '2'
  > Opmerking: Als er een probleem is met het lezen van de resourcestatistieken en de huidige capaciteit lager is dan de standaardcapaciteit, wordt Autoscale, om de beschikbaarheid van de resource te garanderen, uitgeschaald naar de standaardwaarde. Als de huidige capaciteit al hoger is dan de standaardcapaciteit, wordt Automatisch schalen niet inschaald.
- Klik op 'Opslaan'

Gefeliciteerd! U hebt nu uw schaalinstelling gemaakt om uw web-app automatisch te schalen op basis van een aangepaste statistiek.

> Opmerking: dezelfde stappen zijn van toepassing om aan de slag te gaan met een VMSS- of cloudservicerol.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

