---
title: Include-bestand
description: Include-bestand
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466888"
---
Er is een maximum aantal voor elk object type voor Azure Policy. De vermelding _Bereik_ slaat op het abonnement of op de [beheergroep](../articles/governance/management-groups/overview.md).

| Waar | Wat | Maximum |
|---|---|---|
| Bereik | Beleidsdefinities | 500 |
| Bereik | Initiatiefdefinities | 100 |
| Tenant | Initiatiefdefinities | 2500 |
| Bereik | Toewijzing van beleid of initiatief | 100 |
| Beleidsdefinitie | Parameters | 20 |
| Initiatiefdefinitie | Beleidsregels | 100 |
| Initiatiefdefinitie | Parameters | 100 |
| Toewijzing van beleid of initiatief | Uitzonderingen (geen bereiken) | 400 |
| Beleidsregel | Geneste voor waarden | 512 |
| Herstel taak | Resources | 500 |
