---
title: Versies van de gebruikers stroom in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over de versies van gebruikers stromen die beschikbaar zijn in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df411ee288b9759adbb3f8f84c28e4fed05f3c4c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258095"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versies van de gebruikers stroom in Azure Active Directory B2C

Gebruikers stromen in Azure Active Directory B2C (Azure AD B2C) helpen u bij het instellen van een gemeen schappelijke [beleids](active-directory-b2c-reference-policies.md) regel voor het beschrijven van de ervaring van de klant identiteit. Deze ervaring omvat het aanmelden, aanmelden, het opnieuw instellen van wacht woorden of het bewerken van profielen. In Azure AD B2C kunt u kiezen uit een verzameling van zowel aanbevolen gebruikers stromen als voor beeld van gebruikers stromen.

Nieuwe gebruikers stromen worden toegevoegd als nieuwe versies. Wanneer gebruikers stromen stabiel worden, worden ze aanbevolen voor gebruik. Gebruikers stromen worden gemarkeerd als **Aanbevolen** als ze uitvoerig zijn getest. Gebruikers stromen worden in de preview-fase opgenomen tot ze als aanbevolen zijn gemarkeerd. Gebruik een aanbevolen gebruikers stroom voor een productie toepassing, maar kies uit andere versies om de nieuwe functionaliteit te testen zodra deze beschikbaar wordt. Gebruik geen oudere versies van aanbevolen gebruikers stromen.

>[!IMPORTANT]
> Tenzij een gebruikers stroom wordt aangeduid als **Aanbevolen**, wordt deze als *Preview*beschouwd. Gebruik alleen aanbevolen gebruikers stromen voor uw productie toepassingen.

## <a name="v1"></a>V1

| Gebruikersstroom | Aanbevolen | Description |
| --------- | ----------- | ----------- |
| Wachtwoord opnieuw instellen | Ja | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>[Vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profiel bewerken | Ja | Hiermee kan een gebruiker hun gebruikers kenmerken configureren. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li></ul> |
| Aanmelden met ROPC | Nee | Hiermee kan een gebruiker met een lokaal account zich rechtstreeks aanmelden bij systeem eigen toepassingen (er is geen browser vereist). Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li></ul> |
| Aanmelden | Nee | Hiermee kan een gebruiker zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li><li>Aanmelden blok keren</li><li>Wacht woord opnieuw instellen afdwingen</li><li>Aangemeld blijven (KMSI)</ul><br>U kunt de gebruikers interface niet aanpassen met deze gebruikers stroom. |
| Registreren | Nee | Hiermee kan een gebruiker een account maken. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li><li>[Vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registreren en aanmelden | Ja | Hiermee kan een gebruiker een account maken of zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li><li>[Vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Gebruikersstroom | Aanbevolen | Description |
| --------- | ----------- | ----------- |
| Wacht woord opnieuw instellen v 1.1 | Nee | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres (nieuwe pagina-indeling beschikbaar). Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>[Vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Gebruikersstroom | Aanbevolen | Description |
| --------- | ----------- | ----------- |
| Wachtwoord opnieuw instellen v2 | Nee | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>[Leeftijds beperking](basic-age-gating.md)</li><li>[vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profiel bewerken v2 | Ja | Hiermee kan een gebruiker hun gebruikers kenmerken configureren. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li></ul> |
| Aanmelden v2 | Nee | Hiermee kan een gebruiker zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li><li>[Leeftijds beperking](basic-age-gating.md)</li><li>Aanpassing van de aanmeldings pagina</li></ul> |
| Registreren v2 | Nee | Hiermee kan een gebruiker een account maken. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Levens duur van token](active-directory-b2c-reference-tokens.md)</li><li>Instellingen voor tokencompatibiliteit</li><li>Sessiegedrag</li><li>[Leeftijds beperking](basic-age-gating.md)</li><li>[Vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registreren en aanmelden v2 | Nee | Hiermee kan een gebruiker een account maken of zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Leeftijds beperking](basic-age-gating.md)</li><li>[Vereisten voor wachtwoord complexiteit](active-directory-b2c-reference-password-complexity.md)</li></ul> |
