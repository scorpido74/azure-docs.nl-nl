---
title: Aanbevolen procedures voor Azure RBAC
description: Aanbevolen procedures voor het gebruik van Azure role-based access control (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726772"
---
# <a name="best-practices-for-azure-rbac"></a>Aanbevolen procedures voor Azure RBAC

In dit artikel worden enkele aanbevolen procedures beschreven voor het gebruik van Azure-role-based access control (Azure RBAC). Deze best practices zijn afgeleid van onze ervaring met Azure RBAC en de ervaringen van klanten zoals jij.

## <a name="only-grant-the-access-users-need"></a>Geef alleen de toegang die gebruikers nodig hebben

Met Azure RBAC u taken binnen uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te verlenen in uw Azure-abonnement of -resources, kunt u zelf bepalen welke acties er met een bepaalde bereik zijn toegestaan.

Het is een aanbevolen procedure om tijdens het plannen van een strategie voor toegangsbeheer gebruikers minimale bevoegdheden te verlenen om hun werk gedaan te krijgen. In het volgende diagram wordt een voorgesteld patroon weergegeven voor het gebruik van RBAC.

![RBAC en minimale bevoegdheden](./media/best-practices/rbac-least-privilege.png)

Zie [Roltoewijzingen toevoegen of verwijderen](role-assignments-portal.md)voor informatie over het toevoegen van roltoewijzingen.

## <a name="limit-the-number-of-subscription-owners"></a>Het aantal abonnementeigenaren beperken

U moet maximaal 3 abonnementseigenaren hebben om de kans op schending door een gecompromitteerde eigenaar te verminderen. Deze aanbeveling kan worden gecontroleerd in Azure Security Center. Zie Beveiligingsaanbevelingen voor andere identiteits- en toegangsaanbevelingen in Security Center [- een naslaggids](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD-privileged Identity Management gebruiken

Om bevoorrechte accounts te beschermen tegen kwaadaardige cyberaanvallen, u Azure Active Directory Privileged Identity Management (PIM) gebruiken om de blootstellingstijd van bevoegdheden te verlagen en uw zichtbaarheid in het gebruik ervan te vergroten door middel van rapporten en waarschuwingen. PIM helpt bevoorrechte accounts te beschermen door just-in-time bevoorrechte toegang te bieden tot Azure AD- en Azure-bronnen. Toegang kan tijdgebonden zijn waarna bevoegdheden automatisch worden ingetrokken. 

Zie Wat is Azure AD Privileged Identity Management voor meer [informatie?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure RBAC oplossen](troubleshooting.md)