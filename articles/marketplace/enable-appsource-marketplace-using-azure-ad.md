---
title: Een Microsoft AppSource- en Azure Marketplace-vermelding inschakelen met Azure Active Directory | Azure
description: Schakel een vermeldingstype in met Azure Active Directory in de Azure Marketplace en AppSource voor app- en service-uitgevers.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: d2f33fc8b1bdd15b53ad9130b5cc598dd6d5ee1a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684631"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Een AppSource- en Azure Marketplace-vermelding inschakelen met behulp van Azure Active Directory

 Azure Active Directory (Azure AD) is een cloudidentiteitsservice waarmee verificatie met een Microsoft-account mogelijk is. Azure AD maakt gebruik van industriestandaardframeworks. [Meer informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Azure AD-voordelen

Klanten van Microsoft AppSource en Azure Marketplace gebruiken in-productervaringen om in de catalogus te zoeken. Deze acties vereisen dat klanten zich bij het product aanmelden. Azure AD-integratie biedt de volgende voordelen:

- Snellere betrokkenheid en een geoptimaliseerde klantervaring
- Single sign-on (SSO) voor miljoenen zakelijke gebruikers
- Consistente, aanmeldingservaring voor toepassingen die door verschillende partners zijn gepubliceerd
- Schaalbare, cross-platform authenticatie voor mobiele en cloud apps

## <a name="offers-that-require-azure-ad"></a>Aanbiedingen waarvoor Azure AD vereist is

De verschillende [aanbiedingsopties en aanbiedingstypen](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) voor AppSource en Azure Marketplace hebben verschillende vereisten voor de IMPLEMENTATIE van Azure AD. Zie de volgende tabel voor meer informatie:

| **Aanbiedingstype**    | **Azure AD SSO vereist?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Neem contact met mij op | Proefversie | Test Drive | Transact |
| Virtuele machine | N.v.t. | Nee | Nee | Nee |
| Azure Apps (oplossingssjabloon)  | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Beheerde apps  | N.v.t. | N.v.t. | N.v.t. | Nee |
| SaaS  | Nee | Ja | Ja | Ja |
| Containers  | N.v.t. | N.v.t. | N.v.t. | Nee |
| Adviesservices  | Nee | N.v.t. | N.v.t. | N.v.t. |

Zie [SaaS-toepassingen-publicatiegids voor aanbiedingen voor](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)meer informatie over technische vereisten voor SaaS.

## <a name="azure-ad-integration"></a>Azure AD-integratie

- Zie [Azure Active Directory voor ontwikkelaars voor]( https://aka.ms/aaddev)informatie over het inschakelen van één aanmelding door Azure AD in uw vermelding te integreren.
- Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Een proefaanbieding inschakelen

Geautomatiseerde klantinstellingen kunnen de kans op conversie vergroten. Wanneer uw klant uw proefvermelding selecteert en wordt doorgestuurd naar uw proefomgeving, u de klant rechtstreeks instellen zonder dat extra aanmeldingsstappen nodig zijn.

Tijdens de verificatie stuurt Azure AD een token naar uw app of aanbieding. De gebruikersinformatie die door het token wordt verstrekt, maakt het mogelijk een gebruikersaccount in uw app of aanbieding aan te maken. Zie [Voorbeeldtokens voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)meer informatie.

Wanneer u Azure AD gebruikt om verificatie met één klik in te schakelen in uw app of proefvermelding, gaat u als:

- Stroomlijn de klantervaring van de Marketplace naar uw proefaanbieding.
- Houd het gevoel van een in-productervaring behouden, zelfs wanneer de gebruiker wordt doorgestuurd van de Marketplace naar uw domein of proefomgeving.
- Verklein de kans op stopzetting wanneer gebruikers worden omgeleid omdat er geen extra aanmeldingsstappen zijn.
- Verminder implementatiebarrières voor de grote populatie Azure AD-gebruikers.

## <a name="verify-azure-ad-integration"></a>Azure AD-integratie verifiëren

### <a name="multitenant-solutions"></a>Multitenant-oplossingen

Gebruik Azure AD om de volgende acties te ondersteunen:

- Registreer uw app in een van de Marketplace-winkelpuien. Bekijk [app-registratie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) of [AppSource-certificering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) voor meer informatie.
- Schakel de functie multitenancy-ondersteuning in Azure AD in om een proefervaring met één klik te krijgen.

Als u nieuw bent bij het gebruik van Azure AD-federatieve enkele aanmelding, neemt u de volgende stappen:

1. Registreer uw app op de Marketplace.
1. SSO ontwikkelen met Azure AD met [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) of [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Schakel de functie multitenancy-ondersteuning in Azure AD in om een proefervaring met één klik te bieden.

### <a name="single-tenant-solutions"></a>Oplossingen voor één huurder

Gebruik Azure AD om een van de volgende acties te ondersteunen:

- Voeg gastgebruikers toe aan uw directory met [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Handmatig proefversies instellen voor klanten met de optie **Publiceren contact met mij.**
- Ontwikkel een testrit per klant.
- Bouw een demo-app met meerdere tenant's die SSO gebruikt.

## <a name="next-steps"></a>Volgende stappen

Als je dat nog niet gedaan hebt, 

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over de marktplaats.

Als u zich wilt registreren in partnercentrum, begint u met het maken van een nieuwe aanbieding of werkt u aan een bestaand aanbieding:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
