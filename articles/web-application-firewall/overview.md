---
title: Inleiding tot Azure Web Application Firewall
description: In dit artikel vindt u een overzicht van Azure Web Application Firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475784"
---
# <a name="what-is-azure-web-application-firewall"></a>Wat is Azure Web Application Firewall?

WAF (Web Application Firewall) biedt gecentraliseerde bescherming van uw webtoepassingen, van veelvoorkomende aanvallen tot beveiligingsproblemen. Webapplicaties zijn steeds vaker het doelwit van kwaadaardige aanvallen die misbruik maken van bekende kwetsbaarheden. SQL-injectie en cross-site scripting behoren tot de meest voorkomende aanvallen.

![WAF overzicht](media/overview/wafoverview.png)

Het voorkomen van dergelijke aanvallen in de toepassingscode is een uitdaging. Het kan rigoureus onderhoud, patchen en bewaking vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webapplicaties maakt beveiligingsbeheer veel eenvoudiger. Een WAF geeft applicatiebeheerders ook een betere bescherming tegen bedreigingen en inbraken.

Een WAF-oplossing kan sneller reageren op een beveiligingsbedreiging door een bekende kwetsbaarheid centraal te patchen, in plaats van elke afzonderlijke webapplicatie te beveiligen.

## <a name="supported-service"></a>Ondersteunde service

WAF kan worden geïmplementeerd met Azure Application Gateway, Azure Front Door en Azure Content Delivery Network (CDN) service van Microsoft. WAF op Azure CDN staat momenteel in een openbare preview.  WAF heeft functies die zijn aangepast voor elke specifieke service. Zie het overzicht voor elke service voor meer informatie over WAF-functies voor elke service.

## <a name="next-steps"></a>Volgende stappen

- Zie [Webapplication Firewall op Azure Application Gateway](./ag/ag-overview.md)voor meer informatie over Web Application Firewall op Application Gateway.
- Zie Web Application Firewall op Azure Front Door Service voor meer informatie over Web Application Firewall op Azure Front Door [Service.](./afds/afds-overview.md)
- Zie [WebApplication Firewall op Azure CDN-service](./cdn/cdn-overview.md) voor meer informatie over Web Application Firewall op Azure CDN-service
