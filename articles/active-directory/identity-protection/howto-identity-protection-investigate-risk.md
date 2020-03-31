---
title: Risico Azure Active Directory Identity Protection onderzoeken
description: Meer informatie over het onderzoeken van risicovolle gebruikers, detecties en aanmeldingen in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253479"
---
# <a name="how-to-investigate-risk"></a>How To: Risico's onderzoeken

Identity Protection biedt organisaties drie rapporten die ze kunnen gebruiken om identiteitsrisico's in hun omgeving te onderzoeken. Deze rapporten zijn de **riskante gebruikers**, **risicovolle aanmeldingen**en **risicodetecties**. Onderzoek van gebeurtenissen is de sleutel tot een beter begrip en het identificeren van eventuele zwakke punten in uw beveiligingsstrategie.

Alle drie rapporten maken het downloaden van gebeurtenissen in . CSV-indeling voor verdere analyse buiten de Azure-portal. De riskante gebruikers en riskante aanmeldingsrapporten maken het mogelijk om de meest recente 2500 vermeldingen te downloaden, terwijl het rapport risicodetecties het mogelijk maakt om de meest recente 5000 records te downloaden.

Organisaties kunnen profiteren van de Microsoft Graph API-integraties om gegevens samen te voegen met andere bronnen waartoe ze als organisatie toegang hebben.

De drie rapporten zijn te vinden in de **Azure-portal** > **Azure Active Directory** > **Security**.

## <a name="navigating-the-reports"></a>Navigeren door de rapporten

Elk rapport wordt gestart met een lijst van alle detecties voor de periode die boven aan het rapport wordt weergegeven. Elk rapport maakt het mogelijk om kolommen op te tellen of te verwijderen op basis van de voorkeur van de beheerder. Beheerders kunnen ervoor kiezen om de gegevens te downloaden in . CSV of . JSON-indeling. Rapporten kunnen worden gefilterd met behulp van de filters boven aan het rapport.

Als u afzonderlijke vermeldingen selecteert, kunnen aanvullende vermeldingen boven aan het rapport worden ingeschakeld, zoals de mogelijkheid om een aanmelding als gecompromitteerd of veilig te bevestigen, een gebruiker te bevestigen als gecompromitteerd of het gebruikersrisico te verwijderen.

Als u afzonderlijke vermeldingen selecteert, wordt een detailvenster onder de detecties uitgebreid. Met de detailsweergave kunnen beheerders acties op elke detectie onderzoeken en uitvoeren. 

![Voorbeeld rapport Identiteitsbescherming met riskante aanmeldingen en details](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Riskante gebruikers

Met de informatie die door de riskante gebruikers rapport, beheerders kunnen vinden:

- Welke gebruikers lopen risico, hebben risico's gesaneerd of hebben risico's laten vallen?
- Details over detecties
- Geschiedenis van alle risicovolle aanmeldingen
- Risicogeschiedenis
 
Beheerders kunnen er vervolgens voor kiezen om actie te ondernemen voor deze gebeurtenissen. Beheerders kunnen ervoor kiezen om:

- Het gebruikerswachtwoord opnieuw instellen
- Compromis van de gebruiker bevestigen
- Gebruikersrisico's afwijzen
- Gebruiker blokkeren om zich aan te melden
- Verder onderzoeken met Azure ATP

## <a name="risky-sign-ins"></a>Riskante aanmeldingen

Het risicovolle aanmeldingsrapport bevat filterbare gegevens tot de afgelopen 30 dagen (1 maand).

Met de informatie die wordt verstrekt door het risicovolle aanmeldingsrapport, kunnen beheerders het:

- Welke aanmeldingen worden geclassificeerd als risicovol, bevestigd gecompromitteerd, bevestigd veilig, ontslagen of gesaneerd.
- Real-time en geaggregeerde risiconiveaus in verband met aanmeldingspogingen.
- Detectietypen geactiveerd
- Beleid voor voorwaardelijke toegang toegepast
- MFA-details
- Apparaatgegevens
- Toepassingsgegevens
- Locatie-informatie

Beheerders kunnen er vervolgens voor kiezen om actie te ondernemen voor deze gebeurtenissen. Beheerders kunnen ervoor kiezen om:

- Aanmeldingscompromis bevestigen
- Aanmelden veilig bevestigen

## <a name="risk-detections"></a>Risicodetectie

Het rapport risicodetecties bevat filterbare gegevens tot de afgelopen 90 dagen (3 maanden).

Met de informatie die door het rapport risicodetecties wordt verstrekt, kunnen beheerders het:

- Informatie over elke risicodetectie inclusief type.
- Andere risico's die tegelijkertijd worden veroorzaakt
- Locatie voor aanmeldingspoging
- Koppeling naar meer details van Microsoft Cloud App Security (MCAS).

Beheerders kunnen er vervolgens voor kiezen om terug te keren naar het risico- of aanmeldingsrapport van de gebruiker om acties te ondernemen op basis van de verzamelde informatie.

## <a name="next-steps"></a>Volgende stappen

- [Beleid beschikbaar om risico's te beperken](concept-identity-protection-policies.md)

- [Aanmeldings- en gebruikersrisicobeleid inschakelen](howto-identity-protection-configure-risk-policies.md)
