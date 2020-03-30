---
title: Azure AD-beleid voor identiteitsbescherming
description: Identificeren van de drie beleidsregels die zijn ingeschakeld met identiteitsbescherming
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887414"
---
# <a name="identity-protection-policies"></a>Identiteitsbeveiligingbeleid

Azure Active Directory Identity Protection bevat drie standaardbeleidsregels die beheerders kunnen kiezen om in te schakelen. Deze beleidsregels omvatten beperkte aanpassingen, maar zijn van toepassing op de meeste organisaties. Met alle beleidsregels u gebruikers uitsluiten, zoals uw [account voor noodtoegang of beheerdersaccounts voor break-glass.](../users-groups-roles/directory-emergency-access.md)

![Identiteitsbeveiligingbeleid](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA-registratiebeleid

Identiteitsbeveiliging kan organisaties helpen bij het uitrollen van Azure Multi-Factor Authentication (MFA) met behulp van een conditional access-beleid dat registratie vereist bij aanmelding. Het inschakelen van dit beleid is een geweldige manier om ervoor te zorgen dat nieuwe gebruikers in uw organisatie zich op hun eerste dag hebben geregistreerd voor MFA. Multi-factor authenticatie is een van de zelfherstelmethoden voor risicogebeurtenissen binnen Identity Protection. Met zelfherstel kunnen uw gebruikers zelf actie ondernemen om het aantal helpdeskoproepen te verminderen.

Meer informatie over Azure Multi-Factor Authentication vindt u in het artikel, [How it works: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Aanmeldingsrisicobeleid

Identity Protection analyseert signalen van elke aanmelding, zowel real-time als offline, en berekent een risicoscore op basis van de waarschijnlijkheid dat de aanmelding niet door de gebruiker is uitgevoerd. Beheerders kunnen een beslissing nemen op basis van dit risicoscoresignaal om organisatorische vereisten af te dwingen. Beheerders kunnen ervoor kiezen om toegang te blokkeren, toegang toe te staan of toegang toe te staan, maar vereisen meervoudige verificatie.

Als risico's worden gedetecteerd, kunnen gebruikers multi-factor authenticatie uitvoeren om zelf te herstellen en de riskante aanmeldingsgebeurtenis te sluiten om onnodige ruis voor beheerders te voorkomen.

> [!NOTE] 
> Gebruikers moeten zich eerder hebben geregistreerd voor Azure Multi-Factor Authentication voordat ze het aanmeldingsrisicobeleid activeren.

### <a name="custom-conditional-access-policy"></a>Aangepast beleid voor voorwaardelijke toegang

Beheerders kunnen er ook voor kiezen om een aangepast beleid voor voorwaardelijke toegang te maken, inclusief aanmeldingsrisico als toewijzingsvoorwaarde. Meer informatie over voorwaardelijke toegang is te vinden in het artikel, [Wat is voorwaardelijke toegang?](../conditional-access/overview.md)

![Aanmeldingsrisicobeleid voor aangepaste voorwaardelijke toegang](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Gebruikersrisicobeleid

Identity Protection kan berekenen wat volgens hem normaal is voor het gedrag van een gebruiker en dat gebruiken om beslissingen te baseren op hun risico. Gebruikersrisico is een berekening van de waarschijnlijkheid dat een identiteit is aangetast. Beheerders kunnen een beslissing nemen op basis van dit risicoscoresignaal om organisatorische vereisten af te dwingen. Beheerders kunnen ervoor kiezen om toegang te blokkeren, toegang toe te staan of toegang toe te staan, maar vereisen een wachtwoordwijziging met [azure AD selfservice wachtwoordopnieuw instellen.](../authentication/howto-sspr-deployment.md)

Als risico's worden gedetecteerd, kunnen gebruikers zelfservicewachtwoord opnieuw instellen om zelf te herstellen en de gebeurtenis met het gebruikersrisico te sluiten om onnodige ruis voor beheerders te voorkomen.

> [!NOTE] 
> Gebruikers moeten zich eerder hebben geregistreerd voor het opnieuw instellen van selfservicewachtwoorden voordat ze het gebruikersrisicobeleid activeren.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD selfservice wachtwoord opnieuw instellen inschakelen](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication inschakelen](../authentication/howto-mfa-getstarted.md)

- [Registratiebeleid voor Azure-verificatie met meerdere factoren inschakelen](howto-identity-protection-configure-mfa-policy.md)

- [Aanmeldings- en gebruikersrisicobeleid inschakelen](howto-identity-protection-configure-risk-policies.md)
