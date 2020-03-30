---
title: Voorwaardelijke toegang voor B2B-samenwerkingsgebruikers - Azure AD
description: Azure Active Directory B2B-samenwerking ondersteunt multi-factor authenticatie (MFA) voor selectieve toegang tot uw bedrijfstoepassingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273001"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Voorwaardelijke toegang voor Gebruikers van B2B-samenwerkingsverbanden

## <a name="multi-factor-authentication-for-b2b-users"></a>Meervoudige verificatie voor B2B-gebruikers
Met Azure AD B2B-samenwerking kunnen organisaties mfa-beleid (multi-factor authentication) afdwingen voor B2B-gebruikers. Dit beleid kan worden afgedwongen op het niveau van de tenant, app of individuele gebruiker, op dezelfde manier als voor voltijdse werknemers en leden van de organisatie. MFA-beleid wordt afgedwongen bij de resourceorganisatie.

Voorbeeld:
1. Beheerder of informatiemedewerker in bedrijf A nodigt gebruiker van bedrijf B uit voor een applicatie *Foo* in bedrijf A.
2. Toepassing *Foo* in bedrijf A is geconfigureerd om MFA voor toegang te vereisen.
3. Wanneer de gebruiker van bedrijf B probeert toegang te krijgen tot app *Foo* in het bedrijf A-tenant, wordt hem gevraagd een MFA-uitdaging te voltooien.
4. De gebruiker kan zijn MFA instellen met bedrijf A en kiest zijn MFA-optie.
5. Dit scenario werkt voor elke identiteit (Azure AD of MSA, bijvoorbeeld als gebruikers in bedrijf B zich verifiëren met behulp van een sociale id)
6. Bedrijf A moet over voldoende Premium Azure AD-licenties beschikken die MFA ondersteunen. De gebruiker van bedrijf B verbruikt deze licentie van bedrijf A.

De uitnodigende huurovereenkomst is altijd verantwoordelijk voor MFA voor gebruikers uit de partnerorganisatie, zelfs als de partnerorganisatie beschikt over MFA-mogelijkheden.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>MFA instellen voor B2B-samenwerkingsgebruikers
Zie in de volgende video hoe eenvoudig het is om MFA in te stellen voor B2B-samenwerkingsgebruikers:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B-gebruikers MFA-ervaring voor het inwisselen van aanbiedingen
Bekijk de volgende animatie om de inwisselervaring te bekijken:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA-reset voor B2B-samenwerkingsgebruikers
Momenteel kan de beheerder b2b-samenwerkingsgebruikers verplichten om alleen de volgende PowerShell-cmdlets opnieuw te bewijzen:

1. Verbinding maken met Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Alle gebruikers met een bewijs-upmethode krijgen

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Hier volgt een voorbeeld:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Stel de MFA-methode opnieuw in voor een specifieke gebruiker om de B2B-samenwerkingsgebruiker te verplichten opnieuw testmethoden in te stellen. Voorbeeld:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Waarom voeren we MFA uit op de pachtbasis voor resources?

In de huidige release, MFA is altijd in de resource huurovereenkomst, om redenen van voorspelbaarheid. Stel dat een Contoso-gebruiker (Sally) wordt uitgenodigd voor Fabrikam en Fabrikam MFA heeft ingeschakeld voor B2B-gebruikers.

Als Contoso mfa-beleid heeft ingeschakeld voor App1 maar niet App2, dan zien we mogelijk het volgende probleem als we kijken naar de Contoso MFA-claim in het token:

* Dag 1: Een gebruiker heeft MFA in Contoso en heeft toegang tot App1, waarna er geen extra MFA-prompt wordt weergegeven in Fabrikam.

* Dag 2: De gebruiker heeft toegang tot App 2 in Contoso, dus nu bij de toegang tot Fabrikam, moeten ze zich daar registreren voor MFA.

Dit proces kan verwarrend zijn en kan leiden tot een daling van de aanmeldingsvoltooiingen.

Bovendien, zelfs als Contoso heeft MFA vermogen, is het niet altijd het geval de Fabrikam zou vertrouwen op de Contoso MFA beleid.

Ten slotte werkt resource tenant MFA ook voor MSA's en sociale ID's en voor partnerorganisaties die geen MFA hebben opgezet.

Daarom is de aanbeveling voor MFA voor B2B-gebruikers is om altijd MFA in de uitnodigende tenant te vereisen. Deze vereiste kan in sommige gevallen leiden tot dubbele MFA, maar wanneer toegang tot de uitnodigende tenant, de ervaring van eindgebruikers voorspelbaar is: Sally moet zich registreren voor MFA bij de uitnodigende tenant.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Apparaatgebaseerde, locatiegebaseerde en op risico's gebaseerde voorwaardelijke toegang voor B2B-gebruikers

Wanneer Contoso apparaatgebaseerde voorwaardelijke toegangsbeleidsregels voor hun bedrijfsgegevens inschakelt, wordt de toegang verhinderd voor apparaten die niet door Contoso worden beheerd en die niet voldoen aan het beleid van het Contoso-apparaat.

Als het apparaat van de B2B-gebruiker niet wordt beheerd door Contoso, wordt de toegang tot B2B-gebruikers van de partnerorganisaties geblokkeerd in welke context dan ook deze beleidsregels worden afgedwongen. Contoso kan echter uitsluitingslijsten maken met specifieke partnergebruikers om ze uit te sluiten van het op apparaten gebaseerde conditional access-beleid.

#### <a name="mobile-application-management-policies-for-b2b"></a>Beleid voor mobiel applicatiebeheer voor B2B

Beveiligingsbeleid voor voorwaardelijke toegang-apps kan niet worden toegepast op B2B-gebruikers omdat de uitnodigende organisatie geen inzicht heeft in de thuisorganisatie van de B2B-gebruiker.

#### <a name="location-based-conditional-access-for-b2b"></a>Afhankelijke voorwaardelijke toegang op locatie voor B2B

Locatiegebaseerdbeleid voor voorwaardelijke toegang kan worden afgedwongen voor B2B-gebruikers als de uitnodigende organisatie in staat is een vertrouwd IP-adresbereik te maken dat hun partnerorganisaties definieert.

#### <a name="risk-based-conditional-access-for-b2b"></a>Op risico gebaseerde voorwaardelijke toegang voor B2B

Momenteel kan op risico's gebaseerde aanmeldingsbeleid niet worden toegepast op B2B-gebruikers omdat de risicobeoordeling wordt uitgevoerd in de thuisorganisatie van de B2B-gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Licentie voor Azure AD B2B-samenwerking](licensing-guidance.md)
* [Veelgestelde vragen over Azure Active Directory B2B-samenwerking](faq.md)
