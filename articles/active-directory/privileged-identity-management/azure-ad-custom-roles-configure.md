---
title: Aangepaste Azure AD-rol configureren - Pim voor privileged identity management (PIM)
description: Aangepaste Azure-rollen configureren in Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498684"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Aangepaste Azure AD-rollen configureren in Privileged Identity Management

Een bevoegde rolbeheerder kan de rolinstellingen wijzigen die van toepassing zijn op een gebruiker wanneer deze de toewijzing activeert naar een aangepaste rol en voor andere toepassingsbeheerders die aangepaste rollen toewijzen.

> [!NOTE]
> Aangepaste Azure AD-rollen zijn niet geïntegreerd met de ingebouwde maprollen tijdens de preview. Zodra de capaciteit algemeen beschikbaar is, zal het rolbeheer plaatsvinden in de ingebouwde rollenervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ervaring met ingebouwde rollen](pim-how-to-activate-role.md) en is dit artikel niet van toepassing:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure AD-rol te openen.

1. Meld u aan bij [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) in de Azure-portal met een gebruikersaccount dat is toegewezen aan de rol van privileged role administrator.
1. Selecteer **aangepaste azure-rollen voor AD (voorbeeld)**.

    ![De aangepaste rollenvoorA Voorrol van Azure AD selecteren om in aanmerking komende roltoewijzingen te bekijken](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Selecteer **Instellen** om de pagina **Instellingen te** openen. Selecteer de rol voor de instellingen die u wilt configureren.
1. Selecteer **Bewerken** om de pagina **Rolinstellingen** te openen.

    ![De aangepaste Azure AD-rol openen om instellingen te bewerken](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Rolinstellingen

Er zijn verschillende instellingen die u configureren.

### <a name="assignment-duration"></a>Toewijzingsduur

U kiezen uit twee opties voor toewijzingsduur voor elk toewijzingstype (in aanmerking komend of actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een lid is toegewezen aan de rol in Privileged Identity Management.

U een van deze *in aanmerking komende* opties voor de duur van de toewijzing kiezen.

- **Permanente toewijzing toestaan:** beheerders kunnen een permanent lidmaatschap toewijzen.
- **In aanmerking komende toewijzing vervallen na**: Beheerders kunnen eisen dat alle in aanmerking komende toewijzingen een opgegeven begin- en einddatum hebben.

U ook een van deze opties voor *actieve* toewijzingsduur kiezen:

- **Permanente actieve toewijzing toestaan:** beheerders kunnen een permanent actief lidmaatschap toewijzen.
- **Actieve toewijzing verlopen na**: Beheerders kunnen eisen dat alle actieve toewijzingen een opgegeven begin- en einddatum hebben.

### <a name="require-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication vereisen

Privileged Identity Management biedt optionele handhaving van Azure Multi-Factor Authentication voor twee verschillende scenario's.

- **Multifactorauthenticatie vereisen bij actieve toewijzing**

  Als u een lid alleen voor een korte duur (bijvoorbeeld één dag) aan een rol wilt toewijzen, is het mogelijk te traag om de toegewezen leden te verplichten activering aan te vragen. In dit scenario kan Privileged Identity Management geen meervoudige verificatie afdwingen wanneer de gebruiker zijn roltoewijzing activeert, omdat deze al actief is in de rol vanaf het moment dat deze is toegewezen. Als u ervoor wilt zorgen dat de beheerder die de toewijzing vervult, is wie hij zegt dat ze zijn, selecteert u het vak **Multifactorverificatie vereisen voor actieve toewijzing.**

- **Multi-Factor Authentication vereisen bij activering**

  U in aanmerking komende gebruikers die zijn toegewezen aan een rol vereisen dat ze zich inschrijven voor Azure Multi-Factor Authentication voordat ze kunnen worden geactiveerd. Dit proces zorgt ervoor dat de gebruiker die activering aanvraagt, met redelijke zekerheid is wie hij zegt dat hij of zij is. Als u deze optie afdwingt, worden kritieke rollen beschermd in situaties waarin het gebruikersaccount mogelijk is gecompromitteerd. Als u wilt dat een in aanmerking komend lid Azure Multi-Factor Authentication uitvoert voordat u wordt geactiveerd, schakelt u het selectievakje **Multi-Factor Authentication vereisen op activering** in.

Zie [Multi-factor authenticatie en Privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

### <a name="activation-maximum-duration"></a>Maximale duur activering

Gebruik de schuifregelaar **Activering seinmaximale duur** om de maximale tijd in te stellen, in uren, dat een rol actief blijft voordat deze verloopt. Deze waarde kan van 1 en 24 uur zijn.

### <a name="require-justification"></a>Rechtvaardiging vereisen

U eisen dat leden een rechtvaardiging invoeren voor actieve toewijzing of wanneer ze worden geactiveerd. Als u verantwoording wilt behoeven, schakelt u het selectievakje **Rechtvaardiging bij actieve toewijzing** vereisen in of het selectievakje **Motivering bij activering vereisen** in.

### <a name="require-approval-to-activate"></a>Goedkeuring vereisen om te activeren

Als u goedkeuring nodig wilt hebben om een rol te activeren, voert u deze stappen uit.

1. Schakel het selectievakje **Goedkeuring vereisen om te activeren** in.
1. Selecteer **Goederen selecteren** om de lijst Een lid of groep selecteren te **openen.**

    ![De aangepaste Azure AD-rol openen om instellingen te bewerken](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Selecteer ten minste één lid of groep en klik op **Selecteren**. U moet ten minste één goedkeurder selecteren. Er zijn geen standaard fiatteurs. Uw selecties worden weergegeven in de lijst met geselecteerde goedkeurders.
1. Zodra u de rolinstellingen hebt opgegeven, selecteert u **Bijwerken** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol activeren](azure-ad-custom-roles-activate.md)
- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste azure-roltoewijzing verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Roldefinities in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
