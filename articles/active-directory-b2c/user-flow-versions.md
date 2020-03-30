---
title: Gebruikersstroomversies in Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over de versies van gebruikersstromen die beschikbaar zijn in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185617"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Gebruikersstroomversies in Azure Active Directory B2C

Gebruikersstromen in Azure Active Directory B2C (Azure AD B2C) helpen u bij het instellen van algemene [beleidsregels](user-flow-overview.md) waarin de identiteitservaringen van klanten volledig worden beschreven. Deze ervaringen omvatten aanmelding, aanmelding, wachtwoordreset of profielbewerking. In Azure AD B2C u kiezen uit een verzameling aanbevolen gebruikersstromen en gebruikersstromen bekijken.

Nieuwe gebruikersstromen worden toegevoegd als nieuwe versies. Naarmate gebruikersstromen stabiel worden, worden ze aanbevolen voor gebruik. Gebruikersstromen worden gemarkeerd als **Aanbevolen** als ze grondig zijn getest. Gebruikersstromen worden in preview in aanmerking genomen totdat deze zijn gemarkeerd als aanbevolen. Gebruik een aanbevolen gebruikersstroom voor elke productietoepassing, maar kies uit andere versies om nieuwe functionaliteit te testen zodra deze beschikbaar is. U mag geen oudere versies van aanbevolen gebruikersstromen gebruiken.

>[!IMPORTANT]
> Tenzij een gebruikersstroom wordt geïdentificeerd als **aanbevolen,** wordt deze beschouwd als in *preview*. U moet alleen aanbevolen gebruikersstromen gebruiken voor uw productietoepassingen.

## <a name="v1"></a>V1

| Gebruikersstroom | Aanbevolen | Beschrijving |
| --------- | ----------- | ----------- |
| Wachtwoord opnieuw instellen | Ja | Hiermee kan een gebruiker een nieuw wachtwoord kiezen nadat hij zijn e-mail heeft geverifieerd. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>[Vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul> |
| Profielbewerking | Ja | Hiermee kan een gebruiker zijn gebruikerskenmerken configureren. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li></ul> |
| Aanmelden met ROPC | Nee | Hiermee kan een gebruiker met een lokaal account zich rechtstreeks aanmelden in native toepassingen (geen browser vereist). Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li></ul> |
| Aanmelden | Nee | Hiermee kan een gebruiker zich aanmelden bij zijn/haar account. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li><li>Aanmelden blokkeren</li><li>Wachtwoord opnieuw instellen forceren</li><li>Houd me ingelogd (KMSI)</ul><br>U de gebruikersinterface met deze gebruikersstroom niet aanpassen. |
| Aanmelden | Nee | Hiermee kan een gebruiker een account maken. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li><li>[Vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul> |
| Registreren en aanmelden | Ja | Hiermee kan een gebruiker een account maken of zich aanmelden bij zijn/haar account. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li><li>[Vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Gebruikersstroom | Aanbevolen | Beschrijving |
| --------- | ----------- | ----------- |
| Wachtwoordreset v1.1 | Nee | Hiermee kan een gebruiker een nieuw wachtwoord kiezen nadat hij zijn e-mail heeft geverifieerd (nieuwe pagina-indeling beschikbaar). Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>[Vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Gebruikersstroom | Aanbevolen | Beschrijving |
| --------- | ----------- | ----------- |
| Wachtwoordreset v2 | Nee | Hiermee kan een gebruiker een nieuw wachtwoord kiezen nadat hij zijn e-mail heeft geverifieerd. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>[vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul> |
| Profielbewerking v2 | Ja | Hiermee kan een gebruiker zijn gebruikerskenmerken configureren. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li></ul> |
| Inloggen v2 | Nee | Hiermee kan een gebruiker zich aanmelden bij zijn/haar account. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>Aanpassing aan de aanmeldingspagina</li></ul> |
| Aanmelden v2 | Nee | Hiermee kan een gebruiker een account maken. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token levensduur](tokens-overview.md)</li><li>Compatibiliteitsinstellingen voor token's</li><li>Sessiegedrag</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>[Vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul> |
| Meld je aan en meld je aan v2 | Nee | Hiermee kan een gebruiker een account maken of zich aanmelden voor zijn/haar account. Met deze gebruikersstroom u het als volgt configureren: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>[Vereisten voor complexiteit van wachtwoorden](user-flow-password-complexity.md)</li></ul> |
