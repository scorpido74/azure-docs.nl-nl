---
title: bestand opnemen
description: bestand opnemen
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67133184"
---
Er is een maximum aantal voor elk object type voor Azure Policy. De vermelding _Bereik_ slaat op het abonnement of op de [beheergroep](../articles/governance/management-groups/overview.md).

| Waar | Wat | Maximum |
|---|---|---|
| Scope | Beleidsdefinities | 500 |
| Scope | Initiatiefdefinities | 100 |
| Tenant | Initiatiefdefinities | 1000 |
| Scope | Toewijzing van beleid of initiatief | 100 |
| Beleidsdefinitie | Parameters | 20 |
| Initiatiefdefinitie | Beleidsregels | 100 |
| Initiatiefdefinitie | Parameters | 100 |
| Toewijzing van beleid of initiatief | Uitzonderingen (geen bereiken) | 400 |
| Beleidsregel | Geneste voor waarden | 512 |
