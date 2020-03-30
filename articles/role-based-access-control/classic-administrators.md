---
title: Azure classic subscription administrators | Microsoft Documenten
description: Beschrijft hoe u de rollen Azure Co-Administrator en Service Administrator toevoegen of wijzigen en hoe u de accountbeheerder weergeven.
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
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243482"
---
# <a name="azure-classic-subscription-administrators"></a>Klassieke abonnementsbeheerders van Azure

Microsoft raadt u aan de toegang tot Azure-bronnen te beheren met behulp van RBAC (Role-based access control). Als u echter nog steeds het klassieke implementatiemodel gebruikt, moet u een klassieke rol van abonnementsbeheerder gebruiken: Serviceadministrator en co-administrator. Zie [Azure Resource Manager vs. klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie.

In dit artikel wordt beschreven hoe u de rollen Co-administrator en servicebeheerder toevoegen of wijzigen en hoe u de accountbeheerder weergeven.

## <a name="add-a-co-administrator"></a>Een medebeheerder toevoegen

> [!TIP]
> U hoeft alleen een co-administrator toe te voegen als de gebruiker klassieke Azure-implementaties moet beheren met Azure [Service Management PowerShell Module.](https://docs.microsoft.com/powershell/module/servicemanagement/azure) Als de gebruiker alleen de Azure-portal gebruikt om de klassieke bronnen te beheren, hoeft u de klassieke beheerder voor de gebruiker niet toe te voegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als servicebeheerder of co-beheerder.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

    Medebeheerders kunnen alleen worden toegewezen aan het abonnementsbereik.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Klassieke beheerders.**

    ![Schermafbeelding van klassieke beheerders](./media/classic-administrators/classic-administrators.png)

1. Klik **op** > **Voeg medebeheerder toevoegen toe** om het deelvenster Medebeheerders toevoegen te openen.

    Als de optie Co-administrator toevoegen is uitgeschakeld, hebt u geen machtigingen.

1. Selecteer de gebruiker die u wilt toevoegen en klik op **Toevoegen**.

    ![Schermafbeelding van een andere beheerder](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Een gastgebruiker toevoegen als medebeheerder

Als u een gastgebruiker als co-beheerder wilt toevoegen, voert u dezelfde stappen uit als in de vorige sectie [Een co-administrator toevoegen.](#add-a-co-administrator) De gastgebruiker moet aan de volgende criteria voldoen:

- De gastgebruiker moet aanwezig zijn in uw directory. Dit betekent dat de gebruiker is uitgenodigd voor uw directory en de uitnodiging heeft geaccepteerd.

Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](../active-directory/b2b/add-users-administrator.md)voor meer informatie over het toevoegen van een gastgebruiker aan uw directory.

### <a name="differences-for-guest-users"></a>Verschillen voor gastgebruikers

Gastgebruikers aan wie de rol Co-administrator is toegewezen, kunnen enkele verschillen zien in vergelijking met leden gebruikers met de rol Co-Administrator. Denkt u zich het volgende scenario eens in:

- Gebruiker A met een Azure AD-account (werk- of schoolaccount) is een servicebeheerder voor een Azure-abonnement.
- Gebruiker B heeft een Microsoft-account.
- Gebruiker A wijst de rol co-administrator toe aan gebruiker B.
- Gebruiker B kan bijna alles doen, maar kan geen toepassingen registreren of gebruikers opzoeken in de Azure AD-map.

Je zou verwachten dat gebruiker B alles kon beheren. De reden voor dit verschil is dat het Microsoft-account wordt toegevoegd aan het abonnement als gastgebruiker in plaats van als lidgebruiker. Gastgebruikers hebben verschillende standaardmachtigingen in Azure AD in vergelijking met ledengebruikers. Gebruikers van leden kunnen bijvoorbeeld andere gebruikers lezen in Azure AD en gastgebruikers niet. Ledengebruikers kunnen nieuwe serviceprincipals registreren in Azure AD en gastgebruikers niet.

Als een gastgebruiker deze taken moet kunnen uitvoeren, is een mogelijke oplossing om de specifieke Azure AD-beheerdersrollen toe te wijzen die de gastgebruiker nodig heeft. In het vorige scenario u bijvoorbeeld de rol [Directoryreaders](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) toewijzen om andere gebruikers te lezen en de rol [Toepassingsontwikkelaar](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) toewijzen om serviceprincipals te kunnen maken. Zie [Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory voor](../active-directory/fundamentals/users-default-permissions.md)meer informatie over leden- en gastgebruikers en hun machtigingen? Zie [Toegang tot Azure-bronnen beheren voor externe gastgebruikers met RBAC voor](role-assignments-external-users.md)meer informatie over het verlenen van toegang voor gastgebruikers.

Houd er rekening mee dat de [ingebouwde rollen voor Azure-resources](../role-based-access-control/built-in-roles.md) anders zijn dan de [Azure AD-beheerdersrollen](../active-directory/users-groups-roles/directory-assign-admin-roles.md). De ingebouwde rollen verlenen geen toegang tot Azure AD. Zie [De verschillende rollen begrijpen](../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie .

Zie [Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory voor](../active-directory/fundamentals/users-default-permissions.md)informatie over het vergelijken van ledengebruikers en gastgebruikers?

## <a name="remove-a-co-administrator"></a>Een medebeheerder verwijderen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als servicebeheerder of co-beheerder.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Klassieke beheerders.**

1. Voeg een vinkje toe naast de co-administrator die u wilt verwijderen.

1. Klik op **Verwijderen**.

1. Klik in het berichtvak dat wordt weergegeven op **Ja**.

    ![Schermafbeelding van co-beheerder](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>De servicebeheerder wijzigen

Alleen de accountbeheerder kan de servicebeheerder voor een abonnement wijzigen. Wanneer u zich aanmeldt voor een Azure-abonnement, is de Servicebeheerder standaard hetzelfde als de accountbeheerder. De gebruiker met de functie Accountbeheerder heeft geen toegang tot de Azure-portal. De gebruiker met de functie Servicebeheerder heeft volledige toegang tot de Azure-portal. Als de accountbeheerder en servicebeheerder dezelfde gebruiker zijn en u de servicebeheerder wijzigt in een andere gebruiker, verliest de accountbeheerder de toegang tot de Azure-portal. De accountbeheerder kan accountcentrum echter altijd gebruiken om de servicebeheerder weer in zichzelf te wijzigen.

Voer deze stappen uit om de servicebeheerder te wijzigen in **accountcentrum**.

### <a name="account-center"></a>Accountcentrum

1. Controleer of uw scenario wordt ondersteund door de [beperkingen voor het wijzigen van de servicebeheerder te controleren.](#limitations-for-changing-the-service-administrator)

1. Meld u aan bij [Account Center](https://account.windowsazure.com/subscriptions) als accountbeheerder.

1. Klik op een abonnement.

1. Klik aan de rechterkant op **Abonnementsgegevens bewerken**.

    ![Schermafbeelding van de knop Abonnement bewerken in Accountcentrum](./media/classic-administrators/editsub.png)

1. Voer in het vak **SERVICEBEHEERDER** het e-mailadres van de nieuwe servicebeheerder in.

    ![Schermafbeelding van het vak om de e-mail servicebeheerder te wijzigen](./media/classic-administrators/change-service-admin.png)

1. Klik op het vinkje om de wijziging op te slaan.

### <a name="limitations-for-changing-the-service-administrator"></a>Beperkingen voor het wijzigen van de servicebeheerder

Er kan slechts één servicebeheerder per Azure-abonnement zijn. Als u de servicebeheerder wijzigt, werkt het anders, afhankelijk van of de accountbeheerder een Microsoft-account is of dat het een Azure AD-account is (werk- of schoolaccount).

| Accountbeheerderaccount | Kan de servicebeheerder worden gewijzigd in een ander Microsoft-account? | Kan de servicebeheerder worden gewijzigd in een Azure AD-account in dezelfde map? | Kan de servicebeheerder worden gewijzigd in een Azure AD-account in een andere map? |
| --- | --- | --- | --- |
| Microsoft-account | Ja | Nee | Nee |
| Azure AD-account | Ja | Ja | Nee |

Als de accountbeheerder een Azure AD-account is, u de servicebeheerder wijzigen in een Azure AD-account in dezelfde map, maar niet in een andere map. abby@contoso.com U de servicebeheerder bob@contoso.combijvoorbeeld wijzigen in, john@notcontoso.com maar john@notcontoso.com kan de servicebeheerder niet wijzigen in, tenzij er een aanwezigheid is in de contoso.com-map.

Zie [Wat is Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>De accountbeheerder weergeven

De accountbeheerder is de gebruiker die zich in eerste instantie heeft aangemeld voor het Azure-abonnement en is verantwoordelijk als de factureringseigenaar van het abonnement. Zie [Eigendom van een Azure-abonnement](../cost-management-billing/manage/billing-subscription-transfer.md)overdragen aan een ander account als u de accountbeheerder van een abonnement wilt wijzigen.

Volg deze stappen om de accountbeheerder weer te geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

1. Klik op **Eigenschappen**.

    De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder.**

    ![Schermafbeelding van de accountbeheerder](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in de verschillende rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Toegang tot Azure-resources beheren met op rollen gebaseerd toegangsbeheer en de Azure-portal](../role-based-access-control/role-assignments-portal.md)
* [Azure-abonnementsbeheerders toevoegen of wijzigen](../cost-management-billing/manage/add-change-subscription-administrator.md)
