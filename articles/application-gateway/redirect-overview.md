---
title: Omleidings overzicht voor Azure-toepassing gateway
description: Meer informatie over de omleidings mogelijkheid in Azure-toepassing gateway om verkeer dat is ontvangen op de ene listener, om te leiden naar een andere listener of een externe site.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 8c2bac1bee5a2301a29589f15d72d0f69b4a05b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253848"
---
# <a name="application-gateway-redirect-overview"></a>Overzicht van Application Gateway omleiden

U kunt Application gateway gebruiken om verkeer om te leiden.  De service heeft een algemeen omleidingsmechanisme om verkeer dat is ontvangen door de ene listener om te leiden naar een andere listener of naar een externe site. Dit vereenvoudigt de configuratie van de toepassing, optimaliseert het resource gebruik en ondersteunt nieuwe omleidings scenario's, waaronder globale en op pad gebaseerde omleiding.

Een veelvoorkomend omleidings scenario voor veel webtoepassingen is het ondersteunen van automatische HTTP-naar-HTTPS-omleiding om ervoor te zorgen dat alle communicatie tussen de toepassing en de gebruikers plaatsvindt via een versleuteld pad. In het verleden hebben klanten technieken gebruikt, zoals het maken van een specifieke back-end-pool waarvan het enige doel is om aanvragen te omleiden die worden ontvangen op HTTP voor HTTPS. Met ondersteuning voor omleiding in Application Gateway kunt u dit doen door een nieuwe omleidings configuratie toe te voegen aan een routerings regel en een andere listener met HTTPS-protocol als de doel-listener op te geven.

De volgende typen omleiding worden ondersteund:

- 301 permanente omleiding
- 302 gevonden
- 303 Zie overige
- 307 tijdelijke omleiding

Ondersteuning voor Application Gateway-omleiding biedt de volgende mogelijkheden:

-  **Wereld wijde omleiding**

   Omleiding van de ene listener naar een andere listener op de gateway. Hierdoor is HTTP-naar-HTTPS-omleiding op een site mogelijk.
- **Omleiding op basis van pad**

   Met dit type omleiding is HTTP-naar-HTTPS-omleiding alleen mogelijk voor een specifiek site gebied, bijvoorbeeld een winkel wagentje die wordt aangeduid met/cart/*.
- **Omleiden naar externe site**

![mappen](./media/redirect-overview/redirect.png)

Met deze wijziging moeten klanten een nieuw omleidings configuratie object maken, waarmee de doel-listener of externe site wordt opgegeven waarnaar de omleiding gewenst is. Het configuratie-element biedt ook ondersteuning voor opties om het URI-pad en de query reeks toe te voegen aan de omgeleide URL. U kunt ook het type omleiding kiezen. Zodra deze omleidings configuratie is gemaakt, wordt deze doorgestuurd naar de bron-listener via een nieuwe regel. Wanneer u een basis regel gebruikt, wordt de omleidings configuratie gekoppeld aan een bronhost en is dit een globale omleiding. Wanneer een op een pad gebaseerde regel wordt gebruikt, wordt de omleidings configuratie gedefinieerd op het URL-pad met de toewijzing. Dit geldt alleen voor het specifieke pad van een site.

### <a name="next-steps"></a>Volgende stappen

[URL-omleiding configureren op een toepassings gateway](tutorial-url-redirect-powershell.md)
