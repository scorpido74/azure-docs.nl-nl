---
title: Aangepaste Azure AD-rol activeren - Pim voor privileged identity management (PIM)
description: Een aangepaste Azure-rol activeren voor toewijzing Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: cbd60d1311bd84adb303a0d329ab4e42f4d61525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498739"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Een aangepaste Azure-rol van AD activeren in Privileged Identity Management

Privileged Identity Management in Azure Active Directory (Azure AD) ondersteunt nu just-in-time en tijdgebonden toewijzing aan aangepaste rollen die zijn gemaakt voor Toepassingsbeheer in de beheerervaring Voor identiteits- en toegangsbeheer. Zie [Aangepaste beheerdersrollen in Azure Active Directory (preview)](../users-groups-roles/roles-custom-overview.md)voor meer informatie over het maken van aangepaste rollen om toepassingsbeheer in Azure AD te delegeren.

> [!NOTE]
> Aangepaste Azure AD-rollen zijn niet geïntegreerd met de ingebouwde maprollen tijdens de preview. Zodra de capaciteit algemeen beschikbaar is, zal het rolbeheer plaatsvinden in de ingebouwde rollenervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ervaring met ingebouwde rollen](pim-how-to-activate-role.md) en is dit artikel niet van toepassing:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een aangepaste Azure AD-rol moet activeren, vraagt u activering aan door de navigatieoptie Mijn rollen te selecteren in Privileged Identity Management.

1. Meld u aan bij [de Azure-portal](https://portal.azure.com).
1. Azure AD [Privileged Identity Management openen](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Selecteer **aangepaste Azure AD-rollen** om een lijst met de aangepaste azure AD-roltoewijzingen te bekijken.

   ![Bekijk de lijst met in aanmerking komende aangepaste Azure AD-roltoewijzingen](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Voordat u een rol toewijst, moet u een rol maken/configureren. Zie [hier] voor meer informatie over het configureren van AAD Aangepaste rollen (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Zoek op de pagina **Aangepaste rollen (Preview) van Azure AD** de toewijzing die u nodig hebt.
1. Selecteer **Uw rol activeren** om de pagina Activeren **te** openen.
1. Als uw rol meervoudige verificatie vereist, selecteert **u Uw identiteit verifiëren voordat u verdergaat.** U hoeft u slechts één keer per sessie te verifiëren.
1. Selecteer **Mijn identiteit verifiëren** en volg de instructies om aanvullende beveiligingsverificatie te bieden.
1. Als u een aangepast toepassingsbereik wilt opgeven, selecteert u **Bereik** om het filtervenster te openen. U moet toegang vragen tot een rol op het minimumbereik dat nodig is. Als uw toewijzing zich op een toepassingsbereik bevindt, u alleen activeren op dat bereik.

   ![Een Azure AD-bronbereik toewijzen aan de roltoewijzing](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Geef indien nodig een aangepaste begintijd voor activering op. Bij gebruik wordt het rollid geactiveerd op het opgegeven tijdstip.
1. Voer in het vak **Reden** de reden voor de activeringsaanvraag in. Deze kunnen worden gemaakt vereist of niet in de rol instelling.
1. Selecteer **Activeren**.

Als de rol geen goedkeuring vereist, wordt deze geactiveerd volgens uw instellingen en wordt deze toegevoegd aan de lijst met actieve rollen. Als u de geactiveerde rol wilt gebruiken, begint u met de stappen in [Een aangepaste Azure AD-rol toewijzen in Privileged Identity Management](azure-ad-custom-roles-assign.md).

Als de rol goedkeuring vereist om te activeren, ontvangt u een Azure-melding waarin u wordt geïnformeerd dat de aanvraag in afwachting van goedkeuring is.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste azure-roltoewijzing verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste azure-functietoewijzing configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
