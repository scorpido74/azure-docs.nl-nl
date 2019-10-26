---
title: Beleid voor voorwaardelijke toegang plannen in Azure Active Directory | Microsoft Docs
description: In dit artikel leert u hoe u beleids regels voor voorwaardelijke toegang kunt plannen voor Azure Active Directory.
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
ms.openlocfilehash: 894530aa9624af18f2f33a061d5cde683e9f01be
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880263"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Procedure: de implementatie van voorwaardelijke toegang plannen in Azure Active Directory

Het plannen van de implementatie van voorwaardelijke toegang is van cruciaal belang om ervoor te zorgen dat u beschikt over de vereiste toegangs strategie voor apps en resources in uw organisatie. Besteed uw tijd aan de plannings fase van uw implementatie om de verschillende beleids regels te ontwerpen die u nodig hebt om de toegang tot uw gebruikers te verlenen of blok keren onder de voor waarden die u kiest. In dit document worden de stappen beschreven die u moet uitvoeren om een veilig en effectief beleid voor voorwaardelijke toegang te implementeren. Voordat u begint, moet u weten hoe [voorwaardelijke toegang](overview.md) werkt en wanneer u deze moet gebruiken.

## <a name="what-you-should-know"></a>Wat u moet weten

U kunt voorwaardelijke toegang beschouwen als een framework waarmee u de toegang tot de apps en resources van uw organisatie, in plaats van een zelfstandige functie, beheert. Daarom moeten voor sommige instellingen voor voorwaardelijke toegang aanvullende functies worden geconfigureerd. U kunt bijvoorbeeld een beleid configureren dat reageert op een specifiek [risico niveau voor aanmelding](../identity-protection/howto-identity-protection-configure-risk-policies.md). Een beleid dat is gebaseerd op een risico niveau voor aanmelden vereist echter dat [Azure Active Directory identiteits beveiliging](../identity-protection/overview-identity-protection.md) is ingeschakeld.

Als aanvullende functies vereist zijn, moet u mogelijk ook gerelateerde licenties ophalen. Hoewel voorwaardelijke toegang bijvoorbeeld Azure AD Premium P1-functie is, is voor identiteits beveiliging een Azure AD Premium P2-licentie vereist.

Er zijn twee soorten beleids regels voor voorwaardelijke toegang: basis lijn en standaard. Een [basislijn beleid](baseline-protection.md) is een vooraf gedefinieerd beleid voor voorwaardelijke toegang. Het doel van dit beleid is ervoor te zorgen dat u ten minste het basis niveau voor beveiliging hebt ingeschakeld. Basislijn beleidsregels. Basislijn beleids regels zijn beschikbaar in alle versies van Azure AD en bieden alleen beperkte aanpassings opties. Als voor een scenario meer flexibiliteit is vereist, schakelt u het basislijn beleid uit en implementeert u uw vereisten in een aangepast standaard beleid.

In een standaard beleid voor voorwaardelijke toegang kunt u alle instellingen aanpassen om het beleid aan te passen aan uw bedrijfs vereisten. Voor het standaard beleid is een Azure AD Premium P1-licentie vereist.

## <a name="draft-policies"></a>Concept beleid

Met Azure Active Directory voorwaardelijke toegang kunt u de beveiliging van uw Cloud-apps naar een nieuw niveau. In dit nieuwe niveau is het mogelijk om toegang te krijgen tot een Cloud-app op basis van een dynamische beleids evaluatie in plaats van een statische toegangs configuratie. Met een beleid voor voorwaardelijke toegang definieert u een antwoord (**doe dit**) aan een toegangs voorwaarde (**wanneer dit gebeurt**).

![Reden en antwoord](./media/plan-conditional-access/10.png)

Definieer elk beleid voor voorwaardelijke toegang dat u wilt implementeren met dit plannings model. De plannings oefening:

- Helpt u bij het samen vatting van de reacties en voor waarden voor elk beleid.
- Resulteert in een catalogus met een goed gedocumenteerde beleids regel voor voorwaardelijke toegang voor uw organisatie. 

U kunt uw catalogus gebruiken om te beoordelen of uw beleids implementatie aansluit op de bedrijfs vereisten van uw organisatie. 

