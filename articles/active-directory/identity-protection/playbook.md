---
title: Azure Active Directory Identity Protection Playbook | Microsoft Docs
description: Meer informatie over hoe u met Azure AD Identity Protection kunt u de mogelijkheid van een aanvaller om misbruik te maken van een identiteit of apparaat te beperken en om een identiteit of een apparaat te beveiligen dat eerder is vermoed of waarvan bekend is dat deze zijn aangetast.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fcf24256634ef11b575348d9da7d6bbbab8b67c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127772"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection Playbook

Met deze Playbook kunt u het volgende doen:

* Gegevens in de identiteits beschermings omgeving vullen door risico detecties en beveiligings problemen te simuleren
* Beleid voor voorwaardelijke toegang op basis van een risico instellen en de impact van dit beleid testen

## <a name="simulating-risk-detections"></a>Risico detecties simuleren

In deze sectie worden de stappen beschreven voor het simuleren van de volgende typen risico detectie:

* Aanmeldingen vanaf anonieme IP-adressen (eenvoudig)
* Aanmeldingen vanaf onbekende locaties (gemiddeld)
* Onmogelijke reis naar ongewone locaties (moeilijk)

Andere risico detecties kunnen niet op een veilige manier worden gesimuleerd.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Aanmeldingen vanaf anonieme IP-adressen

Zie aanmeldingen [vanaf anonieme IP-adressen](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses)voor meer informatie over deze risico detectie. 

Als u de volgende procedure wilt uitvoeren, moet u gebruikmaken van:

- De [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) om anonieme IP-adressen te simuleren. Mogelijk moet u een virtuele machine gebruiken als uw organisatie het gebruik van de Tor-browser beperkt.
- Een test account dat nog niet is geregistreerd voor multi-factor Authentication.

**Als u een aanmelding vanaf een anoniem IP-adres wilt simuleren, voert u de volgende stappen**uit:

