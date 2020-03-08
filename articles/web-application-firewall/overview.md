---
title: Inleiding tot de firewall van Azure Web Application
description: Dit artikel bevat een overzicht van Azure Web Application firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851182"
---
# <a name="what-is-azure-web-application-firewall"></a>Wat is Azure Web Application Firewall?

WAF (Web Application Firewall) biedt gecentraliseerde bescherming van uw webtoepassingen, van veelvoorkomende aanvallen tot beveiligingsproblemen. Webtoepassingen worden steeds gericht op kwaad aardige aanvallen die veelvoorkomende beveiligings problemen misbruiken. SQL-injectie en cross-site scripting zijn onder de meest voorkomende aanvallen.

![Overzicht van WAF](media/overview/wafoverview.png)

Het voor komen van dergelijke aanvallen in toepassings code is lastig. Dit kan strenge onderhoud, patching en bewaking vereisen op meerdere lagen van de toepassings topologie. Een gecentraliseerde Web Application Firewall helpt het beveiligings beheer veel eenvoudiger te maken. Een WAF biedt toepassings beheerders ook betere bescherming tegen bedreigingen en indringers.

Een WAF-oplossing kan sneller reageren op een beveiligings risico door een bekende zwakke plek in plaats van elke afzonderlijke webtoepassing te beveiligen.

## <a name="supported-services"></a>Ondersteunde services

WAF kan worden geïmplementeerd met [Azure-toepassing gateway](../application-gateway/overview.md) en de [Azure front-deur service](../frontdoor/front-door-overview.md). Beide services zijn laag-7 (HTTP/S) load balancers, maar Application Gateway is een regionale service en de voor deur is een wereld wijde service. WAF heeft functies die zijn aangepast voor elke specifieke service.

Zie het overzicht van WAF voor elke service voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Web Application firewall op Application Gateway raadpleegt u [Web Application firewall op Azure-toepassing gateway](./ag/ag-overview.md).
- Zie [Web Application firewall op de Azure front-deur-service](./afds/afds-overview.md)voor meer informatie over Web Application firewall op de Azure front-deur service.
