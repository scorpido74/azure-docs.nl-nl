---
title: Overzicht van Peering Service-partners
titleSuffix: Azure
description: Overzicht van Peering Service-partners
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720245"
---
# <a name="peering-service-partner-walkthrough"></a>Overzicht van Peering Service-partners

In dit gedeelte worden de stappen uitgelegd die een provider moet volgen om een Direct-peering voor Peering-service mogelijk te maken.

## <a name="create-direct-peering-connection-for-peering-service"></a>Directe peering-verbinding maken voor Peering-service
Serviceproviders kunnen hun geografische bereik uitbreiden door nieuwe Direct-peering te maken die Peering Service ondersteunen. Om dit te bereiken,
1. Word peering servicepartner als dit nog niet het jaar is
1. Volg de instructies voor [het maken of wijzigen van een Direct-peering met behulp van de portal.](howto-direct-portal.md) Zorg ervoor dat het voldoet aan de vereiste voor hoge beschikbaarheid.
1. Volg vervolgens stappen om Peering Service in te [schakelen op een Direct-peering met behulp van de portal.](howto-peering-service-portal.md)

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Verouderde directe peering-verbinding gebruiken voor Peering Service
Als u legacy Direct-peering hebt die u wilt gebruiken om Peering Service te ondersteunen,
1. Word een Peering Service-partner, zo niet al.
1. Volg de instructies voor [het converteren van een verouderde direct peering naar Azure-bron via de portal.](howto-legacy-direct-portal.md) Bestel indien nodig extra circuits om te voldoen aan de vereiste voor hoge beschikbaarheid.
1. Volg vervolgens stappen om Peering Service in te [schakelen op een Direct-peering met behulp van de portal.](howto-peering-service-portal.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [peeringbeleid](https://peering.azurewebsites.net/peering).
* Volg [Direct peering walkthrough](walkthrough-direct-all.md)voor meer informatie over stappen voor het instellen van Direct-peering met Microsoft.
* Volg [Exchange-peering walkthrough](walkthrough-exchange-all.md)voor meer informatie over stappen voor het instellen van Exchange-peering met Microsoft.