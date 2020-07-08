---
title: Leeftijds beperking inschakelen in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over hoe u minder jarigen kunt identificeren met uw toepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bd93f9062f8446ce20436a7a04e2054aaf5be71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386128"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Leeftijds beperking inschakelen in Azure Active Directory B2C

>[!IMPORTANT]
>Deze functie is beschikbaar voor openbare preview. Gebruik geen functie voor productie toepassingen.
>

Met leeftijds beperking in Azure Active Directory B2C (Azure AD B2C) kunt u minder jarigen identificeren die uw toepassing willen gebruiken. U kunt ervoor kiezen om de secundaire te blok keren om u aan te melden bij de toepassing. Gebruikers kunnen ook teruggaan naar de toepassing, hun leeftijds groep en de status van de ouderlijke toestemming identificeren. Azure AD B2C kunnen minder jarigen blok keren zonder toestemming van een ouder. Azure AD B2C kan ook zo worden ingesteld dat de toepassing kan bepalen wat met minder jarigen kan worden gedaan.

Nadat u leeftijds beperking in uw [gebruikers stroom](user-flow-overview.md)hebt ingeschakeld, worden gebruikers gevraagd wanneer ze geboren zijn en in welk land of welke regio ze wonen. Als een gebruiker zich aanmeldt dat de gegevens niet eerder zijn ingevoerd, moeten ze de volgende keer dat ze zich aanmelden invoeren. De regels worden toegepast telkens wanneer een gebruiker zich aanmeldt.

Azure AD B2C gebruikt de informatie die de gebruiker invoert om te bepalen of ze een kleine zijn. Het veld **ageGroup** wordt vervolgens bijgewerkt in hun account. De waarde kan,,, `null` `Undefined` `Minor` `Adult` en `NotAdult` .  De velden **ageGroup** en **consentProvidedForMinor** worden vervolgens gebruikt om de waarde van **legalAgeGroupClassification**te berekenen.

De leeftijds beperking omvat twee leeftijds waarden: de leeftijd die niet langer wordt beschouwd als een kleine, en de leeftijd waarbij een kleine persoon toestemming moet geven over ouders. De volgende tabel bevat de leeftijds regels die worden gebruikt voor het definiëren van een kleine en kleine, vereiste toestemming.

| Land/regio | Naam van land/regio | Duur van de secundaire toestemming | Secundaire leeftijd |
| -------------- | ------------------- | ----------------- | --------- |
| Standaard | Geen | Geen | 18 |
| AE | Verenigde Arabische Emiraten | Geen | 21 |
| AT | Oostenrijk | 14 | 18 |
| BE | België | 14 | 18 |
| BG | Bulgarije | 16 | 18 |
| BH | Bahrein | Geen | 21 |
| CM | Kameroen | Geen | 21 |
| CY | Cyprus | 16 | 18 |
| CZ | Tsjechische Republiek | 16 | 18 |
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
| NA | Namibië | Geen | 21 |
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

## <a name="age-gating-options"></a>Opties voor leeftijds beperking

### <a name="allowing-minors-without-parental-consent"></a>Minder jarigen toestaan zonder ouderlijke toestemming

Voor gebruikers stromen die registreren, aanmelden of beide toestaan, kunt u minder jarigen toestaan zonder toestemming te geven aan uw toepassing. Minder jarigen zonder toestemming van een ouder recht zijn toegestaan om zich aan te melden of zich als normaal aan te melden en Azure AD B2C een ID-token met de claim van de **legalAgeGroupClassification** uit te geven. Met deze claim wordt de ervaring gedefinieerd die gebruikers hebben, zoals het verzamelen van de ouderlijke toestemming en het bijwerken van het veld **consentProvidedForMinor** .

### <a name="blocking-minors-without-parental-consent"></a>Minder jarigen blok keren zonder ouderlijke toestemming

Voor gebruikers stromen die registratie toestaan, aanmelden of beide, kunt u minder jarigen blok keren zonder toestemming van de toepassing. De volgende opties zijn beschikbaar voor het afhandelen van geblokkeerde gebruikers in Azure AD B2C:

- Een JSON terugsturen naar de toepassing: met deze optie wordt een antwoord verzonden naar de toepassing die een secundaire is geblokkeerd.
- Een fout pagina weer geven: er wordt een pagina weer gegeven waarin de gebruiker wordt geïnformeerd dat ze geen toegang tot de toepassing hebben.

## <a name="set-up-your-tenant-for-age-gating"></a>Uw Tenant instellen voor leeftijds beperking

Als u leeftijds beperking in een gebruikers stroom wilt gebruiken, moet u uw Tenant configureren voor extra eigenschappen.

1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu. Selecteer de map die uw Tenant bevat.
2. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal, zoek en selecteer **Azure AD B2C**.
3. Selecteer **Eigenschappen** voor uw Tenant in het menu aan de linkerkant.
2. Klik onder de sectie **leeftijds beperking** op **configureren**.
3. Wacht totdat de bewerking is voltooid en uw Tenant is ingesteld voor leeftijds beperking.

## <a name="enable-age-gating-in-your-user-flow"></a>Leeftijds beperking inschakelen in uw gebruikers stroom

Nadat uw Tenant is ingesteld voor het gebruik van leeftijd beperking, kunt u deze functie gebruiken in [gebruikers stromen](user-flow-versions.md) waar deze is ingeschakeld. U schakelt leeftijds beperking in met de volgende stappen:

1. Maak een gebruikers stroom waarvoor leeftijds beperking is ingeschakeld.
2. Nadat u de gebruikers stroom hebt gemaakt, selecteert u **Eigenschappen** in het menu.
3. Selecteer in de sectie **leeftijds beperking** de optie **ingeschakeld**.
4. Vervolgens bepaalt u hoe u gebruikers wilt beheren die als minder jarigen worden geïdentificeerd. Als u **zich wilt aanmelden of aanmelden**, selecteert u `Allow minors to access your application` of `Block minors from accessing your application` . Als blokkerende minder jarigen is geselecteerd, selecteert u `Send a JSON back to the application` of `Show an error message` .




