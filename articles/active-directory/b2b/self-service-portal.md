---
title: Self-service-aanmeldings portal voor B2B-samen werking-Azure AD
description: Azure Active Directory B2B-samenwerking ondersteunt uw externe bedrijfsrelaties door zakelijke partners selectief toegang te verlenen tot uw zakelijke toepassingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 794a13a3f863c732d4e7ed8cedcbd73f7cbc0d0b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272110"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Self-service aanmeldportal voor Azure AD B2B-samenwerking | Microsoft Docs

Klanten kunnen veel doen met de ingebouwde functies die via de [Azure-portal](https://portal.azure.com) en het [Toepassingstoegangsvenster](https://myapps.microsoft.com) beschikbaar worden gesteld voor eindgebruikers. Het kan echter nodig zijn om de onboarding-workflow voor B2B-gebruikers aan te passen aan de behoeften van uw organisatie. Dit kunt u doen met [de uitnodigings-API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Als uitnodigende organisatie weet u misschien niet van tevoren wie de individuele externe medewerkers zijn die toegang tot uw resources nodig hebben. Gebruikers van partnerbedrijven moeten de mogelijkheid krijgen om zich aan te melden met een set van beleidsregels die u als uitnodigende organisatie beheert. Dit scenario is mogelijk via de API's. Er staat een [Voorbeeldproject op GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) dat precies deze mogelijkheid laat zien.

Dit GitHub-project laat zien hoe organisaties de API's kunnen gebruiken om een op beleid gebaseerde, self-service aanmeldmogelijkheid voor uw vertrouwde partners aan te bieden, met regels die bepalen tot welke apps zij toegang hebben. Partnergebruikers kunnen toegang krijgen tot resources wanneer ze die nodig hebben. Deze toegang kan veilig verlopen, zonder dat de uitnodigende organisatie een handmatige onboarding voor hen hoeft uit te voeren. U kunt het project eenvoudig implementeren binnen een Azure-abonnement naar uw keuze.

## <a name="as-is-code"></a>As-is-code

Deze code is beschikbaar gemaakt als voorbeeld om het gebruik van de B2B-uitnodigings-API voor Azure Active Directory te laten zien. De code moet worden aangepast door uw ontwikkelingsteam of een partner, en moet worden beoordeeld voordat u het in een productiescenario inzet.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Licenties voor Azure AD B2B-samenwerking](licensing-guidance.md)
* [Veelgestelde vragen over Azure Active Directory B2B-samenwerking](faq.md)
