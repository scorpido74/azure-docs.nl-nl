---
title: Risico Azure Active Directory Identity Protection onderzoeken
description: Meer informatie over het onderzoeken van Risk ante gebruikers, detecties en aanmeldingen in Azure Active Directory Identity Protection
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375907"
---
# <a name="how-to-investigate-risk"></a>Procedure: risico onderzoeken

Identiteits beveiliging biedt organisaties drie rapporten die ze kunnen gebruiken om identiteits Risico's te onderzoeken in hun omgeving. Deze rapporten zijn de **Risk ante gebruikers**, **Risk ante aanmeldingen**en **risico detecties**. Onderzoek van gebeurtenissen is belang rijk voor een beter inzicht in en het identificeren van zwakke punten in uw beveiligings strategie.

In alle drie de rapporten kunnen gebeurtenissen in worden gedownload. CSV-indeling voor verdere analyse buiten het Azure Portal. Met de rapporten Risk ante gebruikers en Risk ante aanmeldingen kunt u de meest recente 2500-vermeldingen downloaden, terwijl u met het rapport risico detecties de meest recente 5000-records kunt downloaden.

Organisaties kunnen profiteren van de Microsoft Graph API-integraties om gegevens samen te voegen met andere bronnen waartoe ze mogelijk toegang hebben als een organisatie.

De drie rapporten vindt u in de **Azure Portal** > **Azure Active Directory** > - **beveiliging**.

## <a name="navigating-the-reports"></a>Navigeren door de rapporten

Elk rapport wordt gestart met een lijst met alle detecties voor de periode die boven aan het rapport wordt weer gegeven. Elk rapport maakt het mogelijk om kolommen toe te voegen of te verwijderen op basis van de voor keur van de beheerder. Beheerders kunnen ervoor kiezen om de gegevens in te downloaden. CSV of. JSON-indeling. Rapporten kunnen worden gefilterd met behulp van de filters aan de bovenkant van het rapport.

Als u afzonderlijke items selecteert, kunnen boven aan het rapport aanvullende vermeldingen worden ingeschakeld, zoals de mogelijkheid om een aanmelding te bevestigen als aangetast of veilig, een gebruiker te bevestigen als aangetast of een gebruikers risico te sluiten.

Als u afzonderlijke items selecteert, wordt een detail venster uitgebreid met de detecties. De weer gave Details stelt beheerders in staat om op elke detectie acties te onderzoeken en uit te voeren. 

![Voor beeld van een identiteits beveiligings rapport met Risk ante aanmeldingen en Details](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Risk ante gebruikers

Met de informatie die door het rapport Risk ante gebruikers wordt verstrekt, kunnen beheerders het volgende vinden:

- Welke gebruikers lopen risico, hebben het risico gelost of hebben ze een risico gemist?
- Details over detecties
- Geschiedenis van alle Risk ante aanmeldingen
- Risico geschiedenis
 
Beheerders kunnen er vervolgens voor kiezen om actie te ondernemen op deze gebeurtenissen. Beheerders kunnen kiezen uit:

- Het gebruikers wachtwoord opnieuw instellen
- Inbreuk op gebruikers bevestigen
- Gebruikers risico negeren
- Blok keren dat de gebruiker zich aanmeldt
- Meer onderzoeken met behulp van Azure ATP

## <a name="risky-sign-ins"></a>Riskante aanmeldingen

Het rapport Risk ante aanmeldingen bevat filter bare gegevens voor de afgelopen 30 dagen (1 maand).

Met de informatie die door het rapport Risk ante aanmeldingen wordt verstrekt, kunnen beheerders het volgende vinden:

- Welke aanmeldingen zijn geclassificeerd als risico, bevestigde schade, bevestigd veilig, gesloten of opgelost.
- Real-time en aggregatie risico niveaus die zijn gekoppeld aan aanmeldings pogingen.
- Detectie typen geactiveerd
- Beleid voor voorwaardelijke toegang toegepast
- MFA-Details
- Apparaatgegevens
- Toepassingsgegevens
- Locatie gegevens

Beheerders kunnen er vervolgens voor kiezen om actie te ondernemen op deze gebeurtenissen. Beheerders kunnen kiezen uit:

- Inbreuk op aanmelden bevestigen
- Aanmelden bevestigen veilig

## <a name="risk-detections"></a>Risicodetectie

Het rapport van de risico detectie bevat filter bare gegevens gedurende de afgelopen 90 dagen (3 maanden).

Met de informatie die door het rapport van de risico detectie wordt verstrekt, kunnen beheerders het volgende vinden:

- Informatie over elke risico detectie, inclusief type.
- Andere Risico's die tegelijkertijd worden geactiveerd
- Locatie van aanmeldings poging
- Koppeling naar meer details van Microsoft Cloud App Security (MCAS).

Beheerders kunnen vervolgens teruggaan naar het rapport met het risico of het aanmeldingen van de gebruiker om acties uit te voeren op basis van verzamelde gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Beleids regels beschikbaar om Risico's te beperken](concept-identity-protection-policies.md)

- [Aanmeldings-en gebruikers risico beleid inschakelen](howto-identity-protection-configure-risk-policies.md)
