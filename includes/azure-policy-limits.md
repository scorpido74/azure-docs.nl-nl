---
title: bestand opnemen
description: bestand opnemen
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272692"
---
Er geldt een maximumaantal voor elk objecttype voor Azure Policy. De vermelding _Bereik_ slaat op het abonnement of op de [beheergroep](../articles/governance/management-groups/overview.md).

| Waar | Wat | Maximum |
|---|---|---|
| Bereik | Beleidsdefinities | 500 |
| Bereik | Initiatiefdefinities | 100 |
| Tenant | Initiatiefdefinities | 1000 |
| Bereik | Beleids- of initiatiefopdrachten | 100 |
| Beleidsdefinitie | Parameters | 20 |
| Initiatiefdefinitie | Beleidsregels | 100 |
| Initiatiefdefinitie | Parameters | 100 |
| Beleids- of initiatiefopdrachten | Uitzonderingen (geen bereiken) | 400 |
| Beleidsregel | Geneste aandoeningen | 512 |
| Hersteltaak | Resources | 1000 |
