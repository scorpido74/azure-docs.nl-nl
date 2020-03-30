---
title: Licentievereisten voor het gebruik van privileged identity management - Azure Active Directory | Microsoft Documenten
description: Beschrijft de licentievereisten voor het gebruik van Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932326"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licentievereisten voor het gebruik van Privileged Identity Management

Als u Azure Active Directory (Azure AD) Privileged Identity Management (PIM) wilt gebruiken, moet een map een geldige licentie hebben. Bovendien moeten licenties worden toegewezen aan de beheerders en relevante gebruikers. In dit artikel worden de licentievereisten beschreven voor het gebruik van Privileged Identity Management.

## <a name="valid-licenses"></a>Geldige licenties

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Hoeveel licenties moet u hebben?

Controleer of uw directory minstens evenveel Azure AD Premium P2-licenties heeft als werknemers die de volgende taken uitvoeren:

- Gebruikers die in aanmerking komen voor Azure AD-rollen die worden beheerd met PIM
- Gebruikers kunnen activeringsaanvragen goedkeuren of weigeren in PIM
- Gebruikers die zijn toegewezen aan een Azure-bronrol met just-in-time of directe (op tijd gebaseerde) toewijzingen  
- Gebruikers die zijn toegewezen aan een toegangscontrole
- Gebruikers die toegangsbeoordelingen uitvoeren

Azure AD Premium **P2-licenties** zijn niet vereist voor de volgende taken:

- Er zijn geen licenties vereist voor gebruikers met de rollen Globale beheerder of bevoorrechte rolbeheerder die PIM instellen, beleid configureren, waarschuwingen ontvangen en toegangsbeoordelingen instellen.

Zie [Licenties toewijzen of verwijderen met de Azure Active Directory-portal](../fundamentals/license-users-groups.md)voor meer informatie over licenties.

## <a name="example-license-scenarios"></a>Voorbeeldlicentiescenario's

Hier volgen enkele voorbeeldlicentiescenario's waarmee u bepalen hoeveel licenties u moet hebben.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Woodgrove Bank heeft 10 beheerders voor verschillende afdelingen en 2 Globale Beheerders die PIM configureren en beheren. Ze maken vijf beheerders in aanmerking. | Vijf licenties voor de beheerders die in aanmerking komen | 5 |
| Graphic Design Institute heeft 25 beheerders waarvan er 14 worden beheerd via PIM. Rolactivering vereist goedkeuring en er zijn drie verschillende gebruikers in de organisatie die activeringen kunnen goedkeuren. | 14 licenties voor de in aanmerking komende rollen + drie fiatteurs | 17 |
| Contoso heeft 50 beheerders waarvan er 42 via PIM worden beheerd. Rolactivering vereist goedkeuring en er zijn vijf verschillende gebruikers in de organisatie die activeringen kunnen goedkeuren. Contoso doet ook maandelijkse beoordelingen van gebruikers die zijn toegewezen aan beheerdersrollen en revisoren zijn de beheerders van de gebruikers waarvan er zes niet in beheerdersrollen zijn die door PIM worden beheerd. | 42 licenties voor de in aanmerking komende rollen + vijf fiatteurs + zes revisoren | 53 |

## <a name="what-happens-when-a-license-expires"></a>Wat gebeurt er als een licentie verloopt?

Als een Azure AD Premium P2- of EMS E5- of proeflicentie verloopt, zijn privileged Identity Management-functies niet langer beschikbaar in uw directory:

- Permanente roltoewijzingen voor Azure AD-rollen worden niet beïnvloed.
- De privileged identity management-service in de Azure-portal en de Graph API-cmdlets en PowerShell-interfaces van Privileged Identity Management zijn niet langer beschikbaar voor gebruikers om bevoorrechte rollen te activeren, bevoorrechte toegang te beheren of uit te voeren toegang tot beoordelingen van bevoorrechte rollen.
- In aanmerking komende roltoewijzingen van Azure AD-rollen worden verwijderd, omdat gebruikers geen bevoorrechte rollen meer kunnen activeren.
- Alle doorlopende toegangsbeoordelingen van Azure AD-rollen worden beëindigd en de configuratie-instellingen voor privileged identity management worden verwijderd.
- Privileged Identity Management stuurt geen e-mails meer over wijzigingen in roltoewijzingen.

## <a name="next-steps"></a>Volgende stappen

- [Privileged Identity Management implementeren](pim-deployment-plan.md)
- [Aan de slag met Privileged Identity Management](pim-getting-started.md)
- [Rollen die u niet beheren in Privileged Identity Management](pim-roles.md)
