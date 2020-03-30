---
title: Veelgestelde vragen over identiteitsbescherming in Azure Active Directory
description: Veelgestelde vragen Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443576"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Veelgestelde vragen Identiteitsbescherming in Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Bekende problemen met gebruikersrisico's afwijzen

**Door gebruikersrisico's** te verwijderen in klassieke identiteitsbeveiliging wordt de actor in de risicogeschiedenis van de gebruiker in Identiteitsbeveiliging ingesteld op **Azure AD**.

**Als u het gebruikersrisico** in Identiteitsbeveiliging plaatst, wordt de actor in de risicogeschiedenis van de gebruiker in identiteitsbeveiliging ingesteld ** \<op de naam\>van de beheerder met een hyperlink die naar het mes van de gebruiker wijst.**

Er is een huidig bekend probleem dat latentie veroorzaakt in de ontslagstroom van het gebruikersrisico. Als u een "Gebruikersrisicobeleid" hebt, wordt dit beleid binnen enkele minuten na het klikken op 'Gebruikersrisico' op ontslagen gebruikers niet meer toegepast. Er zijn echter bekende vertragingen met de UX verfrissende de "Risk state" van ontslagen gebruikers. Als tijdelijke oplossing vernieuwt u de pagina op browserniveau om de nieuwste gebruikersstatus "Risicostatus" te zien.

## <a name="risky-users-report-known-issues"></a>Riskante gebruikers melden bekende problemen

Query's op het **gebruikersnaamveld** zijn hoofdlettergevoelig, terwijl query's in het veld **Naam** case-agnostisch zijn.

Toggling **Toon datums als** verbergt de **kolom LAATST BIJGEWERKT RISICO.** Als u de kolom wilt lezen, klikt u op **Kolommen** boven aan het blad Riskante gebruikers.

**Als u alle gebeurtenissen** in de klassieke identiteitsbeveiliging plaatst, stelt u de status van de risicodetecties **in op Gesloten (opgelost).**

## <a name="risky-sign-ins-report-known-issues"></a>Risicovolle aanmeldingen melden bekende problemen

**Resolve** on a risk detection stelt de status in **voor Gebruikers die MFA hebben doorgegeven op basis van risico's.**

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="why-is-a-user-is-at-risk"></a>Waarom loopt een gebruiker risico?

