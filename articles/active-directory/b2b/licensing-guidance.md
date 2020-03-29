---
title: Richtlijnen voor B2B-samenwerkingslicenties - Azure Active Directory | Microsoft Documenten
description: Azure Active Directory B2B-samenwerking vereist geen betaalde Azure AD-licenties, maar u ook betaalde functies krijgen voor B2B-gastgebruikers
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868844"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Richtlijnen voor azure Active Directory B2B-samenwerkingslicenties

Met B2B-samenwerking (Azure Active Directory) business-to-business (Business-to-Business) u externe gebruikers (of gastgebruikers) uitnodigen om uw betaalde Azure AD-services te gebruiken. Sommige functies zijn gratis, maar voor betaalde Azure AD-functies u maximaal vijf gastgebruikers uitnodigen voor elke Azure AD-editielicentie die u bezit voor een werknemer of een niet-gastgebruiker in uw tenant.

> [!NOTE]
> Raadpleeg [de prijzen van Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) voor meer informatie over azure AD-prijzen en B2B-samenwerkingsfuncties.

B2B gastgebruiker licenties wordt automatisch berekend en gerapporteerd op basis van de 1:5 ratio. 

Bovendien kunnen gastgebruikers gratis Azure AD-functies gebruiken zonder extra licentievereisten. Gastgebruikers hebben toegang tot gratis Azure AD-functies, zelfs als u geen betaalde Azure AD-licenties hebt. 

## <a name="examples-calculating-guest-user-licenses"></a>Voorbeelden: Gastgebruikerslicenties berekenen
Zodra u bepaalt hoeveel gastgebruikers toegang nodig hebben tot uw betaalde Azure AD-services, moet u ervoor zorgen dat u over voldoende door Azure AD betaalde licenties beschikt om gastgebruikers te dekken in de vereiste 1:5-verhouding. Hier volgen enkele voorbeelden:

- U wilt 100 gastgebruikers uitnodigen voor uw Azure AD-apps of -services en toegangsbeheer en -inrichting bieden. Voor 50 van die gastgebruikers wilt u ook MFA en voorwaardelijke toegang nodig hebben, dus voor die functies hebt u 10 Azure AD Premium P1-licenties nodig. Als u van plan bent om functies voor identiteitsbescherming te gebruiken bij uw gastgebruikers, hebt u Azure AD Premium P2-licenties in dezelfde verhouding van 1:5 nodig om de gastgebruikers te dekken.
- U wilt 60 gastgebruikers uitnodigen die allemaal MFA nodig hebben, dus u moet ten minste 12 Azure AD Premium P1-licenties hebben. U hebt 10 werknemers met Azure AD Premium P1-licenties, waarmee maximaal 50 gastgebruikers onder de 1:5-licentieratio kunnen vallen. U moet twee extra Premium P1-licenties aanschaffen om 10 extra gastgebruikers te dekken.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende bronnen voor Azure AD B2B-samenwerking:

* [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/)
* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Veelgestelde vragen over Azure Active Directory B2B-samenwerking](faq.md)
