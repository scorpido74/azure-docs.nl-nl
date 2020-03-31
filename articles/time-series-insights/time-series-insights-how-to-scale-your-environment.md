---
title: Hoe schaal je je omgeving - Azure Time Series Insights| Microsoft Documenten
description: Meer informatie over het schalen van uw Azure Time Series Insights-omgeving met behulp van de Azure-portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310982"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Uw Time Series Insights-omgeving schalen

In dit artikel wordt beschreven hoe u de capaciteit van uw Time Series Insights-omgeving wijzigt met behulp van de [Azure-portal.](https://portal.azure.com) Capaciteit is de multiplier die wordt toegepast op het invallende tarief, de opslagcapaciteit en de kosten die zijn gekoppeld aan de geselecteerde SKU.

U de Azure-portal gebruiken om de capaciteit binnen een bepaalde prijs-SKU te vergroten of te verkleinen.

Het wijzigen van de prijscategorie SKU is echter niet toegestaan. Een omgeving met een S1-prijsSKU kan bijvoorbeeld niet worden omgezet in een S2, of vice versa.

## <a name="ga-limits"></a>GA-limieten

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>De capaciteit van uw omgeving wijzigen

1. Zoek en selecteer in de Azure-portal uw Time Series Insights-omgeving.

1. Selecteer **opslagconfiguratie**in het menu voor uw Time Series Insights-omgeving.

   [![Uw Time Series Insights-capaciteit configureren](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Pas de schuifregelaar **Capaciteit** aan om de capaciteit te selecteren die voldoet aan de vereisten voor uw invallende snelheden en opslagcapaciteit. Let op het **invallentarief**, **opslagcapaciteit**en **geschatte kostenupdate** dynamisch om de impact van de wijziging weer te geven.

   [![Uw omgeving configureren met de schuifregelaar capaciteit](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   U ook het nummer van de capaciteitsvermenigvuldiger in het tekstvak rechts van de schuifregelaar typen.

1. Selecteer **Opslaan** om de omgeving te schalen. De voortgangsindicator wordt weergegeven totdat de wijziging is vastgelegd, tijdelijk.

1. Controleer of de nieuwe capaciteit voldoende is [om beperking te voorkomen.](time-series-insights-diagnose-and-solve-problems.md)

## <a name="next-steps"></a>Volgende stappen

- Bekijk [inzicht in Time Series Insights](time-series-insights-concepts-retention.md)voor meer informatie.

- Meer informatie over [het configureren van gegevensbehoud in Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Meer informatie over [het plannen van uw omgeving.](time-series-insights-environment-planning.md)