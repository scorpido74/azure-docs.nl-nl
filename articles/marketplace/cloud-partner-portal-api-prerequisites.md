---
title: API-vereisten-Azure Marketplace
description: Vereisten voor het gebruik van de Cloud Partner-portal-Api's.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: b672afcd8f52d8293b1bb81e30097062e2ee49c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053598"
---
<a name="api-prerequisites"></a>API-vereisten
================

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Er zijn twee vereiste programmeer middelen die u nodig hebt voor het gebruik van de Cloud Partner-portal-Api's: een Service-Principal en een Azure Active Directory Azure AD-toegangs token.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Een service-principal maken in uw Azure Active Directory-Tenant
----------------------------------------------------------------

Eerst moet u een service-principal maken in uw Azure AD-Tenant. Aan deze Tenant wordt een eigen set machtigingen toegewezen in de Cloud Partner-portal. De code roept Api's aan die als deze Tenant worden gebruikt in plaats van uw persoonlijke referenties te gebruiken.  Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken voor toegang tot resources](../active-directory/develop/howto-create-service-principal-portal.md)voor een volledige uitleg over het maken van een service-principal.


<a name="add-the-service-principal-to-your-account"></a>De Service-Principal toevoegen aan uw account
-----------------------------------------

Nu u de Service-Principal in uw Tenant hebt gemaakt, kunt u deze als een gebruiker aan uw Cloud Partner-portal-account toevoegen. Net als bij een gebruiker kan de service-principal een eigenaar of een bijdrager aan de portal zijn.

Gebruik de volgende stappen om de Service-Principal toe te voegen:

1. Meld u aan bij de Cloud Partner-portal. 
2. Klik op **gebruikers** op de linker menu balk en kies **gebruiker toevoegen**.

   ![Een gebruiker toevoegen aan de portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Selecteer in de vervolg keuzelijst **type** de optie **Service-Principal** en voeg de volgende gegevens toe:

-   Een **beschrijvende naam** voor de Service-Principal, bijvoorbeeld `spAccount` .
-   De **toepassings-id**. Als u deze id wilt vinden, gaat u naar de [Azure Portal](https://portal.azure.com), klikt u op **Azure Active Directory**, kiest u **app-registraties**en klikt u op uw app.
-   De **Tenant-id**, ook wel de **Directory-id**genoemd, voor uw Azure AD-Tenant. U kunt deze id vinden op de pagina Azure Active Directory in het [Azure Portal](https://portal.azure.com)onder **Eigenschappen**.
-   De **object-id** voor uw Service-Principal-object. U kunt deze id ophalen van de Azure Portal. Ga naar **Azure Active Directory**, kies **app-registraties**, klik op uw app en klik op de naam van de app onder **beheerde toepassing in de lokale map**. Ga vervolgens naar de pagina **Eigenschappen** om de object-id te vinden. Zorg ervoor dat u de eerste object-ID die in uw app is, niet vastmaakt, maar in plaats daarvan de object-ID in de beheerde toepassing.
-   De **rol** die is gekoppeld aan het account, dat wordt gebruikt voor RBAC.

     ![Een beheerde app toevoegen aan de portal](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Klik op **toevoegen** om de Service-Principal toe te voegen aan uw account.

   ![Een Service-Principal toevoegen](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Een Azure AD-toegangstoken ophalen
----------------------------

De Cloud Partner-portal-Api's gebruiken de volgende assets en protocollen tijdens de verificatie:

- Een JSON Web Token (JWT) Bearer-token om toegang tot resources aan te vragen
- Het [OpenID Connect Connect](https://openid.net/connect/) (OIDC)-protocol om identiteit te verifiëren
- [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) als de identiteits instantie

Er zijn twee principe benaderingen voor het programmatisch ophalen van een JWT-token:

- Gebruik de micro soft Authentication Library voor .NET ([MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Deze methode op hoger niveau wordt aanbevolen voor .NET-ontwikkel aars. 
- Een **http post-** aanvraag indienen bij het Azure AD OAuth- **token** eind punt, waarbij de volgende indeling wordt gebruikt:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

U kunt dit token nu door geven als onderdeel van de autorisatie-header voor API-aanvragen.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Voor alle Api's in deze Naslag informatie wordt de autorisatie-header altijd verondersteld door gegeven, zodat deze niet expliciet wordt vermeld.

Zie [problemen oplossen met verificatie fouten](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)als u verificatie fouten in uw aanvraag uitvoert.
