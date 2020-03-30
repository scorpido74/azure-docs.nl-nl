---
title: Dynamisch verbannen wachtwoorden - Azure Active Directory
description: Zwakke wachtwoorden uit uw omgeving verbannen met Azure AD-wachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263996"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Onjuiste wachtwoorden uit uw organisatie verwijderen

Marktleiders vertellen u om hetzelfde wachtwoord niet op meerdere plaatsen te gebruiken, om het complex te maken en het niet eenvoudig te maken zoals "Password123". Hoe kunnen organisaties garanderen dat hun gebruikers de richtlijnen voor de beste praktijken volgen? Hoe kunnen ze ervoor zorgen dat gebruikers geen zwakke wachtwoorden gebruiken, of zelfs variaties op zwakke wachtwoorden?

De eerste stap in het hebben van sterkere wachtwoorden is om begeleiding te bieden aan uw gebruikers. De huidige richtlijnen van Microsoft over dit onderwerp zijn te vinden op de volgende link:

[Richtlijnen voor Microsoft-wachtwoorden](https://www.microsoft.com/research/publication/password-guidance)

Het hebben van goede begeleiding is belangrijk, maar zelfs met dat weten we dat veel gebruikers nog steeds uiteindelijk kiezen voor zwakke wachtwoorden. Azure AD Password Protection beschermt uw organisatie door bekende zwakke wachtwoorden en hun varianten te detecteren en te blokkeren, evenals optioneel extra zwakke termen te blokkeren die specifiek zijn voor uw organisatie.

Zie het Microsoft Security Intelligence [Report](https://www.microsoft.com/security/operations/security-intelligence-report)voor meer informatie over de huidige beveiligingsinspanningen.

## <a name="global-banned-password-list"></a>Lijst met wereldwijd verboden wachtwoorden

Het Azure AD Identity Protection-team analyseert voortdurend telemetriegegevens van Azure AD-beveiliging op zoek naar veelgebruikte zwakke of gecompromitteerde wachtwoorden, of meer specifiek, de zwakke basistermen die vaak worden gebruikt als basis voor zwakke wachtwoorden. Wanneer dergelijke zwakke termen worden gevonden, worden ze toegevoegd aan de wereldwijde lijst met verboden wachtwoorden. De inhoud van de lijst met wereldwijd verboden wachtwoorden is niet gebaseerd op externe gegevensbronnen. De wereldwijde lijst met verboden wachtwoorden is volledig gebaseerd op de lopende resultaten van azure AD-beveiligingstelemetrie en -analyse.

Wanneer een nieuw wachtwoord wordt gewijzigd of opnieuw ingesteld voor een gebruiker in een tenant in Azure AD, wordt de huidige versie van de lijst met algemene geblokkeerde wachtwoorden gebruikt als de belangrijkste invoer wanneer de sterkte van het wachtwoord wordt geldig. Deze validatie resulteert in veel sterkere wachtwoorden voor alle Azure AD-klanten.

> [!NOTE]
> Cybercriminelen gebruiken ook soortgelijke strategieën in hun aanvallen. Daarom publiceert Microsoft de inhoud van deze lijst niet openbaar.

## <a name="custom-banned-password-list"></a>Aangepaste lijst met verboden wachtwoorden

Sommige organisaties willen de beveiliging misschien nog verder verbeteren door hun eigen aanpassingen toe te voegen bovenop de lijst met wereldwijd verboden wachtwoorden in wat Microsoft de aangepaste lijst met verboden wachtwoorden noemt. Microsoft raadt aan dat de aan deze lijst toegevoegde termen voornamelijk gericht zijn op organisatiespecifieke termen zoals:

- Merknamen
- Productnamen
- Locaties (bijvoorbeeld het hoofdkantoor van het bedrijf)
- Bedrijfsspecifieke interne voorwaarden
- Afkortingen die een specifieke bedrijfsbetekenis hebben.

Zodra de voorwaarden worden toegevoegd aan de aangepaste lijst met verboden wachtwoorden, worden ze gecombineerd met de termen in de lijst met het wereldwijde geblokkeerde wachtwoord bij het valideren van wachtwoorden.

> [!NOTE]
> De aangepaste lijst met verboden wachtwoorden is beperkt tot maximaal 1000 termen. Het is niet ontworpen voor het blokkeren van extreem grote lijsten met wachtwoorden. Om de voordelen van de aangepaste lijst met verboden wachtwoorden volledig te benutten, raadt Microsoft u aan eerst het algoritme voor wachtwoordevaluatie (zie [Hoe worden wachtwoorden geëvalueerd)](concept-password-ban-bad.md#how-are-passwords-evaluated)te bekijken en te begrijpen voordat u nieuwe termen toevoegt aan de aangepaste verboden lijst. Als u begrijpt hoe het algoritme werkt, kan uw onderneming grote aantallen zwakke wachtwoorden en hun varianten efficiënt detecteren en blokkeren.

Denk bijvoorbeeld aan een klant met de naam "Contoso", die is gevestigd in Londen, en dat maakt een product met de naam "Widget". Voor een dergelijke klant zou het zowel verspillend als minder veilig zijn om te proberen specifieke variaties van deze termen te blokkeren, zoals:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ... Etcetera

In plaats daarvan is het veel efficiënter en veiliger om alleen de belangrijkste basistermen te blokkeren:

- Contoso
- "Londen"
- "Widget"

Het wachtwoordvalidatiealgoritme blokkeert dan automatisch zwakke varianten en combinaties van het bovenstaande.

De aangepaste lijst met verboden wachtwoorden en de mogelijkheid om on-premises Active Directory-integratie in te schakelen, worden beheerd met behulp van de Azure-portal.

![De lijst met aangepaste geblokkeerde wachtwoorden wijzigen onder Verificatiemethoden](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Wachtwoord spray aanvallen en derden gecompromitteerde wachtwoordlijsten

Een belangrijk voordeel voor wachtwoordbescherming van Azure AD is om u te helpen zich te verdedigen tegen aanvallen met wachtwoordspray. De meeste wachtwoord spray aanvallen niet proberen om een bepaald individueel account aan te vallen meer dan een paar keer, omdat dergelijk gedrag sterk verhoogt de kans op detectie, hetzij via account lockout of andere middelen. De meerderheid van de wachtwoord spray aanvallen dus vertrouwen op het indienen van slechts een klein aantal van de bekende zwakste wachtwoorden tegen elk van de rekeningen in een onderneming. Met deze techniek kan de aanvaller snel zoeken naar een gemakkelijk gecompromitteerd account en tegelijkertijd potentiële detectiedrempels vermijden.

Azure AD-wachtwoordbeveiliging is ontworpen om alle bekende zwakke wachtwoorden die waarschijnlijk worden gebruikt bij wachtwoordspray-aanvallen efficiënt te blokkeren, op basis van telemetriegegevens over realtime beveiliging, zoals die worden gezien door Azure AD.  Microsoft is op de hoogte van websites van derden die miljoenen wachtwoorden opsommen die zijn gecompromitteerd in eerdere bekende inbreuken op de beveiliging. Het is gebruikelijk dat producten voor wachtwoordvalidatie van derden worden gebaseerd op een vergelijking met brute kracht met die miljoenen wachtwoorden. Microsoft is van mening dat dergelijke technieken zijn niet de beste manier om de algehele wachtwoord sterkte te verbeteren gezien de typische strategieën die worden gebruikt door wachtwoord spray aanvallers.

> [!NOTE]
> De lijst met wereldwijd verboden wachtwoorden van Microsoft is helemaal niet gebaseerd op gegevensbronnen van derden, inclusief gecompromitteerde wachtwoordlijsten.

Hoewel de Microsoft global banned lijst is klein in vergelijking met sommige derden bulk lijsten, de effecten worden versterkt door het feit dat het afkomstig is van real-world security telemetrie op de werkelijke wachtwoord spray aanvallen, plus het feit dat de Microsoft wachtwoord validatie algoritme maakt gebruik van slimme fuzzy-matching technieken. Het eindresultaat is dat het efficiënt zal detecteren en blokkeren miljoenen van de meest voorkomende zwakke wachtwoorden worden gebruikt in uw onderneming. Klanten die ervoor kiezen om organisatiespecifieke termen toe te voegen aan de aangepaste lijst met verboden wachtwoorden profiteren ook van hetzelfde algoritme.

Aanvullende informatie over beveiligingsproblemen op basis van wachtwoorden kan worden beoordeeld op [uw Pa$$word maakt niet uit.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

## <a name="on-premises-hybrid-scenarios"></a>On-premises hybride scenario's

Het beveiligen van alleen cloudaccounts is handig, maar veel organisaties onderhouden hybride scenario's, waaronder on-premises Windows Server Active Directory. De beveiligingsvoordelen van Azure AD-wachtwoordbeveiliging kunnen ook worden uitgebreid naar uw Windows Server Active Directory-omgeving via de installatie van on-premises agents. Gebruikers en beheerders die wachtwoorden in Active Directory wijzigen of opnieuw instellen, moeten nu voldoen aan hetzelfde wachtwoordbeleid als alleen cloudgebruikers.

## <a name="how-are-passwords-evaluated"></a>Hoe worden wachtwoorden geëvalueerd

Wanneer een gebruiker zijn wachtwoord wijzigt of opnieuw instelt, wordt het nieuwe wachtwoord gecontroleerd op sterkte en complexiteit door het te valideren aan de hand van de gecombineerde lijst met termen uit de algemene en aangepaste lijsten met verboden wachtwoorden (als deze is geconfigureerd).

Zelfs als het wachtwoord van een gebruiker een verboden wachtwoord bevat, kan het wachtwoord nog steeds worden geaccepteerd als het algemene wachtwoord anders sterk genoeg is. Een nieuw geconfigureerd wachtwoord gaat door de volgende stappen om de algehele sterkte ervan te beoordelen om te bepalen of het moet worden geaccepteerd of afgewezen.

### <a name="step-1-normalization"></a>Stap 1: Normalisatie

Een nieuw wachtwoord gaat eerst door een normalisatieproces. Deze techniek maakt het mogelijk voor een kleine set van verboden wachtwoorden in kaart worden gebracht om een veel grotere set van potentieel zwakke wachtwoorden.

Normalisatie bestaat uit twee delen.  Ten eerste worden alle hoofdletters gewijzigd in kleine letters.  Ten tweede worden gemeenschappelijke tekenvervangingen uitgevoerd, bijvoorbeeld:  

| Originele brief  | Vervangen brief |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | "a" |

Voorbeeld: stel dat het wachtwoord 'leeg' is geblokkeerd en datBl@nKeen gebruiker zijn wachtwoord probeert te wijzigen in " ". Hoewel "Bl@nkniet specifiek verboden is, zet het normalisatieproces dit wachtwoord om in "blanco", wat een verboden wachtwoord is.

### <a name="step-2-check-if-password-is-considered-banned"></a>Stap 2: Controleer of wachtwoord als verboden wordt beschouwd

#### <a name="fuzzy-matching-behavior"></a>Fuzzy matching gedrag

Fuzzy matching wordt gebruikt op het genormaliseerde wachtwoord om te bepalen of het een wachtwoord bevat dat is gevonden op de algemene of aangepaste geblokkeerde wachtwoordlijsten. Het matchingproces is gebaseerd op een bewerkingsafstand van één (1) vergelijking.  

Voorbeeld: ga ervan uit dat het wachtwoord "abcdef" is verboden en dat een gebruiker probeert zijn wachtwoord te wijzigen in een van de volgende opties:

'abcdeg' *(laatste personage veranderd van 'f' naar 'g')* 'abcdefg' *'(g' toegevoegd aan het einde)* 'abcde' *(trailing 'f' werd van het einde verwijderd)*

Elk van de bovenstaande wachtwoorden komt niet specifiek overeen met het verboden wachtwoord "abcdef". Echter, omdat elk voorbeeld is binnen een bewerkingsafstand van 1 van de verboden term 'abcdef', worden ze allemaal beschouwd als een match met "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Subtekenreeksmatching (onder specifieke voorwaarden)

Substring matching wordt gebruikt op het genormaliseerde wachtwoord om te controleren op de voor- en achternaam van de gebruiker en de tenantnaam (merk op dat tenantnaammatching niet wordt gedaan bij het valideren van wachtwoorden op een Active Directory-domeincontroller).

Voorbeeld: stel dat we een gebruiker hebben, Pol, die zijn wachtwoord wil resetten naar "P0l123fb". Na de normalisatie zou dit wachtwoord "pol123fb" worden. Substring matching vindt dat het wachtwoord de voornaam van de gebruiker "Pol" bevat. Hoewel "P0l123fb" was niet specifiek op een van beide verboden wachtwoord lijst, substring matching gevonden "Pol" in het wachtwoord. Daarom zou dit wachtwoord worden afgewezen.

#### <a name="score-calculation"></a>Scoreberekening

De volgende stap is het identificeren van alle exemplaren van verboden wachtwoorden in het genormaliseerde nieuwe wachtwoord van de gebruiker. Daarna kunt u het volgende doen:

1. Elk verboden wachtwoord dat wordt gevonden in het wachtwoord van een gebruiker krijgt één punt.
2. Elk overgebleven uniek karakter krijgt één punt.
3. Een wachtwoord moet ten minste vijf (5) punten zijn om het te accepteren.

Voor de volgende twee voorbeelden, laten we aannemen dat Contoso azure AD Password Protection gebruikt en "contoso" op hun aangepaste lijst heeft. Laten we er ook van uitgaan dat 'leeg' op de globale lijst staat.

Voorbeeld: een gebruiker wijzigt zijn wachtwoord in "C0ntos0Blank12"

Na normalisatie wordt dit wachtwoord "contosoblank12". Het matchingproces stelt vast dat dit wachtwoord twee verboden wachtwoorden bevat: contoso en blank. Dit wachtwoord krijgt dan een score:

[contoso] + [blanco] + [1] + [2] = 4 punten Aangezien dit wachtwoord onder vijf (5) punten is, wordt het afgewezen.

Voorbeeld: een gebruiker wijzigtContoS0Bl@nkf9zijn wachtwoord in " !".

Na normalisatie wordt dit wachtwoord "contosoblankf9!". Het matchingproces stelt vast dat dit wachtwoord twee verboden wachtwoorden bevat: contoso en blank. Dit wachtwoord krijgt dan een score:

[contoso] + [blanco] + [f] + [9] + [!] = 5 punten Aangezien dit wachtwoord ten minste vijf (5) punten is, wordt het geaccepteerd.

   > [!IMPORTANT]
   > Houd er rekening mee dat het verbannen wachtwoordalgoritme samen met de globale lijst op elk gewenst moment in Azure kan en kan veranderen op basis van lopende beveiligingsanalyse en -onderzoek. Voor de on-premises DC-agentservice worden bijgewerkte algoritmen pas van kracht nadat de DC-agentsoftware opnieuw is geïnstalleerd.

## <a name="license-requirements"></a>Licentievereisten

|   | Azure AD-wachtwoordbeveiliging met wereldwijde lijst met verboden wachtwoorden | Azure AD-wachtwoordbeveiliging met aangepaste lijst met verboden wachtwoorden|
| --- | --- | --- |
| Alleen in de cloud | Azure AD Free | Azure AD Premium P1 of P2 |
| Gebruikers gesynchroniseerd vanuit on-premises Windows Server Active Directory | Azure AD Premium P1 of P2 | Azure AD Premium P1 of P2 |

> [!NOTE]
> On-premises Windows Server Active Directory-gebruikers die niet zijn gesynchroniseerd met Azure Active Directory, profiteren ook van Azure AD-wachtwoordbeveiliging op basis van bestaande licenties voor gesynchroniseerde gebruikers.

Aanvullende licentiegegevens, inclusief kosten, zijn te vinden op de [azure Active Directory-prijssite.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="what-do-users-see"></a>Wat gebruikers zien

Wanneer een gebruiker probeert een wachtwoord opnieuw in te stellen op iets dat zou worden verboden, ziet hij het volgende foutbericht:

Helaas bevat uw wachtwoord een woord, woordgroep of patroon dat uw wachtwoord gemakkelijk te raden maakt. Probeer het opnieuw met een ander wachtwoord.

## <a name="next-steps"></a>Volgende stappen

- [De lijst met aangepaste geblokkeerde wachtwoorden configureren](howto-password-ban-bad.md)
- [On-premises Azure AD-wachtwoordbeveiligingsagents inschakelen](howto-password-ban-bad-on-premises-deploy.md)
