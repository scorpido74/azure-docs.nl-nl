---
title: Azure-bronrolinstellingen configureren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het configureren van Azure-bronrolinstellingen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638677"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Azure-bronrolinstellingen configureren in Privileged Identity Management

Wanneer u Azure-bronrolinstellingen configureert, definieert u de standaardinstellingen die worden toegepast op Azure-bronroltoewijzingen in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Gebruik de volgende procedures om de goedkeuringswerkstroom te configureren en geef op wie aanvragen kan goedkeuren of weigeren.

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure-bronrol te openen.

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker in de rol Privileged [Role Administrator.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Selecteer de resource die u wilt beheren, zoals een abonnement of beheergroep.

    ![Azure-bronnenpagina met resources die kunnen worden beheerd](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selecteer **Rolinstellingen**.

    ![Pagina Met pagina Met Azure-bronrollen voor rolinstellingen](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecteer de rol waarvan u de instellingen wilt configureren.

    ![Functie-instelling details pagina met verschillende toewijzing en activering instellingen](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selecteer **Bewerken** om het deelvenster **Rolinstellingen** te openen. Met het eerste tabblad u de configuratie bijwerken voor rolactivering in Privileged Identity Management.

    ![Pagina Rolinstellingen bewerken met het tabblad Activering geopend](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selecteer het tabblad **Toewijzing** of de knop **Volgende: Toewijzing** onder aan de pagina om het tabblad Toewijzing su.a. Deze instellingen beheren roltoewijzingen die zijn gemaakt binnen de interface Voor privileged identity management.

    ![Tabblad Roltoewijzing op de pagina Rolinstellingen](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Gebruik het tabblad **Melding** of de knop **Volgende: Activering** onder aan de pagina om naar het tabblad meldingsinstelling voor deze rol te gaan. Deze instellingen beheren alle e-mailmeldingen met betrekking tot deze rol.

    ![Tabblad Rolmeldingen op de pagina Rolinstellingen](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Op het tabblad **Meldingen** op de pagina Rolinstellingen biedt Privileged Identity Management gedetailleerde controle over wie meldingen ontvangt en welke meldingen ze ontvangen.

    - **Een e-mail uitschakelen**<br>U specifieke e-mails uitschakelen door het selectievakje Standaardontvanger uit te schakelen en eventuele extra ontvangers te verwijderen.  

    - **E-mails beperken tot opgegeven e-mailadressen**<br>U e-mails die naar standaardontvangers worden verzonden uitschakelen door het selectievakje standaardontvanger te wissen. U vervolgens extra e-mailadressen toevoegen als extra geadresseerden. Als u meer dan één e-mailadres wilt toevoegen, scheidt u deze met behulp van een puntkomma (;).

    - **E-mails verzenden naar zowel standaardontvangers als extra ontvangers**<br>U e-mails verzenden naar zowel de standaardontvanger als de extra ontvanger door het standaardselectievakje voor geadresseerden in te schakelen en e-mailadressen toe te voegen voor extra ontvangers.

    - **Alleen kritieke e-mails**<br>Voor elk type e-mail u het selectievakje inschakelen om alleen kritieke e-mails te ontvangen. Dit betekent dat Privileged Identity Management alleen e-mails naar de geconfigureerde ontvangers blijft verzenden wanneer de e-mail onmiddellijk actie vereist. E-mails waarin gebruikers worden gevraagd hun roltoewijzing uit te breiden, worden bijvoorbeeld niet geactiveerd, terwijl een e-mail die beheerders verplicht om een extensieaanvraag goed te keuren, wordt geactiveerd.

1. Selecteer op elk gewenst moment de knop **Bijwerken** om de rolinstellingen bij te werken.

## <a name="assignment-duration"></a>Toewijzingsduur

U kiezen uit twee opties voor toewijzingsduur voor elk toewijzingstype (in aanmerking komend en actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een gebruiker is toegewezen aan de rol in Privileged Identity Management.

U een van deze **in aanmerking komende** opties voor de duur van de toewijzing kiezen:

| | |
| --- | --- |
| **Permanente toewijzing toestaan** | Resourcebeheerders kunnen een permanente toewijzing toewijzen. |
| **In aanmerking komende toewijzing aflopen na** | Resourcebeheerders kunnen eisen dat alle in aanmerking komende toewijzingen een opgegeven begin- en einddatum hebben. |

En u een van deze opties voor **actieve** toewijzingsduur kiezen:

| | |
| --- | --- |
| **Permanente actieve toewijzing toestaan** | Resourcebeheerders kunnen permanente actieve toewijzing toewijzen. |
| **Actieve toewijzing verlopen na** | Resourcebeheerders kunnen vereisen dat alle actieve toewijzingen een opgegeven begin- en einddatum hebben. |

> [!NOTE]
> Alle toewijzingen met een opgegeven einddatum kunnen worden verlengd door resourcebeheerders. Gebruikers kunnen ook selfserviceaanvragen initiëren om [roltoewijzingen uit](pim-resource-roles-renew-extend.md)te breiden of te vernieuwen.

## <a name="require-multi-factor-authentication"></a>Meervoudige verificatie vereisen

Privileged Identity Management biedt optionele handhaving van Azure Multi-Factor Authentication voor twee verschillende scenario's.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multifactorauthenticatie vereisen bij actieve toewijzing

In sommige gevallen u een gebruiker of groep voor een korte duur (bijvoorbeeld één dag) aan een rol toewijzen. In dit geval hoeven de toegewezen gebruikers geen activering aan te vragen. In dit scenario kan Privileged Identity Management geen meervoudige verificatie afdwingen wanneer de gebruiker zijn roltoewijzing gebruikt omdat hij al actief is in de rol vanaf het moment dat deze is toegewezen.

Als u ervoor wilt zorgen dat de resourcebeheerder die de toewijzing vervult, is wie ze zeggen dat ze zijn, u multi-factor authenticatie afdwingen bij actieve toewijzing door het vak **Multifactorverificatie vereisen in actieve toewijzing aan** te vinken.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication vereisen bij activering

U gebruikers die in aanmerking komen voor een rol vereisen dat ze bewijzen wie ze Azure Multi-Factor Authentication gebruiken voordat ze kunnen worden geactiveerd. Multi-factor authenticatie zorgt ervoor dat de gebruiker is wie ze zeggen dat ze zijn met redelijke zekerheid. Als u deze optie afdwingt, worden kritieke resources beschermd in situaties waarin het gebruikersaccount mogelijk is gecompromitteerd.

Als u multifactorauthenticatie wilt vereisen voordat u wordt geactiveerd, schakelt u het selectievakje **Multifactorverificatie bij activering** vereisen in.

Zie [Multi-factor authenticatie en Privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="activation-maximum-duration"></a>Maximale duur activering

Gebruik de schuifregelaar **Activering seinmaximale duur** om de maximale tijd in te stellen, in uren, dat een rol actief blijft voordat deze verloopt. Deze waarde kan van één tot 24 uur zijn.

## <a name="require-justification"></a>Rechtvaardiging vereisen

U vereisen dat gebruikers een zakelijke rechtvaardiging invoeren wanneer ze worden geactiveerd. Als u rechtvaardiging wilt vereisen, schakelt u het vak **Eis voor actieve toewijzing** of het selectievakje **Motivering op activering vereisen in.**

## <a name="require-approval-to-activate"></a>Goedkeuring vereisen om te activeren

Als u goedkeuring nodig wilt hebben om een rol te activeren, voert u deze stappen uit.

1. Schakel het selectievakje **Goedkeuring vereisen in om te activeren.**

1. Selecteer **Goederen selecteren** om de pagina Een lid of groep selecteren te **openen.**

    ![Een gebruiker of groepsvenster selecteren om fiatteurs te selecteren](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecteer ten minste één gebruiker of groep en klik op **Selecteren**. U elke combinatie van gebruikers en groepen toevoegen. U moet ten minste één goedkeurder selecteren. Er zijn geen standaard fiatteurs.

    Uw selecties worden weergegeven in de lijst met geselecteerde goedkeurders.

1. Zodra u al uw rolinstellingen hebt opgegeven, selecteert u **Bijwerken** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Beveiligingswaarschuwingen configureren voor Azure-bronrollen in Privileged Identity Management](pim-resource-roles-configure-alerts.md)
