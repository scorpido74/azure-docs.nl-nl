---
title: Beleid voor voorwaardelijke toegang plannen in Azure Active Directory | Microsoft Documenten
description: In dit artikel vindt u informatie over het plannen van beleid voor voorwaardelijke toegang voor Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671819"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>How To: Uw implementatie met voorwaardelijke toegang plannen in Azure Active Directory

Het plannen van uw implementatie met voorwaardelijke toegang is essentieel om ervoor te zorgen dat u de vereiste toegangsstrategie voor apps en resources in uw organisatie bereikt. Besteed het grootste deel van uw tijd tijdens de planningsfase van uw implementatie om de verschillende beleidsregels te ontwerpen die u nodig hebt om toegang tot uw gebruikers te verlenen of te blokkeren onder de voorwaarden die u kiest. In dit document worden de stappen uitgelegd die u moet nemen om veilig en effectief beleid voor voorwaardelijke toegang te implementeren. Voordat u begint, moet u begrijpen hoe [voorwaardelijke toegang](overview.md) werkt en wanneer u deze moet gebruiken.

## <a name="what-you-should-know"></a>Wat u moet weten

Denk aan Voorwaardelijke toegang als een framework waarmee u de toegang tot de apps en bronnen van uw organisatie beheren, in plaats van een zelfstandige functie. Daarom vereisen sommige instellingen voor voorwaardelijke toegang dat extra functies moet worden geconfigureerd. U bijvoorbeeld een beleid configureren dat reageert op een specifiek [aanmeldingsrisiconiveau.](../identity-protection/howto-identity-protection-configure-risk-policies.md) Een beleid dat is gebaseerd op een aanmeldingsrisiconiveau vereist echter [dat Azure Active Directory-identiteitsbeveiliging](../identity-protection/overview-identity-protection.md) moet worden ingeschakeld.

Als er extra functies nodig zijn, moet u mogelijk ook gerelateerde licenties aanschaffen. Afhankelijke toegang is bijvoorbeeld azure AD Premium P1-functie, maar voor identiteitsbescherming is een Azure AD Premium P2-licentie vereist.

Er zijn twee soorten beleid voor voorwaardelijke toegang: basislijn en standaard. Een [basislijnbeleid](baseline-protection.md) is een vooraf gedefinieerd beleid voor voorwaardelijke toegang. Het doel van dit beleid is ervoor te zorgen dat u ten minste het basisniveau van beveiliging hebt ingeschakeld. Basislijnbeleid. Basislijnbeleid is beschikbaar in alle edities van Azure AD en biedt slechts beperkte aanpassingsopties. Als een scenario meer flexibiliteit vereist, schakelt u het basislijnbeleid uit en implementeert u uw vereisten in een aangepast standaardbeleid.

In een standaard beleid voor voorwaardelijke toegang u alle instellingen aanpassen om het beleid aan te passen aan uw bedrijfsvereisten. Voor standaardbeleid is een Azure AD Premium P1-licentie vereist.

>[!NOTE]
> We raden u aan het op Azure AD-apparaat gebaseerde voorwaardelijke toegangsbeleid te gebruiken om de beste handhaving te krijgen na de eerste apparaatverificatie. Dit geldt ook voor het sluiten van sessies als het apparaat uit de compliance en de stroom van apparaatcodes valt.

## <a name="draft-policies"></a>Ontwerpbeleid

Met Azure Active Directory Conditional Access u de beveiliging van uw cloud-apps naar een nieuw niveau tillen. In dit nieuwe niveau is de manier waarop u toegang krijgt tot een cloud-app gebaseerd op een dynamische beleidsevaluatie in plaats van een statische toegangsconfiguratie. Met een beleid voor voorwaardelijke toegang definieert u een antwoord **(dit doen)** op een toegangsvoorwaarde **(wanneer dit gebeurt).**

![Reden en reactie](./media/plan-conditional-access/10.png)

Definieer elk beleid voor voorwaardelijke toegang dat u wilt implementeren met behulp van dit planningsmodel. De planning oefening:

- Helpt u bij het schetsen van de antwoorden en voorwaarden voor elk beleid.
- Resulteert in een goed gedocumenteerde beleidcatalogus voor voorwaardelijke toegang voor uw organisatie. 

U uw catalogus gebruiken om te beoordelen of uw beleidsimplementatie de zakelijke vereisten van uw organisatie weerspiegelt. 

