---
title: Aangepaste besturingselementen in voorwaardelijke toegang tot Azure AD
description: Lees hoe aangepaste besturingselementen in Azure Active Directory Voorwaardelijke toegang werken.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050538"
---
# <a name="custom-controls-preview"></a>Aangepaste besturingselementen (voorbeeld)

Aangepaste besturingselementen is een voorbeeldmogelijkheid van de Azure Active Directory. Wanneer u aangepaste besturingselementen gebruikt, worden uw gebruikers doorgestuurd naar een compatibele service om te voldoen aan verificatievereisten buiten Azure Active Directory. Om aan dit controlepunt te voldoen, wordt de browser van een gebruiker doorgestuurd naar de externe service, voert u de vereiste verificatie uit en wordt deze vervolgens teruggestuurd naar Azure Active Directory. Azure Active Directory verifieert het antwoord en als de gebruiker is geverifieerd of gevalideerd, gaat de gebruiker verder in de stroom voor voorwaardelijke toegang.

> [!NOTE]
> Voor meer informatie over wijzigingen zijn we van plan om de custom control-mogelijkheid te gebruiken, zie de [nieuwe update van](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)februari 2020.

## <a name="creating-custom-controls"></a>Aangepaste besturingselementen maken

Custom Controls werkt met een beperkte set goedgekeurde verificatieproviders. Als u een aangepast besturingselement wilt maken, moet u eerst contact opnemen met de provider die u wilt gebruiken. Elke niet-Microsoft-provider heeft zijn eigen proces en vereisten om zich aan te melden, u te abonneren of anderszins deel uit te maken van de service en aan te geven dat u wilt integreren met Voorwaardelijke toegang. Op dat moment zal de provider u voorzien van een blok van gegevens in JSON-formaat. Met deze gegevens kunnen de provider en voorwaardelijke toegang samenwerken voor uw tenant, wordt het nieuwe besturingselement gemaakt en wordt gedefinieerd hoe voorwaardelijke toegang kan zien of uw gebruikers verificatie met de provider hebben uitgevoerd.

Kopieer de JSON-gegevens en plak deze vervolgens in het bijbehorende tekstvak. Breng geen wijzigingen aan in de JSON, tenzij u de wijziging die u aanbrengt expliciet begrijpt. Als u een wijziging doormaakt, kan de verbinding tussen de provider en Microsoft worden verbroken en u en uw gebruikers mogelijk uw accounts afsluiten.

De optie om een aangepast besturingselement te maken bevindt zich in de sectie **Beheren** van de pagina **Voorwaardelijke toegang.**

![Beheer](./media/controls/82.png)

Als u op **Nieuw aangepast besturingselement**klikt, opent u een blad met een tekstvak voor de JSON-gegevens van uw besturingselement.  

![Beheer](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Aangepaste besturingselementen verwijderen

Als u een aangepast besturingselement wilt verwijderen, moet u er eerst voor zorgen dat het niet wordt gebruikt in een beleid voor voorwaardelijke toegang. Eenmaal voltooid:

1. Ga naar de lijst Aangepaste besturingselementen
1. Klik...  
1. Selecteer **Verwijderen**.

## <a name="editing-custom-controls"></a>Aangepaste besturingselementen bewerken

Als u een aangepast besturingselement wilt bewerken, moet u het huidige besturingselement verwijderen en een nieuw besturingselement maken met de bijgewerkte informatie.

## <a name="known-limitations"></a>Bekende beperkingen

Aangepaste besturingselementen kunnen niet worden gebruikt met de automatisering van Identity Protection die Azure Multi-Factor Authentication, Azure AD self-service password reset (SSPR) vereist, voldoen aan vereisten voor multi-factor authenticatie claim of om rollen in Privileged te verhogen Identity Manager (PIM).

## <a name="next-steps"></a>Volgende stappen

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)

- [Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
