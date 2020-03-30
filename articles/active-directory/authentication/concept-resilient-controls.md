---
title: Een strategie voor beheer van veerkrachtige toegangscontrole maken - Azure AD
description: Dit document geeft richtlijnen voor strategieën die een organisatie moet toepassen om veerkracht te bieden om het risico op uitsluiting bij onvoorziene verstoringen te verminderen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908731"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Een beheerstrategie voor veerkrachtigtoegangsbeheer maken met Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Omdat Microsoft moet reageren op veranderende marktomstandigheden, mag dit niet worden geïnterpreteerd als een verbintenis van Microsoft en kan Microsoft de juistheid van informatie die na de publicatiedatum wordt gepresenteerd, niet garanderen.

Organisaties die vertrouwen op één toegangscontrole, zoals multi-factor authentication (MFA) of één netwerklocatie, om hun IT-systemen te beveiligen, zijn gevoelig voor toegangsfouten tot hun apps en bronnen als dat ene toegangscontrolebeheer niet beschikbaar wordt of verkeerd geconfigureerd. Een natuurramp kan bijvoorbeeld leiden tot het onbeschikbaar zijn van grote segmenten van telecommunicatie-infrastructuur of bedrijfsnetwerken. Een dergelijke onderbreking kan voorkomen dat eindgebruikers en beheerders zich kunnen aanmelden.

Dit document biedt richtlijnen voor strategieën die een organisatie moet toepassen om veerkracht te bieden om het risico op uitsluiting bij onvoorziene verstoringen te verminderen met de volgende scenario's:

 1. Organisaties kunnen hun veerkracht vergroten om het risico op uitsluiting **vóór een onderbreking** te verminderen door mitigatiestrategieën of rampenplannen te implementeren.
 2. Organisaties kunnen tijdens een onderbreking toegang blijven houden tot apps en resources die ze tijdens **een onderbreking** kiezen door mitigatiestrategieën en noodplannen te hebben.
 3. Organisaties moeten ervoor zorgen dat ze informatie, zoals logboeken, **bewaren na een onderbreking** en voordat ze alle onvoorziene gebeurtenissen die ze hebben geïmplementeerd, terugdraaien.
 4. Organisaties die geen preventiestrategieën of alternatieve plannen hebben geïmplementeerd, kunnen mogelijk **noodopties** implementeren om de verstoring aan te pakken.

## <a name="key-guidance"></a>Belangrijkste richtlijnen

Er zijn vier belangrijke afhaalmaaltijden in dit document:

* Vermijd de uitsluiting van beheerders door gebruik te maken van noodtoegangsaccounts.
* Implementeer MFA met behulp van Conditional Access (CA) in plaats van mfa per gebruiker.
* Verminder de vergrendeling van gebruikers door meerdere Ca-besturingselementen (Conditional Access) te gebruiken.
* Verminder de vergrendeling van gebruikers door meerdere verificatiemethoden of -equivalenten voor elke gebruiker in te richten.

## <a name="before-a-disruption"></a>Vóór een verstoring

Het beperken van een daadwerkelijke onderbreking moet de primaire focus van een organisatie zijn bij het omgaan met toegangscontroleproblemen die zich kunnen voordoen. Mitigating omvat planning voor een werkelijke gebeurtenis plus het implementeren van strategieën om ervoor te zorgen dat toegangscontroles en bewerkingen niet worden beïnvloed tijdens onderbrekingen.

### <a name="why-do-you-need-resilient-access-control"></a>Waarom heb je veerkrachtige toegangscontrole nodig?

 Identiteit is het controlevlak van gebruikers die toegang hebben tot apps en bronnen. Uw identiteitssysteem bepaalt welke gebruikers en onder welke voorwaarden, zoals toegangscontroles of verificatievereisten, gebruikers toegang krijgen tot de toepassingen. Wanneer een of meer verificatie- of toegangscontrolevereisten niet beschikbaar zijn voor gebruikers om te verifiëren vanwege onvoorziene omstandigheden, kunnen organisaties een of beide van de volgende problemen ondervinden:

* **Uitsluiting van administrator:** Beheerders kunnen de tenant of services niet beheren.
* **Vergrendeling van de gebruiker:** Gebruikers hebben geen toegang tot apps of bronnen.

