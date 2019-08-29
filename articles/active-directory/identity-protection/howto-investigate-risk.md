---
title: Risico's in Azure Active Directory identiteits beveiliging (vernieuwd) onderzoeken | Microsoft Docs
description: Meer informatie over het onderzoeken van Risk ante gebruikers, detecties en aanmeldingen in Azure Active Directory identiteits beveiliging (vernieuwd).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129716"
---
# <a name="how-to-investigate-risk"></a>Procedure: Risico onderzoeken

Door gebruik te maken van de rapporten Risk ante aanmeldingen, Risk ante gebruikers en risico detecties kunt u het risico van uw omgeving onderzoeken en er inzicht in krijgen. Met de mogelijkheid om de Risk ante aanmeldingen, gebruikers en detecties te filteren en te sorteren, kunt u de mogelijke inbreuk in uw organisatie beter begrijpen. 

## <a name="risky-users-report"></a>Rapport over riskante gebruikers

Met de informatie van het rapport Risk ante gebruikers kunt u antwoorden vinden op vragen zoals:

- Welke gebruikers zijn een hoog risico?
- Welke gebruikers hebben een risico status opgelost?

Uw eerste ingangs punt voor dit rapport is de sectie **onderzoek** op de pagina beveiliging.

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/01.png)

Het rapport Risk ante gebruikers heeft een standaard weergave waarin het volgende wordt weer gegeven:

- Name
- Status van risico
- Risiconiveau
- Details van risico
- Risico voor het laatst bijgewerkt
- type
- Status

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/03.png)

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/04.png)

In het dialoog venster kolommen kunt u extra velden weer geven of velden verwijderen die al worden weer gegeven.

Wanneer u op een item in de lijstweergave klikt, worden in een horizontale weergave alle beschikbare gegevens voor het item vermeld.

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/05.png)

In de detail weergave ziet u het volgende:

- Basisgegevens
- Recente Risk ante aanmeldingen
- Risico detecties die niet zijn gekoppeld aan een aanmelding
- Risicogeschiedenis

Daarnaast kunt u het volgende doen:

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/08.png)

- De snelkoppeling alle aanmeldingen weer geven om het rapport met aanmeldingen voor die gebruiker weer te geven.
- Bekijk alle Risk ante aanmeldingen om alle aanmeldingen voor die gebruiker weer te geven die als riskant zijn gemarkeerd.
- Stel het wacht woord van een gebruiker opnieuw in als u denkt dat de identiteit van de gebruiker is aangetast.
- Sluit gebruikers risico af als u denkt dat de actieve risico detecties van een gebruiker vals-positief zijn. Zie het artikel [feedback geven over risico detecties in azure AD Identity Protection](howto-provide-risk-event-feedback.md)voor meer informatie.

### <a name="filter-risky-users"></a>Risk ante gebruikers filteren

Als u de gerapporteerde gegevens wilt beperken tot een niveau dat geschikt is voor u, kunt u de Risk ante gebruikers gegevens filteren met behulp van de volgende standaard velden:

- Name
- Gebruikersnaam
- Status van risico
- Risiconiveau
- type
- Status

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/06.png)

Met het filter **naam** kunt u de naam of de User Principal Name (UPN) opgeven van de gebruiker die u bezorgt.

Met het filter **risico status** kunt u het volgende selecteren:

- Loopt risico
- Hersteld
- Verwijderd

Met het filter **risico niveau** kunt u het volgende selecteren:

- Hoog
- Gemiddeld
- Laag

Met het filter **type** kunt u het volgende selecteren:

- Gast
- Lid

Met het **status** filter kunt u het volgende selecteren:

- Verwijderd
- Actief

### <a name="download-risky-users-data"></a>Risk ante gebruikers gegevens downloaden

U kunt de Risk ante gebruikers gegevens downloaden als u deze buiten het Azure Portal wilt gebruiken. Als u op downloaden klikt, wordt er een CSV-bestand van de meest recente 2.500 records gemaakt. 

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/07.png)

