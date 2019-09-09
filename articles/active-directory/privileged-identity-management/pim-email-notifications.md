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
ms.openlocfilehash: f0db4828ef7a6d3392ce53b2f2f95b0f7f014d65
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804542"
---
# <a name="email-notifications-in-pim"></a>E-mail meldingen in PIM

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u zien wanneer er belang rijke gebeurtenissen optreden, zoals wanneer een rol is toegewezen of geactiveerd. PIM houdt u op de hoogte door u en andere deel nemers e-mail meldingen te sturen. Deze e-mail berichten kunnen ook koppelingen naar relevante taken bevatten, zoals het activeren of vernieuwen van een rol. In dit artikel wordt beschreven hoe deze e-mails eruitzien wanneer ze worden verzonden en wie ze ontvangt.

## <a name="sender-email-address-and-subject-line"></a>E-mail adres van afzender en onderwerpregel

E-mails die zijn verzonden vanuit PIM voor zowel Azure AD-als Azure-resource rollen, hebben het volgende e-mail adres van de afzender:

- E-mail adres: **Azure-\@Microsoft.com-antwoord**
- Weergave naam: Microsoft Azure

Deze e-mail berichten bevatten een **PIM** -voor voegsel in de onderwerpregel. Hier volgt een voorbeeld:

- PIM: De rol van back-uplezer is permanent toegewezen aan Alain Charon

## <a name="pim-emails-for-azure-ad-roles"></a>PIM-e-mails voor Azure AD-rollen

PIM verzendt e-mails wanneer de volgende gebeurtenissen optreden voor Azure AD-rollen:

- Wanneer de activering van een bevoorrechte rol in behandeling is
- Wanneer een aanvraag voor het activeren van een bevoorrechte rol is voltooid
- Wanneer Azure AD PIM is ingeschakeld

Wie deze e-mails voor Azure AD-rollen ontvangt, is afhankelijk van uw rol, de gebeurtenis en de instelling voor meldingen:

| Gebruiker | Activering van rol is in behandeling | De activerings aanvraag voor de rol is voltooid | PIM is ingeschakeld |
| --- | --- | --- | --- |
| Beheerder met bevoorrechte rol</br>(Geactiveerd/in aanmerking komend) | Ja</br>(alleen als er geen expliciete goed keurders zijn opgegeven) | Ja* | Ja |
| Beveiligingsbeheerder</br>(Geactiveerd/in aanmerking komend) | Nee | Ja* | Ja |
| Globale beheerder</br>(Geactiveerd/in aanmerking komend) | Nee | Ja* | Ja |

\*Als de [instelling **meldingen** ](pim-how-to-change-default-settings.md#notifications) is ingesteld op **inschakelen**.

Hieronder ziet u een voor beeld van een e-mail bericht dat wordt verzonden wanneer een gebruiker een Azure AD-rol activeert voor de fictieve Contoso-organisatie.

![Nieuwe PIM-e-mail voor Azure AD-rollen](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Wekelijks PIM-Digest-e-mail adres voor Azure AD-rollen

Een wekelijks PIM-overzichts-e-mail voor Azure AD-rollen wordt verzonden naar bevoegde beheerdersrol, beveiligings beheerders en globale beheerders die PIM hebben ingeschakeld. Deze wekelijkse e-mail bevat een moment opname van PIM-activiteiten voor de week, evenals privileged Role Assignments. Het is alleen beschikbaar voor tenants in de open bare Cloud. Hier volgt een voor beeld van een e-mail bericht:

![Wekelijks PIM-Digest-e-mail adres voor Azure AD-rollen](./media/pim-email-notifications/email-directory-weekly.png)

Het e-mail bericht bevat vier tegels:

| Naast elkaar | Description |
| --- | --- |
| **Geactiveerde gebruikers** | Aantal keren dat gebruikers hun in aanmerking komende rol in de Tenant hebben geactiveerd. |
| **Gebruikers die permanent zijn gemaakt** | Aantal keren dat gebruikers met een in aanmerking komende toewijzing permanent worden gemaakt. |
| **Roltoewijzingen in PIM** | Aantal keren dat gebruikers een in aanmerking komende rol in PIM hebben toegewezen. |
| **Roltoewijzingen buiten PIM** | Aantal keren dat gebruikers een permanente rol hebben toegewezen buiten PIM (binnen Azure AD). |

In het gedeelte **overzicht van uw belangrijkste rollen** worden de vijf meest voorkomende rollen in uw Tenant vermeld, op basis van het totale aantal permanente en in aanmerking komende beheerders voor elke rol. Met de koppeling **actie ondernemen** opent u de [PIM-wizard](pim-security-wizard.md) waarin u permanente beheerders kunt converteren naar in aanmerking komende beheerders in batches.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-mails voor Azure-resource rollen

PIM verzendt e-mails naar eigen aars en beheerders van gebruikers toegang wanneer de volgende gebeurtenissen optreden voor Azure-resource rollen:

- Wanneer een roltoewijzing in afwachting is van goed keuring
- Wanneer een rol is toegewezen
- Wanneer een rol binnenkort verloopt
- Wanneer een rol in aanmerking komt voor uitbrei ding
- Wanneer een rol wordt vernieuwd door een eind gebruiker
- Wanneer een aanvraag voor het activeren van een rol is voltooid

PIM verzendt e-mail berichten naar eind gebruikers wanneer de volgende gebeurtenissen optreden voor Azure-resource rollen:

- Wanneer een rol aan de gebruiker is toegewezen
- Wanneer de rol van een gebruiker is verlopen
- Wanneer de rol van een gebruiker wordt uitgebreid
- Wanneer de activerings aanvraag van een gebruiker is voltooid

Hieronder ziet u een voor beeld van een e-mail bericht dat wordt verzonden wanneer aan een gebruiker een Azure-resource functie is toegewezen voor de fictieve Contoso-organisatie.

![Nieuwe PIM-e-mail voor Azure-resource rollen](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-rollen configureren in PIM](pim-how-to-change-default-settings.md)
- [Aanvragen voor Azure AD-rollen in PIM goed keuren of weigeren](azure-ad-pim-approval-workflow.md)
