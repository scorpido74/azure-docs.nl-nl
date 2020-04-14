---
title: API-vereisten | Azure Marketplace
description: Vereisten met behulp van de API's van cloudpartnerportalen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: df94cba1f77ae3ea8cf595e7c651af7a69108bb6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255971"
---
<a name="api-prerequisites"></a>API-vereisten
================

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Er zijn twee vereiste programmatische elementen die u nodig hebt om de API's van cloudpartnerportalen te gebruiken: een serviceprincipal en een Azure AD-toegangstoken (Azure Active Directory).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Een serviceprincipal maken in uw Azure Active Directory-tenant
----------------------------------------------------------------

Eerst moet u een serviceprincipal maken in uw Azure AD-tenant. Deze tenant krijgt een eigen set machtigingen toegewezen in de Cloud Partner Portal. Uw code roept API's op met behulp van deze tenant in plaats van uw persoonlijke referenties te gebruiken.  Zie [Portal gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)voor een volledige uitleg van het maken van een serviceprincipal.


<a name="add-the-service-principal-to-your-account"></a>De serviceprincipal toevoegen aan uw account
-----------------------------------------

Nu u de serviceprincipal in uw tenant hebt gemaakt, u deze als gebruiker toevoegen aan uw Cloud Partner Portal-account. Net als een gebruiker kan de serviceprincipal eigenaar of bijdrager zijn aan het portaal.

Gebruik de volgende stappen om de serviceprincipal toe te voegen:

1. Meld u aan bij de Cloud Partner Portal. 
2. Klik op **Gebruikers** op de linkermenubalk en kies **Gebruiker toevoegen**.

   ![Een gebruiker toevoegen aan de portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Selecteer **serviceprincipal** in de vervolgkeuzelijst **Type** en voeg de volgende details toe:

-   Een **vriendelijke naam** voor de `spAccount`service principal, bijvoorbeeld .
-   De **toepassings-id**. Als u deze id wilt vinden, gaat u naar de [Azure-portal,](https://portal.azure.com)klikt u op **Azure Active Directory,** kiest **u App-registraties**en klikt u op uw app.
-   De **tenant-id**, ook wel **directory-id**genoemd, voor uw Azure AD-tenant. U deze id vinden in de Azure Active Directory-pagina in de [Azure-portal](https://portal.azure.com)onder **Eigenschappen**.
-   De **object-id** voor uw servicehoofdobject. U deze id ophalen via de Azure-portal. Ga naar **Azure Active Directory,** kies **App-registraties,** klik op uw app en klik op de app-naam onder **Beheerde toepassing in de lokale map**. Ga vervolgens naar de pagina **Eigenschappen** om de object-id te zoeken. Zorg ervoor dat u niet de eerste object-id in uw app pakt, maar in plaats daarvan de object-id in de beheerde toepassing.
-   De **rol** die aan het account is gekoppeld, die wordt gebruikt voor RBAC.

     ![Een beheerde app toevoegen aan de portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Klik **op Toevoegen** om de serviceprincipal aan uw account toe te voegen.

   ![Een serviceprincipal toevoegen](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Een Azure AD-toegangstoken ophalen
----------------------------

De API's van cloudpartnerportalen gebruiken de volgende elementen en protocollen tijdens de verificatie:

- Een JSON-token (JWT) toon om toegang tot bronnen aan te vragen
- Het [OpenID Connect](https://openid.net/connect/) -protocol (OIDC) om de identiteit te verifiëren
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) als identiteitsautoriteit

Er zijn twee principiële benaderingen voor het programmatisch verwerven van een JWT-token:

- Gebruik de Microsoft-verificatiebibliotheek voor .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Deze aanpak op een hoger niveau wordt aanbevolen voor .NET-ontwikkelaars. 
- Een **HTTP POST-aanvraag** indienen bij het eindpunt van azure **AD-token,** dat het formulier aanneemt:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

U dit token nu doorgeven als onderdeel van de autorisatiekop voor API-aanvragen.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Voor alle API's in deze verwijzing wordt altijd aangenomen dat de autorisatiekop is geslaagd, zodat deze niet expliciet wordt vermeld.

Zie [Verificatiefouten oplossen](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)als u in uw aanvraag verificatiefouten tegenkomt.
