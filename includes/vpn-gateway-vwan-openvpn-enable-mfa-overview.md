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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471504"
---
Als u wilt dat gebruikers wordt gevraagd om een tweede authenticatie factor voordat ze toegang verlenen, kunt u Azure Multi-Factor Authentication (MFA) configureren. U kunt MFA configureren op basis van per gebruiker, of u kunt gebruikmaken van MFA via [voorwaardelijke toegang](../articles/active-directory/conditional-access/overview.md).

* MFA per gebruiker kan gratis worden ingeschakeld. Als MFA per gebruiker wordt ingeschakeld, wordt de gebruiker gevraagd om een tweede factor verificatie uit te scha kelen voor alle toepassingen die zijn verbonden met de Azure AD-Tenant. Zie [optie 1](#peruser) voor de stappen.
* Met voorwaardelijke toegang kunt u nauw keuriger bepalen hoe een tweede factor moet worden bevorderd. Het kan de toewijzing van MFA aan alleen VPN toestaan en andere toepassingen uitsluiten die zijn gekoppeld aan de Azure AD-Tenant. Zie [optie 2](#conditional) voor stappen.