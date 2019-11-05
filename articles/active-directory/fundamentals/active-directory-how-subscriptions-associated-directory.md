---
title: Een bestaand Azure-abonnement toevoegen aan uw Tenant-Azure Active Directory | Microsoft Docs
description: Instructies voor het toevoegen van een bestaand Azure-abonnement aan uw Azure Active Directory-Tenant.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473399"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Een Azure-abonnement koppelen aan of toevoegen aan uw Azure Active Directory-Tenant

Een Azure-abonnement heeft een vertrouwens relatie met Azure Active Directory (Azure AD). Dit betekent dat het abonnement Azure AD vertrouwt voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde Azure AD-Directory vertrouwen, maar elk abonnement kan slechts één map vertrouwen.

Als uw abonnement is verlopen, hebt u geen toegang meer tot alle andere resources die zijn gekoppeld aan het abonnement. De Azure AD-Directory blijft echter aanwezig in azure, zodat u de Directory kunt koppelen en beheren met een ander Azure-abonnement.

Al uw gebruikers hebben één *basis* Directory voor verificatie. Uw gebruikers kunnen echter ook gasten in andere directory's zijn. U kunt zowel de Home-als de gast mappen voor elke gebruiker in azure AD bekijken.

> [!Important]
> Wanneer u een abonnement aan een andere map koppelt, verliezen gebruikers die rollen hebben die zijn toegewezen via [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/role-assignments-portal.md) hun toegang. Klassieke abonnements beheerders (Service beheerder en mede beheerders) hebben ook geen toegang meer. 
>
> Beleids toewijzingen worden ook verwijderd uit een abonnement wanneer het abonnement is gekoppeld aan een andere map.
> 
> Als u uw Azure Kubernetes service-cluster (AKS) verplaatst naar een ander abonnement of het cluster eigenaar-abonnement verplaatst naar een nieuwe Tenant, zorgt het cluster er daarom voor dat de functionaliteit verloren gaat als gevolg van kwijt raken van roltoewijzingen en machtigingen voor service-principals. Zie [Azure Kubernetes service (AKS) (Engelstalig)](https://docs.microsoft.com/azure/aks/)voor meer informatie over AKS.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw abonnement kunt koppelen of toevoegen, moet u de volgende taken uitvoeren:

1. Bekijk de volgende lijst met wijzigingen en de manier waarop u dit kunt beïnvloeden:

    - Gebruikers aan wie rollen zijn toegewezen via RBAC, raken hun toegang kwijt
    - Service beheerder en mede beheerders hebben geen toegang meer
    - Als u sleutel kluizen hebt, zijn ze niet toegankelijk en moet u deze na de koppeling oplossen
    - Als u beheerde identiteiten hebt voor resources zoals Virtual Machines of Logic Apps, moet u deze opnieuw inschakelen of opnieuw maken na de koppeling
    - Als u een geregistreerde Azure Stack hebt, moet u deze na de koppeling opnieuw registreren

1. Meld u aan met een account dat:
    - Beschikt over [een roltoewijzing](../../role-based-access-control/built-in-roles.md#owner) voor het abonnement. Zie [toegang tot Azure-resources beheren met RBAC en de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het toewijzen van de rol van eigenaar.
    - Bevindt zich in zowel de huidige map die is gekoppeld aan het abonnement als in de nieuwe map waaraan u het abonnement wilt koppelen. Zie voor meer informatie over het verkrijgen van toegang tot een andere map de [procedures hoe Azure Active Directory beheerders B2B-samenwerkings gebruikers toevoegen?](../b2b/add-users-administrator.md).

1. Zorg ervoor dat u geen gebruik maakt van een Azure Cloud service provider (CSP)-abonnement (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), een intern micro soft-abonnement (MS-AZR-0015P) of een Microsoft Imagine-abonnement (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Een bestaand abonnement koppelen aan uw Azure AD Directory

1. Meld u aan en selecteer het abonnement dat u wilt gebruiken op de [pagina Abonnementen in azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecteer **map wijzigen**.

    ![Pagina Abonnementen, met de optie map wijzigen gemarkeerd](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Bekijk eventuele waarschuwingen die worden weer gegeven en selecteer **wijzigen**.

    ![Wijzig de Directory-pagina met de Directory waarnaar moet worden gewijzigd](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    De map wordt gewijzigd voor het abonnement en er wordt een bericht weer gegeven.

    ![Geslaagd bericht over Directory wijziging](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Gebruik de **Directory-schakelaar** om naar uw nieuwe map te gaan. Het kan enkele uren duren voordat alles goed wordt weer gegeven. Als het te lang lijkt te duren, controleert u het **algemene abonnements filter** voor het verplaatste abonnement om te controleren of het niet is verborgen. Mogelijk moet u zich afmelden bij de Azure Portal en u vervolgens weer aanmelden om de nieuwe map te kunnen zien. 

    ![Pagina Directory-wisselaar, met voorbeeld gegevens](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Het wijzigen van de abonnements Directory is een bewerking op service niveau. Dit heeft geen invloed op het eigendom van het abonnement. De account beheerder kan de service beheerder nog steeds wijzigen vanuit het [account centrum](https://account.azure.com/subscriptions). Als u de oorspronkelijke map wilt verwijderen, moet u het eigendom van het abonnement overdragen aan een nieuwe account beheerder. Zie [eigendom van een Azure-abonnement overdragen aan een ander account](../../billing/billing-subscription-transfer.md)voor meer informatie over het overdragen van het eigendom van de facturering.

## <a name="post-association-steps"></a>Stappen na de koppeling
Nadat u een abonnement aan een andere map hebt gekoppeld, zijn er mogelijk extra stappen die u moet uitvoeren om de bewerkingen te hervatten.

1. Als u sleutel kluizen hebt, moet u de Tenant-ID van de sleutel kluis wijzigen. Zie voor meer informatie [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../../key-vault/key-vault-subscription-move-fix.md).

2. Als u door het systeem toegewezen beheerde identiteiten voor resources gebruikt, moet u deze opnieuw inschakelen. Als u door de gebruiker toegewezen beheerde identiteiten gebruikt, moet u deze opnieuw maken. Nadat u de beheerde identiteiten opnieuw hebt ingeschakeld of opnieuw hebt gemaakt, moet u de machtigingen die zijn toegewezen aan deze identiteiten opnieuw instellen. Zie [Wat is beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md)voor meer informatie.

3. Als u een Azure Stack met dit abonnement hebt geregistreerd, moet u zich opnieuw registreren. Zie [Azure stack registreren bij Azure](/azure-stack/operator/azure-stack-registration)voor meer informatie.



## <a name="next-steps"></a>Volgende stappen

- Als u een nieuwe Azure AD-Tenant wilt maken, raadpleegt [u de Access-Azure Active Directory voor het maken van een nieuwe Tenant](active-directory-access-create-new-tenant.md)

- Als u meer wilt weten over hoe de toegang tot resources wordt beheerd in Microsoft Azure, ziet u [Inzicht krijgen in toegang tot resources in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Zie [Directory rollen toewijzen aan gebruikers met Azure Active Directory](active-directory-users-assign-role-azure-portal.md) voor meer informatie over het toewijzen van rollen in azure AD.
