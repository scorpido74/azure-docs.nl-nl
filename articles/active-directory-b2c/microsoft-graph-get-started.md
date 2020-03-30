---
title: Resources beheren met Microsoft Graph
titleSuffix: Azure AD B2C
description: Bereid u voor op het beheer van Azure AD B2C-resources met Microsoft Graph door een toepassing te registreren waarvoor de vereiste Graph API-machtigingen zijn verleend.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184338"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Azure AD B2C beheren met Microsoft Graph

[Met Microsoft Graph][ms-graph] u veel van de bronnen binnen uw Azure AD B2C-tenant beheren, waaronder gebruikersaccounts van klanten en aangepaste beleidsregels. Door scripts of toepassingen te schrijven die de [Microsoft Graph API][ms-graph-api]aanroepen, u tenantbeheertaken automatiseren, zoals:

* Een bestaande gebruikerswinkel migreren naar een Azure AD B2C-tenant
* Aangepaste beleidsregels implementeren met een Azure Pipeline in Azure DevOps en aangepaste beleidssleutels beheren
* Gebruikersregistratie hosten op uw eigen pagina en gebruikersaccounts maken in uw Azure AD B2C-map achter de schermen
* Registratie van toepassingen automatiseren
* Controlelogboeken verkrijgen

Met de volgende secties u zich voorbereiden op het gebruik van de Microsoft Graph API om het beheer van resources in uw Azure AD B2C-map te automatiseren.

## <a name="microsoft-graph-api-interaction-modes"></a>Interactiemodi voor Microsoft Graph API

Er zijn twee communicatiemodi die u gebruiken wanneer u met de Microsoft Graph API werkt om resources in uw Azure AD B2C-tenant te beheren:

* **Interactief** - Geschikt voor run-once taken, u gebruikt een beheerdersaccount in de B2C-tenant om de beheertaken uit te voeren. Voor deze modus moet een beheerder zich aanmelden met behulp van zijn referenties voordat hij de Microsoft Graph API aanroept.

* **Geautomatiseerd** - Voor geplande of continu uitvoerenvan taken maakt deze methode gebruik van een serviceaccount dat u configureert met de machtigingen die nodig zijn om beheertaken uit te voeren. U maakt het 'serviceaccount' in Azure AD B2C door een toepassing te registreren die uw toepassingen en scripts gebruiken om te authenticeren met behulp van de *client-id (Application)* en de oauth 2.0-clientreferentiesverlenen. In dit geval fungeert de toepassing als zichzelf om de Microsoft Graph API aan te roepen, niet de beheerdergebruiker zoals in de eerder beschreven interactieve methode.

U schakelt het **scenario voor geautomatiseerde** interactie in door een toepassingsregistratie te maken die in de volgende secties wordt weergegeven.

## <a name="register-management-application"></a>Register management applicatie

Voordat uw scripts en toepassingen kunnen communiceren met de [Microsoft Graph API][ms-graph-api] om Azure AD B2C-resources te beheren, moet u een toepassingsregistratie maken in uw Azure AD B2C-tenant die de vereiste API-machtigingen verleent.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>API-toegang verlenen

Geef vervolgens de geregistreerde toepassingsmachtigingen om tenantbronnen te manipuleren via aanroepen naar de Microsoft Graph API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Clientgeheim maken

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

U hebt nu een toepassing die toestemming heeft om gebruikers te *maken,* *lezen,* *bijwerken*en *verwijderen* in uw Azure AD B2C-tenant. Ga verder naar de volgende sectie om *wachtwoordupdatemachtigingen* toe te voegen.

## <a name="enable-user-delete-and-password-update"></a>Gebruiker verwijderen en wachtwoordbijwerken inschakelen

De machtigingen *voor lees- en schrijfmapgegevens* bevat **niet** de mogelijkheid om gebruikers te verwijderen of wachtwoorden van gebruikersaccounts bij te werken.

Als uw toepassing of script gebruikers moet verwijderen of hun wachtwoorden moet bijwerken, wijst u de functie *Gebruikersbeheerder* toe aan uw toepassing:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en gebruik het filter **Directory + Abonnement** om over te schakelen naar uw Azure AD B2C-tenant.
1. Zoeken naar en selecteer **Azure AD B2C**.
1. Selecteer Rollen **en beheerders**onder **Beheren**.
1. Selecteer de rol **Gebruikersbeheerder.**
1. Selecteer **Toewijzingen toevoegen**.
1. Voer in het vak **Tekst selecteren** de naam in van de eerder geregistreerde toepassing, bijvoorbeeld *managementapp1*. Selecteer uw toepassing wanneer deze wordt weergegeven in de zoekresultaten.
1. Selecteer **Toevoegen**. Het kan enkele minuten duren voordat de machtigingen volledig worden doorgegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u uw beheertoepassing hebt geregistreerd en de vereiste machtigingen hebt verleend, kunnen uw toepassingen en services (bijvoorbeeld Azure Pipelines) de referenties en machtigingen gebruiken om te communiceren met de Microsoft Graph API.

* [B2C-bewerkingen ondersteund door Microsoft Graph](microsoft-graph-operations.md)
* [Azure AD B2C-gebruikersaccounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)
* [Controlelogboeken verzamelen met de Azure AD-rapportage-API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
