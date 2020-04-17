---
title: Een bestaand Azure-abonnement toevoegen aan uw tenant - Azure AD
description: Instructies voor het toevoegen van een bestaand Azure-abonnement aan uw Azure Active Directory-tenant.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457923"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen

Een Azure-abonnement heeft een vertrouwensrelatie met Azure Active Directory (Azure AD). Een abonnement vertrouwt Azure AD om gebruikers, services en apparaten te verifiëren.

Meerdere abonnementen kunnen dezelfde Azure AD-map vertrouwen. Elk abonnement kan slechts één map vertrouwen.

Als uw abonnement verloopt, verliest u de toegang tot alle andere bronnen die aan het abonnement zijn gekoppeld. De AD-map azure blijft echter in Azure. U de directory koppelen en beheren met een ander Azure-abonnement.

Al uw gebruikers hebben één *thuismap* voor verificatie. Uw gebruikers kunnen ook te gast zijn in andere mappen. U zowel de thuis- als de gastmappen voor elke gebruiker in Azure AD bekijken.

> [!Important]
> Wanneer u een abonnement koppelt aan een andere map, verliezen gebruikers met rollen die zijn toegewezen met [behulp van rbac (role-based access control)](../../role-based-access-control/role-assignments-portal.md) hun toegang. Klassieke abonnementsbeheerders, waaronder de servicebeheerder en co-beheerders, verliezen ook hun toegang.
>
> Beleidstoewijzingen worden verwijderd uit een abonnement wanneer het abonnement wordt gekoppeld aan een andere Active Directory.
>
> Door uw AZURE Kubernetes Service (AKS)-cluster naar een ander abonnement te verplaatsen of het clusterabonnement naar een nieuwe tenant te verplaatsen, verliest het cluster functionaliteit als gevolg van verloren roltoewijzingen en serviceprincipalrechten. Zie Azure Kubernetes Service [(AKS)](https://docs.microsoft.com/azure/aks/)voor meer informatie over AKS.


## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw abonnement koppelen of toevoegen, moet u de volgende taken uitvoeren:

- Bekijk de volgende lijst met wijzigingen en hoe u mogelijk wordt beïnvloed:

  - Gebruikers die rollen hebben toegewezen met RBAC, verliezen hun toegang
  - Servicebeheerder en co-beheerders verliezen de toegang
  - Als je sleutelkluizen hebt, zijn ze ontoegankelijk en moet je ze na associatie repareren
  - Als u beheerde identiteiten hebt voor bronnen zoals virtuele machines of logische apps, moet u deze opnieuw in- of opnieuw inschakelen of opnieuw maken na de koppeling
  - Als u een geregistreerde Azure Stack hebt, moet u deze opnieuw registreren na

- Meld u aan met een account dat:

  - Heeft een [roltoewijzing voor](../../role-based-access-control/built-in-roles.md#owner) de eigenaar voor het abonnement. Zie [Toegang tot Azure-resources beheren met RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md)voor informatie over het toewijzen van de rol Eigenaar.
  - Bestaat zowel in de huidige map als in de nieuwe map. De huidige map is gekoppeld aan het abonnement. U koppelt de nieuwe map aan het abonnement. Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](../b2b/add-users-administrator.md)voor meer informatie over het verkrijgen van toegang tot een andere map.

- Zorg ervoor dat u geen Azure Cloud Service Providers (CSP)-abonnement (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), een Microsoft Internal-abonnement (MS-AZR-0015P) of een Microsoft-abonnement (MS-AZR-0144P) gebruikt.

## <a name="associate-a-subscription-to-a-directory"></a>Een abonnement koppelen aan een map<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Voer de volgende stappen uit om een bestaand abonnement aan uw Azure AD-map te koppelen:

1. Meld u aan en selecteer het abonnement dat u wilt gebruiken op de [pagina Abonnementen in Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

1. Selecteer **Map wijzigen**.

    ![Pagina Abonnementen, met de optie Map wijzigen gemarkeerd](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Controleer alle waarschuwingen die worden weergegeven en selecteer **Wijzigen**.

    ![De mappagina wijzigen en de map weergeven om in](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    De directory wordt gewijzigd voor het abonnement en je krijgt een succesbericht.

    ![Succesbericht over directorywijziging](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Gebruik **de Switch-map** om naar uw nieuwe map te gaan. Het kan enkele uren duren voordat alles goed is weergegeven. Als het te lang lijkt te duren, raadpleegt u het **filter voor het globale abonnement**. Zorg ervoor dat het verplaatste abonnement niet wordt verborgen. Mogelijk moet u zich afmelden bij de Azure-portal en u weer aanmelden om de nieuwe map te bekijken.

![Pagina directoryswitcher, met voorbeeldgegevens](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Het wijzigen van de abonnementsmap is een bewerking op serviceniveau, dus dit heeft geen invloed op het eigendom van abonnementsfacturering. De accountbeheerder kan de servicebeheerder nog steeds wijzigen vanuit het [Accountcentrum](https://account.azure.com/subscriptions). Als u de oorspronkelijke map wilt verwijderen, moet u het factureringseigendom van het abonnement overdragen aan een nieuwe accountbeheerder. Zie [Eigendom van een Azure-abonnement overdragen aan een ander account voor](../../cost-management-billing/manage/billing-subscription-transfer.md)meer informatie over het overdragen van het eigendom van facturering.

## <a name="post-association-steps"></a>Stappen na vereniging

Nadat u een abonnement aan een andere map hebt koppelen, moet u mogelijk de volgende taken uitvoeren om bewerkingen te hervatten:

- Als u sleutelkluizen hebt, moet u de sleutelkluistenant-id wijzigen. Zie [Een sleutelkluistenant-id wijzigen na een verhuizing van een abonnement](../../key-vault/general/subscription-move-fix.md)voor meer informatie.

- Als u beheerde identiteiten met systeemtoegewezen voor resources hebt gebruikt, moet u deze identiteiten opnieuw inschakelen. Als u beheerde identiteiten met door de gebruiker hebt toegewezen, moet u deze identiteiten opnieuw maken. Nadat u de beheerde identiteiten opnieuw hebt in- of opnieuw hebt ingesteld, moet u de machtigingen die aan die identiteiten zijn toegewezen, opnieuw instellen. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md) voor meer informatie.

- Als u een Azure Stack met dit abonnement hebt geregistreerd, moet u zich opnieuw registreren. Zie [Azure Stack registreren met Azure](/azure-stack/operator/azure-stack-registration)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart: Een nieuwe tenant maken in Azure Active Directory](active-directory-access-create-new-tenant.md)als u een nieuwe Azure AD-tenant wilt maken.

- Zie [Klassieke abonnementsbeheerdersrollen, Azure RBAC-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie over hoe Microsoft Azure toegang tot bronnen beheert.

- Zie [Beheerders- en niet-beheerdersrollen toewijzen aan gebruikers met Azure Active Directory voor](active-directory-users-assign-role-azure-portal.md)meer informatie over het toewijzen van rollen in Azure AD.
