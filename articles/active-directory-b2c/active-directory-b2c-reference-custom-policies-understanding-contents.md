---
title: Onderdelen van het aangepaste beleids Starter Pack
titleSuffix: Azure AD B2C
description: Een overzicht van de beleids regels in het Azure Active Directory B2C aangepast beleids Starter Pack.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7d49bd9af80b1bb9bd86466269b14ba0a47181e0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948178"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Meer informatie over het aangepaste beleid van de Azure AD B2C aangepast beleids Starter Pack

In deze sectie vindt u alle kern elementen van het B2C_1A_base-beleid dat bij het **Starter Pack** wordt geleverd en die worden gebruikt voor het ontwerpen van uw eigen beleid via de overname van het *beleid voor B2C_1A_base_extensions*.

Daarom is het met name gericht op de reeds gedefinieerde claim typen, claim transformaties, inhouds definities, claim providers met hun technische profiel (en) en de kern gebruikers trajecten.

> [!IMPORTANT]
> Micro soft biedt geen enkele expliciete of impliciete garantie met betrekking tot de informatie die hieronder wordt verstrekt. Wijzigingen kunnen op elk gewenst moment worden geïntroduceerd, vóór de GA-tijd, om te gaan, of na.

Zowel uw eigen beleid als het B2C_1A_base_extensions-beleid kan deze definities overschrijven en dit bovenliggende beleid uitbreiden door de benodigde extra beleids regels op te geven.

De kern elementen van het *B2C_1A_base-beleid* zijn claim typen, claim transformaties en inhouds definities. Deze elementen kunnen vatbaar zijn voor verwijzingen in uw eigen beleid en in het *B2C_1A_base_extensions-beleid*.

## <a name="claims-schemas"></a>Claim schema's

Deze claim schema's zijn onderverdeeld in drie secties:

1.  Een eerste sectie met een lijst met de minimale claims die vereist zijn voor het goed functioneren van de gebruikers Reiss.
2.  Een tweede sectie met een lijst met de claims die vereist zijn voor query reeks parameters en andere speciale para meters die moeten worden door gegeven aan andere claim providers, met name login.microsoftonline.com voor authenticatie. **Wijzig deze claims niet**.
3.  En uiteindelijk een derde sectie met een lijst met aanvullende, optionele claims die kunnen worden verzameld van de gebruiker, opgeslagen in de Directory en verzonden in tokens tijdens het aanmelden. Het nieuwe claim type dat moet worden verzameld van de gebruiker en/of verzonden in het token kan in deze sectie worden toegevoegd.

> [!IMPORTANT]
> Het claim schema bevat beperkingen voor bepaalde claims, zoals wacht woorden en gebruikers namen. Het beleid Trust Framework (TF) behandelt Azure AD als een andere claim provider en alle beperkingen worden in het aangepaste beleid gemodelleerd. Een beleid kan worden aangepast om meer beperkingen toe te voegen of om een andere claim provider te gebruiken voor referentie opslag die eigen beperkingen heeft.

De beschik bare claim typen worden hieronder weer gegeven.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Claims die vereist zijn voor de gebruikers ritten

De volgende claims zijn vereist voor een goede werking van gebruikers trajecten:

| Claim type | Beschrijving |
|-------------|-------------|
| *Naam* | Gebruikersnaam |
| *signInName* | Aanmeldings naam |
| *tenantId* | De Tenant-id (ID) van het gebruikers object in Azure AD B2C |
| *objectId* | De object-id (ID) van het gebruikers object in Azure AD B2C |
| *Wachtwoord* | Wachtwoord |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Wachtwoord beleid dat door Azure AD B2C wordt gebruikt om de wachtwoord sterkte, verval datum, enzovoort te bepalen. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Telefoonnummer van gebruiker |
| *Verified.strongAuthenticationPhoneNumber* | |
| *E-mail* | E-mail adres dat kan worden gebruikt om contact op te nemen met de gebruiker |
| *signInNamesInfo.emailAddress* | Het e-mail adres dat de gebruiker kan gebruiken om zich aan te melden |
| *otherMails* | E-mail adressen die kunnen worden gebruikt om contact op te nemen met de gebruiker |
| *userPrincipalName* | Gebruikers naam die is opgeslagen in de Azure AD B2C |
| *upnUserName* | Gebruikers naam voor het maken van user principal name |
| *mailNickName* | De e-mail naam van de gebruiker die is opgeslagen in de Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Claim waarmee wordt opgegeven of kenmerken van de gebruiker zijn verzameld |
| *executed-PhoneFactor-Input* | Claim waarmee wordt opgegeven of een nieuw telefoon nummer van de gebruiker is verzameld |
| *authenticationSource* | Hiermee geeft u op of de gebruiker is geverifieerd bij sociale ID-provider, login.microsoftonline.com of lokaal account |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Claims vereist voor query reeks parameters en andere speciale para meters

De volgende claims zijn vereist voor het door geven van speciale para meters (inclusief enkele query reeks parameters) aan andere claim providers:

