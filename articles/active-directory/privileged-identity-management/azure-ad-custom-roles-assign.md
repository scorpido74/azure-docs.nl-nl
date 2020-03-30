---
title: Aangepaste Azure AD-rol toewijzen - Privileged Identity Management (PIM)
description: Een aangepaste Azure-rol toewijzen aan Azure AD voor toewijzing Privileged Identity Management (PIM)
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
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499250"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Een aangepaste Azure-rol toewijzen aan Azure AD in Privileged Identity Management

In dit artikel vindt u hoe u Privileged Identity Management (PIM) gebruiken om just-in-time en tijdgebonden toewijzing te maken voor aangepaste rollen die zijn gemaakt voor het beheren van toepassingen in de beheerervaring azure active directory (Azure AD).

- Zie [Aangepaste beheerdersrollen in Azure Active Directory (preview)](../users-groups-roles/roles-custom-overview.md)voor meer informatie over het maken van aangepaste rollen om toepassingsbeheer in Azure AD te delegeren.
- Als u Privileged Identity Management nog niet hebt gebruikt, krijgt u meer informatie bij [Start met Privileged Identity Management](pim-getting-started.md).
- Zie Toegang verlenen aan andere beheerders om privileged [identity management te beheren](pim-how-to-give-access-to-pim.md)voor informatie over het verlenen van toegang aan een andere beheerder.

> [!NOTE]
> Aangepaste Azure AD-rollen zijn niet geïntegreerd met de ingebouwde maprollen tijdens de preview. Zodra de capaciteit algemeen beschikbaar is, zal het rolbeheer plaatsvinden in de ingebouwde rollenervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ervaring met ingebouwde rollen](pim-how-to-activate-role.md) en is dit artikel niet van toepassing:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Een rol toewijzen

Privileged Identity Management kan aangepaste rollen beheren die u maken in Azure Active Directory (Azure AD) toepassingsbeheer.  Met de volgende stappen wordt een in aanmerking komende toewijzing gemaakt voor een aangepaste maprol.

1. Meld u aan bij [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) in de Azure-portal met een gebruikersaccount dat is toegewezen aan de rol van privileged role administrator.
1. Selecteer **aangepaste azure-rollen voor AD (voorbeeld)**.

    ![De aangepaste rollenvoorA Voorrol van Azure AD selecteren om in aanmerking komende roltoewijzingen te bekijken](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecteer **Rollen** om een lijst met aangepaste rollen voor Azure AD-toepassingen te bekijken.

    ![In rollen selecteren ziet u de lijst met in aanmerking komende roltoewijzingen](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Selecteer **Lid toevoegen** om de toewijzingspagina te openen.
1. Als u het bereik van de roltoewijzing wilt beperken tot één toepassing, selecteert u **Bereik** om een toepassingsbereik op te geven.

    ![het bereik van in aanmerking komende roltoewijzingen in Azure AD beperken](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Selecteer **Selecteer een rol** selecteren om de lijst Een rol **selecteren** te openen.

    ![de in aanmerking komende rol selecteren die aan een gebruiker moet worden toegewezen](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selecteer een rol die u wilt toewijzen en klik op **Selecteren**. De lijst **Met een lid selecteren** wordt geopend.

    ![de gebruiker selecteren aan wie u de rol toevertrouwt](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selecteer een gebruiker die u aan de rol wilt toewijzen en klik op **Selecteren**. De lijst **met lidmaatschapsinstellingen** wordt geopend.

    ![Het type roltoewijzing instellen op in aanmerking komende of actieve](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Selecteer op de pagina **Lidmaatschapsinstellingen** de optie **In aanmerking komen** of **Actief:**

    - **In aanmerking komende** toewijzingen vereisen dat de gebruiker die aan de rol is toegewezen, een actie uitvoert voordat hij de rol kan gebruiken. Acties kunnen bestaan uit het passeren van een multi-factor authenticatie controle, het verstrekken van een zakelijke rechtvaardiging, of het aanvragen van goedkeuring van aangewezen fiatteurs.
    - **Actieve** toewijzingen vereisen niet dat de toegewezen gebruiker actie uitvoert om de rol te gebruiken. Actieve gebruikers hebben te allen tijde de bevoegdheden toegewezen aan de rol.

1. Als het selectievakje **Permanent** aanwezig en beschikbaar is (afhankelijk van de rolinstellingen), u opgeven of de toewijzing permanent is. Schakel het selectievakje in om de toewijzing permanent in aanmerking te laten komen of permanent toegewezen te maken. Schakel het selectievakje uit om een toewijzingsduur op te geven.
1. Als u de nieuwe roltoewijzing wilt maken, klikt u op **Opslaan** en vervolgens **toevoegen**. Er wordt een melding van de status van het toewijzingsproces weergegeven.

Als u de roltoewijzing wilt verifiëren, selecteert u in een open rol **Toewijzingen** > **toewijzen** en controleert u of uw roltoewijzing naar behoren is geïdentificeerd als in aanmerking komend of actief.

 ![Controleren of de roltoewijzing zichtbaar is als in aanmerking komend of actief](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol activeren](azure-ad-custom-roles-assign.md)
- [Een aangepaste azure-roltoewijzing verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste azure-functietoewijzing configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