Gebruik de volgende voorbeeld sjabloon voor het maken van beleid voor voorwaardelijke toegang voor uw organisatie:

|Als *Dit* gebeurt:|Ga vervolgens *als volgt te werk:*|
|-|-|
|Er wordt een toegangs poging gedaan:<br>-Naar een Cloud-app *<br>-door gebruikers en groepen*<br>Basis<br>-Voor waarde 1 (bijvoorbeeld buiten Corp-netwerk)<br>-Voor waarde 2 (bijvoorbeeld platformen)|Toegang tot de toepassing blok keren|
|Er wordt een toegangs poging gedaan:<br>-Naar een Cloud-app *<br>-door gebruikers en groepen*<br>Basis<br>-Voor waarde 1 (bijvoorbeeld buiten Corp-netwerk)<br>-Voor waarde 2 (bijvoorbeeld platformen)|Toegang verlenen met (en):<br>-Vereiste 1 (bijvoorbeeld MFA)<br>-Vereiste 2 (bijvoorbeeld apparaatcompatibiliteit)|
|Er wordt een toegangs poging gedaan:<br>-Naar een Cloud-app *<br>-door gebruikers en groepen*<br>Basis<br>-Voor waarde 1 (bijvoorbeeld buiten Corp-netwerk)<br>-Voor waarde 2 (bijvoorbeeld platformen)|Toegang verlenen met (of):<br>-Vereiste 1 (bijvoorbeeld MFA)<br>-Vereiste 2 (bijvoorbeeld apparaatcompatibiliteit)|

**Als dit het geval** is, definieert u ten minste de principal (**wie**) die probeert toegang te krijgen tot een Cloud-app (**wat**). Als dat nodig is, kunt u ook **zien hoe** een toegangs poging wordt uitgevoerd. In voorwaardelijke toegang, de elementen die de wie, wat en hoe bekend zijn als voor waarden definiëren. Zie [Wat zijn voor waarden in azure Active Directory voorwaardelijke toegang?](conditions.md) voor meer informatie. 

**Vervolgens**definieert u de reactie van uw beleid op een toegangs voorwaarde. In uw antwoord blokkeert of verleent u toegang met aanvullende vereisten, bijvoorbeeld multi-factor Authentication (MFA). Zie [Wat is Access controls in azure Active Directory voorwaardelijke toegang?](controls.md) voor een volledig overzicht.  

De combi natie van voor waarden van uw toegangs beheer vertegenwoordigt een beleid voor voorwaardelijke toegang.

![Reden en antwoord](./media/plan-conditional-access/51.png)

