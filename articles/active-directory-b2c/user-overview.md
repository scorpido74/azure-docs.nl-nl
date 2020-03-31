---
title: Overzicht van gebruikersaccounts in Azure Active Directory B2C
description: Meer informatie over de typen gebruikersaccounts die kunnen worden gebruikt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185655"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Overzicht van gebruikersaccounts in Azure Active Directory B2C

In Azure Active Directory B2C (Azure AD B2C) zijn er verschillende typen accounts die kunnen worden gemaakt. Azure Active Directory, Active Directory B2B en Active Directory B2C delen in de typen gebruikersaccounts die kunnen worden gebruikt.

De volgende typen accounts zijn beschikbaar:

- **Werkaccount** - Een werkaccount heeft toegang tot bronnen in een tenant en met een beheerdersrol kunnen tenants beheren.
- **Gastaccount** - Een gastaccount kan alleen een Microsoft-account of een Azure Active Directory-gebruiker zijn die kan worden gebruikt om toegang te krijgen tot toepassingen of tenants te beheren.
- **Consumentenaccount** : een consumentenaccount wordt gebruikt door een gebruiker van de toepassingen die u hebt geregistreerd bij Azure AD B2C. Consumentenaccounts kunnen worden gemaakt door:
  - De gebruiker die een aanmeldingsgebruikersstroom doorloopt in een Azure AD B2C-toepassing
  - Microsoft Graph API gebruiken
  - Azure Portal gebruiken

## <a name="work-account"></a>Werkaccount

Een werkaccount wordt op dezelfde manier gemaakt voor alle tenants op basis van Azure AD. Als u een werkaccount wilt maken, u de gegevens gebruiken in [Quickstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Er wordt een werkaccount gemaakt met de **nieuwe gebruikerskeuze** in de Azure-portal.

Wanneer u een nieuw werkaccount toevoegt, moet u rekening houden met de volgende configuratie-instellingen:

- **Naam** en **gebruikersnaam** - De eigenschap **Naam** bevat de opgegeven en achternaam van de gebruiker. De **gebruikersnaam** is de id die de gebruiker invoert om zich aan te melden. De gebruikersnaam bevat het volledige domein. Het domeinnaamgedeelte van de gebruikersnaam moet de initiële standaarddomeinnaam *zijn your-domain.onmicrosoft.com,* ofwel een geverifieerde, niet-federatieve [aangepaste domeinnaam](../active-directory/fundamentals/add-custom-domain.md) zoals *contoso.com*.
- **Profiel** - Het account is ingesteld met een profiel van gebruikersgegevens. U een voornaam, achternaam, functietitel en afdelingsnaam invoeren. Je het profiel bewerken nadat het account is gemaakt.
- **Groepen** : gebruik een groep om beheertaken uit te voeren, zoals het toewijzen van licenties of machtigingen aan een aantal gebruikers of apparaten tegelijk. U het nieuwe account in een bestaande [groep](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) in uw tenant plaatsen.
- **Maprol** : u moet het toegangsniveau opgeven dat het gebruikersaccount heeft om resources in uw tenant te gebruiken. De volgende machtigingsniveaus zijn beschikbaar:

    - **Gebruiker** - Gebruikers hebben toegang tot toegewezen resources, maar kunnen de meeste tenantbronnen niet beheren.
    - **Globale beheerder** - Globale beheerders hebben volledige controle over alle tenantresources.
    - **Beperkte beheerder** - Selecteer de beheerrol of rollen voor de gebruiker. Zie [Beheerdersrollen toewijzen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de rollen die kunnen worden geselecteerd.

### <a name="create-a-work-account"></a>Een werkaccount maken

U de volgende gegevens gebruiken om een nieuw werkaccount te maken:

- [Azure-portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Een gebruikersprofiel bijwerken

U de volgende informatie gebruiken om het profiel van een gebruiker bij te werken:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Een wachtwoord voor een gebruiker opnieuw instellen

U de volgende gegevens gebruiken om het wachtwoord van een gebruiker opnieuw in te stellen:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Gastgebruiker

U externe gebruikers uitnodigen voor uw tenant als gastgebruiker. Een typisch scenario voor het uitnodigen van een gastgebruiker voor uw Azure AD B2C-tenant is het delen van beheerverantwoordelijkheden. Zie Eigenschappen van een Azure [Active Directory B2B-samenwerkingsgebruiker](../active-directory/b2b/user-properties.md)voor een voorbeeld van het gebruik van een gastaccount.

Wanneer u een gastgebruiker uitnodigt voor uw tenant, geeft u het e-mailadres van de ontvanger en een bericht op waarin de uitnodiging wordt beschreven. De uitnodigingskoppeling brengt de gebruiker naar de toestemmingspagina waar de knop **Aan de slag** is geselecteerd en de controle van machtigingen wordt geaccepteerd. Als een postvak IN niet aan het e-mailadres is gekoppeld, kan de gebruiker naar de toestemmingspagina navigeren door naar een Microsoft-pagina te gaan met de uitgenodigde referenties. De gebruiker wordt dan gedwongen om de uitnodiging op dezelfde manier in te wisselen als het klikken op de link in de e-mail. Bijvoorbeeld: `https://myapps.microsoft.com/B2CTENANTNAME`.

U de [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) ook gebruiken om een gastgebruiker uit te nodigen.

## <a name="consumer-user"></a>Consument gebruiker

De gebruiker van de consument kan zich aanmelden bij toepassingen die zijn beveiligd door Azure AD B2C, maar heeft geen toegang tot Azure-bronnen zoals de Azure-portal. De consument kan een lokaal account of federatieve accounts gebruiken, zoals Facebook of Twitter. Een consumentenaccount wordt gemaakt met behulp van een [aanmeldings- of aanmeldingsstroom,](user-flow-overview.md)met behulp van de Microsoft Graph-API of met behulp van de Azure-portal.

U de gegevens opgeven die worden verzameld wanneer een gebruikersaccount van een consument wordt gemaakt met behulp van aangepaste gebruikerskenmerken. Zie [Aangepaste kenmerken definiëren in Azure Active Directory B2C](user-flow-custom-attributes.md)voor meer informatie.

Zie [Azure AD B2C-gebruikersaccounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)voor meer informatie over het beheren van consumentenaccounts.

### <a name="migrate-consumer-user-accounts"></a>Gebruikersaccounts van consumenten migreren

Mogelijk moet u bestaande gebruikersaccounts van consumenten migreren van elke identiteitsprovider naar Azure AD B2C. Zie [Gebruikers migreren naar Azure AD B2C](user-migration.md)voor meer informatie.
