---
title: Overzicht van azure application gateway omleiden
description: Meer informatie over de omleidingsmogelijkheid in Azure Application Gateway om verkeer dat op de ene listener is ontvangen, om te leiden naar een andere listener of naar een externe site.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129882"
---
# <a name="application-gateway-redirect-overview"></a>Overzicht van omleiding van toepassingsgateway

U toepassingsgateway gebruiken om verkeer om te leiden.  De service heeft een algemeen omleidingsmechanisme om verkeer dat is ontvangen door de ene listener om te leiden naar een andere listener of naar een externe site. Dit vereenvoudigt de configuratie van toepassingen, optimaliseert het resourcegebruik en ondersteunt nieuwe omleidingsscenario's, waaronder globale en op paden gebaseerde omleiding.

Een veelvoorkomend omleidingsscenario voor veel webtoepassingen is het ondersteunen van automatische HTTP naar HTTPS-omleiding om ervoor te zorgen dat alle communicatie tussen toepassing en gebruikers plaatsvindt via een versleuteld pad. In het verleden hebben klanten technieken gebruikt, zoals het maken van een speciale backendpool waarvan het enige doel is om aanvragen die het op HTTP ontvangt om te leiden naar HTTPS. Met omleidingsondersteuning in Application Gateway u dit eenvoudig bereiken door een nieuwe omleidingsconfiguratie toe te voegen aan een routeringsregel en een andere listener op te geven met HTTPS-protocol als doellistener.

De volgende soorten omleidingworden ondersteund:

- 301 Permanente omleiding
- 302 Gevonden
- 303 Zie Andere
- 307 Tijdelijke omleiding

Ondersteuning voor Application Gateway-omleiding biedt de volgende mogelijkheden:

-  **Wereldwijde omleiding**

   Wordt omgeleid van de ene luisteraar naar de andere luisteraar op de gateway. Hierdoor is HTTP-naar-HTTPS-omleiding op een site mogelijk.
- **Omleiding op basis van pad**

   Met dit type omleiding kan HTTP alleen https-omleiding op een specifiek sitegebied, bijvoorbeeld een winkelwagentjegebied aangeduid met /cart/*.
- **Omleiden naar externe site**

![Omleiden](./media/redirect-overview/redirect.png)

Met deze wijziging moeten klanten een nieuw omleidingsconfiguratieobject maken, dat de doellistener of externe site aangeeft waarnaar omleiding gewenst is. Het configuratie-element ondersteunt ook opties om het toevoegen van het URI-pad en de querytekenreeks toe te laten aan de doorgestuurde URL. U ook het type omleiding kiezen. Eenmaal gemaakt, wordt deze omleidingsconfiguratie via een nieuwe regel aan de bronlistener gekoppeld. Bij het gebruik van een basisregel wordt de omleidingsconfiguratie gekoppeld aan een bronlistener en is het een globale omleiding. Wanneer een op paden gebaseerde regel wordt gebruikt, wordt de omleidingsconfiguratie gedefinieerd op de URL-padkaart. Het is dus alleen van toepassing op het specifieke padgebied van een site.

### <a name="next-steps"></a>Volgende stappen

[URL-omleiding configureren op een toepassingsgateway](tutorial-url-redirect-powershell.md)
