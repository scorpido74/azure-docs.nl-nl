---
title: Self-service aanmeldingsportal voor B2B-samenwerking - Azure AD
description: Azure Active Directory B2B-samenwerking ondersteunt uw externe bedrijfsrelaties door zakelijke partners selectief toegang te verlenen tot uw zakelijke toepassingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195790"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Selfservice voor Azure AD B2B-samenwerking aanmelden

Klanten kunnen veel doen met de ingebouwde functies die via de [Azure-portal](https://portal.azure.com) en het [Toepassingstoegangsvenster](https://myapps.microsoft.com) beschikbaar worden gesteld voor eindgebruikers. Het kan echter nodig zijn om de onboarding-workflow voor B2B-gebruikers aan te passen aan de behoeften van uw organisatie.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Azure AD-rechtenbeheer voor aanmelding door B2B-gastgebruikers

Als uitnodigende organisatie weet u mogelijk niet van tevoren wie de afzonderlijke externe medewerkers zijn die toegang tot uw resources nodig hebben. U hebt een manier nodig voor gebruikers van partnerbedrijven om zich aan te melden met beleid dat u beheert. Als u gebruikers van andere organisaties toegang wilt laten aanvragen en na goedkeuring is ingericht met gastaccounts en toegewezen aan groepen, apps en SharePoint Online-sites, u [Azure AD-rechtenbeheer](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) gebruiken om beleid te configureren dat [toegang voor externe gebruikers beheert.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B-uitnodigings-API

Organisaties kunnen de [Microsoft Graph invitation manager API](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) gebruiken om hun eigen onboarding-ervaringen te bouwen voor B2B-gastgebruikers. Wanneer u selfservice B2B-gasten aanbiedt, raden we u aan [azure AD-rechtenbeheer te](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)gebruiken. Maar als u uw eigen ervaring wilt opbouwen, u de [API voor uitnodiging maken](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) gebruiken om uw aangepaste uitnodigingse-mail automatisch rechtstreeks naar de B2B-gebruiker te sturen, bijvoorbeeld. Of uw app kan de uitnodigingRedeemUrl gebruiken die is geretourneerd in het creatieantwoord om uw eigen uitnodiging (via uw communicatiemechanisme naar keuze) aan de uitgenodigde gebruiker te maken.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Licentie voor Azure AD B2B-samenwerking](licensing-guidance.md)
* [Veelgestelde vragen over Azure Active Directory B2B-samenwerking](faq.md)
