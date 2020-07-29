---
title: Aangepaste besturings elementen in voorwaardelijke toegang voor Azure AD
description: Meer informatie over het werken met aangepaste besturings elementen in Azure Active Directory voorwaardelijke toegang.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d926d027b59a225d76f6a73ab3ee4ec09b595e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253304"
---
# <a name="custom-controls-preview"></a>Aangepaste besturings elementen (preview-versie)

Aangepaste besturings elementen zijn een preview-functie van de Azure Active Directory. Wanneer u aangepaste besturings elementen gebruikt, worden uw gebruikers omgeleid naar een compatibele service om te voldoen aan de verificatie vereisten buiten Azure Active Directory. Om aan dit besturings element te voldoen, wordt de browser van een gebruiker omgeleid naar de externe service, wordt elke vereiste verificatie uitgevoerd en wordt vervolgens teruggeleid naar Azure Active Directory. Azure Active Directory controleert het antwoord en, als de gebruiker is geverifieerd of gevalideerd, wordt de gebruiker in de stroom voor voorwaardelijke toegang voortgezet.

> [!NOTE]
> Zie de [nieuwe update van](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)februari 2020 voor meer informatie over wijzigingen die zijn gepland voor de aangepaste beheer functie.

## <a name="creating-custom-controls"></a>Aangepaste besturings elementen maken

Aangepaste besturings elementen werken met een beperkt aantal goedgekeurde verificatie providers. Als u een aangepast besturings element wilt maken, moet u eerst contact opnemen met de provider die u wilt gebruiken. Elke niet-micro soft-provider heeft een eigen proces en vereisten om zich aan te melden, te abonneren of anderszins deel te nemen aan de service, en om aan te geven dat u wilt integreren met voorwaardelijke toegang. Op dat moment geeft de provider u een blok gegevens in JSON-indeling. Met deze gegevens kan de provider en de voorwaardelijke toegang samen werken voor uw Tenant, wordt het nieuwe besturings element gemaakt en wordt gedefinieerd hoe voorwaardelijke toegang kan zien of uw gebruikers verificatie hebben uitgevoerd met de provider.

Kopieer de JSON-gegevens en plak deze in het bijbehorende tekstvak. Breng geen wijzigingen aan in de JSON, tenzij u de wijziging die u aanbrengt expliciet begrijpt. Als u een wijziging aanbrengt, kan de verbinding tussen de provider en micro soft worden verbroken en kunnen u en uw gebruikers uw accounts vergren delen.

De optie voor het maken van een aangepast besturings element bevindt zich in de sectie **beheren** van de pagina **voorwaardelijke toegang** .

![Beheer](./media/controls/82.png)

Als u op **nieuw aangepast besturings element**klikt, wordt een Blade geopend met een tekstvak voor de JSON-gegevens van uw besturings element.  

![Beheer](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Aangepaste besturings elementen verwijderen

Als u een aangepast besturings element wilt verwijderen, moet u er eerst voor zorgen dat het niet wordt gebruikt in een beleid voor voorwaardelijke toegang. Eenmaal voltooid:

1. Ga naar de lijst met aangepaste besturings elementen
1. Klik op...  
1. Selecteer **Verwijderen**.

## <a name="editing-custom-controls"></a>Aangepaste besturings elementen bewerken

Als u een aangepast besturings element wilt bewerken, moet u het huidige besturings element verwijderen en een nieuw besturings element maken met de bijgewerkte gegevens.

## <a name="known-limitations"></a>Bekende beperkingen

Aangepaste besturings elementen kunnen niet worden gebruikt met automatisering van identiteits beveiliging waarvoor Azure Multi-Factor Authentication is vereist, Azure AD self-service password reset (SSPR), voldoen aan de vereisten voor de multi-factor Authentication-claim of voor het verhogen van rollen in privileged Identity Manager (PIM).

## <a name="next-steps"></a>Volgende stappen

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)

- [Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
