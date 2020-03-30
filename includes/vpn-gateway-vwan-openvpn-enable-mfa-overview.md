---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471504"
---
Als u wilt dat gebruikers worden gevraagd om een tweede verificatiefactor voordat u toegang verleent, u Azure Multi-Factor Authentication (MFA) configureren. U MFA per gebruiker configureren, of u MFA gebruiken via [Voorwaardelijke toegang.](../articles/active-directory/conditional-access/overview.md)

* MFA per gebruiker kan zonder extra kosten worden ingeschakeld. Wanneer u MFA per gebruiker inschakelt, wordt de gebruiker gevraagd om tweedefactorverificatie ten opzichte van alle toepassingen die zijn gekoppeld aan de Azure AD-tenant. Zie [Optie 1](#peruser) voor stappen.
* Voorwaardelijke toegang zorgt voor fijnmazige controle over hoe een tweede factor moet worden bevorderd. Hiermee kan de toewijzing van MFA alleen vpn worden toegewezen en andere toepassingen uitsluiten die zijn gekoppeld aan de Azure AD-tenant. Zie [Optie 2](#conditional) voor stappen.