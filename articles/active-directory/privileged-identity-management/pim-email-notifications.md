---
title: E-mailmeldingen in PIM - Azure Active Directory | Microsoft Documenten
description: Beschrijft e-mailmeldingen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72756386"
---
# <a name="email-notifications-in-pim"></a>E-mailmeldingen in PIM

Met Privileged Identity Management (PIM) u weten wanneer belangrijke gebeurtenissen plaatsvinden in uw Azure Active Directory-organisatie (Azure AD), zoals wanneer een rol is toegewezen of geactiveerd. Privileged Identity Management houdt u op de hoogte door u en andere deelnemers e-mailmeldingen te sturen. Deze e-mails kunnen ook koppelingen bevatten naar relevante taken, zoals het activeren of vernieuwen van een rol. In dit artikel wordt beschreven hoe deze e-mails eruit zien, wanneer ze worden verzonden en wie ze ontvangt.

## <a name="sender-email-address-and-subject-line"></a>E-mailadres en onderwerpregel van afzender

E-mails die zijn verzonden vanuit Privileged Identity Management voor zowel Azure AD- als Azure-bronrollen hebben het volgende e-mailadres van afzender:

- E-mailadres: **azure-noreply\@microsoft.com**
- Weergavenaam: Microsoft Azure

Deze e-mails bevatten een PIM-voorvoegsel in de onderwerpregel. **PIM** Hier volgt een voorbeeld:

- PIM: Alain Charon kreeg de rol Backup Reader permanent toegewezen

## <a name="notifications-for-azure-ad-roles"></a>Meldingen voor Azure AD-rollen

Privileged Identity Management verzendt e-mails wanneer de volgende gebeurtenissen plaatsvinden voor Azure AD-rollen:

- Wanneer een geprivilegieerde rolactivering in behandeling is, moet deze worden goedgekeurd
- Wanneer een geprivilegieerde activeringsaanvraag is voltooid
- Wanneer Azure AD Privileged Identity Management is ingeschakeld

Wie deze e-mails ontvangt voor Azure AD-rollen, is afhankelijk van uw rol, de gebeurtenis en de instelling voor meldingen:

| Gebruiker | Rolactivering is in afwachting van goedkeuring | Aanvraag voor activering van rollen is voltooid | PIM is ingeschakeld |
| --- | --- | --- | --- |
| Beheerder van een bevoorrechte rol</br>(Geactiveerd/in aanmerking komend) | Ja</br>(alleen als er geen expliciete fiatteurs zijn opgegeven) | Ja* | Ja |
| Beveiligingsbeheer</br>(Geactiveerd/in aanmerking komend) | Nee | Ja* | Ja |
| Globale beheerder</br>(Geactiveerd/in aanmerking komend) | Nee | Ja* | Ja |

\*Als de instelling [ **Meldingen** ](pim-how-to-change-default-settings.md#notifications) is ingesteld **op Inschakelen**.

In het volgende ziet u een voorbeelde-mail die wordt verzonden wanneer een gebruiker een Azure AD-rol activeert voor de fictieve Contoso-organisatie.

![Nieuwe e-mail voor privileged identity management voor Azure AD-rollen](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Wekelijkse privileged identity management digest e-mail voor Azure AD rollen

Een wekelijkse samenvatting van het overzicht voor bestandsbeheer van Azure-functies wordt verzonden naar beheerders van bevoorrechte rollen, beveiligingsbeheerders en globale beheerders die Privileged Identity Management hebben ingeschakeld. Deze wekelijkse e-mail bevat een momentopname van privileged identity management-activiteiten voor de week en bevoorrechte roltoewijzingen. Het is alleen beschikbaar voor huurders in de openbare cloud. Hier is een voorbeeld e-mail:

![Wekelijkse privileged identity management digest e-mail voor Azure AD rollen](./media/pim-email-notifications/email-directory-weekly.png)

De e-mail bevat vier tegels:

| Tegel | Beschrijving |
| --- | --- |
| **Gebruikers geactiveerd** | Het aantal keren dat gebruikers hun in aanmerking komende rol in de tenant hebben geactiveerd. |
| **Gebruikers maakten permanente** | Het aantal keren dat gebruikers met een in aanmerking komende opdracht permanent worden gemaakt. |
| **Roltoewijzingen in Privileged Identity Management** | Het aantal keren dat gebruikers een in aanmerking komende rol krijgen toegewezen binnen Privileged Identity Management. |
| **Rolopdrachten buiten PIM** | Het aantal keren dat gebruikers een permanente rol krijgen buiten Privileged Identity Management (binnen Azure AD). |

In het overzicht van de sectie **Toprollen** worden de vijf belangrijkste rollen in uw tenant weergegeven op basis van het totale aantal permanente en in aanmerking komende beheerders voor elke rol. Met de koppeling **Actie uitvoeren** wordt de wizard [PIM geopend,](pim-security-wizard.md) waar u permanente beheerders converteren naar in aanmerking komende beheerders in batches.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-mails voor Azure-bronrollen

Privileged Identity Management stuurt e-mails naar eigenaren en beheerders van gebruikerstoegang wanneer de volgende gebeurtenissen plaatsvinden voor Azure-bronrollen:

- Wanneer een roltoewijzing in behandeling is, moet deze worden goedgekeurd
- Wanneer een rol is toegewezen
- Wanneer een rol binnenkort verloopt
- Wanneer een rol in aanmerking komt om
- Wanneer een rol wordt vernieuwd door een eindgebruiker
- Wanneer een aanvraag voor activering van een rol is voltooid

Privileged Identity Management stuurt e-mails naar eindgebruikers wanneer de volgende gebeurtenissen plaatsvinden voor Azure-bronrollen:

- Wanneer een rol aan de gebruiker is toegewezen
- Wanneer de rol van een gebruiker is verlopen
- Wanneer de rol van een gebruiker wordt uitgebreid
- Wanneer de activeringsaanvraag van een gebruiker is voltooid

In het volgende ziet u een voorbeelde-mail die wordt verzonden wanneer een gebruiker een Azure-bronrol voor de fictieve Contoso-organisatie krijgt toegewezen.

![Nieuwe e-mail voor privileged identity management voor Azure-bronrollen](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rolinstellingen configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Aanvragen voor Azure AD-rollen in Privileged Identity Management goedkeuren of weigeren](azure-ad-pim-approval-workflow.md)
