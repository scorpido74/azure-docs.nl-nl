---
title: Include-bestand
description: Include-bestand
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854097"
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