Als u een Azure AD Identity Protection-klant bent, gaat u naar de [riskante gebruikersweergave](howto-identity-protection-investigate-risk.md#risky-users) en klikt u op een risicogebruiker. In de lade onderaan geeft het tabblad 'Risicogeschiedenis' alle gebeurtenissen weer die hebben geleid tot een wijziging van het gebruikersrisico. Als u alle riskante aanmeldingen voor de gebruiker wilt zien, klikt u op 'Risicovolle aanmeldingen van de gebruiker'. Klik op 'Gebruikersrisicodetecties' om alle risicodetecties voor deze gebruiker te bekijken.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Hoe kan ik een melding krijgen van detecties van een bepaald type?

Ga naar de weergave risicodetecties en filter op 'Detectietype'. U dit rapport vervolgens downloaden in. CSV of . JSON-indeling met de knop **Downloaden** bovenaan. Zie voor meer informatie het artikel [How To: Investigate risk](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Waarom kan ik mijn eigen risiconiveaus niet instellen voor elke risicodetectie?

Risiconiveaus in identiteitsbescherming zijn gebaseerd op de precisie van de detectie en worden aangedreven door onze onder toezicht staande machine learning. Als u wilt aanpassen welke ervaring gebruikers worden gepresenteerd, kan de beheerder bepaalde gebruikers/groepen opnemen/uitsluiten van het risicobeleid voor gebruikersrisico's en aanmeldingsrisico's.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Waarom komt de locatie van een aanmelding niet overeen met waar de gebruiker zich echt heeft aangemeld?

IP geolocatie mapping is een branchebrede uitdaging. Als u van mening bent dat de locatie in het aanmeldingsrapport niet overeenkomt met de werkelijke locatie, neemt u contact op met Microsoft-ondersteuning. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Hoe kan ik specifieke risicodetecties sluiten zoals in de oude gebruikersinterface?

U feedback geven over risicodetecties door de gekoppelde aanmelding als gecompromitteerd of veilig te bevestigen. De feedback op de aanmelding sijpelt door naar alle detecties die op die aanmelding zijn gedaan. Als u detecties wilt sluiten die niet zijn gekoppeld aan een aanmelding, u die feedback op gebruikersniveau geven. Zie voor meer informatie het artikel [Hoe: Risicofeedback geven in Azure AD-identiteitsbeveiliging](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Hoe ver kan ik teruggaan in de tijd om te begrijpen wat er aan de hand is met mijn gebruiker?

- De [riskante gebruikersweergave](howto-identity-protection-investigate-risk.md#risky-users) toont het risico dat een gebruiker heeft op basis van alle eerdere aanmeldingen. 
- De [riskante aanmeldingsweergave](howto-identity-protection-investigate-risk.md#risky-sign-ins) toont risicoborden in de afgelopen 30 dagen. 
- De [risicodetectiesweergave](howto-identity-protection-investigate-risk.md#risk-detections) toont risicodetecties die in de afgelopen 90 dagen zijn gemaakt.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Hoe kan ik meer te weten komen over een specifieke detectie?

Alle risicodetecties zijn gedocumenteerd in het artikel [Wat is risico](concept-identity-protection-risks.md#risk-types-and-detection). U de boven de (i) symbool naast de detectie op de Azure-portal plaatsen voor meer informatie over een detectie.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hoe werken de feedbackmechanismen in Identiteitsbescherming?

**Bevestig gecompromitteerd** (op een aanmelding) – Informeert Azure AD-identiteitsbeveiliging dat de aanmelding niet is uitgevoerd door de eigenaar van de identiteit en geeft een compromis aan.

- Na ontvangst van deze feedback verplaatsen we de aanmeldings- en gebruikersrisicostatus naar **Bevestigd gecompromitteerd** en risiconiveau naar **Hoog.**

- Daarnaast verstrekken we de informatie aan onze machine learning-systemen voor toekomstige verbeteringen in risicobeoordeling.

    > [!NOTE]
    > Als de gebruiker al is gesaneerd, klikt u niet op **Gecompromitteerd bevestigen** omdat de aanmelding en de gebruikersrisicostatus worden verplaatst naar **Bevestigd gecompromitteerd** en risiconiveau naar **Hoog**.

**Veilig bevestigen** (op een aanmelding) – Informeert Azure AD-identiteitsbeveiliging dat de aanmelding is uitgevoerd door de eigenaar van de identiteit en geeft geen compromis aan.

- Na ontvangst van deze feedback verplaatsen we de aanmeldingsstatus (niet de gebruiker) naar **Bevestigde kluis** en het risiconiveau naar **-**.

- Daarnaast verstrekken we de informatie aan onze machine learning-systemen voor toekomstige verbeteringen in risicobeoordeling.

    > [!NOTE]
    > Als u van mening bent dat de gebruiker niet is gecompromitteerd, gebruikt u **Gebruikersrisico afwijzen** op gebruikersniveau in plaats van Bevestigde kluis op aanmeldingsniveau te gebruiken. **Confirmed safe** Een **gebruikersrisico van de gebruiker** op gebruikersniveau sluit het gebruikersrisico en alle eerdere riskante aanmeldingen en risicodetecties.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Waarom zie ik een gebruiker met een lage (of hoger) risicoscore, zelfs als er geen risicovolle aanmeldingen of risicodetecties worden weergegeven in Identiteitsbescherming?

Aangezien het gebruikersrisico cumulatief van aard is en niet verloopt, kan een gebruiker een gebruikersrisico van laag of hoger hebben, zelfs als er geen recente risicovolle aanmeldingen of risicodetecties worden weergegeven in Identiteitsbescherming. Deze situatie kan gebeuren als de enige kwaadaardige activiteit op een gebruiker plaatsvond na het tijdsbestek waarvoor we de details van risicovolle aanmeldingen en risicodetecties opslaan. We vervallen het gebruikersrisico niet omdat van slechte acteurs bekend is dat ze meer dan 140 dagen in de omgeving van klanten blijven achter een gecompromitteerde identiteit voordat ze hun aanval opvoeren. Klanten kunnen de risicotijdlijn van de gebruiker bekijken om te begrijpen waarom een gebruiker risico loopt door naar:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Waarom heeft een aanmeldingsrisico (geaggregeerde)score van Hoog wanneer de detecties die ermee gepaard gaan een laag of gemiddeld risico hebben?

De hoge geaggregeerde risicoscore kan worden gebaseerd op andere kenmerken van de aanmelding, of het feit dat meer dan één detectie voor die aanmelding is geactiveerd. En omgekeerd kan een aanmeldingsrisico (aggregaat) van Medium hebben, zelfs als de detecties die verband houden met de aanmelding een hoog risico hebben. 
