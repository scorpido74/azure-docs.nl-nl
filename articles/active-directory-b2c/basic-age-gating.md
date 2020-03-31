---
title: Leeftijdsgat inschakelen in Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het identificeren van minderjarigen met behulp van uw toepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56cbeb8e8fe21f4b39c2f5c6af43e83ae330e5d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189970"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Leeftijdgat in Azure Active Directory B2C inschakelen

>[!IMPORTANT]
>Deze functie is beschikbaar voor openbare preview. Gebruik de functie niet voor productietoepassingen.
>

Met age gating in Azure Active Directory B2C (Azure AD B2C) u minderjarigen identificeren die uw toepassing willen gebruiken. U ervoor kiezen om de minderjarige te blokkeren bij het aanmelden bij de toepassing. Gebruikers kunnen ook teruggaan naar de toepassing en hun leeftijdsgroep en hun status van ouderlijke toestemming identificeren. Azure AD B2C kan minderjarigen blokkeren zonder ouderlijke toestemming. Azure AD B2C kan ook worden ingesteld om de toepassing te laten beslissen wat te doen met minderjarigen.

Nadat u leeftijd gating in uw [gebruikersstroom](user-flow-overview.md), gebruikers wordt gevraagd wanneer ze zijn geboren en in welk land / regio ze wonen in. Als een gebruiker zich aanmeldt die de informatie nog niet eerder heeft ingevoerd, moet deze de volgende keer dat hij zich aanmeldt, deze invoeren. De regels worden toegepast elke keer dat een gebruiker zich aanmeldt.

Azure AD B2C gebruikt de informatie die de gebruiker invoert om te bepalen of deze minderjarig is. Het veld **leeftijdsgroepgroep** wordt vervolgens bijgewerkt in hun account. De waarde `null`kan `Undefined` `Minor`worden `Adult`, `NotAdult`, , , en .  De **velden ageGroup** en **consentProvidedForMinor** worden vervolgens gebruikt om de waarde van **legalAgeGroupClassification**te berekenen.

Leeftijdsbekniedering omvat twee leeftijdswaarden: de leeftijd dat iemand niet langer als minderjarige wordt beschouwd en de leeftijd waarop een minderjarige toestemming van de ouders moet hebben. In de volgende tabel worden de leeftijdsregels weergegeven die worden gebruikt voor het definiëren van een minderjarige en een minderjarige die toestemming nodig hebben.

| Land/regio | Naam land/regio | Minderjarige toestemmingsleeftijd | Minderjarige leeftijd |
| -------------- | ------------------- | ----------------- | --------- |
| Standaard | Geen | Geen | 18 |
| AE | Verenigde Arabische Emiraten | Geen | 21 |
| AT | Oostenrijk | 14 | 18 |
| BE | België | 14 | 18 |
| BG | Bulgarije | 16 | 18 |
| BH | Bahrein | Geen | 21 |
| CM | Kameroen | Geen | 21 |
| CY | Cyprus | 16 | 18 |
| CZ | Tsjechië | 16 | 18 |
| DE | Duitsland | 16 | 18 |
| DK | Denemarken | 16 | 18 |
| EE | Estland | 16 | 18 |
| EG | Egypte | Geen | 21 |
| ES | Spanje | 13 | 18 |
| FR | Frankrijk | 16 | 18 |
| GB | Verenigd Koninkrijk | 13 | 18 |
| GR | Griekenland | 16 | 18 |
| HR | Kroatië | 16 | 18 |
| HU | Hongarije | 16 | 18 |
| IE | Ierland | 13 | 18 |
| IT | Italië | 16 | 18 |
| KR | Zuid-Korea | 14 | 18 |
| LT | Litouwen | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Letland | 16 | 18 |
| MT | Malta | 16 | 18 |
| N.v.t. | Namibië | Geen | 21 |
| NL | Nederland | 16 | 18 |
| PL | Polen | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Roemenië | 16 | 18 |
| SE | Zweden | 13 | 18 |
| SG | Singapore | Geen | 21 |
| SI | Slovenië | 16 | 18 |
| SK | Slowakije | 16 | 18 |
| TD | Tsjaad | Geen | 21 |
| TH | Thailand | Geen | 20 |
| TW | Taiwan | Geen | 20 |
| VS | Verenigde Staten | 13 | 18 |

## <a name="age-gating-options"></a>Leeftijdsgatopties

### <a name="allowing-minors-without-parental-consent"></a>Minderjarigen toestaan zonder ouderlijke toestemming

Voor gebruikersstromen die aanmelden, aanmelden of beide toestaan, u ervoor kiezen minderjarigen zonder toestemming toe te staan in uw toepassing. Minderjarigen zonder ouderlijke toestemming mogen zich als normaal aanmelden of aanmelden en Azure AD B2C geeft een ID-token uit met de **legalAgeGroupClassification-claim.** Deze claim definieert de ervaring die gebruikers hebben, zoals het verzamelen van ouderlijke toestemming en het bijwerken van het veld **ConsentProvidedForMinor.**

### <a name="blocking-minors-without-parental-consent"></a>Minderjarigen blokkeren zonder ouderlijke toestemming

Voor gebruikersstromen die aanmelden, aanmelden of beide toestaan, u ervoor kiezen minderjarigen te blokkeren zonder toestemming van de toepassing. De volgende opties zijn beschikbaar voor het verwerken van geblokkeerde gebruikers in Azure AD B2C:

- Stuur een JSON terug naar de toepassing - deze optie stuurt een antwoord terug naar de toepassing dat een minderjarige is geblokkeerd.
- Een foutpagina weergeven - de gebruiker krijgt een pagina te zien waarin staat dat hij geen toegang heeft tot de toepassing.

## <a name="set-up-your-tenant-for-age-gating"></a>Uw huurder instellen voor leeftijdsgating

Als u leeftijdsgating wilt gebruiken in een gebruikersstroom, moet u uw tenant configureren om extra eigenschappen te hebben.

1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren. Selecteer de map die uw tenant bevat.
2. Selecteer **Alle services** in de linkerbovenhoek van de Azure-portal, zoek naar en selecteer Azure AD **B2C**.
3. Selecteer **Eigenschappen** voor uw tenant in het menu aan de linkerkant.
2. Klik onder de sectie **Leeftijdgating** op **Configureren**.
3. Wacht tot de bewerking is voltooid en uw tenant wordt ingesteld voor leeftijdgating.

## <a name="enable-age-gating-in-your-user-flow"></a>Leeftijdsgat in uw gebruikersstroom inschakelen

Nadat uw tenant is ingesteld om leeftijdsgating te gebruiken, u deze functie gebruiken in [gebruikersstromen](user-flow-versions.md) waar deze is ingeschakeld. U schakelt leeftijdsgat in met de volgende stappen:

1. Maak een gebruikersstroom met ingeschakelde leeftijdsgating.
2. Nadat u de gebruikersstroom hebt gemaakt, selecteert u **Eigenschappen** in het menu.
3. Selecteer **ingeschakeld**in de sectie **Gating** leeftijd .
4. Vervolgens bepaalt u hoe u gebruikers wilt beheren die zich als minderjarig identificeren. Voor **Aanmelden of aanmelden**selecteert u `Allow minors to access your application` `Block minors from accessing your application`of . Als het blokkeren van minderjarigen is geselecteerd, selecteert `Send a JSON back to the application` u of `Show an error message`.




