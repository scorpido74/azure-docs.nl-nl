---
title: Azure Migrate voorbereiden om te werken met ISV-hulpprogram ma's/-overzetten
description: Azure Migrate voorbereiden om te werken met ISV-hulpprogram ma's/-overzetten
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906980"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Voorbereiden op het werken met ISV-hulpprogram ma's/-overzetten

Als u een ISV- [hulp programma](migrate-services-overview.md#isv-integration)hebt toegevoegd, of als u wilt overstappen op een Azure migrate project, moet u een aantal stappen voorbereiden voordat u het hulp programma koppelt en gegevens naar Azure migrate verzendt. 

## <a name="check-azure-ad-permissions"></a>Azure AD-machtigingen controleren

Uw Azure-gebruikers account heeft een aantal machtigingen nodig:

- Machtiging voor het registreren van een Azure AD-app met uw Azure-Tenant.
- Machtiging voor het toewijzen van een rol aan de Azure AD-app op abonnements niveau.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Machtigingen instellen voor het registreren van een Azure AD-app

1. Controleer in Azure Active Directory de rol voor uw account. Als u de gebruikersrol hebt, klikt u op **gebruikers instellingen** aan de linkerkant en controleert u of gebruikers toepassingen kunnen registreren.
2. Als deze is ingesteld op **Ja**, kunnen gebruikers in de Azure AD-Tenant een app registreren. Als dat niet het geval is, kunnen alleen gebruikers met beheerders rechten apps registreren.
3. Als u geen machtigingen hebt, kan een gebruiker met beheerders rechten uw gebruikers account met de rol [toepassings beheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) opgeven, zodat u de app kunt registreren.
4. Wanneer het hulp programma is gekoppeld aan Azure Migrate, kan de beheerder de rol verwijderen uit uw account.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Machtigingen instellen om een rol toe te wijzen aan een Azure AD-app
 
Uw account heeft in uw Azure-abonnement **micro soft. Authorization/*/Write Access**nodig om een rol toe te wijzen aan een Azure AD-app. 

1. Als u de machtigingen voor het abonnement wilt controleren, opent u in het Azure Portal **abonnementen**.
2. Selecteer het betreffende abonnement. Als u deze niet ziet, selecteert u het **Filter globale abonnementen**. 
3. Selecteer **Mijn machtigingen**. Selecteer vervolgens **Klik hier om de volledige toegangs gegevens voor dit abonnement weer te geven**.
4. In de**weer gave** **roltoewijzingen** > en controleer de machtigingen.
5. Als uw account geen machtigingen heeft, vraagt u de abonnements beheerder om u toe te voegen aan de rol [beheerder van gebruikers toegang](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) of de rol [eigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) .
 

## <a name="start-using-the-tool"></a>Beginnen met het gebruik van het hulp programma

1. Als u nog geen licentie of gratis proef versie voor het hulp programma hebt, klikt u in het hulp programma in Azure Migrate in het **REGI ster**op meer **informatie**.
2. Volg de instructies in het hulp programma om een koppeling te maken van het hulp programma naar het Azure Migrate project en om gegevens te verzenden naar Azure Migrate.

## <a name="next-steps"></a>Volgende stappen

Volg de instructies voor het door voeren van de ISV of het door sturen van gegevens naar Azure Migrate.

   
