---
title: Include-bestand
description: Include-bestand
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 08183cc2cbe2fce83bc7347da22308931b99dcc1
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010972"
---
Voor elk objecttype in Azure Policy bestaat er een maximum. De vermelding _Bereik_ slaat op het abonnement of op de [beheergroep](../articles/governance/management-groups/overview.md).

| Waar | Wat | Maximum |
|---|---|---|
| Bereik | Beleidsdefinities | 500 |
| Bereik | Initiatiefdefinities | 200 |
| Tenant | Initiatiefdefinities | 2500 |
| Bereik | Beleids- of initiatieftoewijzingen | 200 |
| Beleidsdefinitie | Parameters | 20 |
| Initiatiefdefinitie | Beleidsregels | 1000 |
| Initiatiefdefinitie | Parameters | 100 |
| Beleids- of initiatieftoewijzingen | Uitzonderingen (geen bereiken) | 400 |
| Beleidsregel | Geneste voorwaarden | 512 |
| Hersteltaak | Resources | 500 |