### <a name="administrator-lockout-contingency"></a>Gebeurtenis voor de uitsluiting van beheerders

Als u beheerderstoegang tot uw tenant wilt ontgrendelen, moet u accounts voor noodtoegang maken. Deze noodtoegangsaccounts, ook wel *break glass-accounts* genoemd, bieden toegang tot de Configuratie van Azure AD wanneer er geen normale procedures voor toegang tot accounts voor bevoegde accounts beschikbaar zijn. Er moeten ten minste twee noodtoegangsaccounts worden aangemaakt naar aanleiding van de [aanbevelingen voor accountaanbevelingen voor noodtoegang]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Vergrendeling van de gebruiker beperken

 Gebruik beleid voor voorwaardelijke toegang met meerdere besturingselementen om gebruikers de keuze te geven hoe ze toegang krijgen tot apps en bronnen om het risico op uitsluiting van gebruikers te beperken. Door een gebruiker de keuze te geven tussen bijvoorbeeld aanmelden bij MFA **of** aanmelden vanaf een beheerd apparaat **of** aanmelden vanuit het bedrijfsnetwerk, heeft de gebruiker andere opties om te blijven werken als een van de toegangsbesturingselementen niet beschikbaar is.

#### <a name="microsoft-recommendations"></a>Microsoft-aanbevelingen

Neem de volgende toegangsbesturingselementen op in uw bestaande beleid voor voorwaardelijke toegang voor de organisatie:

