---
title: Een Microsoft AppSource en een Azure Marketplace-vermelding inschakelen met behulp van Azure Active Directory | Azure
description: Een vermeldings type in te scha kelen met behulp van Azure Active Directory in azure Marketplace en AppSource voor app-en service-Publishers.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 9423263270313659017b16a5e34511e7ef385af5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825893"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Een AppSource- en Azure Marketplace-vermelding inschakelen met behulp van Azure Active Directory

 Azure Active Directory (Azure AD) is een Cloud-identiteits service die verificatie met een Microsoft-account mogelijk maakt. Azure AD maakt gebruik van gestandaardiseerde frameworks. Meer [informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Voor delen van Azure AD

Microsoft AppSource-en Azure Marketplace-klanten gebruiken in-product ervaringen om de lijst catalogi te doorzoeken. Voor deze acties moeten klanten zich aanmelden bij het product. Azure AD-integratie biedt de volgende voor delen:

- Snellere betrokkenheid en een geoptimaliseerde klant ervaring
- Eenmalige aanmelding (SSO) voor miljoenen zakelijke gebruikers
- Consistente, aanmeldings ervaring voor toepassingen die worden gepubliceerd door verschillende partners
- Schaal bare, platformoverschrijdende verificatie voor mobiele en Cloud-apps

## <a name="offers-that-require-azure-ad"></a>Aanbiedingen waarvoor Azure AD is vereist

De verschillende [aanbiedings opties en aanbiedings typen](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) voor AppSource en Azure Marketplace hebben verschillende vereisten voor de implementatie van Azure AD. Raadpleeg de volgende tabel voor meer informatie:

| **Aanbiedings type**    | **Is Azure AD SSO vereist?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Contact opnemen | Proefversie | Test Drive | Handelen |
| Virtuele machine | N.v.t. | Nee | Nee | Nee |
| Azure-apps (oplossings sjabloon)  | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Beheerde apps  | N.v.t. | N.v.t. | N.v.t. | Nee |
| SaaS  | Nee | Ja | Ja | Ja |
| Containers  | N.v.t. | N.v.t. | N.v.t. | Nee |
| Advies Services  | Nee | N.v.t. | N.v.t. | N.v.t. |

Zie voor meer informatie over SaaS technische vereisten de [hand leiding voor SaaS-toepassingen](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)die u publiceert.

## <a name="azure-ad-integration"></a>Azure AD-integratie

- Zie [Azure Active Directory voor ontwikkel aars]( https://aka.ms/aaddev)voor meer informatie over het inschakelen van eenmalige aanmelding door Azure ad te integreren in uw vermelding.
- Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over eenmalige aanmelding voor Azure AD.

## <a name="enable-a-trial-listing"></a>Een proef lijst inschakelen

Door automatische klant instellingen kan de kans op conversie worden verhoogd. Wanneer uw klant uw proef lijst selecteert en wordt omgeleid naar uw proef omgeving, kunt u de klant rechtstreeks instellen zonder aanvullende aanmeldings stappen.

Tijdens de verificatie verzendt Azure AD een token naar uw app of aanbieding. Met de gebruikers gegevens die door het token worden verstrekt, wordt het maken van een gebruikers account in uw app of aanbieding ingeschakeld. Zie [voorbeeld tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)voor meer informatie.

Wanneer u Azure AD gebruikt om verificatie met één klik in te scha kelen in uw app of proef versie, kunt u het volgende doen:

- Stroom lijn de klant ervaring van de Marketplace met uw proef versie.
- Zorg ervoor dat u een in-product ervaring hebt, zelfs wanneer de gebruiker wordt omgeleid van de Marketplace naar uw domein of test omgeving.
- Verminder de kans op onbreken wanneer gebruikers worden omgeleid omdat er geen aanvullende aanmeldings stappen zijn.
- Verminder implementatie barrières voor de grote populatie van Azure AD-gebruikers.

## <a name="verify-azure-ad-integration"></a>Azure AD-integratie verifiëren

### <a name="multitenant-solutions"></a>Multi tenant-oplossingen

Gebruik Azure AD om de volgende acties te ondersteunen:

- Registreer uw app in een van de Marketplace-winkels. Bekijk de [app-registratie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) of AppSource- [certificering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) voor meer informatie.
- Schakel de functie ondersteuning voor multitenancy in azure AD in om een proef ervaring met één klik te krijgen.

Voer de volgende stappen uit als u geen ervaring hebt met het gebruik van Azure AD federatieve eenmalige aanmelding:

1. Registreer uw app in Marketplace.
1. Ontwikkel SSO met Azure AD met behulp van [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) of [OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Schakel de functie ondersteuning voor multitenancy in azure AD in om een proef ervaring met één klik te bieden.

### <a name="single-tenant-solutions"></a>Oplossingen met één Tenant

Gebruik Azure AD om een van de volgende acties te ondersteunen:

- Gast gebruikers toevoegen aan uw directory met behulp van [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- U kunt voor klanten hand matig proef versies instellen met behulp van de publicatie optie **contact opnemen** .
- Ontwikkel een test drive per klant.
- Bouw een multi tenant-voorbeeld demo-app die gebruikmaakt van SSO.

## <a name="next-steps"></a>Volgende stappen

- Zorg ervoor dat u zich hebt [geregistreerd op de Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Zie [een partner centrum-account maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) voor meer informatie over het maken of volt ooien van uw aanbieding.
