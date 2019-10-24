---
title: E-mail meldingen in PIM-Azure Active Directory | Microsoft Docs
description: Beschrijft e-mail meldingen in Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756386"
---
# <a name="email-notifications-in-pim"></a>E-mail meldingen in PIM

Met Privileged Identity Management (PIM) kunt u weten wanneer er belang rijke gebeurtenissen optreden in uw Azure Active Directory-organisatie (Azure AD), bijvoorbeeld wanneer een rol is toegewezen of geactiveerd. Privileged Identity Management houdt u op de hoogte door u en andere deel nemers e-mail meldingen te sturen. Deze e-mail berichten kunnen ook koppelingen naar relevante taken bevatten, zoals het activeren of vernieuwen van een rol. In dit artikel wordt beschreven hoe deze e-mails eruitzien wanneer ze worden verzonden en wie ze ontvangt.

## <a name="sender-email-address-and-subject-line"></a>E-mail adres van afzender en onderwerpregel

E-mails die zijn verzonden vanuit Privileged Identity Management voor zowel Azure AD-als Azure-resource rollen, hebben het volgende e-mail adres van de afzender:

- E-mail adres: **Azure-reply \@microsoft. com**
- Weergave naam: Microsoft Azure

Deze e-mail berichten bevatten een **PIM** -voor voegsel in de onderwerpregel. Hier volgt een voorbeeld:

- PIM: Alain Charon is permanent toegewezen aan de rol van back-uplezer

## <a name="notifications-for-azure-ad-roles"></a>Meldingen voor Azure AD-rollen

Privileged Identity Management verzendt e-mails wanneer de volgende gebeurtenissen optreden voor Azure AD-rollen:

- Wanneer de activering van een bevoorrechte rol in behandeling is
- Wanneer een aanvraag voor het activeren van een bevoorrechte rol is voltooid
- Wanneer Azure AD Privileged Identity Management is ingeschakeld

Wie deze e-mails voor Azure AD-rollen ontvangt, is afhankelijk van uw rol, de gebeurtenis en de instelling voor meldingen:

| Gebruiker | Activering van rol is in behandeling | De activerings aanvraag voor de rol is voltooid | PIM is ingeschakeld |
| --- | --- | --- | --- |
| Beheerder van geprivilegieerde rol</br>(Geactiveerd/in aanmerking komend) | Ja</br>(alleen als er geen expliciete goed keurders zijn opgegeven) | Ja* | Ja |
| Beveiligings beheerder</br>(Geactiveerd/in aanmerking komend) | Nee | Ja* | Ja |
| Globale beheerder</br>(Geactiveerd/in aanmerking komend) | Nee | Ja* | Ja |

\* als de [instelling **meldingen** ](pim-how-to-change-default-settings.md#notifications) is ingesteld op **inschakelen**.

Hieronder ziet u een voor beeld van een e-mail bericht dat wordt verzonden wanneer een gebruiker een Azure AD-rol activeert voor de fictieve Contoso-organisatie.

![Nieuwe Privileged Identity Management e-mail voor Azure AD-rollen](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Wekelijks Privileged Identity Management Digest-e-mail voor Azure AD-rollen

Een wekelijks Privileged Identity Management overzichts-e-mail voor Azure AD-rollen wordt verzonden naar bevoegde beheerdersrol, beveiligings beheerders en globale beheerders die Privileged Identity Management hebben ingeschakeld. Deze wekelijkse e-mail bevat een moment opname van Privileged Identity Management activiteiten voor de week, evenals privileged Role Assignments. Het is alleen beschikbaar voor tenants in de open bare Cloud. Hier volgt een voor beeld van een e-mail bericht:

![Wekelijks Privileged Identity Management Digest-e-mail voor Azure AD-rollen](./media/pim-email-notifications/email-directory-weekly.png)

Het e-mail bericht bevat vier tegels:

| Tegel | Beschrijving |
| --- | --- |
| **Geactiveerde gebruikers** | Aantal keren dat gebruikers hun in aanmerking komende rol in de Tenant hebben geactiveerd. |
| **Gebruikers die permanent zijn gemaakt** | Aantal keren dat gebruikers met een in aanmerking komende toewijzing permanent worden gemaakt. |
| **Roltoewijzingen in Privileged Identity Management** | Aantal keren dat gebruikers een in aanmerking komende rol binnen Privileged Identity Management worden toegewezen. |
| **Roltoewijzingen buiten PIM** | Aantal keren dat gebruikers een permanente rol hebben gekregen buiten Privileged Identity Management (binnen Azure AD). |

In het gedeelte **overzicht van uw belangrijkste rollen** worden de vijf meest voorkomende rollen in uw Tenant vermeld, op basis van het totale aantal permanente en in aanmerking komende beheerders voor elke rol. Met de koppeling **actie ondernemen** opent u de [PIM-wizard](pim-security-wizard.md) waarin u permanente beheerders kunt converteren naar in aanmerking komende beheerders in batches.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-mails voor Azure-resource rollen

Privileged Identity Management verzendt e-mails naar eigen aars en beheerders van gebruikers toegang wanneer de volgende gebeurtenissen optreden voor Azure-resource rollen:

- Wanneer een roltoewijzing in afwachting is van goed keuring
- Wanneer een rol is toegewezen
- Wanneer een rol binnenkort verloopt
- Wanneer een rol in aanmerking komt voor uitbrei ding
- Wanneer een rol wordt vernieuwd door een eind gebruiker
- Wanneer een aanvraag voor het activeren van een rol is voltooid

Privileged Identity Management stuurt e-mail berichten naar eind gebruikers wanneer de volgende gebeurtenissen optreden voor Azure-resource rollen:

- Wanneer een rol aan de gebruiker is toegewezen
- Wanneer de rol van een gebruiker is verlopen
- Wanneer de rol van een gebruiker wordt uitgebreid
- Wanneer de activerings aanvraag van een gebruiker is voltooid

Hieronder ziet u een voor beeld van een e-mail bericht dat wordt verzonden wanneer aan een gebruiker een Azure-resource functie is toegewezen voor de fictieve Contoso-organisatie.

![Nieuw Privileged Identity Management e-mail voor Azure-resource rollen](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-functies configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Aanvragen voor Azure AD-rollen in Privileged Identity Management goed keuren of weigeren](azure-ad-pim-approval-workflow.md)