Zie [Wat is vereist om een beleid te maken](best-practices.md#whats-required-to-make-a-policy-work)voor meer informatie.

Op dit moment is het een goed idee om een naamgevings standaard voor uw beleid te kiezen. De naamgevings standaard helpt u bij het vinden van beleids regels en het begrijpen van het doel ervan zonder ze te openen in de Azure-beheer Portal. Geef een naam op voor het beleid dat u wilt weer geven:

- Een Volg nummer
- De Cloud-app waarop deze van toepassing is
- Het antwoord
- Wie is van toepassing op
- Wanneer dit van toepassing is (indien van toepassing)
 
![Naamgevings standaard](./media/plan-conditional-access/11.png)

Hoewel een beschrijvende naam u helpt een overzicht van de implementatie van voorwaardelijke toegang te blijven gebruiken, is het Volg nummer nuttig als u moet verwijzen naar een beleid in een gesprek. Als u bijvoorbeeld een andere beheerder op de telefoon spreekt, kunt u hen vragen om het beleid EM063 te openen om een probleem op te lossen.

De volgende naam geeft bijvoorbeeld aan dat het beleid MFA vereist voor de marketing van gebruikers op externe netwerken met behulp van de Dynamics CRP-app:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Naast uw actieve beleid is het raadzaam om ook uitgeschakelde beleids regels te implementeren die fungeren als secundaire, [flexibele toegangs controles in storingen/nood scenario's](../authentication/concept-resilient-controls.md). De naamgevings standaard voor het beleid voor nood gevallen moet een aantal meer items bevatten: 

- `ENABLE IN EMERGENCY` aan het begin om de naam onder het andere beleid te laten opvallen.
- De naam van de onderbreking moet van toepassing zijn op.
- Een Volg nummer van een volg orde om de beheerder te helpen weten in welke volg orde het order beleid moet worden ingeschakeld. 

De volgende naam geeft bijvoorbeeld aan dat dit beleid het eerste beleid is van vier u moet inschakelen als er sprake is van een MFA-onderbreking:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Beleid plannen

Bepaal bij het plannen van de oplossing voor het beleid voor voorwaardelijke toegang of u beleid moet maken om de volgende resultaten te verkrijgen. 

### <a name="block-access"></a>Toegang blok keren

De optie voor het blok keren van de toegang is krachtig omdat:

- Troeft alle andere toewijzingen voor een gebruiker
- Biedt de kracht om uw hele organisatie te blok keren om u aan te melden bij uw Tenant
 
Als u toegang wilt blok keren voor alle gebruikers, moet u ten minste één gebruiker (doorgaans toegangs accounts voor nood gevallen) uitsluiten van het beleid. Zie [gebruikers en groepen selecteren](block-legacy-authentication.md#select-users-and-cloud-apps)voor meer informatie.  

### <a name="require-mfa"></a>MFA vereisen

Als u de aanmeldings ervaring van uw gebruikers wilt vereenvoudigen, kunt u hen toestaan om zich met een gebruikers naam en wacht woord aan te melden bij uw Cloud-apps. Normaal gesp roken zijn er echter ten minste enkele scenario's waarvoor het aanbeveling verdient een sterkere vorm van account verificatie te vereisen. Met een beleid voor voorwaardelijke toegang kunt u de vereiste voor MFA beperken tot bepaalde scenario's. 

Algemene use cases om MFA te vereisen is toegang:

- [Door beheerders](howto-baseline-protect-administrators.md)
- [Naar specifieke apps](app-based-mfa.md) 
- [Vanaf netwerk locaties vertrouwt u niet](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Reageren op mogelijk gemanipuleerde accounts

Met beleid voor voorwaardelijke toegang kunt u automatische antwoorden op aanmeldingen implementeren van mogelijk aangetast identiteiten. De kans dat een account is aangetast, wordt uitgedrukt in de vorm van risico niveaus. Er zijn twee risico niveaus berekend door identiteits beveiliging: aanmeldings risico en gebruikers risico. Als u een reactie op een aanmeldings risico wilt implementeren, hebt u twee opties:

- [De voor waarde voor aanmeldings risico in het beleid voor](conditions.md#sign-in-risk) voorwaardelijke toegang
- [Het aanmeldings risico beleid](../identity-protection/howto-sign-in-risk-policy.md) voor identiteits beveiliging 

Het adresseren van het aanmeldings risico als voor waarde is de voorkeurs methode omdat u meer aanpassings opties hebt.

Het gebruikers risico niveau is alleen beschikbaar als [beleid voor gebruikers Risico's](../identity-protection/howto-user-risk-policy.md) in identiteits beveiliging. 

Zie [Wat is Azure Active Directory Identity Protection?](../identity-protection/overview.md) voor meer informatie. 

### <a name="require-managed-devices"></a>Beheerde apparaten vereisen

De verspreiding van ondersteunde apparaten om toegang te krijgen tot uw Cloud bronnen helpt u bij het verbeteren van de productiviteit van uw gebruikers. Op de gespiegelde zijde wilt u waarschijnlijk niet dat bepaalde resources in uw omgeving toegankelijk zijn voor apparaten met een onbekend beveiligings niveau. Voor de betrokken resources moet u vereisen dat gebruikers alleen toegang hebben tot ze via een beheerd apparaat. Zie voor meer informatie [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Goedgekeurde client-apps vereisen

Een van de eerste beslissingen die u moet nemen voor het nemen van uw eigen apparaten (BYOD), is of u nu het hele apparaat moet beheren of alleen de gegevens erop. Uw werk nemers gebruiken mobiele apparaten voor zowel privé-als werk taken. Hoewel uw werk nemers productief kunnen zijn, moet u ook gegevens verlies voor komen. Met Azure Active Directory (Azure AD) voorwaardelijke toegang kunt u de toegang tot uw Cloud-apps beperken tot goedgekeurde client-apps waarmee uw bedrijfs gegevens kunnen worden beveiligd. Zie voor meer informatie [goedgekeurde client-apps vereisen voor toegang tot Cloud app met voorwaardelijke toegang](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren

Azure AD biedt ondersteuning voor een aantal van de meest gebruikte verificatie-en autorisatie protocollen, inclusief verouderde verificatie. Hoe kunt u voor komen dat apps die verouderde verificatie gebruiken, toegang krijgen tot de resources van uw Tenant? De aanbeveling is om ze alleen te blok keren met een beleid voor voorwaardelijke toegang. Als dat nodig is, kunt u alleen bepaalde gebruikers en specifieke netwerk locaties gebruiken voor het gebruik van apps die zijn gebaseerd op verouderde verificatie. Zie voor meer informatie [verouderde verificatie blok keren voor Azure AD met voorwaardelijke toegang](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Uw beleid testen

Voordat u een beleid in productie uitrollen, moet u controleren of het werkt zoals verwacht.

1. Test gebruikers maken
1. Een test plan maken
1. Het beleid configureren
1. Een gesimuleerde aanmelding evalueren
1. Uw beleid testen
1. Opschonen

### <a name="create-test-users"></a>Test gebruikers maken

Als u een beleid wilt testen, maakt u een set gebruikers die vergelijkbaar is met de gebruikers in uw omgeving. Door test gebruikers te maken, kunt u controleren of uw beleid werkt zoals verwacht voordat u van invloed is op echte gebruikers en de toegang tot apps en resources mogelijk verstoort. 

Sommige organisaties hebben test tenants voor dit doel einde. Het kan echter lastig zijn om alle voor waarden en apps in een test Tenant opnieuw te maken om het resultaat van een beleid volledig te testen. 

### <a name="create-a-test-plan"></a>Een test plan maken

Het test plan is belang rijk voor een vergelijking tussen de verwachte resultaten en de werkelijke resultaten. U moet altijd een verwachting hebben voordat u iets kunt testen. De volgende tabel geeft een overzicht van voor beelden van test cases. Pas de scenario's en verwachte resultaten aan op basis van de configuratie van uw CA-beleid.

|Beleid |Scenario |Verwacht resultaat | Resultaat |
|---|---|---|---|
|[MFA vereisen wanneer dat niet aan het werk is](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Geautoriseerde gebruiker meldt zich aan bij de *app* op een vertrouwde locatie/werk|Gebruiker wordt niet gevraagd om MFA| |
|[MFA vereisen wanneer dat niet aan het werk is](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Geautoriseerde gebruiker meldt zich aan bij de *app* , maar niet op een vertrouwde locatie/werk|De gebruiker wordt gevraagd om te MFA en kan zich aanmelden| |
|[MFA vereisen (voor beheerder)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|De globale beheerder meldt zich aan bij de *app*|Beheerder wordt gevraagd om te MFA| |
|[Riskante aanmeldingen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Gebruiker meldt zich aan bij de *app* met een [Tor-Browser](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Beheerder wordt gevraagd om te MFA| |
|[Apparaatbeheer](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Geautoriseerde gebruiker probeert zich aan te melden bij een bevoegd apparaat|Toegang verleend| |
|[Apparaatbeheer](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Geautoriseerde gebruiker probeert zich aan te melden vanaf een niet-geautoriseerd apparaat|Toegang geblokkeerd| |
|[Wachtwoord wijziging voor Risk ante gebruikers](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Geautoriseerde gebruiker meldt zich aan met de aangetaste referenties (aanmelden met een hoog risico)|De gebruiker wordt gevraagd om het wacht woord te wijzigen of de toegang wordt geblokkeerd op basis van uw beleid| |

### <a name="configure-the-policy"></a>Het beleid configureren

Het beheren van beleid voor voorwaardelijke toegang is een hand matige taak. In de Azure Portal kunt u uw beleid voor voorwaardelijke toegang beheren op één centrale locatie: de pagina voor voorwaardelijke toegang. Een ingangs punt naar de pagina voorwaardelijke toegang is de sectie **beveiliging** in het navigatie deel venster **Active Directory** . 

![Voorwaardelijke toegang](media/plan-conditional-access/03.png)

Als u meer wilt weten over het maken van beleid voor voorwaardelijke toegang, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md). Deze Snelstartgids helpt u bij het volgende:

- Vertrouwd raken met de gebruikers interface.
- Profiteer van de eerste indruk van de werking van voorwaardelijke toegang. 

### <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u het beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u de functie voor voorwaardelijke toegang [Wat als-beleid](what-if-tool.md) voor het simuleren van een aanmelding van uw test gebruiker. Met de simulatie wordt de impact van deze aanmelding op uw beleid geschat en wordt een simulatierapport gegenereerd.

>[!NOTE]
> Hoewel een gesimuleerde uitvoering u indruk geeft van de impact van het beleid voor voorwaardelijke toegang, wordt een daad werkelijk uitgevoerde test niet vervangen.

### <a name="test-your-policy"></a>Uw beleid testen

Voer test cases uit op basis van uw test plan. In deze stap voert u een end-to-end-test uit voor elk beleid voor uw test gebruikers om ervoor te zorgen dat elk beleid correct functioneert. Gebruik de scenario's die hierboven zijn gemaakt om elke test uit te voeren.

Het is belang rijk om ervoor te zorgen dat u de uitsluitings criteria van een beleid test. U kunt bijvoorbeeld een gebruiker of groep uitsluiten van een beleid waarvoor MFA vereist is. Test of de uitgesloten gebruikers om MFA wordt gevraagd, omdat de combi natie van andere beleids regels mogelijk MFA vereist voor die gebruikers.

### <a name="cleanup"></a>Opschonen

De opschoon procedure bestaat uit de volgende stappen:

1. Schakel het beleid uit.
1. Verwijder de toegewezen gebruikers en groepen.
1. De test gebruikers verwijderen.  

## <a name="move-to-production"></a>Verplaatsen naar productie

Wanneer nieuwe beleids regels gereed zijn voor uw omgeving, implementeert u deze in fasen:

- Bied interne wijzigings communicatie aan eind gebruikers aan.
- Begin met een kleine groep gebruikers en controleer of het beleid zich gedraagt zoals verwacht.
- Wanneer u een beleid uitbreidt om meer gebruikers op te nemen, moet u alle beheerders blijven uitsluiten. Met uitzonde ring van beheerders weet u zeker dat iemand nog steeds toegang tot een beleid heeft als een wijziging is vereist.
- Pas een beleid toe op alle gebruikers als dat nodig is.

Als best practice moet u ten minste één gebruikers account maken dat:

- Toegewezen aan beleids beheer
- Uitgesloten van al uw beleids regels

## <a name="rollback-steps"></a>Stappen voor ongedaan maken

Als u de zojuist geïmplementeerde beleids regels wilt terugdraaien, gebruikt u een of meer van de volgende opties om terug te draaien:

1. Als u **het beleid** uitschakelt, wordt het beleid niet toegepast wanneer een gebruiker zich probeert aan te melden. U kunt altijd terugkomen en het beleid inschakelen wanneer u het wilt gebruiken.

   ![beleid uitschakelen](media/plan-conditional-access/07.png)

1. **Een gebruiker of groep uitsluiten van een beleid** : als een gebruiker geen toegang kan krijgen tot de app, kunt u ervoor kiezen om de gebruiker uit te sluiten van het beleid

   ![Exluce gebruikers](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Deze optie moet spaarzaam worden gebruikt, alleen in situaties waarin de gebruiker wordt vertrouwd. De gebruiker moet zo snel mogelijk worden toegevoegd aan het beleid of de groep.

1. **Het beleid verwijderen** : als het beleid niet meer nodig is, verwijdert u het.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [documentatie voor voorwaardelijke toegang van Azure AD](index.yml) voor een overzicht van de beschik bare informatie.
