---
title: Licentie vereisten voor het gebruik van Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Beschrijft de licentie vereisten voor het gebruik van Azure AD Privileged Identity Management (PIM).
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
ms.topic: how-to
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d931206e99516e0320d0cbedd0812389ee41aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742161"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licentie vereisten voor het gebruik van Privileged Identity Management

Als u Azure Active Directory (Azure AD) Privileged Identity Management (PIM) wilt gebruiken, moet een directory een geldige licentie hebben. Daarnaast moeten de licenties aan de beheerders en relevante gebruikers worden toegewezen. In dit artikel worden de licentie vereisten beschreven voor het gebruik van Privileged Identity Management.

## <a name="valid-licenses"></a>Geldige licenties

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Hoeveel licenties moet u hebben?

Zorg ervoor dat uw directory ten minste evenveel Azure AD Premium P2-licenties heeft wanneer u werk nemers hebt die de volgende taken uitvoeren:

- Gebruikers die in aanmerking komen voor Azure AD-rollen die worden beheerd met PIM
- Gebruikers kunnen activerings aanvragen goed keuren of afwijzen in PIM
- Gebruikers die zijn toegewezen aan een Azure-resource functie met Just-in-time-of directe (op tijd gebaseerde) toewijzingen  
- Gebruikers die zijn toegewezen aan een toegangs beoordeling
- Gebruikers die toegangs beoordelingen uitvoeren

Azure AD Premium P2-licenties zijn **niet** vereist voor de volgende taken:

- Er zijn geen licenties vereist voor gebruikers met de beheerders rollen globale beheerder of bevoorrechte rol die PIM instellen, beleids regels configureren, waarschuwingen ontvangen en toegangs beoordelingen instellen.

Zie [licenties toewijzen of verwijderen met de Azure Active Directory-Portal](../fundamentals/license-users-groups.md)voor meer informatie over licenties.

## <a name="example-license-scenarios"></a>Voor beeld van licentie scenario's

Hier volgen enkele voor beelden van licentie scenario's die u helpen bij het bepalen van het aantal licenties dat u nodig hebt.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Woodgrove Bank heeft 10 beheerders voor verschillende afdelingen en twee globale beheerders die PIM configureren en beheren. Ze maken vijf beheerders in aanmerking. | Vijf licenties voor de beheerders die in aanmerking komen | 5 |
| Grafisch ontwerp Institute heeft 25 beheerders waarvan 14 wordt beheerd via PIM. Activering van rol vereist goed keuring en er zijn drie verschillende gebruikers in de organisatie die activeringen kunnen goed keuren. | 14 licenties voor de in aanmerking komende rollen + drie goed keurders | 17 |
| Contoso heeft 50 beheerders van welke 42 via PIM worden beheerd. Activering van rol vereist goed keuring en er zijn vijf verschillende gebruikers in de organisatie die activeringen kunnen goed keuren. Contoso houdt ook maandelijkse beoordelingen bij van gebruikers die zijn toegewezen aan beheerders rollen en controleurs zijn de managers van de gebruikers waarvan zes geen beheerder rollen hebben die worden beheerd door PIM. | 42 licenties voor de in aanmerking komende rollen + vijf goed keurders + zes revisoren | 53 |

## <a name="what-happens-when-a-license-expires"></a>Wat gebeurt er wanneer een licentie verloopt?

Als een Azure AD Premium P2, EMS E5 of een proef licentie verloopt, zijn Privileged Identity Management-functies niet meer beschikbaar in uw Directory:

- Permanente roltoewijzingen aan Azure AD-rollen worden niet beïnvloed.
- De Privileged Identity Management-service in de Azure Portal en de Graph API-cmdlets en Power shell-interfaces van Privileged Identity Management zijn niet langer beschikbaar voor gebruikers om geprivilegieerde rollen te activeren, bevoorrechte toegang te beheren of toegangs beoordelingen van geprivilegieerde rollen uit te voeren.
- De in aanmerking komende roltoewijzingen van Azure AD-rollen worden verwijderd omdat gebruikers geen bevoegde rollen meer kunnen activeren.
- Alle doorlopende toegangs beoordelingen van Azure AD-rollen worden beëindigd en Privileged Identity Management configuratie-instellingen worden verwijderd.
- Privileged Identity Management verzendt geen e-mail berichten meer over roltoewijzings wijzigingen.

## <a name="next-steps"></a>Volgende stappen

- [Privileged Identity Management implementeren](pim-deployment-plan.md)
- [Aan de slag met Privileged Identity Management](pim-getting-started.md)
- [Rollen die u niet kunt beheren in Privileged Identity Management](pim-roles.md)
