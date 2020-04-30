---
title: Inleiding tot de firewall van Azure Web Application
description: Dit artikel bevat een overzicht van Azure Web Application firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79475784"
---
# <a name="what-is-azure-web-application-firewall"></a>Wat is Azure Web Application Firewall?

WAF (Web Application Firewall) biedt gecentraliseerde bescherming van uw webtoepassingen, van veelvoorkomende aanvallen tot beveiligingsproblemen. Webtoepassingen worden steeds gericht op kwaad aardige aanvallen die veelvoorkomende beveiligings problemen misbruiken. SQL-injectie en cross-site scripting zijn onder de meest voorkomende aanvallen.

![Overzicht van WAF](media/overview/wafoverview.png)

Het voor komen van dergelijke aanvallen in toepassings code is lastig. Dit kan strenge onderhoud, patching en bewaking vereisen op meerdere lagen van de toepassings topologie. Een gecentraliseerde Web Application Firewall helpt het beveiligings beheer veel eenvoudiger te maken. Een WAF biedt toepassings beheerders ook betere bescherming tegen bedreigingen en indringers.

Een WAF-oplossing kan sneller reageren op een beveiligings risico door een bekende zwakke plek in plaats van elke afzonderlijke webtoepassing te beveiligen.

## <a name="supported-service"></a>Ondersteunde service

WAF kan worden geïmplementeerd met Azure-toepassing gateway, Azure front-deur en de Azure Content Delivery Network (CDN)-service van micro soft. WAF op Azure CDN is momenteel beschikbaar als open bare preview.  WAF heeft functies die zijn aangepast voor elke specifieke service. Zie het overzicht voor elke service voor meer informatie over WAF-functies voor elke service.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Web Application firewall op Application Gateway raadpleegt u [Web Application firewall op Azure-toepassing gateway](./ag/ag-overview.md).
- Zie [Web Application firewall op de Azure front-deur-service](./afds/afds-overview.md)voor meer informatie over Web Application firewall op de Azure front-deur service.
- Zie [Web Application firewall on Azure CDN-service](./cdn/cdn-overview.md) voor meer informatie over Web Application firewall in azure CDN service.
