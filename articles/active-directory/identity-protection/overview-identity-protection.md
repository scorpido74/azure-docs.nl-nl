---
title: Wat is Azure Active Directory Identity Protection
description: Het risico met Azure AD Identity Protection detecteren, herstellen, onderzoeken en analyseren
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2b1d9748b243dcc2104ce7b8e0e8735a7b7276f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79497670"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Wat is Azure Active Directory Identity Protection

Identiteits beveiliging is een hulp programma waarmee organisaties drie belang rijke taken kunnen uitvoeren:

- Automatiseer de detectie en het herstel van Risico's op basis van de identiteit.
- Onderzoek Risico's met behulp van gegevens in de portal.
- Gegevens van risico detectie exporteren naar hulpprogram ma's van derden voor verdere analyse.

Identiteits bescherming maakt gebruik van de informatie die micro soft heeft verkregen van hun positie in organisaties met Azure AD, de consument ruimte met micro soft-accounts en in games met Xbox om uw gebruikers te beschermen. Micro soft analyseert 6.500.000.000.000 signalen per dag om klanten te identificeren en beveiligen tegen bedreigingen.

De signalen die worden gegenereerd door en worden gevoederd tot identiteits beveiliging, kunnen verder worden gevoederd in hulpprogram ma's zoals voorwaardelijke toegang om toegang te krijgen tot beslissingen, of worden teruggedraaid naar een SIEM-hulp programma (Security Information and Event Management) voor verdere onderzoek op basis van het afgedwongen beleid van uw organisatie.

## <a name="why-is-automation-important"></a>Waarom is automatisering belang rijk?