U kunt de lijst weergave aanpassen door te klikken op de kolommen in de werk balk.
 
Hiermee kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.
 
Als u meer wilt weten over een Risk ante gebruiker, klikt u op de gegevens lade om deze uit te vouwen

## <a name="risky-sign-ins-report"></a>Rapport Riskante aanmeldingen

Met de informatie van het rapport Risk ante aanmeldingen kunt u antwoorden vinden op vragen zoals:

- Hoeveel geslaagde aanmeldingen zijn er in de afgelopen week een anonieme detectie van IP-adressen?
- Welke gebruikers zijn in de afgelopen maand in het gedrang zijn bevestigd?
- Welke gebruikers hebben Risk ante aanmeldingen bij de Office 365-Portal?

Uw eerste ingangs punt voor dit rapport is de sectie **onderzoek** op de pagina beveiliging.

![Rapport Riskante aanmeldingen](./media/howto-investigate-risky-users-signins/02.png)

Het rapport Risk ante aanmeldingen heeft een standaard weergave waarin het volgende wordt weer gegeven:

- Date
- Gebruiker
- Toepassing
- Aanmeldingsstatus
- Status van risico
- Risico niveau (aggregatie)
- Risico niveau (realtime)
- Voorwaardelijke toegang
- MFA vereist  

![Rapport Riskante aanmeldingen](./media/howto-investigate-risky-users-signins/09.png)

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/11.png)

In het dialoog venster kolommen kunt u extra velden weer geven of velden verwijderen die al worden weer gegeven.

Wanneer u op een item in de lijstweergave klikt, worden in een horizontale weergave alle beschikbare gegevens voor het item vermeld.

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/12.png)

In de detail weergave ziet u het volgende:

- Basisgegevens
- Apparaatgegevens
- Risicogegevens
- MFA-info
- Voorwaardelijke toegang

Daarnaast kunt u het volgende doen:

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/13.png)

- Inbraak bevestigen 
- Veiligheid bevestigen

Zie het artikel [feedback geven over risico detecties in azure AD Identity Protection](howto-provide-risk-event-feedback.md)voor meer informatie.

### <a name="filter-risky-sign-ins"></a>Risk ante aanmeldingen filteren

Als u de gerapporteerde gegevens wilt beperken tot een niveau dat geschikt is voor u, kunt u de Risk ante gebruikers gegevens filteren met behulp van de volgende standaard velden:

- Gebruiker
- Toepassing
- Aanmeldingsstatus
- Status van risico
- Risico niveau (aggregatie)
- Risico niveau (realtime)
- Voorwaardelijke toegang
- Date
- Type risico niveau

![Rapport Riskante aanmeldingen](./media/howto-investigate-risky-users-signins/14.png)

Met het filter **naam** kunt u de naam of de User Principal Name (UPN) opgeven van de gebruiker die u bezorgt.

Met het filter **toepassing** kunt u de Cloud-app opgeven waartoe de gebruiker toegang probeerde te krijgen.

Met het filter **Aanmeldingsstatus** kunt u selecteren:

- Alle
- Geslaagd
- Fout

Met het filter **risico status** kunt u het volgende selecteren:

- Loopt risico
- Inbraak bevestigd
- Bevestigd (veilig)
- Verwijderd
- Hersteld

Met het filter **risico niveau (aggregatie)** kunt u het volgende selecteren:

- Hoog
- Gemiddeld
- Laag

Met het filter **risico niveau (realtime)** kunt u het volgende selecteren:

- Hoog
- Gemiddeld
- Laag

Met het filter **voorwaardelijke toegang** kunt u het volgende selecteren:

- Alle
- Niet toegepast
- Geslaagd
- Fout

Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens.
Mogelijke waarden zijn:

- Afgelopen maand
- Laatste 7 dagen
- Afgelopen 24 uur
- Aangepaste tijdinterval

### <a name="download-risky-sign-ins-data"></a>Risk ante aanmeldingen gegevens downloaden

