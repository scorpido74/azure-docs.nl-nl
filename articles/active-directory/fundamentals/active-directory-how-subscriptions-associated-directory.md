---
title: Een bestaand Azure-abonnement toevoegen aan uw Tenant-Azure AD
description: Instructies over hoe u een bestaande Azure-abonnement toevoegen aan uw Azure Active Directory-tenant.
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
ms.openlocfilehash: 8d2111e631dd43b5d86a0bd486642657a20cc3ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422985"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Koppelen of een Azure-abonnement toevoegen aan uw Azure Active Directory-tenant

Een Azure-abonnement heeft een vertrouwens relatie met Azure Active Directory (Azure AD). Een abonnement vertrouwt Azure AD voor het verifiëren van gebruikers, services en apparaten.

Meerdere abonnementen kunnen dezelfde Azure AD-Directory vertrouwen. Elk abonnement kan slechts één map vertrouwen.

Als uw abonnement is verlopen, verliest u toegang tot alle andere resources die zijn gekoppeld aan het abonnement. De Azure AD-Directory blijft echter aanwezig in Azure. U kunt de Directory koppelen en beheren met een ander Azure-abonnement.

Al uw gebruikers hebben één *basis* Directory voor verificatie. Uw gebruikers kunnen ook gasten zijn in andere directory's. U ziet de startpagina en Gast mappen voor elke gebruiker in Azure AD.

> [!Important]
> Wanneer u een abonnement aan een andere map koppelt, verliezen gebruikers die rollen hebben die zijn toegewezen via [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/role-assignments-portal.md) hun toegang. Klassieke abonnements beheerders, waaronder service beheerder en mede beheerders, verliezen ook geen toegang.
>
> Beleids toewijzingen worden ook verwijderd uit een abonnement wanneer het abonnement is gekoppeld aan een andere map.
>
> Door uw Azure Kubernetes service-cluster (AKS) naar een ander abonnement te verplaatsen of het cluster eigenaar-abonnement te verplaatsen naar een nieuwe Tenant, wordt de functionaliteit van het cluster verloren gegaan vanwege verloren Roltoewijzingen en de rechten van de Service-Principal. Zie [Azure Kubernetes service (AKS) (Engelstalig)](https://docs.microsoft.com/azure/aks/)voor meer informatie over AKS.


## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw abonnement kunt koppelen of toevoegen, moet u de volgende taken uitvoeren:

- Bekijk de volgende lijst met wijzigingen en de manier waarop u dit kunt beïnvloeden:

  - Gebruikers aan wie rollen zijn toegewezen via RBAC, raken hun toegang kwijt
  - Service beheerder en mede beheerders hebben geen toegang meer
  - Als u sleutel kluizen hebt, zijn ze niet toegankelijk en moet u deze na de koppeling oplossen
  - Als u beheerde identiteiten hebt voor resources zoals Virtual Machines of Logic Apps, moet u deze opnieuw inschakelen of opnieuw maken na de koppeling
  - Als u een geregistreerde Azure Stack hebt, moet u deze na de koppeling opnieuw registreren

- Meld u aan met een account dat:

  - Beschikt over [een roltoewijzing](../../role-based-access-control/built-in-roles.md#owner) voor het abonnement. Zie [toegang tot Azure-resources beheren met RBAC en de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het toewijzen van de rol van eigenaar.
  - Bestaat zowel in de huidige map als in de nieuwe map. De huidige map is gekoppeld aan het abonnement. U koppelt de nieuwe map aan het abonnement. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../b2b/add-users-administrator.md)voor meer informatie over het verkrijgen van toegang tot een andere map.

- Zorg ervoor dat u niet een Azure Cloud Service Providers (CSP)-abonnement (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), een abonnement op Microsoft Internal (MS-AZR - 0015P) of een Microsoft Imagine-abonnement (MS-AZR - 0144P).

## Een abonnement aan een directory koppelen<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Voer de volgende stappen uit om een bestaand abonnement aan uw Azure AD-adres lijst te koppelen:

1. Meld u aan en selecteer het abonnement dat u gebruiken wilt in de [pagina met abonnementen in Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecteer **Wijzig de map**.

    ![De pagina abonnementen, met de wijziging directory optie gemarkeerd](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Bekijk eventuele waarschuwingen die worden weergegeven, en selecteer vervolgens **wijziging**.

    ![Wijzig de directory-pagina met de map wijzigen](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    De map wordt gewijzigd voor het abonnement en u krijgt een bericht weergegeven.

    ![Geslaagd bericht over Directory wijziging](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Gebruik de **Active** Directory om naar uw nieuwe map te gaan. Het kan enkele uren duren voordat alles goed wordt weer gegeven. Als het te lang lijkt te duren, controleert u het **filter voor algemeen abonnement**. Zorg ervoor dat het verplaatste abonnement niet is verborgen. Mogelijk moet u zich afmelden bij de Azure Portal en u opnieuw aanmelden om de nieuwe map te zien.

![Pagina Directory-wisselaar, met voorbeeld gegevens](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Wijzigen van de abonnementsdirectory is een bewerking op serviceniveau, zodat deze niet van invloed op abonnement eigendom. De accountbeheerder kan nog steeds wijzigen voor de servicebeheerder van de [Accountcentrum](https://account.azure.com/subscriptions). Als u de oorspronkelijke map wilt verwijderen, moet u het eigendom van het abonnement overdragen aan een nieuwe account beheerder. Zie [eigendom van een Azure-abonnement overdragen aan een ander account](../../billing/billing-subscription-transfer.md)voor meer informatie over het overdragen van het eigendom van de facturering.

## <a name="post-association-steps"></a>Stappen na de koppeling

Nadat u een abonnement aan een andere map hebt gekoppeld, moet u mogelijk de volgende taken uitvoeren om de bewerkingen te hervatten:

- Als u sleutel kluizen hebt, moet u de Tenant-ID van de sleutel kluis wijzigen. Zie voor meer informatie [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../../key-vault/key-vault-subscription-move-fix.md).

- Als u door het systeem toegewezen beheerde identiteiten voor bronnen hebt gebruikt, moet u deze identiteiten weer inschakelen. Als u door de gebruiker toegewezen beheerde identiteiten hebt gebruikt, moet u deze identiteiten opnieuw maken. Nadat u de beheerde identiteiten opnieuw hebt ingeschakeld of opnieuw hebt gemaakt, moet u de machtigingen die zijn toegewezen aan deze identiteiten opnieuw instellen. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md) voor meer informatie.

- Als u een Azure Stack met dit abonnement hebt geregistreerd, moet u zich opnieuw registreren. Zie [Azure stack registreren bij Azure](/azure-stack/operator/azure-stack-registration)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Als u een nieuwe Azure AD-Tenant wilt maken, raadpleegt u [Quick Start: een nieuwe Tenant maken in azure Active Directory](active-directory-access-create-new-tenant.md).

- Zie voor meer informatie over hoe Microsoft Azure het beheren van toegang tot resources, [klassieke abonnements beheerders rollen, Azure RBAC-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Zie voor meer informatie over het toewijzen van rollen in azure AD, [beheerders en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
