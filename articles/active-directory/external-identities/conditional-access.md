---
title: Voorwaardelijke toegang voor B2B-samenwerkings gebruikers-Azure AD
description: Azure Active Directory B2B-samen werking ondersteunt multi-factor Authentication (MFA) voor selectieve toegang tot uw bedrijfs toepassingen
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
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908699"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Voorwaardelijke toegang voor B2B-samenwerkings gebruikers

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-factor Authentication voor B2B-gebruikers
Met Azure AD B2B-samen werking kunnen organisaties multi-factor Authentication (MFA)-beleid afdwingen voor B2B-gebruikers. Deze beleids regels kunnen worden afgedwongen op Tenant-, app-of individuele gebruikers niveau, op dezelfde manier als ze zijn ingeschakeld voor fulltime werk nemers en leden van de organisatie. MFA-beleids regels worden afgedwongen voor de resource organisatie.

Voorbeeld:
1. Beheerder of informatie medewerker in bedrijf A verzoekt een gebruiker van bedrijf B naar een toepassing *Foo* in bedrijf A.
2. Application *Foo* in bedrijf A is geconfigureerd om MFA te vereisen voor toegang.
3. Wanneer de gebruiker van bedrijf B probeert toegang te krijgen tot de app *Foo* in het bedrijf een Tenant, wordt deze gevraagd een MFA-uitdaging te volt ooien.
4. De gebruiker kan hun MFA instellen met bedrijf A en de optie MFA kiezen.
5. Dit scenario werkt voor elke identiteit (Azure AD of MSA, bijvoorbeeld als gebruikers in bedrijf B een verificatie uitvoeren met sociale ID)
6. Bedrijf A moet voldoende Premium Azure AD-licenties hebben die ondersteuning bieden voor MFA. De gebruiker van bedrijf B gebruikt deze licentie van bedrijf A.

De uitnodigende pacht is altijd verantwoordelijk voor MFA voor gebruikers van de partner organisatie, zelfs als de partner organisatie MFA-mogelijkheden heeft.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>MFA instellen voor gebruikers van B2B-samen werking
Als u wilt weten hoe gemakkelijk het is om MFA in te stellen voor gebruikers van B2B-samen werking, raadpleegt u de volgende video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B-gebruikers MFA-ervaring voor aanbiedings inwisseling
Bekijk de volgende animatie om de aflossings ervaring te bekijken:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA-reset voor gebruikers van B2B-samen werking
Op dit moment kan de beheerder alleen opnieuw controleren met behulp van de volgende Power shell-cmdlets voor B2B-samenwerkings gebruikers:

1. Verbinding maken met Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Alle gebruikers ophalen met methoden voor testen

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Hier volgt een voorbeeld:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Stel de MFA-methode voor een specifieke gebruiker opnieuw in, zodat de B2B-samenwerkings gebruiker opnieuw proef methoden kan instellen. Voorbeeld:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Waarom wordt MFA op de bron pacht uitgevoerd?

In de huidige release is MFA altijd in de resource pacht, om redenen van voorspel baarheid. Stel bijvoorbeeld dat een contoso-gebruiker (Sandra) wordt uitgenodigd voor fabrikam en fabrikam MFA voor B2B-gebruikers heeft ingeschakeld.

Als contoso beleid heeft ingeschakeld voor App1, maar niet App2, dan zien we mogelijk het volgende probleem als we de contoso MFA-claim in het token bekijken:

* Dag 1: een gebruiker heeft MFA in contoso en heeft toegang tot App1. vervolgens wordt er geen extra MFA-prompt weer gegeven in fabrikam.

* Dag 2: de gebruiker heeft app 2 in contoso geopend, dus nu moet u bij het openen van Fabrikam zich registreren voor MFA.

Dit proces kan verwarrend zijn en kan ertoe leiden dat het aanmelden wordt voltooid.

Ook als contoso een MFA-functionaliteit heeft, is het niet altijd het geval dat fabrikam het beleid van Contoso MFA vertrouwt.

Ten slotte werkt resource Tenant MFA ook voor Msa's-en sociale Id's en voor partner-organisaties waarop MFA niet is ingesteld.

Daarom moet de aanbeveling voor MFA voor B2B-gebruikers altijd MFA vereisen in de uitnodigende Tenant. Deze vereiste zou kunnen leiden tot dubbele MFA in sommige gevallen, maar wanneer toegang wordt verkregen tot de uitnodigende Tenant, is de ervaring voor eind gebruikers voorspelbaar: Sandra moet zich registreren voor MFA met de uitnodigende Tenant.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Op apparaten gebaseerde, op locatie gebaseerde en op Risico's gebaseerde voorwaardelijke toegang voor B2B-gebruikers

Wanneer contoso op apparaten gebaseerd beleid voor voorwaardelijke toegang voor hun bedrijfs gegevens inschakelt, wordt de toegang verhinderd vanaf apparaten die niet worden beheerd door Contoso en niet compatibel zijn met het contoso-apparaatbeleid.

Als het apparaat van de B2B-gebruiker niet wordt beheerd door contoso, wordt de toegang van B2B-gebruikers van de partner organisaties geblokkeerd in welke context deze beleids regels worden afgedwongen. Contoso kan echter uitsluitings lijsten maken met specifieke partner gebruikers om ze uit te sluiten van het beleid voor voorwaardelijke toegang op basis van apparaten.

#### <a name="mobile-application-management-policies-for-b2b"></a>Mobile Application Management-beleid voor B2B

App-beveiligings beleid voor voorwaardelijke toegang kan niet worden toegepast op B2B-gebruikers omdat de uitnodigende organisatie geen zicht baarheid heeft in de thuis organisatie van de B2B-gebruiker.

#### <a name="location-based-conditional-access-for-b2b"></a>Voorwaardelijke toegang op basis van locaties voor B2B

Op locatie gebaseerd beleid voor voorwaardelijke toegang kan worden afgedwongen voor B2B-gebruikers als de uitgenodigde organisatie een vertrouwd IP-adres bereik kan maken dat hun partner organisaties definieert.

#### <a name="risk-based-conditional-access-for-b2b"></a>Voorwaardelijke toegang op basis van Risico's voor B2B

Op dit moment kunnen aanmeldings beleid op basis van Risico's niet worden toegepast op B2B-gebruikers omdat de risico-evaluatie wordt uitgevoerd in de thuis organisatie van de B2B-gebruiker.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Licentie voor Azure AD B2B-samenwerking](licensing-guidance.md)
* [Veelgestelde vragen over Azure Active Directory B2B-samenwerking](faq.md)
