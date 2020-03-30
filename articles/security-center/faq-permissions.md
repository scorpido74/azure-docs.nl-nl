---
title: Veelgestelde vragen over Azure Security Center - vragen over machtigingen
description: Deze veelgestelde vragen beantwoorden vragen over machtigingen in Azure Security Center, een product waarmee u bedreigingen voorkomen, detecteren en erop reageren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599390"
---
# <a name="permissions"></a>Machtigingen

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hoe werken machtigingen in Azure Security Center?

Het Azure Beveiligingscentrum gebruikt [op rollen gebaseerd toegangsbeheer (RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center beoordeelt de configuratie van uw resources om beveiligingsproblemen en kwetsbaarheden te identificeren. In Security Center ziet u alleen informatie met betrekking tot een resource wanneer u de rol van eigenaar, bijdrager of lezer krijgt toegewezen voor het abonnement of de resourcegroep waartoe een resource behoort.

Zie [Machtigingen in Azure Security Center](security-center-permissions.md) voor meer informatie over rollen en toegestane acties in beveiligingscentrum.



## <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligingsbeleid wijzigen?

Als u een beveiligingsbeleid wilt wijzigen, moet u een beveiligingsbeheerder of eigenaar of bijdrager van dat abonnement zijn.

Zie [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligingsbeleid.