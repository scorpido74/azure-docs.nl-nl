---
title: Risicofeedback geven in Azure Active Directory Identity Protection
description: Hoe en waarom zou u feedback moeten geven over risicodetecties van identiteitsbescherming.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382083"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>How To: Risicofeedback geven in Azure AD-identiteitsbeveiliging

Azure AD Identity Protection stelt u in staat om feedback te geven over de risicobeoordeling. In het volgende document worden de scenario's weergegeven waarin u feedback wilt geven over de risicobeoordeling van Azure AD Identity Protection en hoe we deze opnemen.

## <a name="what-is-a-detection"></a>Wat is een detectie?

Een detectie van identiteitsbescherming is een indicator van verdachte activiteiten vanuit het perspectief van identiteitsrisico's. Deze verdachte activiteiten worden risicodetecties genoemd. Deze identiteitsgebaseerde detecties kunnen gebaseerd zijn op heuristiek, machine learning of kunnen afkomstig zijn van partnerproducten. Deze detecties worden gebruikt om aanmeldingsrisico's en gebruikersrisico's te bepalen,

* Gebruikersrisico vertegenwoordigt de waarschijnlijkheid dat een identiteit wordt aangetast.
* Aanmeldingsrisico vertegenwoordigt de waarschijnlijkheid dat een aanmelding wordt aangetast (de aanmelding is bijvoorbeeld niet geautoriseerd door de eigenaar van de identiteit).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Waarom zou ik risicofeedback geven aan de risicobeoordelingen van Azure AD? 

Er zijn verschillende redenen waarom u Azure AD-risicofeedback moet geven:

- **U hebt de gebruiker van Azure AD of de inlogrisicobeoordeling onjuist gevonden.** Een aanmelding in het rapport 'Risicovolle aanmeldingen' was bijvoorbeeld goedaardig en alle detecties op die aanmelding waren valse positieven.
- **U hebt gevalideerd dat de gebruiker of aanmeldingsrisicobeoordeling van Azure AD juist was.** Een aanmelding in het rapport 'Riskante aanmeldingen' was bijvoorbeeld inderdaad schadelijk en u wilt dat Azure AD weet dat alle detecties op die aanmelding echte positieven waren.
- **U hebt het risico voor die gebruiker buiten Azure AD-identiteitsbeveiliging verholpen** en u wilt dat het risiconiveau van de gebruiker wordt bijgewerkt.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hoe gebruikt Azure AD mijn risicofeedback?

Azure AD gebruikt uw feedback om het risico van de onderliggende gebruiker en/of aanmelding en de nauwkeurigheid van deze gebeurtenissen bij te werken. Deze feedback helpt de eindgebruiker te beveiligen. Zodra u bijvoorbeeld bevestigt dat een aanmelding is gecompromitteerd, verhoogt Azure AD onmiddellijk het risico van de gebruiker en verhoogt het totale risico (niet realtime risico) van de gebruiker naar Hoog. Als deze gebruiker is opgenomen in uw gebruikersrisicobeleid om gebruikers met een hoog risico te dwingen hun wachtwoorden veilig te resetten, zal de gebruiker zichzelf automatisch herstellen wanneer ze zich opnieuw aanmelden.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hoe moet ik risicofeedback geven en wat gebeurt er onder de motorkap?

Hier volgen de scenario's en mechanismen om risicofeedback te geven aan Azure AD.

| Scenario | Hoe feedback geven? | Wat gebeurt er onder de motorkap? | Opmerkingen |
| --- | --- | --- | --- |
| **Aanmelden niet gecompromitteerd (Foutpositief)** <br> 'Risicovolle aanmeldingen' tonen een risicovolle aanmelding [Risicotoestand = Risicostaat] maar die aanmelding is niet in gevaar gebracht. | Selecteer de aanmelding en klik op 'Meld je veilig bevestigen'. | Azure AD verplaatst het totale risico van de aanmelding naar geen [Risicostatus = Bevestigde kluis; Risiconiveau (Aggregaat) = -] en zal de impact ervan op het gebruikersrisico omkeren. | Momenteel is de optie 'Bevestig aanmeldingsveilig' alleen beschikbaar in het rapport 'Riskante aanmeldingen'. |
| **Aanmelden gecompromitteerd (True positive)** <br> 'Risky sign-ins' rapport toont een risico-inlog [Risicotoestand = Risico] met een laag risico [Risiconiveau (Aggregaat) = Laag] en dat aanmelding inderdaad in het gedrang kwam. | Selecteer de aanmelding en klik op 'Aanmelden sluiten bevestigen'. | Azure AD verplaatst het totale risico van de aanmelding en het gebruikersrisico naar Hoog [Risicostatus = Bevestigde gecompromitteerde status; Risiconiveau = Hoog]. | Momenteel is de optie 'Bevestig inlog gecompromitteerd' alleen beschikbaar in het rapport 'Riskante aanmeldingen'. |
| **Gebruiker gecompromitteerd (True positive)** <br> 'Risicovolle gebruikers' rapport toont een risicogebruiker [Risicotoestand = Risico] met een laag risico [Risiconiveau = Laag] en die gebruiker is inderdaad gecompromitteerd. | Selecteer de gebruiker en klik op 'Bevestig gebruiker gecompromitteerd'. | Azure AD verplaatst het gebruikersrisico naar Hoog [Risicostatus = Bevestigde gecompromitteerde status; Risiconiveau = Hoog] en voegt een nieuwe detectie toe 'Admin confirmed user compromised'. | Momenteel is de optie 'Gebruiker gecompromitteerd bevestigen' alleen beschikbaar in het rapport 'Riskante gebruikers'. <br> De detectie 'Admin confirmed user compromised' wordt weergegeven op het tabblad 'Risicodetecties niet gekoppeld aan een aanmelding' in het rapport 'Riskante gebruikers'. |
| **Gebruiker gesaneerd buiten Azure AD-identiteitsbeveiliging (True positive + Remediated)** <br> 'Risicovolle gebruikers' rapport toont een risicogebruiker en ik heb vervolgens de gebruiker gesaneerd buiten Azure AD Identity Protection. | 1. Selecteer de gebruiker en klik op 'Bevestig gebruiker gecompromitteerd'. (Dit proces bevestigt aan Azure AD dat de gebruiker inderdaad is gecompromitteerd.) <br> 2. Wacht tot het 'Risiconiveau' van de gebruiker naar High gaat. (Deze keer geeft Azure AD de benodigde tijd om de bovenstaande feedback naar de risicoengine te brengen.) <br> 3. Selecteer de gebruiker en klik op 'Gebruikersrisico afwijzen'. (Dit proces bevestigt aan Azure AD dat de gebruiker niet langer is gecompromitteerd.) |  Azure AD verplaatst het gebruikersrisico naar geen [Risicostatus = Afgewezen; Risiconiveau = -] en sluit het risico op alle bestaande aanmeldingen met actief risico. | Als u op 'Gebruikersrisico afwijzen' klikt, worden alle risico's voor de gebruiker en eerdere aanmeldingen gesloten. Deze actie kan niet ongedaan worden gemaakt. |
| **Gebruiker niet gecompromitteerd (False positive)** <br> 'Risicovolle gebruikers' rapport toont op risico gebruiker, maar de gebruiker is niet gecompromitteerd. | Selecteer de gebruiker en klik op 'Gebruikersrisico afwijzen'. (Dit proces bevestigt aan Azure AD dat de gebruiker niet is gecompromitteerd.) | Azure AD verplaatst het gebruikersrisico naar geen [Risicostatus = Afgewezen; Risiconiveau = -]. | Als u op 'Gebruikersrisico afwijzen' klikt, worden alle risico's voor de gebruiker en eerdere aanmeldingen gesloten. Deze actie kan niet ongedaan worden gemaakt. |
| Ik wil het risico van de gebruiker te sluiten, maar ik ben niet zeker of de gebruiker is gecompromitteerd / veilig. | Selecteer de gebruiker en klik op 'Gebruikersrisico afwijzen'. (Dit proces bevestigt aan Azure AD dat de gebruiker niet langer is gecompromitteerd.) | Azure AD verplaatst het gebruikersrisico naar geen [Risicostatus = Afgewezen; Risiconiveau = -]. | Als u op 'Gebruikersrisico afwijzen' klikt, worden alle risico's voor de gebruiker en eerdere aanmeldingen gesloten. Deze actie kan niet ongedaan worden gemaakt. We raden u aan de gebruiker te herstellen door op 'Wachtwoord opnieuw instellen' te klikken of de gebruiker te vragen zijn of zijn of haar referenties veilig te resetten/wijzigen. |

Feedback over gebruikersrisicodetecties in identiteitsbeveiliging wordt offline verwerkt en kan enige tijd duren om bij te werken. De kolom risicoverwerkingsstatus geeft de huidige status van feedbackverwerking.

![Status voor risicobeheer voor riskant gebruikersrapport](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

- [Verwijzing naar risicodetecties van Azure Active Directory Identity Protection](risk-events-reference.md)
