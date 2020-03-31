---
title: Wat is Azure Active Directory Identity Protection
description: Risico's detecteren, herstellen, onderzoeken en analyseren met Azure AD Identity Protection
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79497670"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Wat is Azure Active Directory Identity Protection

Identiteitsbescherming is een hulpmiddel waarmee organisaties drie belangrijke taken kunnen uitvoeren:

- Automatiseer de detectie en herstel van identiteitsrisico's.
- Onderzoek risico's met behulp van gegevens in het portaal.
- Export risicodetectiegegevens naar externe nutsbedrijven voor verdere analyse.

Identiteitsbescherming maakt gebruik van de learnings die Microsoft heeft verworven vanuit hun positie in organisaties met Azure AD, de consumentenruimte met Microsoft-accounts en in gaming met Xbox om uw gebruikers te beschermen. Microsoft analyseert 6,5 biljoen signalen per dag om klanten te identificeren en te beschermen tegen bedreigingen.

De signalen die worden gegenereerd door en worden gevoed aan identiteitsbescherming, kunnen verder worden ingevoerd in tools zoals voorwaardelijke toegang om toegangsbeslissingen te nemen, of terugte voeren naar een SIEM-tool (Security Information and Event Management) voor verder onderzoek op basis van de afgedwongen beleid.

## <a name="why-is-automation-important"></a>Waarom is automatisering belangrijk?

In zijn [blogpost in oktober 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) legt Alex Weinert, die het Team Identiteitsbeveiliging en Bescherming van Microsoft leidt, uit waarom automatisering zo belangrijk is bij het omgaan met het aantal gebeurtenissen:

> Elke dag bieden onze machine learning- en heuristische systemen risicoscores voor 18 miljard inlogpogingen voor meer dan 800 miljoen verschillende accounts, waarvan 300 miljoen merkbaar worden gedaan door tegenstanders (entiteiten zoals: criminele actoren, hackers).
>
> Op Ignite vorig jaar, sprak ik over de top 3 aanvallen op onze identiteitssystemen. Hier is het recente volume van deze aanvallen
>   
>   - **Replay :** 4,6 BN-aanvallen gedetecteerd in mei 2018
>   - **Password spray**: 350k in april 2018
>   - **Phishing**: Dit is moeilijk precies te kwantificeren, maar we zagen 23M risicogebeurtenissen in maart 2018, waarvan vele phish gerelateerd zijn

## <a name="risk-detection-and-remediation"></a>Risicodetectie en -herstel

Identiteitsbescherming identificeert risico's in de volgende classificaties:

