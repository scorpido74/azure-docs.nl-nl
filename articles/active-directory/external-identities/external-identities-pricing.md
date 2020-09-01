---
title: MAU-facturerings model voor externe Azure AD-identiteiten
description: Meer informatie over Azure AD externe identiteiten maandelijks actieve gebruikers (MAU) facturerings model voor de samen werking van gast gebruikers (B2B) in azure AD. Meer informatie over hoe u uw Azure AD-Tenant kunt koppelen aan een Azure-abonnement.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12aa400d6ca44043d3d90e78a93ae49d97a927e8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270149"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Facturerings model voor externe Azure AD-identiteiten

Azure Active Directory (Azure AD) prijzen voor externe identiteiten zijn gebaseerd op maandelijkse actieve gebruikers (MAU). Dit is het aantal unieke gebruikers met verificatie activiteiten binnen een kalender maand. Dit facturerings model is van toepassing op zowel de samen werking van Azure AD gast gebruikers (B2B) als [Azure AD B2C tenants](https://docs.microsoft.com/azure/active-directory-b2c/billing). MAU-facturering helpt u kosten te verlagen door een gratis laag en flexibele, voorspel bare prijzen te bieden. In dit artikel vindt u meer informatie over MAU-facturering en het koppelen van uw Azure AD-tenants aan een abonnement.

> [!IMPORTANT]
> Dit artikel bevat geen prijs informatie. Zie [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor de meest recente informatie over het gebruik van facturering en prijzen.

## <a name="what-do-i-need-to-do"></a>Wat moet ik doen?

Als u gebruik wilt maken van MAU-facturering, moet uw Azure AD-Tenant zijn gekoppeld aan een Azure-abonnement.

|Als uw Tenant:  |U moet het volgende doen:  |
|---------|---------|
| Een Azure AD-Tenant die al is gekoppeld aan een abonnement     | Geen actie uitvoeren. Wanneer u functies voor externe identiteiten gebruikt om samen te werken met gast gebruikers, wordt u automatisch gefactureerd met behulp van het MAU-model.        |
| Een Azure AD-Tenant die nog niet is gekoppeld aan een abonnement     | [Koppel uw Azure AD-Tenant aan een abonnement](#link-your-azure-ad-tenant-to-a-subscription) om Mau-facturering te activeren.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Over maandelijkse actieve gebruikers (MAU) facturering

In uw Azure AD-Tenant wordt het gebruik van gast gebruikers samen werking gefactureerd op basis van het aantal unieke gast gebruikers met verificatie activiteiten binnen een kalender maand. Dit model vervangt het facturerings model 1:5 quote, dat Maxi maal vijf gast gebruikers heeft toegestaan voor elke Azure AD Premium licentie in uw Tenant. Wanneer uw Tenant is gekoppeld aan een abonnement en u functies voor externe identiteiten gebruikt om samen te werken met gast gebruikers, wordt u automatisch gefactureerd met behulp van het facturerings model op basis van MAU.
  
De prijs categorie die van toepassing is op uw gast gebruikers is gebaseerd op de hoogste prijs categorie die is toegewezen aan uw Azure AD-Tenant. Als de hoogste prijs categorie in uw Tenant bijvoorbeeld Azure AD Premium P1 is, is de prijs categorie Premium P1 ook van toepassing op uw gast gebruikers. Als de hoogste prijs Azure AD Free is, wordt u gevraagd om een upgrade uit te proberen naar een prijs categorie Premium wanneer u Premium-functies voor gast gebruikers wilt gebruiken.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Uw Azure AD-Tenant koppelen aan een abonnement

Een Azure AD-Tenant moet worden gekoppeld aan een Azure-abonnement voor juiste facturering en toegang tot functies.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een Azure-account waaraan ten minste de rol [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) is toegewezen binnen het abonnement of een resource groep binnen het abonnement.

2. Selecteer de map waarin uw abonnement zich bevindt: Selecteer op de werk balk Azure Portal het pictogram voor de map en het **abonnement** en selecteer vervolgens de map met uw abonnement.

    ![Het pictogram van de map en het abonnement selecteren](media/external-identities-pricing/portal-mau-pick-directory.png)

3. Onder **Azure-Services**selecteert u **Azure Active Directory**.

4. Selecteer in het linkermenu **externe identiteiten**.

5. Onder **abonnementen**selecteert u **gekoppelde abonnementen**.

6. Selecteer in de lijst Tenant het selectie vakje naast de Tenant en selecteer vervolgens **abonnement koppelen**.

    ![De Tenant selecteren en een abonnement koppelen](media/external-identities-pricing/linked-subscriptions.png)

7. Selecteer een **abonnement** en een **resource groep**in het deel venster een abonnement koppelen. Selecteer vervolgens **Toepassen**.

    ![Een abonnement en resource groep selecteren](media/external-identities-pricing/link-subscription-resource.png)

Nadat u deze stappen hebt voltooid, wordt uw Azure-abonnement gefactureerd op basis van uw Azure direct-of Enterprise Agreement-gegevens, indien van toepassing.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor de meest recente prijs informatie.
