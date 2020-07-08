---
title: Dynamisch verboden wacht woorden-Azure Active Directory
description: Zwakke wacht woorden van uw omgeving verzwakken met dynamisch uitgesloten wacht woorden van Azure AD
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
ms.openlocfilehash: 0f905b3eb6d1675f0bc252c3500169b3144287d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550698"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Onjuiste wachtwoorden uit uw organisatie verwijderen

Industrie leiders weten dat u niet hetzelfde wacht woord op meerdere locaties kunt gebruiken om het complex te maken en om het niet eenvoudig te maken zoals ' Password123 '. Hoe kunnen organisaties garanderen dat hun gebruikers de volgende best practice-richt lijnen volgen? Hoe kunnen ze ervoor zorgen dat gebruikers geen zwakke wacht woorden gebruiken of zelfs variaties in zwakke wacht woorden?

De eerste stap bij het hebben van sterkere wacht woorden is het bieden van hulp aan uw gebruikers. De huidige richt lijnen voor dit onderwerp van micro soft zijn te vinden op de volgende koppeling:

[Richt lijnen voor micro soft-wacht woorden](https://www.microsoft.com/research/publication/password-guidance)

Het is belang rijk dat u weet dat veel gebruikers nog steeds zwakke wacht woorden kunnen kiezen. Met Azure AD-wachtwoord beveiliging kunt u uw organisatie beveiligen door bekende zwakke wacht woorden en hun varianten te detecteren en te blok keren, en desgewenst extra zwakke termen die specifiek zijn voor uw organisatie, te blok keren.

Zie het [micro soft Security Intelligence-rapport](https://www.microsoft.com/security/operations/security-intelligence-report)voor meer informatie over de huidige beveiligings inspanningen.

## <a name="global-banned-password-list"></a>Algemene lijst met geblokkeerde wacht woorden

Het Azure AD Identity Protection Team analyseert gegevens van de telemetrie van Azure AD-beveiliging die vaak worden gebruikt voor veelvoorkomende, zwakke of gemanipuleerde wacht woorden, of de zwakke basis termen die veelvuldig worden gebruikt als basis voor zwakke wacht woorden. Wanneer dergelijke zwakke voor waarden worden gevonden, worden deze toegevoegd aan de lijst met globale verboden wacht woorden. De inhoud van de lijst met globale verboden wacht woorden is niet gebaseerd op een externe gegevens bron. De algemene lijst met geblokkeerde wacht woorden is volledig gebaseerd op de lopende resultaten van de telemetrie en analyse van Azure AD-beveiliging.

Wanneer een nieuw wacht woord wordt gewijzigd of opnieuw wordt ingesteld voor een gebruiker in een Tenant in azure AD, wordt de huidige versie van de lijst met globale verboden wacht woorden gebruikt als de sleutel invoer bij het valideren van de sterkte van het wacht woord. Deze validatie resulteert in veel sterkere wacht woorden voor alle klanten van Azure AD.

> [!NOTE]
> Cyber criminelen gebruiken vergelijk bare strategieën in hun aanvallen. Daarom publiceert micro soft de inhoud van deze lijst openbaar niet.

## <a name="custom-banned-password-list"></a>Aangepaste lijst met verboden wacht woorden

Sommige organisaties willen de beveiliging nog verder verbeteren door hun eigen aanpassingen toe te voegen boven op de lijst met algemene verboden wacht woorden in wat micro soft de aangepaste lijst met geblokkeerde wacht woorden aanroept. Micro soft adviseert dat de voor waarden die zijn toegevoegd aan deze lijst voornamelijk gericht zijn op organisatie-specifieke voor waarden, zoals:

- Merk namen
- Product namen
- Locaties (bijvoorbeeld het hoofd kantoor van het bedrijf)
- Bedrijfsspecifieke interne termen
- Afkortingen die een specifieke betekenis van het bedrijf hebben.

Wanneer de voor waarden worden toegevoegd aan de lijst met aangepaste geblokkeerde wacht woorden, worden deze gecombineerd met de voor waarden in de lijst met globale verboden wacht woorden bij het valideren van wacht woord.

> [!NOTE]
> De aangepaste lijst met verboden wacht woorden is beperkt tot een maximum van 1000 voor waarden. Het is niet ontworpen voor het blok keren van extreem grote lijsten met wacht woorden. Als u de voor delen van de aangepaste lijst met geblokkeerde wacht woorden volledig wilt benutten, raadt micro soft u aan eerst de wachtwoord evaluatie algoritme te controleren en te begrijpen (Zie [hoe wacht woorden worden geëvalueerd](concept-password-ban-bad.md#how-are-passwords-evaluated)) voordat nieuwe voor waarden worden toegevoegd aan de lijst met aangepaste verboden. Als u begrijpt hoe het algoritme werkt, kan uw onderneming een grote hoeveelheid zwakke wacht woorden en hun varianten efficiënt detecteren en blok keren.

Bijvoorbeeld: overweeg een klant met de naam contoso, die is gebaseerd op Londen, en die een product met de naam ' widget ' maakt. Voor een dergelijke klant zou het verspilling en minder veilig zijn om bepaalde variaties van deze voor waarden te blok keren, zoals:

- Contoso! 1
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"
- ... etcetera

In plaats daarvan is het veel efficiënter en veiliger om alleen de basis begrippen van de sleutel te blok keren:

- Contoso
- Meubel
- Opmaken

De wachtwoord validatie algoritme blokkeert vervolgens automatisch zwakke varianten en combi Naties van bovenstaande voor delen.

De aangepaste lijst met geblokkeerde wacht woorden en de mogelijkheid om on-premises Active Directory integratie in te scha kelen, wordt beheerd met behulp van de Azure Portal.

![De aangepaste lijst met verboden wacht woorden onder verificatie methoden wijzigen](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Wachtwoord sproei-aanvallen en lijsten met wacht woorden van derden

Een van de belangrijkste voor delen van Azure AD-wachtwoord beveiliging is om u te helpen bij het beschermen van aanvallen op wachtwoord spray. De meeste aanvallen met een wacht woord worden niet meer dan een paar keer geprobeerd een aanval uit te voeren, omdat dit gedrag de kans op detectie aanzienlijk verg root, hetzij via account vergrendeling of op andere wijze. Het meren deel van wachtwoord spray-aanvallen is daarom afhankelijk van het verzenden van slechts een klein aantal bekende zwakke wacht woorden voor elk van de accounts in een onderneming. Met deze techniek kan de aanvaller snel een eenvoudig te vinden account zoeken, terwijl mogelijke drempel waarden voor detectie worden voor komen.

Azure AD-wachtwoord beveiliging is ontworpen voor het efficiënt blok keren van alle bekende zwakke wacht woorden die waarschijnlijk worden gebruikt in aanvallen met een wacht woord, op basis van de gegevens van de actuele beveiligings-telemetriegegevens, zoals wordt gezien door Azure AD.  Micro soft is op de hoogte van websites van derden die miljoenen wacht woorden opsommen die zijn aangetast in eerdere bekende inbreuken op de veiligheid. Het is gebruikelijk dat producten voor wachtwoord validatie van derden worden gebaseerd op de verdubbeling van een felle vergelijking met die miljoenen wacht woorden. Micro soft voelt dat dergelijke technieken niet de beste manier zijn om de algehele wachtwoord sterkte te verbeteren, gezien de typische strategieën die worden gebruikt door kwaadwillende gebruikers met een wacht woord.

> [!NOTE]
> De lijst met niet-micro soft-wacht woorden is niet gebaseerd op gegevens bronnen van derden, met inbegrip van gemanipuleerde wachtwoord lijsten.

Hoewel de lijst met algemene geblokkeerde micro soft-lijsten klein is in vergelijking met een bulk lijst van derden, worden de beveiligings effecten versterkt door het feit dat deze is gebrond op basis van de beveiligings-telemetrie van Real-World op effectief wacht woord voor wachtwoord spray, plus het feit dat het micro soft-wachtwoord validatie algoritme gebruikmaakt van slimme methoden voor het afstemmen van benadering. Het eind resultaat is dat hiermee miljoenen van de meest voorkomende zwakke wacht woorden efficiënt worden gedetecteerd en geblokkeerd voor gebruik in uw onderneming. Klanten die ervoor kiezen organisatie-specifieke voor waarden toe te voegen aan de aangepaste lijst met geblokkeerde wacht woorden, hebben ook voor deel van hetzelfde algoritme.

Meer informatie over beveiligings problemen op basis van wacht woorden kan worden gecontroleerd op [uw PA $ $Word niet van belang](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)is.

## <a name="on-premises-hybrid-scenarios"></a>On-premises hybride scenario's

Het beveiligen van alleen Cloud accounts is handig, maar veel organisaties onderhouden hybride scenario's met inbegrip van on-premises Windows Server-Active Directory. De beveiligings voordelen van Azure AD-wachtwoord beveiliging kunnen ook worden uitgebreid naar uw Windows Server-Active Directory omgeving via de installatie van on-premises agents. Gebruikers en beheerders die wacht woorden in Active Directory wijzigen of opnieuw instellen, moeten zich verplichten om te voldoen aan hetzelfde wachtwoord beleid als alleen-Cloud gebruikers.

## <a name="how-are-passwords-evaluated"></a>Hoe wacht woorden worden geëvalueerd

Wanneer een gebruiker het wacht woord wijzigt of opnieuw instelt, wordt het nieuwe wacht woord gecontroleerd op sterkte en complexiteit door het te valideren op basis van de gecombineerde lijst met voor waarden uit de lijsten met globale en aangepaste verboden wacht woorden (als de laatste is geconfigureerd).

Zelfs als het wacht woord van een gebruiker een verboden wacht woord bevat, kan het wacht woord nog steeds worden geaccepteerd als het algemene wacht woord sterk genoeg is. Een nieuw geconfigureerd wacht woord gaat door de volgende stappen om de algehele sterkte te beoordelen om te bepalen of het moet worden geaccepteerd of afgewezen.

### <a name="step-1-normalization"></a>Stap 1: normalisatie

Een nieuw wacht woord gaat eerst door een normalisatie proces. Met deze techniek kan een klein aantal verboden wacht woorden worden toegewezen aan een veel grotere set van mogelijk zwakke wacht woorden.

Normalisatie bestaat uit twee delen.  Als eerste worden alle hoofd letters gewijzigd in een kleine letter.  Ten tweede worden algemene teken vervangingen uitgevoerd, bijvoorbeeld:  

| Oorspronkelijke brief  | Vervangend letter |
| --- | --- |
| 0,3  | o |
| i  | winst |
| '$'  | maatschappij |
| '\@'  | één |

Voor beeld: Stel dat het wacht woord ' leeg ' is verboden en dat een gebruiker het wacht woord wil wijzigen in ' Bl@nK '. Hoewel " Bl@nk " niet specifiek is verboden, wordt dit wacht woord door het normalisatie proces geconverteerd naar "blank", een verboden wacht woord.

### <a name="step-2-check-if-password-is-considered-banned"></a>Stap 2: controleren of het wacht woord wordt beschouwd als verboden

#### <a name="fuzzy-matching-behavior"></a>Gedrag bij benadering treffers

Fuzzy matching wordt gebruikt op het genormaliseerde wacht woord om te bepalen of het een wacht woord bevat dat is gevonden in de lijsten globaal of aangepast verboden wacht woorden. Het overeenkomende proces is gebaseerd op een bewerkings afstand van één (1) vergelijking.  

Voor beeld: Stel dat het wacht woord "ABCDEF" is verboden en dat een gebruiker het wacht woord wil wijzigen in een van de volgende:

' abcdeg ' *(laatste teken gewijzigd van ' f ' naar ' g ')* ' ABCDEFG ' *' (g ' toegevoegd aan het einde)* ' abcde ' *(gevolgd door ' f ' is verwijderd van het einde)*

Elk van de bovenstaande wacht woorden komt niet specifiek overeen met het verboden wacht woord ' ABCDEF '. Omdat elk voor beeld echter binnen een bewerkings afstand van 1 van de verboden term ' ABCDEF ' ligt, worden deze allemaal beschouwd als een overeenkomst met ' ABCDEF '.

#### <a name="substring-matching-on-specific-terms"></a>Overeenkomende subtekenreeks (op specifieke voor waarden)

Overeenkomende subtekenreeks wordt gebruikt op het genormaliseerde wacht woord om te controleren op de voor-en achternaam van de gebruiker en de naam van de Tenant (Houd er rekening mee dat er geen Tenant naam wordt gevonden bij het valideren van wacht woorden op een Active Directory domein controller).

Voor beeld: Stel dat we een gebruiker, Pol, hebben die hun wacht woord opnieuw willen instellen op ' P0l123fb '. Na normalisatie zou dit wacht woord "pol123fb" worden. Met subtekenreeks-overeenkomst wordt gezocht naar het wacht woord de voor naam van de gebruiker ' Pol ' bevat. Hoewel "P0l123fb" niet specifiek is op een van de lijst met geblokkeerde wacht woorden, komt de subtekenreeks die overeenkomt met "Pol" in het wacht woord. Daarom zou dit wacht woord worden afgewezen.

#### <a name="score-calculation"></a>Score berekening

De volgende stap is het identificeren van alle instanties van verboden wacht woorden in het genormaliseerde nieuwe wacht woord van de gebruiker. Vervolgens:

1. Elk verboden wacht woord dat is gevonden in het wacht woord van een gebruiker wordt één punt gegeven.
2. Elk resterend uniek teken wordt op één punt gegeven.
3. Een wacht woord moet ten minste vijf (5) punten zijn om te worden geaccepteerd.

Voor de volgende twee voor beelden gaan we ervan uit dat contoso Azure AD-wachtwoord beveiliging gebruikt en ' Contoso ' heeft in de aangepaste lijst. Er wordt ook van uitgegaan dat "leeg" voor komt in de globale lijst.

Voor beeld: een gebruiker wijzigt het wacht woord in ' C0ntos0Blank12 '

Na normalisatie wordt dit wacht woord "contosoblank12". Het overeenkomende proces vindt dat dit wacht woord twee verboden wacht woorden bevat: contoso en blank. Dit wacht woord krijgt vervolgens een score:

[contoso] + [lege] + [1] + [2] = 4 punten omdat dit wacht woord minder is dan vijf (5) punten, wordt het geweigerd.

Voor beeld: een gebruiker wijzigt het wacht woord in " ContoS0Bl@nkf9 !".

Na normalisatie wordt dit wacht woord "contosoblankf9!". Het overeenkomende proces vindt dat dit wacht woord twee verboden wacht woorden bevat: contoso en blank. Dit wacht woord krijgt vervolgens een score:

[contoso] + [lege] + [f] + [9] + [!] = 5 punten omdat dit wacht woord ten minste vijf (5) punten is, wordt het geaccepteerd.

   > [!IMPORTANT]
   > Houd er rekening mee dat de verboden wachtwoord algoritme samen met de globale lijst op elk gewenst moment kan worden gewijzigd in azure, op basis van de continue beveiligings analyse en onderzoek. Voor de on-premises DC-Agent service worden bijgewerkte algoritmen pas van kracht nadat de software van de DC-agent opnieuw is geïnstalleerd.

## <a name="license-requirements"></a>Licentievereisten

| Gebruikers | Azure AD-wachtwoord beveiliging met globale lijst met geblokkeerde wacht woorden | Azure AD-wachtwoord beveiliging met aangepaste lijst met geblokkeerde wacht woorden|
| --- | --- | --- |
| Alleen Cloud gebruikers | Azure AD Free | Azure AD Premium P1 of P2 |
| Gebruikers die zijn gesynchroniseerd vanuit een on-premises Windows Server-Active Directory | Azure AD Premium P1 of P2 | Azure AD Premium P1 of P2 |

> [!NOTE]
> On-premises Windows Server Active Directory gebruikers die niet zijn gesynchroniseerd met Azure Active Directory ook voor delen van Azure AD-wachtwoord beveiliging op basis van bestaande licenties voor gesynchroniseerde gebruikers.

Meer informatie over licenties, waaronder kosten, vindt u op de [Azure Active Directory prijs site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Wat gebruikers zien

Wanneer een gebruiker probeert een wacht woord opnieuw in te stellen op een waarde die niet kan worden geblokkeerd, wordt het volgende fout bericht weer gegeven:

Uw wacht woord bevat helaas een woord, woord groep of patroon waarmee uw wacht woord gemakkelijk kan worden geraden. Probeer het opnieuw met een ander wacht woord.

## <a name="next-steps"></a>Volgende stappen

- [De aangepaste lijst met uitgesloten wacht woorden configureren](howto-password-ban-bad.md)
- [On-premises Azure AD-agenten voor wachtwoord beveiliging inschakelen](howto-password-ban-bad-on-premises-deploy.md)
