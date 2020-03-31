---
title: Risicodetecties simuleren in Azure AD-identiteitsbeveiliging
description: Meer informatie over het simuleren van risicodetecties in Identiteitsbescherming
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886933"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Risicodetecties simuleren in identiteitsbescherming

Beheerders kunnen risico's in hun omgeving simuleren om de volgende items te bereiken:

- Vul gegevens in de omgeving Identiteitsbescherming door risicodetecties en -kwetsbaarheden te simuleren.
- Stel op risico's gebaseerd beleid voor voorwaardelijke toegang in en test de impact van dit beleid.

In dit artikel vindt u stappen voor het simuleren van de volgende typen risicodetectie:

- Anoniem IP-adres (eenvoudig)
- Onbekende aanmeldingseigenschappen (matig)
- Atypische reizen (moeilijk)

Andere risicodetecties kunnen niet op een veilige manier worden gesimuleerd.

Meer informatie over elke risicodetectie is te vinden in het [artikel, Wat is risico](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anoniem IP-adres

Voor het voltooien van de volgende procedure moet u het volgende gebruiken:

- De [Tor-browser](https://www.torproject.org/projects/torbrowser.html.en) om anonieme IP-adressen te simuleren. Mogelijk moet u een virtuele machine gebruiken als uw organisatie het gebruik van de Tor-browser beperkt.
- Een testaccount dat nog niet is geregistreerd voor Azure Multi-Factor Authentication.

**Voer de volgende stappen uit om een aanmelding te simuleren vanuit een anoniem IP-adres:**

1. Navigeer met de [Tor-browser](https://www.torproject.org/projects/torbrowser.html.en)naar [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Voer de referenties in van het account dat u wilt weergeven in de **aanmeldingen van het rapport Anonieme IP-adressen.**

De aanmelding verschijnt binnen 10 - 15 minuten op het dashboard identity protection. 

## <a name="unfamiliar-sign-in-properties"></a>Onbekende aanmeldingseigenschappen

Om onbekende locaties te simuleren, moet u zich aanmelden vanaf een locatie en apparaat dat uw testaccount nog niet van tevoren heeft aangemeld.

De onderstaande procedure maakt gebruik van een nieuw gecreëerd:

- VPN-verbinding, om nieuwe locatie te simuleren.
- Virtuele machine, om een nieuw apparaat te simuleren.

Voor het voltooien van de volgende procedure moet u een gebruikersaccount gebruiken met:

- Ten minste een 30-daagse aanmeldingsgeschiedenis.
- Azure Multi-Factor Authentication ingeschakeld.

**Voer de volgende stappen uit om een aanmelding vanaf een onbekende locatie te simuleren:**

1. Wanneer u zich aanmeldt met uw testaccount, mislukt u de multi-factor authentication (MFA) uitdaging door de MFA-uitdaging niet te halen.
2. Met behulp van uw [https://myapps.microsoft.com](https://myapps.microsoft.com) nieuwe VPN, navigeer naar en voer de referenties van uw testaccount.

De aanmelding verschijnt binnen 10 - 15 minuten op het dashboard identity protection.

## <a name="atypical-travel"></a>Atypische reizen

Het simuleren van de atypische reistoestand is moeilijk omdat het algoritme machine learning gebruikt om false-positives uit te wieden, zoals atypische reizen vanaf bekende apparaten, of aanmeldingen van VPN's die door andere gebruikers in de directory worden gebruikt. Bovendien vereist het algoritme een aanmeldingsgeschiedenis van 14 dagen en 10 aanmeldingen van de gebruiker voordat het begint met het genereren van risicodetecties. Door de complexe machine learning-modellen en bovenstaande regels bestaat de kans dat de volgende stappen niet leiden tot een risicodetectie. U deze stappen voor meerdere Azure AD-accounts repliceren om deze detectie te simuleren.

**Voer de volgende stappen uit om een atypische reisrisicodetectie te simuleren:**

1. Navigeer met uw standaardbrowser naar [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Voer de referenties in van het account waarvoor u een atypische reisrisicodetectie wilt genereren.
3. Wijzig uw gebruikersagent. U de gebruikersagent in Microsoft Edge wijzigen vanuit Hulpprogramma's voor ontwikkelaars (F12).
4. Wijzig uw IP-adres. U uw IP-adres wijzigen door een VPN, een Tor-add-on of een nieuwe virtuele machine in Azure te maken in een ander datacenter.
5. Meld u [https://myapps.microsoft.com](https://myapps.microsoft.com) aan bij het gebruik van dezelfde referenties als voorheen en binnen enkele minuten na de vorige aanmelding.

De aanmelding wordt binnen 2-4 uur weergegeven in het dashboard Identiteitsbeveiliging.

## <a name="testing-risk-policies"></a>Risicobeleid testen

In deze sectie vindt u stappen voor het testen van de gebruiker en het aanmeldingsrisicobeleid dat in het artikel is gemaakt, [How To: Configureren en inschakelen van risicobeleidsregels](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Gebruikersrisicobeleid

Voer de volgende stappen uit om een beveiligingsbeleid voor gebruikersrisico's te testen:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
1. Blader naar Azure Active > **Directory-beveiligingsoverzicht** > **Overview**. **Azure Active Directory**
1. Selecteer **Gebruikersrisicobeleid configureren**.
   1. Onder **Opdrachten**
      1. **Gebruikers** - Kies **Alle gebruikers** of Selecteer personen **en groepen** als u uw implementatie beperkt.
         1. Optioneel u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voorwaarden** - **Gebruikersrisico** De aanbeveling van Microsoft is om deze optie in te stellen op **Hoog.**
   1. Onder **Besturingselementen**
      1. **Toegang** - De aanbeveling van Microsoft is om **toegang toe** te staan en **wachtwoordwijziging vereisen.**
   1. **Beleid** - **afdwingen**
   1. **Opslaan** - Met deze actie wordt u teruggegeven aan de pagina **Overzicht.**
1. Verhoog het gebruikersrisico van een testaccount door bijvoorbeeld een paar keer een van de risicodetecties te simuleren.
1. Wacht een paar minuten en controleer vervolgens of het risico voor uw gebruiker is verhoogd. Zo niet, simuleer dan meer risicodetecties voor de gebruiker.
1. Ga terug naar uw risicobeleid en stel **Beleid afdwingen** **in op Aan** en Sla uw beleidswijziging **op.**
1. U nu voorwaardelijke toegang voor gebruikersrisico's testen door u aan te melden met een gebruiker met een verhoogd risiconiveau.

### <a name="sign-in-risk-security-policy"></a>Aanmeldingsrisicobeveiligingsbeleid

Voer de volgende stappen uit om een aanmeldingsrisicobeleid te testen:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
1. Blader naar Azure Active > **Directory-beveiligingsoverzicht** > **Overview**. **Azure Active Directory**
1. Selecteer **Aanmeldingsrisicobeleid configureren**.
   1. Onder **Opdrachten**
      1. **Gebruikers** - Kies **Alle gebruikers** of Selecteer personen **en groepen** als u uw implementatie beperkt.
         1. Optioneel u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voorwaarden** - **Aanmeldingsrisico** De aanbeveling van Microsoft is om deze optie in te stellen op **Medium en hoger.**
   1. Onder **Besturingselementen**
      1. **Toegang** - De aanbeveling van Microsoft is om **toegang toe** te staan en meervoudige **verificatie vereisen.**
   1. **Beleid afdwingen** - **op**
   1. **Opslaan** - Met deze actie wordt u teruggegeven aan de pagina **Overzicht.**
1. U nu voorwaardelijke toegang op basis van aanmeldingsrisico's testen door u aan te melden met een riskante sessie (bijvoorbeeld met de Tor-browser). 

## <a name="next-steps"></a>Volgende stappen

- [Wat is risico?](concept-identity-protection-risks.md)

- [How To: Risicobeleid configureren en inschakelen](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory-identiteitsbeveiliging](overview-identity-protection.md)