1. Inrichten van meerdere verificatiemethoden voor elke gebruiker die afhankelijk zijn van verschillende communicatiekanalen, bijvoorbeeld de Microsoft Authenticator-app (op internet gebaseerd), OATH-token (gegenereerd op het apparaat) en SMS (telefonisch).
2. Implementeer Windows Hello voor Bedrijven op Windows 10-apparaten om rechtstreeks vanaf de aanmelding van apparaten te voldoen aan de MFA-vereisten.
3. Gebruik vertrouwde apparaten via [Azure AD Hybrid Join-](https://docs.microsoft.com/azure/active-directory/devices/overview) of [Microsoft Intune Managed-apparaten.](https://docs.microsoft.com/intune/planning-guide) Vertrouwde apparaten verbeteren de gebruikerservaring omdat het vertrouwde apparaat zelf kan voldoen aan de sterke verificatievereisten van het beleid zonder een MFA-uitdaging voor de gebruiker. MFA is dan vereist bij het inschrijven van een nieuw apparaat en bij het openen van apps of bronnen vanaf niet-vertrouwde apparaten.
4. Gebruik op Azure AD-identiteitsbeveiliging gebaseerd beleid voor identiteitsbescherming dat toegang verhindert wanneer de gebruiker of aanmelding risico loopt in plaats van vast MFA-beleid.

>[!NOTE]
> Voor beleid op basis van risico's zijn [Azure AD Premium P2-licenties](https://azure.microsoft.com/pricing/details/active-directory/) vereist.

In het volgende voorbeeld wordt beschreven beleid dat u moet maken om een veerkrachtig toegangscontrole te bieden voor de gebruiker om toegang te krijgen tot hun apps en bronnen. In dit voorbeeld hebt u een beveiligingsgroep **AppUsers** nodig met de doelgebruikers waartoe u toegang wilt geven, een groep met de naam **CoreAdmins** met de kernbeheerders en een groep met de naam **EmergencyAccess** met de noodtoegangsaccounts.
Met deze voorbeeldbeleidsset worden geselecteerde gebruikers in **AppUsers,** toegang tot geselecteerde apps toegekend als ze verbinding maken vanaf een vertrouwd apparaat of sterke verificatie bieden, bijvoorbeeld MFA. Het sluit noodaccounts en kernbeheerders uit.

**Ca-mitigatiebeleid ingesteld:**

* Beleid 1: Toegang blokkeren tot mensen buiten doelgroepen
  * Gebruikers en groepen: alle gebruikers opnemen. AppUsers, CoreAdmins en EmergencyAccess uitsluiten
  * Cloud-apps: alle apps opnemen
  * Voorwaarden: (Geen)
  * Subsidiebeheer: Blokkeren
* Beleid 2: Geef appUsers toegang die mfa of vertrouwd apparaat nodig hebben.
  * Gebruikers en groepen: voeg appgebruikers toe. CoreAdministrators en EmergencyAccess uitsluiten
  * Cloud-apps: alle apps opnemen
  * Voorwaarden: (Geen)
  * Grant Control: Verleen toegang, vereist meervoudige verificatie, vereisen dat het apparaat compatibel is. Voor meerdere besturingselementen: vereisen een van de geselecteerde besturingselementen.

### <a name="contingencies-for-user-lockout"></a>Onvoorziene omstandigheden voor de uitsluiting van de gebruiker

Als alternatief kan uw organisatie ook een noodbeleid maken. Als u een noodbeleid wilt maken, moet u afwegingscriteria definiëren tussen bedrijfscontinuïteit, operationele kosten, financiële kosten en beveiligingsrisico's. U bijvoorbeeld een noodbeleid alleen activeren voor een subset van gebruikers, voor een subset van apps, voor een subset van clients of van een subset van locaties. Noodbeleid geeft beheerders en eindgebruikers toegang tot apps en resources tijdens een onderbreking wanneer er geen mitigatiemethode is geïmplementeerd.
Inzicht in uw blootstelling tijdens een verstoring helpt uw risico te verminderen en is een essentieel onderdeel van uw planningsproces. Als u uw noodplan wilt maken, bepaalt u eerst de volgende zakelijke vereisten van uw organisatie:

1. Bepaal uw bedrijfskritische apps van tevoren: waar moeten u toegang toe geven, zelfs met een lager risico/beveiligingshouding? Stel een lijst van deze apps samen en zorg ervoor dat uw andere belanghebbenden (zakelijk, beveiliging, juridisch, leiderschap) het erover eens zijn dat als alle toegangscontrole verdwijnt, deze apps nog steeds moeten blijven draaien. Je bent waarschijnlijk gaan eindigen met categorieën van:
   * **Categorie 1-kritieke apps** voor bedrijfskritieke apps die niet langer dan een paar minuten niet beschikbaar zijn, bijvoorbeeld Apps die rechtstreeks van invloed zijn op de inkomsten van de organisatie.
   * **Categorie 2 belangrijke apps** die het bedrijf moet toegankelijk zijn binnen een paar uur.
   * **Categorie 3 apps met lage prioriteit** die bestand zijn tegen een verstoring van een paar dagen.
2. Voor apps in categorie 1 en 2 raadt Microsoft u aan vooraf te plannen welk type toegangsniveau u wilt toestaan:
   * Wilt u volledige toegang of beperkte sessie toestaan, zoals het beperken van downloads?
   * Wilt u toegang geven tot een deel van de app, maar niet de hele app?
   * Wilt u toegang tot informatiewerkers toestaan en beheerderstoegang blokkeren totdat het toegangscontrolebesturingselement is hersteld?
3. Voor deze apps raadt Microsoft u ook aan te plannen welke toegangsmogelijkheden u bewust opent en welke u zult sluiten:
   * Wilt u alleen toegang voor de browser toestaan en rijke clients blokkeren die offline gegevens kunnen opslaan?
   * Wilt u alleen toegang toestaan voor gebruikers binnen het bedrijfsnetwerk en externe gebruikers blokkeren?
   * Wilt u alleen toegang vanuit bepaalde landen of regio's toestaan tijdens de onderbreking?
   * Wilt u dat beleid voor het noodbeleid, met name voor bedrijfskritieke apps, mislukt of slaagt als er geen alternatieve toegangscontrole beschikbaar is?

#### <a name="microsoft-recommendations"></a>Microsoft-aanbevelingen

Een beleid voor voorwaardelijke toegang voor noodgevallen is een **uitgeschakeld beleid** dat Azure MFA, MFA, op risico's of apparaatgebaseerde besturingselementen weglaat. Wanneer uw organisatie vervolgens besluit uw noodplan te activeren, kunnen beheerders het beleid inschakelen en het reguliere beleid op basis van controle uitschakelen.

>[!IMPORTANT]
> Als u beleid uitschakelt dat de beveiliging van uw gebruikers afdwingt, zelfs tijdelijk, vermindert u uw beveiligingshouding terwijl het noodplan van kracht is.

* Configureer een set terugvalbeleid als een onderbreking in één referentietype of één toegangscontrolemechanisme van invloed is op de toegang tot uw apps. Configureer een beleid in uitgeschakelde status waarvoor Domein join als besturingselement vereist is, als back-up voor een actief beleid waarvoor een MFA-provider van derden vereist is.
* Verminder het risico dat slechte acteurs wachtwoorden raden, wanneer MFA niet vereist is, door de praktijken in de [whitepaper wachtwoordrichtlijnen](https://aka.ms/passwordguidance) te volgen.
* Implementeer [Azure AD Self-Service Password Reset (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) en [Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) om ervoor te zorgen dat gebruikers geen algemeen wachtwoord en voorwaarden gebruiken die u wilt verbieden.
* Gebruik beleidsregels die de toegang binnen de apps beperken als een bepaald verificatieniveau niet wordt bereikt in plaats van simpelweg terug te vallen naar volledige toegang. Bijvoorbeeld:
  * Configureer een back-upbeleid dat de beperkte sessieclaim naar Exchange en SharePoint verzendt.
  * Als uw organisatie Microsoft Cloud App Security gebruikt, u overwegen terug te vallen naar een beleid dat MCAS inschakelt en vervolgens MCAS alleen-lezen toegang toestaat, maar niet uploadt.
* Geef uw beleid een naam om ervoor te zorgen dat het gemakkelijk is om ze te vinden tijdens een onderbreking. Neem de volgende elementen op in de beleidsnaam:
  * Een *labelnummer* voor het beleid.
  * Tekst om aan te tonen, dit beleid is alleen voor noodgevallen. Bijvoorbeeld: **INSCHAKELEN IN NOOD**
  * De *verstoring* is van toepassing op. Bijvoorbeeld: **Tijdens MFA Disruptie**
  * Een *volgnummer* om de volgorde weer te geven, u moet het beleid activeren.
  * De *apps* die het van toepassing is op.
  * De *besturingselementen* zullen van toepassing zijn.
  * De *voorwaarden* die het vereist.
  
Deze naamgevingsnorm voor het noodbeleid ziet er als volgt uit: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Het volgende voorbeeld: **Voorbeeld A - Ca-beleid voor onvoorziene gebeurtenissen om de toegang tot bedrijfskritieke samenwerkingsapps te herstellen,** is een typische gebeurtenis voor bedrijven. In dit scenario vereist de organisatie doorgaans MFA voor alle Exchange Online- en SharePoint Online-toegang en de onderbreking in dit geval is de MFA-provider voor de klant die een storing heeft (of het nu Azure MFA, on-premises MFA-provider of MFA van derden is). Dit beleid vermindert deze storing door specifieke gerichte gebruikers alleen toegang te geven tot deze apps vanaf vertrouwde Windows-apparaten wanneer ze de app openen vanuit hun vertrouwde bedrijfsnetwerk. Het zal ook noodaccounts en kernbeheerders van deze beperkingen uitsluiten. De beoogde gebruikers krijgen dan toegang tot Exchange Online en SharePoint Online, terwijl andere gebruikers nog steeds geen toegang hebben tot de apps als gevolg van de storing. In dit voorbeeld is een benoemde netwerklocatie **CorpNetwork** en een beveiligingsgroep **ContingencyAccess** met de doelgebruikers, een groep met de naam **CoreAdmins** met de kernbeheerders en een groep met de naam **EmergencyAccess** met de noodtoegangsaccounts vereist. De onvoorziene omstandigheden vereisen vier beleidsmaatregelen om de gewenste toegang te bieden. 

**Voorbeeld A - Ca-beleid voor onvoorziene gebeurtenissen om de toegang tot bedrijfskritieke samenwerkingsapps te herstellen:**

* Beleid 1: Domeinsamengevoegde apparaten vereisen voor Exchange en SharePoint
  * Naam: EM001 - INSCHAKELEN IN NOODGEVALLEN: MFA-onderbreking[1/4] - Exchange SharePoint - Hybride Azure AD-deelname vereisen
  * Gebruikers en groepen: Neem Noodtoegang op. CoreAdministrators en EmergencyAccess uitsluiten
  * Cloud-apps: Exchange Online en SharePoint Online
  * Voorwaarden:
  * Subsidiebeheer: Domein vereisen Dat is samengevoegd
  * Staat: Uitgeschakeld
* Beleid 2: Andere platforms blokkeren dan Windows
  * Naam: EM002 - ENABLE IN EMERGENCY: MFA Disruption[2/4] - Exchange SharePoint - Toegang blokkeren behalve Windows
  * Gebruikers en groepen: alle gebruikers opnemen. CoreAdministrators en EmergencyAccess uitsluiten
  * Cloud-apps: Exchange Online en SharePoint Online
  * Voorwaarden: Apparaatplatform Omvatten alle platforms, Windows uitsluiten
  * Subsidiebeheer: Blokkeren
  * Staat: Uitgeschakeld
* Beleid 3: Andere netwerken blokkeren dan CorpNetwork
  * Naam: EM003 - ENABLE IN EMERGENCY: MFA Disruption[3/4] - Exchange SharePoint - Block access except Corporate Network
  * Gebruikers en groepen: alle gebruikers opnemen. CoreAdministrators en EmergencyAccess uitsluiten
  * Cloud-apps: Exchange Online en SharePoint Online
  * Voorwaarden: Locaties Omvatten elke locatie, exclusief CorpNetwork
  * Subsidiebeheer: Blokkeren
  * Staat: Uitgeschakeld
* Beleid 4: EAS expliciet blokkeren
  * Naam: EM004 - ENABLE IN EMERGENCY: MFA Disruption[4/4] - Exchange - Block EAS for all users
  * Gebruikers en groepen: alle gebruikers opnemen
  * Cloud-apps: Exchange Online opnemen
  * Voorwaarden: Client-apps: Exchange Active Sync
  * Subsidiebeheer: Blokkeren
  * Staat: Uitgeschakeld

Volgorde van activering:

1. Sluit ContingencyAccess, CoreAdmins en EmergencyAccess uit het bestaande MFA-beleid. Controleer of een gebruiker in ContingencyAccess toegang heeft tot SharePoint Online en Exchange Online.
2. Beleid 1 inschakelen: Gebruikers op domeinsamengevoegde apparaten die geen deel uitmaken van de uitgesloten groepen, kunnen toegang krijgen tot Exchange Online en SharePoint Online. Controleer of gebruikers in de groep Uitsluiten toegang hebben tot SharePoint Online en Exchange vanaf elk apparaat.
3. Beleid 2 inschakelen: controleer of gebruikers die geen deel uitmaken van de groep uitsluiten, niet vanaf hun mobiele apparaten naar SharePoint Online en Exchange Online kunnen gaan. Controleer of gebruikers in de groep Uitsluiten toegang hebben tot SharePoint en Exchange vanaf elk apparaat (Windows/iOS/Android).
4. Beleid 3 inschakelen: Controleer of gebruikers die zich niet in de uitgesloten groepen bevinden geen toegang hebben tot SharePoint en Exchange buiten het bedrijfsnetwerk, zelfs met een domeingekoppelde machine. Controleer of gebruikers in de groep Uitsluiten toegang hebben tot SharePoint en Exchange vanuit elk netwerk.
5. Beleid 4 inschakelen: controleer of alle gebruikers Exchange Online niet kunnen ophalen van de native mailtoepassingen op mobiele apparaten.
6. Schakel het bestaande MFA-beleid voor SharePoint Online en Exchange Online uit.

In dit volgende voorbeeld wordt **bijvoorbeeld B - Ca-beleid voor onvoorziene omstandigheden om mobiele toegang tot Salesforce toe te staan,** de toegang van een zakelijke app hersteld. In dit scenario vereist de klant doorgaans dat zijn verkoopmedewerkers toegang hebben tot Salesforce (geconfigureerd voor één melding bij Azure AD) vanaf mobiele apparaten om alleen vanaf compatibele apparaten toe te staan. De verstoring in dit geval is dat er een probleem is met het evalueren van de naleving van apparaten en de storing plaatsvindt op een gevoelig moment waarop het verkoopteam toegang tot Salesforce nodig heeft om deals te sluiten. Dit noodbeleid geeft kritieke gebruikers toegang tot Salesforce vanaf een mobiel apparaat, zodat ze deals kunnen blijven sluiten en het bedrijf niet kunnen verstoren. In dit voorbeeld bevat **SalesforceContingency** alle Sales-medewerkers die toegang moeten behouden en **Bevat SalesAdmins** de benodigde beheerders van Salesforce.

**Voorbeeld B - Ca-beleid voor onvoorziene gebeurtenissen:**

* Beleid 1: Blokkeer iedereen die niet deel uit maakt van het SalesContingency-team
  * Naam: EM001 - ENABLE IN EMERGENCY: Device Compliance Disruption[1/2] - Salesforce - Block All users except SalesforceContingency
  * Gebruikers en groepen: alle gebruikers opnemen. SalesAdmins en SalesforceContingency uitsluiten
  * Cloud-apps: Salesforce.
  * Voorwaarden: geen
  * Subsidiebeheer: Blokkeren
  * Staat: Uitgeschakeld
* Beleid 2: Blokkeer het verkoopteam vanaf een ander platform dan mobiel (om het oppervlak van de aanval te verminderen)
  * Naam: EM002 - ENABLE IN EMERGENCY: Device Compliance Disruption[2/2] - Salesforce - Block All platforms behalve iOS en Android
  * Gebruikers en groepen: Neem SalesforceContingency op. Verkoopbeheerders uitsluiten
  * Cloud-apps: Salesforce
  * Voorwaarden: Apparaatplatform Omvatten alle platforms, sluit iOS en Android uit
  * Subsidiebeheer: Blokkeren
  * Staat: Uitgeschakeld

Volgorde van activering:

1. Sluit SalesAdmins en Salesforce uit van het bestaande beleid voor naleving van apparaten voor Salesforce. Controleer of een gebruiker in de SalesforceContingency-groep toegang heeft tot Salesforce.
2. Beleid 1 inschakelen: gebruikers buiten SalesContingency verifiëren heeft geen toegang tot Salesforce. Controleer of gebruikers in de SalesAdmins en Salesforce toegang hebben tot Salesforce.
3. Beleid 2 inschakelen: Controleer of gebruikers in de groep SalesContingency geen toegang hebben tot Salesforce vanaf hun Windows/Mac-laptops, maar nog steeds toegang hebben vanaf hun mobiele apparaten. Controleer of SalesAdmin nog steeds toegang heeft tot Salesforce vanaf elk apparaat.
4. Schakel het bestaande nalevingsbeleid voor apparaten voor Salesforce uit.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Wachtwoordhashsynchronisatie implementeren, zelfs als u gefedereerd bent of pass-through-verificatie gebruikt

Gebruikersuitsluiting kan ook optreden als de volgende voorwaarden waar zijn:

- Uw organisatie maakt gebruik van een hybride identiteitsoplossing met pass-through authenticatie of federatie.
- Uw on-premises identiteitssystemen (zoals Active Directory, AD FS of een afhankelijke component) zijn niet beschikbaar. 
 
Om veerkrachtiger te zijn, moet uw organisatie [wachtwoordhashsynchronisatie inschakelen,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)omdat u hiermee [overschakelen naar het gebruik van wachtwoordhashsynchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) als uw on-premises identiteitssystemen zijn uitgeschakeld.

#### <a name="microsoft-recommendations"></a>Microsoft-aanbevelingen
 Wachtwoordhashsynchroniseren inschakelen met de wizard Azure AD Connect, ongeacht of uw organisatie federatie- of doorgeefverificatie gebruikt.

>[!IMPORTANT]
> Het is niet vereist om gebruikers om te zetten van federatieve naar beheerde verificatie om wachtwoordhashsynchronisatie te gebruiken.

## <a name="during-a-disruption"></a>Tijdens een storing

Als u hebt gekozen voor het implementeren van een mitigatieplan, u automatisch één onderbreking van de toegangscontrole overleven. Als u er echter voor hebt gekozen om een noodplan te maken, u uw noodbeleid activeren tijdens de onderbreking van de toegangscontrole:

1. Schakel uw noodbeleid in dat gerichte gebruikers toegang verleent tot specifieke apps, vanuit specifieke netwerken.
2. Schakel uw normale beleid op basis van controle uit.

### <a name="microsoft-recommendations"></a>Microsoft-aanbevelingen

Afhankelijk van welke beperkingen of onvoorziene omstandigheden worden gebruikt tijdens een onderbreking, kan uw organisatie toegang verlenen met alleen wachtwoorden. Geen enkele waarborg is een aanzienlijk veiligheidsrisico dat zorgvuldig moet worden afgewogen. Organisaties moeten:

1. Als onderdeel van uw strategie voor wijzigingscontrole documenteert u elke wijziging en de vorige status om alle onvoorziene gebeurtenissen die u hebt geïmplementeerd, terug te kunnen draaien zodra de toegangscontroles volledig operationeel zijn.
2. Ga ervan uit dat kwaadwillende actoren zullen proberen om wachtwoorden te oogsten door middel van wachtwoord spray of phishing-aanvallen, terwijl u MFA uitgeschakeld. Slechte acteurs kunnen ook al wachtwoorden hebben die eerder geen toegang hebben tot een bron die in dit venster kan worden geprobeerd. Voor kritieke gebruikers, zoals leidinggevenden, u dit risico gedeeltelijk beperken door hun wachtwoorden opnieuw in te stellen voordat u MFA voor hen uitschakelt.
3. Archiveer alle aanmeldingsactiviteiten om te bepalen wie toegang heeft tot wat gedurende de tijd dat MFA is uitgeschakeld.
4. [Triage alle risicodetecties gemeld](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) tijdens dit venster.

## <a name="after-a-disruption"></a>Na een storing

Maak de wijzigingen die u hebt aangebracht als onderdeel van het geactiveerde noodplan ongedaan nadat de service is hersteld en de storing heeft veroorzaakt. 

1. Het reguliere beleid inschakelen
2. Schakel uw noodbeleid uit. 
3. Alle andere wijzigingen die u tijdens de onderbreking hebt aangebracht en gedocumenteerd, terugdraaien.
4. Als u een account voor noodtoegang hebt gebruikt, moet u de referenties opnieuw genereren en de nieuwe referenties fysiek beveiligen als onderdeel van uw procedures voor een noodtoegangsaccount.
5. Doorgaan [met triage alle risicodetecties gemeld](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) na de verstoring voor verdachte activiteit.
6. Alle vernieuwingstokens intrekken die zijn uitgegeven [met PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) om een reeks gebruikers te targeten. Het intrekken van alle vernieuwingstokens is belangrijk voor bevoegde accounts die tijdens de onderbreking worden gebruikt en als ze dit doen, dwingt ze om opnieuw te verifiëren en te voldoen aan de controle van het herstelde beleid.

## <a name="emergency-options"></a>Noodopties

 In geval van nood en uw organisatie niet eerder een mitigatie- of noodplan heeft geïmplementeerd, volgt u de aanbevelingen in de sectie [Onvoorziene omstandigheden voor vergrendeling van gebruikers](#contingencies-for-user-lockout) als ze al beleid voor voorwaardelijke toegang gebruiken om MFA af te dwingen.
Als uw organisatie mfa-legacy-beleid per gebruiker gebruikt, u het volgende alternatief overwegen:

1. Als u het uitgaande IP-adres van het bedrijfsnetwerk hebt, u deze toevoegen als vertrouwde IP's om verificatie alleen aan het bedrijfsnetwerk in te schakelen.
   1. Als u niet beschikt over de inventaris van uitgaande IP-adressen of als u toegang binnen en buiten het bedrijfsnetwerk moet inschakelen, u de volledige IPv4-adresruimte toevoegen als vertrouwde IP's door 0.0.0.0/1 en 128.0.0.0/1 op te geven.

>[!IMPORTANT]
 > Als u de vertrouwde IP-adressen verruimt om de toegang te deblokkeren, worden risicodetecties die zijn gekoppeld aan IP-adressen (bijvoorbeeld onmogelijke reizen of onbekende locaties) niet gegenereerd.

>[!NOTE]
 > Het configureren van [vertrouwde IP's](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) voor Azure MFA is alleen beschikbaar met [Azure AD Premium-licenties.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)

## <a name="learn-more"></a>Meer informatie

* [Documentatie voor Azure AD-verificatie](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Beheer beheer van beheeraccounts voor noodgevallen in Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Benoemde locaties configureren in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Hybride Azure Active Directory-samengevoegde apparaten configureren](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Implementatiehandleiding Windows Hello voor Bedrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Wachtwoordrichtlijnen - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Wat zijn de voorwaarden in voorwaardelijke toegang voor Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Wat zijn toegangsbesturingselementen in Voorwaardelijke toegang tot Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
