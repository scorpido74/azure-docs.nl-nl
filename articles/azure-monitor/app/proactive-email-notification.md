---
title: Wijziging van detectie melding van Smart-Azure-toepassing inzichten
description: Wijzig de standaard ontvangers voor meldingen van Slimme detectie. Met Slimme detectie kunt u toepassings traceringen bewaken met Azure-toepassing Insights voor ongebruikelijke patronen in de telemetrie Trace.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 493deea89586347d5847895acd5eb73a866f84ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432459"
---
# <a name="smart-detection-e-mail-notification-change"></a>Wijziging e-mail melding voor slimme detectie

Op basis van feedback van klanten worden op 1 april 2019 de standaard rollen gewijzigd die e-mail meldingen ontvangen van Slimme detectie.

## <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Op dit moment worden er e-mail meldingen voor slimme detectie standaard verzonden naar de rol van het _abonnement_, de _mede_werkers van het abonnement en de functie voor _abonnements lezers_ . Deze rollen zijn vaak toegewezen aan gebruikers die niet actief betrokken zijn bij de bewaking, waardoor veel van deze gebruikers de meldingen onnodig ontvangen. Om deze ervaring te verbeteren, worden er een wijziging aangebracht zodat e-mail meldingen alleen de [bewakings lezer controleren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) en [Inzender rollen controleren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) .

## <a name="scope-of-this-change"></a>Bereik van deze wijziging

Deze wijziging is van invloed op alle regels voor slimme detectie, behalve:

* Regels voor slimme detectie die zijn gemarkeerd als preview. Deze slimme detectie regels ondersteunen vandaag nog geen e-mail meldingen.

* Regel voor afwijkende fouten. Deze regel begint met het richten op de nieuwe standaard rollen, zodra deze zijn gemigreerd van een klassieke waarschuwing naar het Unified Alerts-platform (meer informatie is [hier](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)beschikbaar.)

## <a name="how-to-prepare-for-this-change"></a>Hoe bereidt u zich voor op deze wijziging?

Om ervoor te zorgen dat e-mail meldingen van Slimme detectie naar relevante gebruikers worden verzonden, moeten die gebruikers worden toegewezen aan de [bewakings lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) of [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) rollen van het abonnement.

Volg de stappen die worden beschreven in het artikel [een roltoewijzing toevoegen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) om gebruikers toe te wijzen aan de bewakings lezer of Inzender rollen te controleren via de Azure Portal. Zorg ervoor dat u de _bewakings lezer_ of het _bewaken_ van de Inzender selecteert als de rol waaraan gebruikers worden toegewezen.

> [!NOTE]
> Specifieke ontvangers van Slimme detectie meldingen, geconfigureerd met de optie _extra e-mail ontvangers_ in de regel instellingen, worden niet beïnvloed door deze wijziging. Deze ontvangers blijven de e-mail meldingen ontvangen.

Als u vragen hebt of problemen hebt met betrekking tot deze wijziging, kunt u het [beste contact met ons](mailto:smart-alert-feedback@microsoft.com)opnemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over slimme detectie:

- [Foutafwijkingen](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Geheugen lekken](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestatieafwijkingen](../../azure-monitor/app/proactive-performance-diagnostics.md)