1. Navigeer met behulp van de Tor [https://myapps.microsoft.com](https://myapps.microsoft.com)- [browser](https://www.torproject.org/projects/torbrowser.html.en)naar.   
2. Gebruik de referenties van het account dat moet worden weergegeven in het rapport **aanmeldingen vanaf anonieme IP-adressen**.

De aanmeldingspagina wordt binnen 10-15 minuten weergegeven op het Identity Protection-dashboard. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Aanmeldingen vanaf onbekende locaties

Zie aanmeldingen [vanaf onbekende locaties](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations)voor meer informatie over deze risico detectie. 

Om aanmeldingen vanaf onbekende locaties te simuleren, dient zich u aan te melden vanaf een locatie en een apparaat waar u zich nog niet eerder hebt aangemeld met uw testaccount.

In de volgende procedure wordt een nieuw gemaakt:

- VPN-verbinding voor het simuleren van een nieuwe locatie.
- Virtuele machine, om een nieuw apparaat te simuleren.

Om de volgende procedure te voltooien, moet u een gebruikersaccount gebruiken dat voldoet aan de volgende voorwaarden:

- Minstens een aanmeldings geschiedenis van 30 dagen.
- Multi-factor Authentication is ingeschakeld.

**Voer de volgende stappen uit om een aanmelding vanaf een onbekende locatie te simuleren**:

1. Laat bij het aanmelden met uw testaccount de MFA-controle mislukken.
2. Ga met uw nieuwe VPN naar en [https://myapps.microsoft.com](https://myapps.microsoft.com) Voer de referenties van uw test account in.

De aanmeldingspagina wordt binnen 10-15 minuten weergegeven op het Identity Protection-dashboard.

### <a name="impossible-travel-to-atypical-location"></a>Onmogelijke reis naar ongewone locatie

Zie voor meer informatie over deze risico detectie [onmogelijk reizen naar ongewoone locatie](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Het simuleren van de niet-bewaarde reis voorwaarde is lastig, omdat de algoritme gebruikmaakt van machine learning om onbedoelde positieve waarden te vervalsen, zoals niet-verplaatsingen van bekende apparaten, of aanmeldingen vanuit Vpn's die worden gebruikt door andere gebruikers in de Directory. Daarnaast is voor de algoritme een aanmeldings geschiedenis van 14 dagen en 10 aanmeldingen van de gebruiker vereist voordat het genereren van de risico detectie begint. Vanwege de complexe machine learning modellen en de bovenstaande regels is er een kans dat de volgende stappen niet leiden tot een risico detectie. Mogelijk wilt u deze stappen voor meerdere Azure AD-accounts repliceren om deze risico detectie te publiceren.

**Voer de volgende stappen uit om een onmogelijke reis naar een ongewone locatie te simuleren**:

1. Ga in uw standaard browser naar [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Voer de referenties in van het account waarvoor u een onmogelijk risico detectie voor de reis wilt genereren.
3. Wijzig uw gebruikersagent. U kunt uw gebruikersagent in Internet Explorer wijzigen vanuit Ontwikkelhulpprogramma's of in Firefox of Chrome door een invoegtoepassing voor het wisselen van de gebruikersagent te gebruiken.
4. Wijzig uw IP-adres. U kunt uw IP-adres wijzigen door een VPN-of een Tor-invoeg toepassing te gebruiken of door een nieuwe machine in azure te draaien in een ander Data Center.
5. Meld u aan [https://myapps.microsoft.com](https://myapps.microsoft.com) met dezelfde referenties als voor en binnen een paar minuten na de vorige aanmelding.

De aanmelding wordt binnen 2-4 uur weer gegeven in het dash board voor identiteits beveiliging.

## <a name="simulating-vulnerabilities"></a>Beveiligings problemen simuleren
Zwakke plekken zijn zwakke punten in een Azure AD-omgeving die door een kwaadwillend persoon kunnen worden misbruikt. Momenteel zijn er 3 typen zwakke plekken in Azure AD Identity Protection bekend die gebruikmaken van andere functies van Azure AD. Deze beveiligingslekken worden automatisch weergegeven op het Identity Protection-dashboard zodra deze functies zijn ingesteld.

* Azure AD- [multi-factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD- [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD- [privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="testing-security-policies"></a>Beveiligings beleid testen

In deze sectie wordt beschreven hoe u het gebruikers risico en het beveiligings beleid voor aanmeldings Risico's kunt testen.

### <a name="user-risk-security-policy"></a>Beveiligings beleid voor gebruikers Risico's

Zie [Het risicobeleid voor gebruikers configureren](howto-user-risk-policy.md) voor meer informatie.

![Gebruikers risico](./media/playbook/02.png "Playbook")

**Als u een beveiligings beleid voor gebruikers Risico's wilt testen, voert u de volgende stappen uit**:

1. Meld u aan [https://portal.azure.com](https://portal.azure.com) met de referenties van de globale beheerder voor uw Tenant.
2. Navigeer naar **identiteits beveiliging**. 
3. Klik op de pagina **Azure AD Identity Protection** op **beleid voor gebruikers Risico's**.
4. Selecteer in de sectie **toewijzingen** de gewenste gebruikers (en groepen) en het risico niveau van de gebruiker.

    ![Gebruikers risico](./media/playbook/03.png "Playbook")

5. Selecteer in de sectie besturings elementen het gewenste toegangs beheer (bijvoorbeeld vereisen dat het wacht woord is gewijzigd).
5. Selecteer **uit**als **beleid afdwingen**.
6. Het gebruikers risico van een test account verhogen door bijvoorbeeld een van de risico detecties een paar keer te simuleren.
7. Wacht een paar minuten en controleer vervolgens of het gebruikers niveau voor uw gebruiker gemiddeld is. Als dat niet het geval is, Simuleer dan meer risico detecties voor de gebruiker.
8. Selecteer **aan**als **beleid afdwingen**.
9. U kunt nu voorwaardelijke toegang op basis van gebruikers risico testen door zich aan te melden met een gebruiker met een verhoogd risico niveau.

### <a name="sign-in-risk-security-policy"></a>Beveiligings beleid voor aanmeldings Risico's

Zie [Het risicobeleid voor aanmelden configureren](howto-sign-in-risk-policy.md) voor meer informatie.

![Aanmeldings risico](./media/playbook/01.png "Playbook")

**Voer de volgende stappen uit om een beleid voor aanmeldings Risico's te testen:**

1. Meld u aan [https://portal.azure.com](https://portal.azure.com) met de referenties van de globale beheerder voor uw Tenant.
2. Navigeer naar **Azure AD Identity Protection**.
3. Klik op de pagina hoofd **Azure AD Identity Protection** op **aanmeldings risico beleid**. 
4. Selecteer in de sectie **toewijzingen** de gewenste gebruikers (en groepen) en het risico niveau voor aanmelden.

    ![Aanmeldings risico](./media/playbook/04.png "Playbook")

5. Selecteer in de sectie **besturings elementen** het gewenste toegangs beheer (bijvoorbeeld **multi-factor Authentication vereisen**). 
6. Selecteer **aan**als **beleid afdwingen**.
7. Klik op **Opslaan**.
8. U kunt nu de op Risico's gebaseerde voorwaardelijke toegang testen door u aan te melden met behulp van een Risk ante sessie (bijvoorbeeld met behulp van de Tor-browser). 

## <a name="see-also"></a>Zie ook

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
