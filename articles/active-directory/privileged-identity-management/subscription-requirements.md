---
title: Licentie vereisten voor het gebruik van PIM-Azure Active Directory | Microsoft Docs
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
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83c31c2731a8e872dfd2750fced8b91d283d0892
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804073"
---
# <a name="license-requirements-to-use-pim"></a>Licentie vereisten voor het gebruik van PIM

Als u Azure Active Directory (Azure AD) Privileged Identity Management (PIM) wilt gebruiken, moet een directory een geldige licentie hebben. Daarnaast moeten de licenties aan de beheerders en relevante gebruikers worden toegewezen. In dit artikel worden de licentie vereisten voor het gebruik van PIM beschreven.

## <a name="prerequisites"></a>Vereisten

Voor het gebruik van PIM moet uw Directory een van de volgende betaalde of proef licenties hebben:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Zie [Wat is Azure Active Directory?](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="which-users-must-have-licenses"></a>Welke gebruikers moeten licenties hebben?

Elke beheerder of gebruiker die met PIM communiceert of een voor deel ontvangt, moet een licentie hebben. Voorbeelden zijn:

- Beheerders met Azure AD-rollen die worden beheerd met PIM
- Beheerders met Azure-resource rollen die worden beheerd met PIM
- Beheerders die zijn toegewezen aan de rol beheerder privileged Role
- Gebruikers die in aanmerking komen voor Azure AD-rollen die worden beheerd met PIM
- Gebruikers kunnen aanvragen goed keuren of afwijzen in PIM
- Gebruikers die zijn toegewezen aan een Azure-resource functie met Just-in-time-of directe (op tijd gebaseerde) toewijzingen  
- Gebruikers die zijn toegewezen aan een toegangs beoordeling
- Gebruikers die toegangs beoordelingen uitvoeren

Zie [licenties toewijzen of verwijderen met behulp van de Azure Active Directory Portal](../fundamentals/license-users-groups.md)voor informatie over het toewijzen van licenties aan uw gebruik.

## <a name="what-happens-when-a-license-expires"></a>Wat gebeurt er wanneer een licentie verloopt?

Als een Azure AD Premium P2, EMS E5 of een proef licentie verloopt, zullen PIM-functies niet meer beschikbaar zijn in uw Directory:

- Permanente roltoewijzingen aan Azure AD-rollen worden niet beïnvloed.
- De PIM-service in de Azure Portal, evenals de Graph API-cmdlets en Power shell-interfaces van PIM, is niet langer beschikbaar voor gebruikers om geprivilegieerde rollen te activeren, bevoorrechte toegang te beheren of toegangs beoordelingen van geprivilegieerde rollen uit te voeren.
- De in aanmerking komende roltoewijzingen van Azure AD-rollen worden verwijderd omdat gebruikers geen bevoegde rollen meer kunnen activeren.
- Alle doorlopende toegangs beoordelingen van Azure AD-rollen worden beëindigd en PIM-configuratie-instellingen worden verwijderd.
- PIM verzendt geen e-mail berichten meer over roltoewijzings wijzigingen.

## <a name="next-steps"></a>Volgende stappen

- [PIM implementeren](pim-deployment-plan.md)
- [Beginnen met PIM](pim-getting-started.md)
- [Rollen die u niet kunt beheren in PIM](pim-roles.md)
