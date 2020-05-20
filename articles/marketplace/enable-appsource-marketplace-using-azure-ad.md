---
title: Integreer uw aanbieding voor micro soft Commercial Marketplace met Azure Active Directory
description: Gebruik Azure Active Directory om uw Microsoft AppSource en Azure Marketplace-aanbiedingen te verifiëren.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 17cbfe92744ad96f2b5651b7e2f47a6443337068
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658038"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integreer uw commerciële Marketplace-vermelding met Azure Active Directory

 In dit artikel worden de vereisten beschreven voor het integreren van een commerciële Marketplace-vermelding of aanbieding met Azure Active Directory (Azure AD). Azure AD is een Cloud-identiteits service die gebruikmaakt van de industrie standaard frameworks om verificatie met een Microsoft-account in te scha kelen. Meer [informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Voor delen van Azure AD

Microsoft AppSource-en Azure Marketplace-klanten gebruiken in-product ervaringen om te zoeken in de catalogus met winkel aanbiedingen. Voor deze acties moeten klanten zich aanmelden bij het product. Azure AD-integratie biedt de volgende voor delen:

- Snellere betrokkenheid en een geoptimaliseerde klant ervaring
- Eenmalige aanmelding (SSO) voor miljoenen zakelijke gebruikers
- Consistente, aanmeldings ervaring voor toepassingen die worden gepubliceerd door verschillende partners
- Schaal bare, platformoverschrijdende verificatie voor mobiele en Cloud-apps

## <a name="offers-that-require-azure-ad"></a>Aanbiedingen waarvoor Azure AD is vereist

De verschillende [aanbiedings opties en aanbiedings typen](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) voor commerciële Marketplace hebben verschillende vereisten voor de implementatie van Azure AD. Raadpleeg de volgende tabel voor meer informatie.

| **Aanbiedings type**    | **Is Azure AD SSO vereist?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Contact opnemen | Proefversie | Test Drive | Trans |
| Virtuele machine | N.v.t. | Nee | Nee | Nee |
| Azure-apps (oplossings sjabloon)  | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Beheerde apps  | N.v.t. | N.v.t. | N.v.t. | Nee |
| SaaS  | Nee | Ja | Ja | Ja |
| Containers  | N.v.t. | N.v.t. | N.v.t. | Nee |
| Adviesservices  | Nee | N.v.t. | N.v.t. | N.v.t. |

Zie voor meer informatie over SaaS technische vereisten de [hand leiding voor SaaS-toepassingen](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)die u publiceert.

## <a name="azure-ad-integration"></a>Azure AD-integratie

- Zie [Azure Active Directory voor ontwikkel aars]( https://docs.microsoft.com/azure/active-directory/develop/)voor meer informatie over het inschakelen van eenmalige aanmelding door Azure ad te integreren in uw vermelding.
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

Als u dit nog niet hebt gedaan, 

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over Marketplace.

Als u zich wilt registreren in het partner centrum, begint u met het maken van een nieuwe aanbieding of het werken met een bestaand abonnement:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