Gebruik de volgende voorbeeldsjabloon om beleid voor voorwaardelijke toegang voor uw organisatie te maken:

|Wanneer *dit* gebeurt:|Doe *dan dit:*|
|-|-|
|Er wordt een toegangspoging gedaan:<br>- Naar een cloud-app*<br>- Door gebruikers en groepen*<br>Gebruik:<br>- Voorwaarde 1 (bijvoorbeeld buiten het Corp-netwerk)<br>- Conditie 2 (bijvoorbeeld apparaatplatforms)|Toegang tot de toepassing blokkeren|
|Er wordt een toegangspoging gedaan:<br>- Naar een cloud-app*<br>- Door gebruikers en groepen*<br>Gebruik:<br>- Voorwaarde 1 (bijvoorbeeld buiten het Corp-netwerk)<br>- Conditie 2 (bijvoorbeeld apparaatplatforms)|Toegang verlenen met (EN):<br>- Vereiste 1 (bijvoorbeeld MFA)<br>- Vereiste 2 (bijvoorbeeld naleving van het apparaat)|
|Er wordt een toegangspoging gedaan:<br>- Naar een cloud-app*<br>- Door gebruikers en groepen*<br>Gebruik:<br>- Voorwaarde 1 (bijvoorbeeld buiten het Corp-netwerk)<br>- Conditie 2 (bijvoorbeeld apparaatplatforms)|Toegang verlenen met (OF):<br>- Vereiste 1 (bijvoorbeeld MFA)<br>- Vereiste 2 (bijvoorbeeld naleving van het apparaat)|

Op zijn minst, **wanneer dit gebeurt** definieert de opdrachtgever **(die**) die probeert om toegang te krijgen tot een cloud app **(wat).** Indien nodig u ook opnemen **hoe** een toegangspoging wordt uitgevoerd. In Voorwaardelijke toegang worden de elementen gedefinieerd die bepalen wie, wat en hoe voorwaarden worden genoemd. Zie [Wat zijn voorwaarden in voorwaardelijke toegang voor Azure Active Directory?](concept-conditional-access-conditions.md) 

Met **dan dit doen,** definieert u de reactie van uw beleid op een toegangsvoorwaarde. In uw reactie blokkeert of verleent u toegang met aanvullende vereisten, bijvoorbeeld multi-factor authenticatie (MFA). Zie Wat zijn [toegangsbesturingselementen in Azure Active Directory Voorwaardelijke toegang voor](controls.md) een volledig overzicht?  

De combinatie van voorwaarden met uw toegangscontroles vertegenwoordigt een beleid voor voorwaardelijke toegang.

![Reden en reactie](./media/plan-conditional-access/51.png)

