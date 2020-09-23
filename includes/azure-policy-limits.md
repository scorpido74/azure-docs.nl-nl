---
title: Include-bestand
description: Include-bestand
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/22/2020
ms.author: dacoulte
ms.openlocfilehash: 2707523947cae77dd800dd5f560ebe1c47f3893a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905981"
---
Voor elk objecttype in Azure Policy bestaat er een maximum. De vermelding _Bereik_ slaat op het abonnement of op de [beheergroep](../articles/governance/management-groups/overview.md).

| Waar | Wat | Maximum |
|---|---|---|
| Bereik | Beleidsdefinities | 500 |
| Bereik | Initiatiefdefinities | 200 |
| Tenant | Initiatiefdefinities | 2500 |
| Bereik | Beleids- of initiatieftoewijzingen | 200 |
| Bereik | Uitzonderingen | 1000 |
| Beleidsdefinitie | Parameters | 20 |
| Initiatiefdefinitie | Beleidsregels | 1000 |
| Initiatiefdefinitie | Parameters | 100 |
| Beleids- of initiatieftoewijzingen | Uitzonderingen (geen bereiken) | 400 |
| Beleidsregel | Geneste voorwaarden | 512 |
| Hersteltaak | Resources | 500 |