In zijn [blog post in oktober 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, die het micro soft-team voor identiteits beveiliging en-bescherming aanleidt, wordt uitgelegd waarom automatisering zo belang rijk is voor het verwerken van het volume gebeurtenissen:

> Elke dag bieden onze machine learning-en heuristische systemen risico scores voor 18.000.000.000 aanmeldings pogingen voor meer dan 800.000.000 afzonderlijke accounts, 300.000.000 van discernibly die worden uitgevoerd door aanvallers (entiteiten zoals criminele actoren, hackers).
>
> Op Ignite vorig jaar spoke ik op onze identiteits systemen over de belangrijkste 3-aanvallen. Dit is het recente volume van deze aanvallen
>   
>   - **Inbreuk opnieuw afspelen**: 4.6 bn-aanvallen gedetecteerd in mei 2018
>   - **Wachtwoord spuit**: 350K in april 2018
>   - **Phishing**: dit is moeilijk te kwantificeren, maar we hebben 23M-risico gebeurtenissen in maart 2018 gezien, waarvan veel phishing gerelateerd zijn

## <a name="risk-detection-and-remediation"></a>Risico detectie en herstel

Identiteits beveiliging identificeert Risico's in de volgende classificaties:

| Type risico detectie | Beschrijving |
| --- | --- |
| Ongewoone reis | Meld u vanaf een ongewoone locatie aan op basis van de recente aanmeldingen van de gebruiker. |
| Anoniem IP-adres | Meld u aan vanaf een anoniem IP-adres (bijvoorbeeld: Tor browser, Anonymizer Vpn's). |
| Onbekende aanmeldings eigenschappen | Meld u aan met de eigenschappen die niet recent voor de gegeven gebruiker zijn weer gegeven. |
| Gekoppeld IP-adres van malware | Meld u aan bij een IP-adres dat is gekoppeld aan malware |
| Gelekte referenties | Met deze risico detectie wordt aangegeven dat de geldige referenties van de gebruiker zijn gelekt |
| Azure AD Threat Intelligence | De interne en externe Threat Intelligence-bronnen van micro soft hebben een bekend aanvals patroon geïdentificeerd |

Meer informatie over deze Risico's en hoe/wanneer deze worden berekend, vindt u in het artikel, [wat risico](concept-identity-protection-risks.md)loopt.

De risico signalen kunnen herstel pogingen doen, zoals het vereisen van gebruikers om: Azure Multi-Factor Authentication uit te voeren, hun wacht woord opnieuw in te stellen met selfservice voor wachtwoord herstel of door te blok keren totdat een beheerder actie onderneemt.

## <a name="risk-investigation"></a>Risico onderzoek

Beheerders kunnen detecties bekijken en zo nodig hand matig actie ondernemen. Er zijn drie belang rijke rapporten die door beheerders worden gebruikt voor het onderzoeken van identiteits beveiliging:

- Risk ante gebruikers
- Riskante aanmeldingen
- Risicodetectie

Meer informatie vindt u in het artikel, [How to: risico onderzoeken](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Risico gegevens exporteren

Gegevens van identiteits beveiliging kunnen worden geëxporteerd naar andere hulpprogram ma's voor archivering en verder onderzoek en samen hang. Met de op Microsoft Graph gebaseerde Api's kunnen organisaties deze gegevens verzamelen voor verdere verwerking in een hulp programma zoals hun SIEM. Informatie over het openen van de API voor identiteits beveiliging vindt u in het artikel, aan de [slag met Azure Active Directory Identity Protection en Microsoft Graph](howto-identity-protection-graph-api.md)

Informatie over het integreren van gegevens over identiteits beveiliging met Azure Sentinel vindt u in het artikel [Connecting data from Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Machtigingen

Voor identiteits beveiliging moeten gebruikers een beveiligings lezer, beveiligings operator, beveiligings beheerder, globale lezer of globale beheerder zijn om toegang te krijgen.

| Rol | Kan doen | Kan niet |
| --- | --- | --- |
| Globale beheerder | Volledige toegang tot identiteits beveiliging |   |
| Beveiligingsbeheerder | Volledige toegang tot identiteits beveiliging | Wacht woord opnieuw instellen voor een gebruiker |
| Beveiligings operator | Alle rapporten van identiteits beveiliging en overzicht weer geven <br><br> Gebruikers risico negeren, aanmeld aanmelding bevestigen, inbreuk bevestigen | Beleid configureren of wijzigen <br><br> Wacht woord opnieuw instellen voor een gebruiker <br><br> Waarschuwingen configureren |
| Beveiligingslezer | Alle rapporten van identiteits beveiliging en overzicht weer geven | Beleid configureren of wijzigen <br><br> Wacht woord opnieuw instellen voor een gebruiker <br><br> Waarschuwingen configureren <br><br> Feedback geven over detecties |

Beheerders van voorwaardelijke toegang kunnen ook beleids regels maken die als voor waarde worden opgenomen in het geval van een bepaalde factor, meer informatie vindt u in het artikel [voorwaardelijke toegang: voor waarden](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Mogelijkheid | Details | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/gratis |
| --- | --- | --- | --- | --- |
| Risico beleid | Beleid voor gebruikers risico's (via identiteits beveiliging) | Ja | Nee | Nee |
| Risico beleid | Beleid voor aanmeldings risico's (via identiteits beveiliging of voorwaardelijke toegang) | Ja | Nee | Nee |
| Beveiligingsrapporten | Overzicht | Ja | Nee | Nee |
| Beveiligingsrapporten | Risk ante gebruikers | Volledige toegang | Beperkte informatie | Beperkte informatie |
| Beveiligingsrapporten | Riskante aanmeldingen | Volledige toegang | Beperkte informatie | Beperkte informatie |
| Beveiligingsrapporten | Risicodetectie | Volledige toegang | Beperkte informatie | Nee |
| Meldingen | Gebruikers met risico waarschuwingen gedetecteerd | Ja | Nee | Nee |
| Meldingen | Wekelijkse samen vatting | Ja | Nee | Nee |
| | MFA-registratie beleid | Ja | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)

- [Wat is risico](concept-identity-protection-risks.md)

- [Beleids regels beschikbaar om Risico's te beperken](concept-identity-protection-policies.md)