U kunt de gegevens voor Risk ante aanmeldingen downloaden als u deze buiten het Azure Portal wilt gebruiken. Als u op downloaden klikt, wordt er een CSV-bestand van de meest recente 2.500 records gemaakt. 

![Rapport over riskante gebruikers](./media/howto-investigate-risky-users-signins/15.png)


## <a name="risk-detections-report"></a>Rapport over risico detectie

Het rapport risico detectie geeft u inzicht in elke risico detectie van identiteits beveiliging in uw Tenant.


Het rapport risico detectie heeft een standaard weergave waarin het volgende wordt weer gegeven:

- Date

- Gebruiker

- IP-adres

- Location

- Detectietype

- Status van risico

- Risiconiveau

- Aanvraag-ID
 

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk. In het dialoog venster kolommen kunt u extra velden weer geven of velden verwijderen die al worden weer gegeven.

Wanneer u op een item in de lijstweergave klikt, worden in een horizontale weergave alle beschikbare gegevens voor het item vermeld.


In de detail weergave vindt u aanvullende informatie over de risico detectie.

De detail weergave bevat ook koppelingen naar

- Risico rapport van gebruiker weer geven
- Aanmeldingen van gebruiker weer geven
- Risk ante aanmeldingen van gebruiker weer geven
- Een gekoppelde Risk ante aanmelding weer geven
- Risico detecties van de gebruiker weer geven


### <a name="filter-risk-detections"></a>Risico detecties filteren

Als u de gerapporteerde gegevens wilt beperken tot een niveau dat geschikt is voor u, kunt u de gegevens van de risico detectie filteren met behulp van de volgende standaard velden:

- Detectietijdstip
- Detectietype
- Status van risico
- Risiconiveau
- Gebruiker
- Gebruikersnaam
- IP-adres
- Location
- Timing van detectie
- Activiteit 
- Source
- Type token Uitgever
- Aanvraag-ID
- Correlatie-id


Met het filter **detectie tijd** kunt u een tijds bestek voor de geretourneerde gegevens definiëren. Met dit filter kunt u het volgende selecteren:
- Laatste 90 dagen
- Laatste 30 dagen
- Laatste 7 dagen
- Afgelopen 24 uur
- Aangepaste tijdinterval

Met het filter **type detectie** kunt u het type risico detectie opgeven (zoals onbekende aanmeld eigenschappen).

Met het filter **risico status** kunt u het volgende selecteren:

- Loopt risico
- Inbraak bevestigd
- Bevestigd (veilig)
- Verwijderd
- Hersteld

Met het filter **risico niveau** kunt u het volgende selecteren:

- Hoog
- Gemiddeld
- Laag

Met het filter **gebruiker** kunt u de naam of de object-id opgeven van de gebruiker die u bezorgt.

Met het filter **gebruikers naam** kunt u de User Principal Name (UPN) opgeven van de gebruiker die u bezorgt.

Met het filter **timing van detectie** kunt u opgeven of de detectie realtime of offline is. Opmerking: U kunt aanmeldingen aanvragen met realtime detecties met behulp van beleid voor aanmeldings Risico's. 

Met het **activiteiten** filter kunt u opgeven of de detectie is gekoppeld aan een aanmelding (bijvoorbeeld een anoniem IP-adres) of een gebruiker (bijvoorbeeld gelekte referenties).

Met het **bron** filter kunt u de bron van de risico detectie (zoals Azure AD of Microsoft Cloud app Security) opgeven. 

Met het filter type van het **token Uitgever** kunt u opgeven waar het token is uitgegeven (zoals Azure AD of AD Federation Services)


### <a name="download-risk-detections-data"></a>Gegevens over risico detecties downloaden

U kunt de risico detectie gegevens downloaden als u deze buiten de Azure Portal wilt gebruiken. Als u op downloaden klikt, wordt er een CSV-bestand van de meest recente 5.000 records gemaakt. 

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van de [Azure AD Identity Protection](overview-v2.md)voor een overzicht van Azure AD Identity Protection.
