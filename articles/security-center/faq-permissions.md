---
title: 'Azure Security Center Veelgestelde vragen: vragen over machtigingen'
description: Deze veelgestelde vragen vindt u antwoorden op vragen over machtigingen in Azure Security Center, een product waarmee u bedreigingen kunt voor komen, detecteren en reageren.
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599390"
---
# <a name="permissions"></a>Machtigingen

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hoe werken de machtigingen in Azure Security Center?

Het Azure Beveiligingscentrum gebruikt [op rollen gebaseerd toegangsbeheer (RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center beoordeelt de configuratie van uw resources om beveiligingsproblemen met zich mee en beveiligingsproblemen te identificeren. In Security Center ziet u alleen informatie met betrekking tot een bron wanneer u de rol van eigenaar, bijdrager of lezer voor het abonnement of de resourcegroep die deel uitmaakt van een resource om te worden toegewezen.

Zie [machtigingen in azure Security Center](security-center-permissions.md) voor meer informatie over rollen en toegestane acties in Security Center.



## <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligingsbeleid alleen wijzigen?

Als u wilt een beveiligingsbeleid alleen wijzigen, moet u een beveiligingsbeheerder of een eigenaar of bijdrager van het abonnement zijn.

Zie [beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligings beleid.