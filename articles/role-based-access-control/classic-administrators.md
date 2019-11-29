---
title: Administrators van het klassieke Azure-abonnement | Microsoft Docs
description: Hierin wordt beschreven hoe u de rollen Azure mede beheerder en service beheerder toevoegt of wijzigt, en hoe u de account beheerder kunt weer geven.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 340717242d642475217bbe87fd96be66ec9b2e2d
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554229"
---
# <a name="azure-classic-subscription-administrators"></a>Beheerders van klassieke Azure-abonnementen

Micro soft raadt u aan om de toegang tot Azure-resources te beheren met op rollen gebaseerd toegangs beheer (RBAC). Als u nog steeds gebruikmaakt van het klassieke implementatie model, moet u echter een klassieke rol voor abonnements beheerders gebruiken: Service beheerder en mede beheerder. Zie [Azure Resource Manager vs. klassieke implementatie](../azure-resource-manager/resource-manager-deployment-model.md)voor meer informatie.

In dit artikel wordt beschreven hoe u de rollen mede beheerder en service beheerder kunt toevoegen of wijzigen, en hoe u de account beheerder kunt weer geven.

## <a name="add-a-co-administrator"></a>Een mede beheerder toevoegen

> [!TIP]
> U hoeft alleen een mede beheerder toe te voegen als de gebruiker klassieke Azure-implementaties moet beheren met behulp van de [Azure Service Management Power shell-module](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Als de gebruiker alleen de Azure Portal gebruikt voor het beheren van de klassieke resources, hoeft u de klassieke beheerder niet toe te voegen aan de gebruiker.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als service beheerder of mede beheerder.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

    Mede beheerders kunnen alleen worden toegewezen bij het abonnements bereik.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **klassieke beheerders** .

    ![Scherm afbeelding waarmee klassieke beheerders worden geopend](./media/classic-administrators/classic-administrators.png)

1. Klik op **toevoegen** > **mede beheerder toe te voegen** om het deel venster CO-Administrators toevoegen te openen.

    Als de optie co-beheerder toevoegen is uitgeschakeld, bent u niet gemachtigd.

1. Selecteer de gebruiker die u wilt toevoegen en klik op **toevoegen**.

    ![Scherm afbeelding waarmee co-beheerder wordt toegevoegd](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Een gast gebruiker als co-beheerder toevoegen

Als u een gast gebruiker wilt toevoegen als co-beheerder, voert u dezelfde stappen uit als in het vorige gedeelte [een mede beheerder toevoegen](#add-a-co-administrator) . De gast gebruiker moet voldoen aan de volgende criteria:

- De gast gebruiker moet een aanwezigheid hebben in uw Directory. Dit betekent dat de gebruiker is uitgenodigd voor de Directory en de uitnodiging heeft geaccepteerd.

Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/b2b/add-users-administrator.md)voor meer informatie over het toevoegen van een gast gebruiker aan uw Directory.

### <a name="differences-for-guest-users"></a>Verschillen voor gast gebruikers

Gast gebruikers aan wie de rol mede beheerder is toegewezen, kunnen enkele verschillen zien ten opzichte van gebruikers die lid zijn van de rol mede beheerder. Houd rekening met het volgende scenario:

- Gebruiker A met een Azure AD-account (werk-of school account) is een service beheerder voor een Azure-abonnement.
- Gebruiker B heeft een Microsoft-account.
- Gebruiker A wijst de rol mede beheerder toe aan gebruiker B.
- Gebruiker B kan vrijwel alles doen, maar kan geen toepassingen registreren of gebruikers opzoeken in de Azure AD-adres lijst.

U verwacht dat gebruiker B alles kan beheren. De reden hiervoor is dat de Microsoft-account wordt toegevoegd aan het abonnement als gast gebruiker in plaats van een lid van een gebruiker. Gast gebruikers hebben een andere standaard machtiging in azure AD, vergeleken met gebruikers van leden. Gebruikers kunnen bijvoorbeeld andere gebruikers in azure AD en gast gebruikers lezen. Gebruikers van leden kunnen nieuwe service-principals in azure AD en gast gebruikers registreren.

Als een gast gebruiker deze taken moet kunnen uitvoeren, is een mogelijke oplossing om de specifieke Azure AD-beheerders rollen toe te wijzen die de gast gebruiker nodig heeft. In het vorige scenario kunt u bijvoorbeeld de rol van [Directory lezers](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) toewijzen om andere gebruikers te lezen en de rol [toepassings ontwikkelaar](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) toe te wijzen om service-principals te kunnen maken. Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)voor meer informatie over lid-en gast gebruikers en de bijbehorende machtigingen. Voor meer informatie over het verlenen van toegang voor gast gebruikers, Zie [toegang beheren tot Azure-resources voor externe gast gebruikers met RBAC](role-assignments-external-users.md).

Houd er rekening mee dat de [ingebouwde rollen voor Azure-resources verschillen van](../role-based-access-control/built-in-roles.md) de [Azure AD-beheerders rollen](../active-directory/users-groups-roles/directory-assign-admin-roles.md). De ingebouwde rollen verlenen geen toegang tot Azure AD. Zie [inzicht krijgen in de verschillende rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie.

Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)voor informatie waarmee leden van gebruikers en gast gebruikers worden vergeleken.

## <a name="remove-a-co-administrator"></a>Een mede beheerder verwijderen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als service beheerder of mede beheerder.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **klassieke beheerders** .

1. Voeg een vinkje toe naast de co-beheerder die u wilt verwijderen.

1. Klik op **Verwijderen**.

1. Klik op **Ja**in het bericht venster dat wordt weer gegeven.

    ![Scherm afbeelding waarmee co-beheerder wordt verwijderd](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>De servicebeheerder wijzigen

Alleen de account beheerder kan de service beheerder voor een abonnement wijzigen. Wanneer u zich aanmeldt voor een Azure-abonnement, is de service beheerder standaard hetzelfde als de account beheerder. De gebruiker met de rol account beheerder heeft geen toegang tot de Azure Portal. De gebruiker met de rol van service beheerder heeft volledige toegang tot de Azure Portal. Als de account beheerder en service beheerder dezelfde gebruiker zijn en u de service beheerder wijzigt in een andere gebruiker, verliest de account beheerder de toegang tot Azure Portal. De account beheerder kan echter altijd het account centrum gebruiken om de service beheerder weer op zichzelf te zetten.

Er zijn twee manieren om de servicebeheerder te wijzigen. U kunt wijzigingen aanbrengen in de **Azure Portal** of het **account centrum**.

### <a name="azure-portal"></a>Azure Portal

1. Zorg ervoor dat uw scenario wordt ondersteund door de beperkingen voor het wijzigen van service beheerders te controleren.

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com).

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

1. Klik op **Eigenschappen**.

    ![Scherm opname van de account beheerder](./media/classic-administrators/account-admin.png)

1. Klik bovenaan op **service beheerder** om het deel venster Service beheerder te openen.

    Als de knop Service beheer is uitgeschakeld, bent u niet gemachtigd. Alleen de gebruiker die de account beheerder is, kan de service beheerder wijzigen.

1. Selecteer een nieuwe service beheerder en klik vervolgens op **Opslaan**.

### <a name="account-center"></a>Account centrum

1. Zorg ervoor dat uw scenario wordt ondersteund door de beperkingen voor het wijzigen van service beheerders te controleren.

1. Meld u als account beheerder aan bij het [account centrum](https://account.windowsazure.com/subscriptions) .

1. Klik op een abonnement.

1. Klik aan de rechter kant op **abonnements gegevens bewerken**.

    ![Scherm afbeelding met de knop abonnement bewerken in het account centrum](./media/classic-administrators/editsub.png)

1. Voer in het vak **service beheerder** het e-mail adres van de nieuwe service beheerder in.

    ![Scherm opname van het vak voor het wijzigen van het e-mail adres van de service beheerder](./media/classic-administrators/change-service-admin.png)

1. Klik op het vinkje om de wijziging op te slaan.

### <a name="limitations-for-changing-the-service-administrator"></a>Beperkingen voor het wijzigen van de service beheerder

Er kan slechts één service beheerder per Azure-abonnement zijn. Het wijzigen van de service beheerder werkt anders, afhankelijk van het feit of de account beheerder een Microsoft-account is of dat het een Azure AD-account is (werk-of school account).

| Account beheerders account | Kan de service beheerder wijzigen in een andere Microsoft-account? | Kan de service beheerder wijzigen in een Azure AD-account in dezelfde map? | Kan de service beheerder wijzigen in een Azure AD-account in een andere adres lijst? |
| --- | --- | --- | --- |
| Microsoft-account | Ja | Nee | Nee |
| Azure AD-account | Ja | Ja | Nee |

Als de account beheerder een Azure AD-account is, kunt u de service beheerder wijzigen in een Azure AD-account in dezelfde map, maar niet in een andere map. abby@contoso.com kan bijvoorbeeld de service beheerder wijzigen in bob@contoso.com, maar de service beheerder kan niet worden gewijzigd in john@notcontoso.com, tenzij john@notcontoso.com een aanwezigheid heeft in de map contoso.com.

Zie [Wat is Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)voor meer informatie over micro soft-accounts en Azure AD-accounts.

## <a name="view-the-account-administrator"></a>De account beheerder weer geven

De account beheerder is de gebruiker die zich voor het eerst aanmeldt voor het Azure-abonnement en is verantwoordelijk als de facturerings eigenaar van het abonnement. Zie [eigendom van een Azure-abonnement overdragen aan een ander account](../billing/billing-subscription-transfer.md)om de account beheerder van een abonnement te wijzigen.

Volg deze stappen om de account beheerder weer te geven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

1. Klik op **Eigenschappen**.

    De account beheerder van het abonnement wordt weer gegeven in het vak **account beheerder** .

    ![Scherm opname van de account beheerder](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in de verschillende rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Toegang tot Azure-resources beheren met op rollen gebaseerd toegangsbeheer en de Azure-portal](../role-based-access-control/role-assignments-portal.md)
* [Azure-abonnementsbeheerders toevoegen of wijzigen](../billing/billing-add-change-azure-subscription-administrator.md)