| Type risicodetectie | Beschrijving |
| --- | --- |
| Atypische reizen | Meld u aan vanaf een atypische locatie op basis van de recente aanmeldingen van de gebruiker. |
| Anoniem IP-adres | Meld u aan vanaf een anoniem IP-adres (bijvoorbeeld: Tor-browser, anonymizer VPN's). |
| Onbekende aanmeldingseigenschappen | Meld u aan met eigenschappen die we onlangs niet hebben gezien voor de opgegeven gebruiker. |
| Malware gekoppeld IP-adres | Inloggen vanaf een malwaregekoppeld IP-adres |
| Gelekte referenties | Deze risicodetectie geeft aan dat de geldige referenties van de gebruiker zijn gelekt |
| Azure AD-bedreigingsinformatie | De interne en externe threat intelligence bronnen van Microsoft hebben een bekend aanvalspatroon geïdentificeerd |

Meer details over deze risico's en hoe / wanneer ze worden berekend kan worden gevonden in het artikel, [Wat is risico](concept-identity-protection-risks.md).

De risicosignalen kunnen leiden tot herstelinspanningen zoals gebruikers verplichten om Azure Multi-Factor Authentication uit te voeren, hun wachtwoord opnieuw in te stellen met behulp van selfservicewachtwoordopnieuw instellen of blokkeren totdat een beheerder actie onderneemt.

## <a name="risk-investigation"></a>Risicoonderzoek

Beheerders kunnen detecties controleren en er handmatig actie op ondernemen als dat nodig is. Er zijn drie belangrijke rapporten die beheerders gebruiken voor onderzoeken in Identiteitsbescherming:

- Riskante gebruikers
- Riskante aanmeldingen
- Risicodetectie

Meer informatie is te vinden in het artikel, [How To: Investigate risk](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Risicogegevens exporteren

Gegevens van Identiteitsbescherming kunnen worden geëxporteerd naar andere tools voor archivering en verder onderzoek en corelatie. Met de op Microsoft Graph gebaseerde API's kunnen organisaties deze gegevens verzamelen voor verdere verwerking in een tool zoals hun SIEM. Informatie over hoe u toegang krijgt tot de API voor identiteitsbeveiliging vindt u in het artikel, [Aan de slag met Azure Active Directory-identiteitsbeveiliging en Microsoft Graph](howto-identity-protection-graph-api.md)

Informatie over het integreren van identiteitsbeveiligingsgegevens met Azure Sentinel vindt u in het artikel, [Connect-gegevens van Azure AD-identiteitsbeveiliging.](../../sentinel/connect-azure-ad-identity-protection.md)

## <a name="permissions"></a>Machtigingen

Voor identiteitsbescherming zijn gebruikers een beveiligingslezer, beveiligingsoperator, beveiligingsbeheerder, global reader of globale beheerder nodig om toegang te krijgen.

| Rol | Kan doen | Kan niet doen |
| --- | --- | --- |
| Globale beheerder | Volledige toegang tot identiteitsbescherming |   |
| Beveiligingsbeheerder | Volledige toegang tot identiteitsbescherming | Wachtwoord opnieuw instellen voor een gebruiker |
| Beveiligingsoperator | Alle rapporten voor identiteitsbescherming en overzichtsblad weergeven <br><br> Gebruikersrisico's afwijzen, veilige aanmelding bevestigen, compromis bevestigen | Beleidsregels configureren of wijzigen <br><br> Wachtwoord opnieuw instellen voor een gebruiker <br><br> Waarschuwingen configureren |
| Beveiligingslezer | Alle rapporten voor identiteitsbescherming en overzichtsblad weergeven | Beleidsregels configureren of wijzigen <br><br> Wachtwoord opnieuw instellen voor een gebruiker <br><br> Waarschuwingen configureren <br><br> Feedback geven over detecties |

Beheerders van voorwaardelijke toegang kunnen ook beleid maken dat factor in aanmeldingsrisico als voorwaarde is, meer informatie vinden in het artikel [Voorwaardelijke toegang: voorwaarden](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Mogelijkheid | Details | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Gratis |
| --- | --- | --- | --- | --- |
| Risicobeleid | Gebruikersrisicobeleid (via identiteitsbescherming) | Ja | Nee | Nee |
| Risicobeleid | Aanmeldingsrisicobeleid (via identiteitsbescherming of voorwaardelijke toegang) | Ja | Nee | Nee |
| Beveiligingsrapporten | Overzicht | Ja | Nee | Nee |
| Beveiligingsrapporten | Riskante gebruikers | Volledige toegang | Beperkte informatie | Beperkte informatie |
| Beveiligingsrapporten | Riskante aanmeldingen | Volledige toegang | Beperkte informatie | Beperkte informatie |
| Beveiligingsrapporten | Risicodetectie | Volledige toegang | Beperkte informatie | Nee |
| Meldingen | Gebruikers met risico's gedetecteerde waarschuwingen | Ja | Nee | Nee |
| Meldingen | Wekelijkse samenvatting | Ja | Nee | Nee |
| | MFA-registratiebeleid | Ja | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)

- [Wat is risico](concept-identity-protection-risks.md)

- [Beleid beschikbaar om risico's te beperken](concept-identity-protection-policies.md)