Zie voor meer informatie [wat er nodig is om een beleid te laten werken.](best-practices.md#whats-required-to-make-a-policy-work)

Op dit punt is het een goed moment om te beslissen over een naamgevingsstandaard voor uw beleid. De naamgevingsstandaard helpt u om beleid te vinden en hun doel te begrijpen zonder ze te openen in de Azure-beheerportal. Geef uw beleid een naam om weer te geven:

- Een volgnummer
- De cloud-app die van toepassing is op
- Het antwoord
- Op wie is het van toepassing
- Wanneer het van toepassing is (indien van toepassing)
 
![Naamgevingsstandaard](./media/plan-conditional-access/11.png)

Hoewel een beschrijvende naam u helpt om een overzicht te houden van uw implementatie met voorwaardelijke toegang, is het volgnummer handig als u naar een beleid in een gesprek moet verwijzen. Als u bijvoorbeeld een collega-beheerder aan de telefoon spreekt, u deze persoon vragen om beleid EM063 te openen om een probleem op te lossen.

In de volgende naam staat bijvoorbeeld dat het beleid MFA vereist voor marketinggebruikers op externe netwerken met behulp van de Dynamics CRP-app:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Naast uw actieve beleid is het raadzaam om ook uitgeschakeld beleid te implementeren dat fungeert als [secundaire veerkrachtige toegangscontroles in uitval-/noodscenario's.](../authentication/concept-resilient-controls.md) Uw naamgevingsstandaard voor het noodbeleid moet nog een paar items bevatten: 

- `ENABLE IN EMERGENCY`aan het begin om de naam te laten opvallen tussen de andere beleidsterreinen.
- De naam van de verstoring moet van toepassing zijn op.
- Een bestelvolgordenummer waarmee de beheerder weet in welk bestelbeleid moet worden ingeschakeld. 

De volgende naam geeft bijvoorbeeld aan dat dit beleid het eerste beleid is van de vier die u moet inschakelen als er een MFA-onderbreking optreedt:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Beleid plannen

Bij het plannen van uw oplossing voor het beleid voor voorwaardelijke toegang u beoordelen of u beleid moet maken om de volgende resultaten te bereiken. 

### <a name="block-access"></a>Toegang blokkeren

De optie om de toegang te blokkeren is krachtig omdat het:

- Troeft alle andere opdrachten voor een gebruiker af
- Heeft de bevoegdheid om uw hele organisatie te blokkeren van het aanmelden bij uw tenant
 
Als u de toegang voor alle gebruikers wilt blokkeren, moet u ten minste één gebruiker (meestal noodtoegangsaccounts) uitsluiten van het beleid. Zie [Gebruikers en groepen selecteren voor](block-legacy-authentication.md#select-users-and-cloud-apps)meer informatie.  

### <a name="require-mfa"></a>MFA vereisen

Als u de aanmeldingservaring van uw gebruikers wilt vereenvoudigen, u hen toestaan zich aan te melden bij uw cloud-apps met een gebruikersnaam en een wachtwoord. Echter, meestal zijn er ten minste een aantal scenario's waarvoor het raadzaam is om een sterkere vorm van accountverificatie te vereisen. Met een beleid voor voorwaardelijke toegang u de vereiste voor MFA beperken tot bepaalde scenario's. 

Veelvoorkomende use cases die MFA vereisen, zijn toegang:

- [Door beheerders](howto-baseline-protect-administrators.md)
- [Naar specifieke apps](app-based-mfa.md) 
- [Vanaf netwerklocaties vertrouwt u niet.](untrusted-networks.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Reageren op mogelijk gecompromitteerde accounts

Met beleid voor voorwaardelijke toegang u geautomatiseerde reacties implementeren op aanmeldingen van mogelijk gecompromitteerde identiteiten. De kans dat een account is gecompromitteerd wordt uitgedrukt in de vorm van risiconiveaus. Er zijn twee risiconiveaus berekend door identiteitsbescherming: aanmeldingsrisico en gebruikersrisico. Als u een antwoord op een aanmeldingsrisico wilt implementeren, hebt u twee opties:

- [De aanmeldingsrisicovoorwaarde](concept-conditional-access-conditions.md#sign-in-risk) in het beleid voor voorwaardelijke toegang
- [Het aanmeldingsrisicobeleid](../identity-protection/howto-sign-in-risk-policy.md) op het gebied van identiteitsbescherming 

Het aanpakken van het aanmeldingsrisico als voorwaarde is de voorkeursmethode omdat het u meer aanpassingsopties biedt.

Het gebruikersrisiconiveau is alleen beschikbaar als [gebruikersrisicobeleid](../identity-protection/howto-user-risk-policy.md) in identiteitsbescherming. 

Zie Wat is Azure Active Directory Identity Protection voor meer [informatie?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Beheerde apparaten vereisen

De wildgroei aan ondersteunde apparaten om toegang te krijgen tot uw cloudbronnen helpt de productiviteit van uw gebruikers te verbeteren. Aan de andere kant wilt u waarschijnlijk niet dat bepaalde bronnen in uw omgeving worden geopend door apparaten met een onbekend beveiligingsniveau. Voor de betreffende bronnen moet u vereisen dat gebruikers er alleen toegang toe hebben via een beheerd apparaat. Zie [Beheerde apparaten vereisen voor toegang tot cloud-apps met voorwaardelijke toegang](require-managed-devices.md)voor meer informatie. 

### <a name="require-approved-client-apps"></a>Goedgekeurde client-apps vereisen

Een van de eerste beslissingen die u moet nemen voor het brengen van uw eigen apparaten (BYOD) scenario's, is of u nodig hebt om het hele apparaat te beheren of alleen de gegevens op. Uw werknemers gebruiken mobiele apparaten voor zowel privé- als werktaken. Terwijl u ervoor zorgt dat uw medewerkers productief kunnen zijn, wilt u ook gegevensverlies voorkomen. Met voorwaardelijke toegang tot Azure Active Directory (Azure AD) u de toegang tot uw cloud-apps beperken tot goedgekeurde client-apps die uw bedrijfsgegevens kunnen beschermen. Zie [Goedgekeurde client-apps vereisen voor toegang tot cloud-apps met voorwaardelijke toegang](app-based-conditional-access.md)voor meer informatie.

### <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren

Azure AD ondersteunt een aantal van de meest gebruikte verificatie- en autorisatieprotocollen, waaronder verouderde verificatie. Hoe u voorkomen dat apps met behulp van verouderde verificatie toegang krijgen tot de bronnen van uw tenant? De aanbeveling is om ze gewoon te blokkeren met een beleid voor voorwaardelijke toegang. Indien nodig staat u alleen bepaalde gebruikers en specifieke netwerklocaties toe om apps te gebruiken die zijn gebaseerd op verouderde verificatie. Zie [Verouderde verificatie voor Azure AD blokkeren met voorwaardelijke toegang](block-legacy-authentication.md)voor meer informatie.

## <a name="test-your-policy"></a>Uw beleid testen

Voordat u een beleid in productie uitrolt, moet u testen of het zich gedraagt zoals verwacht.

1. Testgebruikers maken
1. Een testplan maken
1. Het beleid configureren
1. Een gesimuleerde aanmelding evalueren
1. Uw beleid testen
1. Opschonen

### <a name="create-test-users"></a>Testgebruikers maken

Als u een beleid wilt testen, maakt u een set gebruikers die vergelijkbaar is met de gebruikers in uw omgeving. Als u testgebruikers maakt, u controleren of uw beleid werkt zoals verwacht voordat u van invloed is op echte gebruikers en mogelijk hun toegang tot apps en bronnen verstoort. 

Sommige organisaties hebben hiervoor een test. Het kan echter moeilijk zijn om alle voorwaarden en apps opnieuw in een testtenant te maken om de resultaten van een beleid volledig te testen. 

### <a name="create-a-test-plan"></a>Een testplan maken

Het testplan is belangrijk om een vergelijking te maken tussen de verwachte resultaten en de werkelijke resultaten. Je moet altijd een verwachting hebben voordat je iets test. In de volgende tabel worden voorbeeldtestcases beschreven. Pas de scenario's en verwachte resultaten aan op basis van de manier waarop uw CA-beleid is geconfigureerd.

|Beleid |Scenario |Verwacht resultaat | Resultaat |
|---|---|---|---|
|[MFA vereisen wanneer u niet op het werk bent](/azure/active-directory/conditional-access/untrusted-networks)|Geautoriseerde gebruiker meldt zich aan bij *App* terwijl u op een vertrouwde locatie / werk bent|Gebruiker wordt niet gevraagd om MFA| |
|[MFA vereisen wanneer u niet op het werk bent](/azure/active-directory/conditional-access/untrusted-networks)|Geautoriseerde gebruiker meldt zich aan bij *App* terwijl u niet op een vertrouwde locatie / werk|Gebruiker wordt gevraagd om MFA en kan zich met succes aanmelden| |
|[MFA vereisen (voor beheerder)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Globale beheerder meldt zich aan bij *App*|Beheerder wordt gevraagd om MFA| |
|[Riskante aanmeldingen](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Gebruiker meldt zich aan bij *App* met een [Tor-browser](/azure/active-directory/active-directory-identityprotection-playbook)|Beheerder wordt gevraagd om MFA| |
|[Apparaatbeheer](/azure/active-directory/conditional-access/require-managed-devices)|Geautoriseerde gebruiker probeert zich aan te melden vanaf een geautoriseerd apparaat|Toegang verleend| |
|[Apparaatbeheer](/azure/active-directory/conditional-access/require-managed-devices)|Geautoriseerde gebruiker probeert zich aan te melden vanaf een ongeautoriseerd apparaat|Toegang geblokkeerd| |
|[Wachtwoordwijziging voor riskante gebruikers](/azure/active-directory/identity-protection/howto-user-risk-policy)|Geautoriseerde gebruiker probeert in te loggen met gecompromitteerde referenties (hoog risico aanmelden)|Gebruiker wordt gevraagd om wachtwoord of toegang te wijzigen wordt geblokkeerd op basis van uw beleid| |

### <a name="configure-the-policy"></a>Het beleid configureren

Het beheren van beleid voor voorwaardelijke toegang is een handmatige taak. In de Azure-portal u uw beleid voor voorwaardelijke toegang beheren op één centrale locatie: de pagina Voorwaardelijke toegang. Een toegangspunt naar de pagina Voorwaardelijke toegang is de sectie **Beveiliging** in het navigatiedeelvenster **van Active Directory.** 

![Voorwaardelijke toegang](media/plan-conditional-access/03.png)

Zie [MFA vereisen voor specifieke apps met Voorwaardelijke toegang in Azure Active Directory.](app-based-mfa.md) Deze quickstart helpt u om:

- Maak kennis met de gebruikersinterface.
- Krijg een eerste indruk van hoe voorwaardelijke toegang werkt. 

### <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u uw beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u het beleid Voor voorwaardelijke toegang [als als het beleid](what-if-tool.md) om een aanmelding van uw testgebruiker te simuleren. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport.

>[!NOTE]
> Hoewel een gesimuleerde run u een indruk geeft van de impact die een beleid voor voorwaardelijke toegang heeft, vervangt deze een echte testrun niet.

### <a name="test-your-policy"></a>Uw beleid testen

Testcases uitvoeren volgens uw testplan. In deze stap voert u een end-to-end test van elk beleid uit voor uw testgebruikers om ervoor te zorgen dat elk beleid zich correct gedraagt. Gebruik de bovenstaande scenario's om elke test uit te voeren.

Het is belangrijk om ervoor te zorgen dat u de uitsluitingscriteria van een beleid test. U bijvoorbeeld een gebruiker of groep uitsluiten van een beleid waarvoor MFA vereist is. Test of de uitgesloten gebruikers worden gevraagd voor MFA, omdat voor de combinatie van ander beleid mogelijk MFA voor die gebruikers vereist is.

### <a name="cleanup"></a>Opschonen

De opruimprocedure bestaat uit de volgende stappen:

1. Schakel het beleid uit.
1. Verwijder de toegewezen gebruikers en groepen.
1. Verwijder de testgebruikers.  

## <a name="move-to-production"></a>Verplaatsen naar productie

Wanneer nieuw beleid klaar is voor uw omgeving, implementeert u deze gefaseerd:

- Interne wijzigingscommunicatie bieden aan eindgebruikers.
- Begin met een kleine groep gebruikers en controleer of het beleid zich gedraagt zoals verwacht.
- Wanneer u een beleid uitbreidt met meer gebruikers, blijft u alle beheerders uitsluiten. Het uitsluiten van beheerders zorgt ervoor dat iemand nog steeds toegang heeft tot een beleid als een wijziging vereist is.
- Pas een beleid alleen toe op alle gebruikers als dit nodig is.

Maak als aanbevolen praktijk ten minste één gebruikersaccount dat is:

- Toegewijd aan beleidsbeheer
- Uitgesloten van al uw beleid

## <a name="rollback-steps"></a>Terugdraaien, stappen

Als u uw nieuw geïmplementeerde beleid moet terugdraaien, gebruikt u een of meer van de volgende opties om terug te draaien:

1. **Het beleid uitschakelen** : Als u een beleid uitschakelt, wordt ervoor gezorgd dat het niet van toepassing is wanneer een gebruiker zich probeert aan te melden. U altijd terugkomen en het beleid inschakelen wanneer u het wilt gebruiken.

   ![Beleid uitschakelen](media/plan-conditional-access/07.png)

1. **Een gebruiker /groep uitsluiten van een beleid** - Als een gebruiker geen toegang heeft tot de app, u ervoor kiezen om de gebruiker uit te sluiten van het beleid

   ![Ontsmetten gebruikers](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Deze optie moet spaarzaam worden gebruikt, alleen in situaties waarin de gebruiker wordt vertrouwd. De gebruiker moet zo snel mogelijk weer aan het beleid of de groep worden toegevoegd.

1. **Het beleid verwijderen** - Als het beleid niet langer vereist is, verwijdert u het.

## <a name="next-steps"></a>Volgende stappen

Bekijk [Azure AD-documentatie voor voorwaardelijke toegang](index.yml) voor voorwaardelijke toegang om een overzicht te krijgen van de beschikbare informatie.