| Claim type | Beschrijving |
|-------------|-------------|
| *nux* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *nca* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *verschijnt* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *mkt* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *lc* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *grant_type* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *ligt* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *client_id* | Speciale para meter door gegeven voor verificatie van lokale account bij login.microsoftonline.com |
| *objectIdFromSession* | De para meter die wordt verschaft door de standaard provider voor sessie beheer om aan te geven dat de object-ID is opgehaald uit een SSO-sessie |
| *isActiveMFASession* | De para meter die wordt verschaft door de MFA-sessie beheer om aan te geven dat de gebruiker een actieve MFA-sessie heeft |

### <a name="additional-optional-claims-that-can-be-collected"></a>Aanvullende (optionele) claims die kunnen worden verzameld

De volgende claims zijn aanvullende claims die kunnen worden verzameld van de gebruikers, opgeslagen in de Directory en verzonden in het token. Zoals eerder beschreven, kunnen er extra claims aan deze lijst worden toegevoegd.

| Claim type | Beschrijving |
|-------------|-------------|
| *givenName* | De opgegeven naam van de gebruiker (ook wel bekend als voor naam) |
| *roep* | De voor naam van de gebruiker (ook bekend als familie naam of achternaam) |
| *Extension_picture* | Afbeelding van de gebruiker van sociale |

## <a name="claim-transformations"></a>Claim transformaties

De beschik bare claim transformaties worden hieronder weer gegeven.

| Claim transformatie | Beschrijving |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Inhouds definities

In deze sectie worden de inhouds definities beschreven die al zijn gedeclareerd in het *B2C_1A_base* -beleid. Deze inhouds definities zijn vatbaar voor verwijzingen, overschreven en/of uitgebreid, indien nodig in uw eigen beleid en in het *B2C_1A_base_extensions* -beleid.

| Claimprovider | Beschrijving |
|-----------------|-------------|
| *Facebook* | |
| *Lokaal account aanmelden* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Automatisch bevestigd* | |
| *Lokaal account* | |
| *Sessie beheer* | |
| *Trustframework Policy engine* | |
| *TechnicalProfiles* | |
| *Token Uitgever* | |

## <a name="technical-profiles"></a>Technische profielen

In deze sectie ziet u de technische profielen die al zijn gedeclareerd per claim provider in het *B2C_1A_base* -beleid. Deze technische profielen zijn vatbaar voor verdere verwijzingen, overschreven en/of uitgebreid naar behoefte in uw eigen beleid en in het *B2C_1A_base_extensions* -beleid.

### <a name="technical-profiles-for-facebook"></a>Technische profielen voor Facebook

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Technische profielen voor aanmelden met een lokaal account

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Technische profielen voor telefoon factor

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Technische profielen voor Azure Active Directory

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *AAD-Common* | Technisch profiel opgenomen in de andere AAD-xxx-technische profielen |
| *AAD-UserWriteUsingAlternativeSecurityId* | Technisch profiel voor sociale aanmeldingen |
| *AAD-UserReadUsingAlternativeSecurityId* | Technisch profiel voor sociale aanmeldingen |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Technisch profiel voor sociale aanmeldingen |
| *AAD-UserWritePasswordUsingLogonEmail* | Technisch profiel voor lokale accounts |
| *AAD-UserReadUsingEmailAddress* | Technisch profiel voor lokale accounts |
| *AAD-UserWriteProfileUsingObjectId* | Technisch profiel voor het bijwerken van de gebruikers record met objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Technisch profiel voor het bijwerken van de gebruikers record met objectId |
| *AAD-UserWritePasswordUsingObjectId* | Technisch profiel voor het bijwerken van de gebruikers record met objectId |
| *AAD-UserReadUsingObjectId* | Technisch profiel wordt gebruikt voor het lezen van gegevens na de verificatie van de gebruiker |

### <a name="technical-profiles-for-self-asserted"></a>Technische profielen voor zelf bevestiging

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Technische profielen voor lokaal account

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Technische profielen voor sessie beheer

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *SM-Nooperation* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Profiel naam wordt gebruikt om AAD-sessie te dubbel zinnigheid tussen registreren en aanmelden |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Technische profielen voor de beleids engine van het vertrouwens raamwerk

Er zijn momenteel geen technische profielen gedefinieerd voor de claim provider **Trustframework Policy engine TechnicalProfiles** .

### <a name="technical-profiles-for-token-issuer"></a>Technische profielen voor de token Uitgever

| Technisch profiel | Beschrijving |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Gebruikersbelevingen

In deze sectie ziet u de gebruikers trajecten die al zijn gedeclareerd in het *B2C_1A_base* -beleid. Deze gebruikers ritten zijn vatbaar voor verdere verwijzingen, overschreven en/of uitgebreid naar behoefte in uw eigen beleid en in het *B2C_1A_base_extensions* -beleid.

| Gebruikers traject | Beschrijving |
|--------------|-------------|
| *Registratie* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
