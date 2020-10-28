---
title: Self-service-aanmeldings portal voor B2B-samen werking-Azure AD
description: Meer informatie over het aanpassen van de voorbereidings werk stroom voor Azure Active Directory B2B-gebruikers die voldoen aan de behoeften van uw organisatie.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9cd421224ad134b4df853b06b3bc34333a0613
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896103"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Self-service voor Azure AD B2B-samenwerkings aanmelding

Klanten kunnen veel doen met de ingebouwde functies die via de [Azure-portal](https://portal.azure.com) en het [Toepassingstoegangsvenster](https://myapps.microsoft.com) beschikbaar worden gesteld voor eindgebruikers. Het kan echter nodig zijn om de onboarding-workflow voor B2B-gebruikers aan te passen aan de behoeften van uw organisatie.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Azure AD-rechts beheer voor B2B gast gebruikers registratie

Als uitnodigende organisatie weet u mogelijk niet vooraf dat de afzonderlijke externe deel nemers toegang tot uw resources nodig hebben. U hebt een manier nodig waarmee gebruikers van partner bedrijven zich kunnen aanmelden met beleids regels die u beheert. Als u gebruikers van andere organisaties in staat wilt stellen om toegang aan te vragen, en op basis van de goed keuring is ingericht met gast accounts en toegewezen aan groepen, apps en share point online-sites, kunt u het [beheer van rechten van Azure AD](../governance/entitlement-management-overview.md) gebruiken voor het configureren van beleids regels waarmee de [toegang voor externe gebruikers wordt beheerd](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory-API voor B2B-uitnodiging

Organisaties kunnen de [API voor Microsoft Graph uitnodigingen](/graph/api/resources/invitation?view=graph-rest-1.0) gebruiken om hun eigen voorbereidings ervaring te bouwen voor B2B-gast gebruikers. Wanneer u de aanmelding van selfservice voor B2B-gast gebruikers wilt aanbieden, kunt u het beste [Azure AD-beheer](../governance/entitlement-management-overview.md)gebruiken. Maar als u uw eigen ervaring wilt maken, kunt u de API voor het maken van een [uitnodiging](/graph/api/invitation-post?tabs=http&view=graph-rest-1.0) gebruiken om uw aangepaste uitnodigings-e-mail automatisch rechtstreeks naar de B2B-gebruiker te verzenden. Of uw app kan gebruikmaken van de inviteRedeemUrl die is geretourneerd in de reactie van het maken van uw eigen uitnodiging (via uw communicatie mechanisme) voor de uitgenodigde gebruiker.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Prijzen van externe identiteiten](external-identities-pricing.md)
* [Veelgestelde vragen over Azure Active Directory B2B-samenwerking](faq.md)