---
title: Verificatie voor hybride identiteitsoplossingen voor Azure AD
titleSuffix: Active Directory
description: Deze handleiding helpt CEO's, CIO's, CISO's, Chief Identity Architects, Enterprise Architects en Security- en IT-besluitvormers die verantwoordelijk zijn voor het kiezen van een verificatiemethode voor hun azure AD-hybride identiteitsoplossing in middelgrote tot grote organisaties.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e263ecde532a8aaed420932bf355910da201723e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365852"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>De juiste verificatiemethode kiezen voor uw azure Active Directory hybride identiteitsoplossing

Het kiezen van de juiste verificatiemethode is de eerste zorg voor organisaties die hun apps naar de cloud willen verplaatsen. Neem deze beslissing niet lichtvaardig, om de volgende redenen:

1. Het is de eerste beslissing voor een organisatie die wil verhuizen naar de cloud.

2. De verificatiemethode is een essentieel onderdeel van de aanwezigheid van een organisatie in de cloud. Het regelt de toegang tot alle cloudgegevens en -bronnen.

3. Het is de basis van alle andere geavanceerde functies voor beveiliging en gebruikerservaring in Azure AD.

Identiteit is het nieuwe controlevlak van IT-beveiliging, dus authenticatie is de toegangsbewaker van een organisatie voor de nieuwe cloudwereld. Organisaties hebben een identiteitscontrolevliegtuig nodig dat hun beveiliging versterkt en hun cloud-apps beschermt tegen indringers.

> [!NOTE]
> Het wijzigen van uw verificatiemethode vereist planning, testen en mogelijk downtime. [Gefaseerde implementatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) is een geweldige manier om te testen en geleidelijk te migreren van federatie naar cloudverificatie.

### <a name="out-of-scope"></a>Buiten het toepassingsgebied
Organisaties die geen bestaande on-premises mapfootprint hebben, richten zich niet op dit artikel. Doorgaans maken deze bedrijven alleen identiteiten in de cloud, waarvoor geen hybride identiteitsoplossing vereist is. Cloud-only identiteiten bestaan alleen in de cloud en zijn niet gekoppeld aan overeenkomstige on-premises identiteiten.

## <a name="authentication-methods"></a>Verificatiemethoden
Wanneer de hybride identiteitsoplossing van Azure AD uw nieuwe besturingsvlak is, is verificatie de basis van cloudtoegang. Het kiezen van de juiste verificatiemethode is een cruciale eerste beslissing bij het opzetten van een hybride azure-identiteitsoplossing voor AD. Implementeer de verificatiemethode die is geconfigureerd met Azure AD Connect, waarin gebruikers ook in de cloud worden voorzien.

Als u een verificatiemethode wilt kiezen, moet u rekening houden met de tijd, bestaande infrastructuur, complexiteit en kosten van het implementeren van uw keuze. Deze factoren zijn verschillend voor elke organisatie en kunnen in de loop van de tijd veranderen.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD ondersteunt de volgende verificatiemethoden voor hybride identiteitsoplossingen.

### <a name="cloud-authentication"></a>Cloudverificatie
Wanneer u deze verificatiemethode kiest, verwerkt Azure AD het aanmeldingsproces van gebruikers. In combinatie met naadloze single sign-on (SSO) kunnen gebruikers zich aanmelden bij cloud-apps zonder dat ze hun referenties opnieuw hoeven in te voeren. Met cloudverificatie u kiezen uit twee opties:

**Synchronisatie van Azure AD-wachtwoordhash**. De eenvoudigste manier om verificatie in te schakelen voor on-premises directoryobjecten in Azure AD. Gebruikers kunnen dezelfde gebruikersnaam en hetzelfde wachtwoord gebruiken als ze on-premises gebruiken zonder dat ze extra infrastructuur hoeven te implementeren. Voor sommige premium functies van Azure AD, zoals Identiteitsbeveiliging en [Azure AD Domain Services,](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)is synchronisatie van wachtwoordhash vereist, ongeacht welke verificatiemethode u kiest.

> [!NOTE]
> Wachtwoorden worden nooit opgeslagen in duidelijke tekst of versleuteld met een omkeerbaar algoritme in Azure AD. Zie Synchronisatie van [wachtwoordhash implementeren met Azure AD Connect-synchronisatie voor](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)meer informatie over het werkelijke proces van synchronisatie van wachtwoordhash.

**Azure AD-pass-through-verificatie**. Biedt een eenvoudige wachtwoordvalidatie voor Azure AD-verificatieservices met behulp van een softwareagent die op een of meer on-premises servers wordt uitgevoerd. De servers valideren de gebruikers rechtstreeks met uw on-premises Active Directory, wat ervoor zorgt dat de wachtwoordvalidatie niet in de cloud plaatsvindt.

Bedrijven met een beveiligingsvereiste om on-premises gebruikersaccountstatussen, wachtwoordbeleid en aanmeldingsuren onmiddellijk af te dwingen, kunnen deze verificatiemethode gebruiken. Zie [Gebruikersaanmelding met Azure AD-verificatie voor](../../active-directory/hybrid/how-to-connect-pta.md)meer informatie over het daadwerkelijke verificatieproces voor doorgeefing.

### <a name="federated-authentication"></a>Federatieve verificatie
Wanneer u deze verificatiemethode kiest, geeft Azure AD het verificatieproces door aan een afzonderlijk vertrouwd verificatiesysteem, zoals on-premises Active Directory Federation Services (AD FS), om het wachtwoord van de gebruiker te valideren.

Het verificatiesysteem kan aanvullende geavanceerde verificatievereisten bieden. Voorbeelden zijn smartcardgebaseerde authenticatie of multifactorauthenticatie van derden. Zie [Active Directory Federation Services implementeren](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)voor meer informatie.

In de volgende sectie u bepalen welke verificatiemethode geschikt is voor u met behulp van een beslissingsstructuur. Hiermee u bepalen of u cloud- of federatieve verificatie wilt implementeren voor uw azure AD-hybride identiteitsoplossing.

## <a name="decision-tree"></a>Beslissingsstructuur

![Beslissingsstructuur voor Azure AD-verificatie](./media/choose-ad-authn/azure-ad-authn-image1.png)

Details over beslissingsvragen:

1. Azure AD kan aanmelding voor gebruikers verwerken zonder afhankelijk te zijn van on-premises onderdelen om wachtwoorden te verifiëren.
2. Azure AD kan zich aanmelden voor gebruikers bij een vertrouwde verificatieprovider zoals ad FS van Microsoft.
3. Als u active directory-beveiligingsbeleid op gebruikersniveau moet toepassen, zoals het verlopen account, het uitgeschakelde account, het verlopen wachtwoord, de account vergrendeld en aanmeldingsuren voor elke aanmeldingsprocedure van elke gebruiker, heeft Azure AD een aantal on-premises onderdelen vereist.
4. Aanmeldingsfuncties die niet native worden ondersteund door Azure AD:
   * Log in met smartcards of certificaten.
   * Aanmelden met on-premises MFA Server.
   * Aanmelden met verificatieoplossing van derden.
   * Multi-site on-premises authenticatieoplossing.
5. Azure AD-identiteitsbeveiliging vereist Password Hash Sync, ongeacht welke aanmeldingsmethode u kiest, om het rapport Gebruikers te voorzien *van gelekte referenties.* Organisaties kunnen niet naar Password Hash Sync als hun primaire aanmeldingsmethode mislukt en deze is geconfigureerd vóór de foutgebeurtenis.

> [!NOTE]
> Azure AD-identiteitsbeveiliging vereist [Azure AD Premium P2-licenties.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Gedetailleerde overwegingen

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloudverificatie: synchronisatie van wachtwoordhash

* **Inspanning**. Synchronisatie van wachtwoordhash vereist de minste inspanning met betrekking tot implementatie, onderhoud en infrastructuur.  Dit inspanningsniveau is meestal van toepassing op organisaties die alleen hun gebruikers nodig hebben om zich aan te melden bij Office 365, SaaS-apps en andere Azure AD-bronnen. Wanneer u de synchronisatie van wachtwoordhash hebt ingeschakeld, maakt het deel uit van het Azure AD Connect-synchronisatieproces en wordt elke twee minuten uitgevoerd.

* **Gebruikerservaring**. Als u de aanmeldingservaring van gebruikers wilt verbeteren, implementeert u naadloze SSO met wachtwoordhashsynchronisatie. Naadloze SSO elimineert onnodige aanwijzingen wanneer gebruikers zijn aangemeld.

* **Geavanceerde scenario's**. Als organisaties hiervoor kiezen, is het mogelijk om inzichten uit identiteiten te gebruiken met Azure AD Identity Protection-rapporten met Azure AD Premium P2. Een voorbeeld is het rapport met gelekte referenties. Windows Hello for Business heeft [specifieke vereisten wanneer u wachtwoordhashsynchronisatie gebruikt.](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification) [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) vereist wachtwoordhashsynchronisatie om gebruikers te voorzien van hun bedrijfsreferenties in het beheerde domein.

    Organisaties die multifactorauthenticatie met wachtwoordhashsynchronisatie vereisen, moeten Azure AD-multifactorauthenticatie of [aangepaste besturingselementen voor voorwaardelijke toegang](../../active-directory/conditional-access/controls.md#custom-controls-preview)gebruiken. Deze organisaties kunnen geen gebruik maken van methoden voor multifactorauthenticatie van derden of on-premises factoren die afhankelijk zijn van federatie.

> [!NOTE]
> Azure AD Conditional Access vereist [Azure AD Premium P1-licenties.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Bedrijfscontinuïteit**. Het gebruik van wachtwoordhashsynchronisatie met cloudverificatie is zeer beschikbaar als cloudservice die wordt geschaald naar alle Microsoft-datacenters. Als u ervoor wilt zorgen dat wachtwoordhashsynchronisatie niet gedurende langere perioden wordt uitgevoerd, implementeert u een tweede Azure AD Connect-server in de faseringsmodus in een stand-byconfiguratie.

* **Overwegingen**. Momenteel worden wijzigingen in on-premises accountstatussen niet onmiddellijk doorgevoerd. In deze situatie heeft een gebruiker toegang tot cloud-apps totdat de status van het gebruikersaccount is gesynchroniseerd met Azure AD. Organisaties willen deze beperking mogelijk overwinnen door een nieuwe synchronisatiecyclus uit te voeren nadat beheerders bulkupdates uitvoeren voor on-premises gebruikersaccountstatussen. Een voorbeeld is het uitschakelen van accounts.

> [!NOTE]
> Het wachtwoord is verlopen en de status van het account dat is vergrendeld, wordt momenteel niet gesynchroniseerd met Azure AD met Azure AD Connect. Wanneer u het wachtwoord van een gebruiker wijzigt en de gebruiker instelt dat het *wachtwoord moet worden gewijzigd bij de volgende aanmeldingsvlag,* wordt de wachtwoordhash niet gesynchroniseerd met Azure AD met Azure AD Connect totdat de gebruiker zijn wachtwoord heeft gewijzigd.

Raadpleeg [het implementeren van wachtwoordhashsynchronisatie](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) voor implementatiestappen.

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudverificatie: verificatie doorgeven  

* **Inspanning**. Voor pass-through authenticatie, moet u een of meer (we raden drie) lichtgewicht agents geïnstalleerd op bestaande servers. Deze agents moeten toegang hebben tot uw on-premises Active Directory Domain Services, inclusief uw on-premises AD-domeincontrollers. Ze hebben uitgaande toegang tot het internet en toegang tot uw domeincontrollers nodig. Om deze reden wordt het niet ondersteund om de agents in een perimeternetwerk te implementeren.

    Pass-through Authentication vereist onbeperkte netwerktoegang tot domeincontrollers. Al het netwerkverkeer is versleuteld en beperkt tot verificatieverzoeken. Voor meer informatie over dit proces, zie de [beveiliging diepe duik](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) op pass-through authenticatie.

* **Gebruikerservaring**. Als u de aanmeldingservaring van gebruikers wilt verbeteren, implementeert u naadloze SSO met Pass-through Authentication. Naadloze SSO elimineert onnodige aanwijzingen nadat gebruikers zich hebben aangemeld.

* **Geavanceerde scenario's**. Pass-through Authentication dwingt het on-premises accountbeleid af op het moment van aanmelden. De toegang wordt bijvoorbeeld geweigerd wanneer de accountstatus van een on-premises gebruiker is uitgeschakeld, is vergrendeld of zijn [wachtwoord verloopt](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) of de aanmeldingspoging buiten de uren valt waarop de gebruiker zich mag aanmelden.

    Organisaties die multifactorauthenticatie met pass-through-verificatie vereisen, moeten Azure Multi-Factor Authentication (MFA) of [Aangepaste besturing voor voorwaardelijke toegang](../../active-directory/conditional-access/controls.md#custom-controls-preview)gebruiken. Deze organisaties kunnen geen gebruik maken van een externe of on-premises multifactorauthenticatiemethode die afhankelijk is van federatie. Geavanceerde functies vereisen dat wachtwoordhashsynchronisatie wordt geïmplementeerd, ongeacht of u kiest voor pass-through-verificatie. Een voorbeeld is het gelekte referentiesrapport van Identiteitsbescherming.

* **Bedrijfscontinuïteit**. We raden u aan twee extra verificatieagents voor doorgeefgegevens in te zetten. Deze extra's zijn een aanvulling op de eerste agent op de Azure AD Connect-server. Deze extra implementatie zorgt voor een hoge beschikbaarheid van verificatieverzoeken. Wanneer u drie agents hebt geïmplementeerd, kan één agent nog steeds falen wanneer een andere agent is uitgeschakeld voor onderhoud.

    Er is nog een voordeel aan het implementeren van wachtwoordhashsynchronisatie naast pass-through-verificatie. Het fungeert als een back-up verificatiemethode wanneer de primaire verificatiemethode niet meer beschikbaar is.

* **Overwegingen**. U wachtwoordhashsynchronisatie gebruiken als een verificatiemethode voor het doorgeven van wachtwoorden, wanneer de agents de referenties van een gebruiker niet kunnen valideren vanwege een aanzienlijke on-premises fout. Niet overwerken naar wachtwoordhashsynchronisatie gebeurt niet automatisch en u moet Azure AD Connect gebruiken om de aanmeldingsmethode handmatig te schakelen.

    Voor andere overwegingen over pass-through-verificatie, waaronder alternatieve id-ondersteuning, raadpleegt u [veelgestelde vragen.](../../active-directory/hybrid/how-to-connect-pta-faq.md)

Raadpleeg [de implementatie van pass-through-verificatie](../../active-directory/hybrid/how-to-connect-pta.md) voor implementatiestappen.

### <a name="federated-authentication"></a>Federatieve verificatie

* **Inspanning**. Een federatief verificatiesysteem is afhankelijk van een extern vertrouwd systeem om gebruikers te verifiëren. Sommige bedrijven willen hun bestaande federatieve systeeminvestering hergebruiken met hun Azure AD-hybride identiteitsoplossing. Het onderhoud en beheer van het federatieve systeem valt buiten de controle van Azure AD. Het is aan de organisatie om het federatieve systeem te gebruiken om ervoor te zorgen dat het veilig wordt geïmplementeerd en de verificatiebelasting kan verwerken.

* **Gebruikerservaring**. De gebruikerservaring van federatieve verificatie is afhankelijk van de implementatie van de functies, topologie en configuratie van de federatiefarm. Sommige organisaties hebben deze flexibiliteit nodig om de toegang tot de federatiefarm aan te passen en te configureren aan hun beveiligingsvereisten. Het is bijvoorbeeld mogelijk om intern verbonden gebruikers en apparaten te configureren om gebruikers automatisch aan te melden, zonder hen om referenties te vragen. Deze configuratie werkt omdat ze zich al hebben aangemeld bij hun apparaten. Indien nodig maken sommige geavanceerde beveiligingsfuncties het aanmeldingsproces van gebruikers moeilijker.

* **Geavanceerde scenario's**. Een federatieve verificatieoplossing is meestal vereist wanneer klanten een verificatievereiste hebben die Azure AD niet native ondersteunt. Zie gedetailleerde informatie om u te helpen [bij het kiezen van de juiste aanmeldingsoptie.](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/) Houd rekening met de volgende algemene vereisten:

  * Verificatie waarvoor smartcards of certificaten nodig zijn.
  * On-premises MFA-servers of externe multifactorproviders die een federatieve identiteitsprovider vereisen.
  * Verificatie met behulp van verificatieoplossingen van derden. Zie de [compatibiliteitslijst van azure AD-federatie](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Aanmelden waarvoor bijvoorbeeld een sAMAccountName, bijvoorbeeld DOMAIN\username, in plaats van een user@domain.comgebruikersnaam (UPN) vereist is.

* **Bedrijfscontinuïteit**. Federatieve systemen vereisen doorgaans een load-balanced array van servers, bekend als een farm. Deze farm is geconfigureerd in een interne netwerk- en perimeternetwerktopologie om een hoge beschikbaarheid voor verificatieaanvragen te garanderen.

    Implementeren van wachtwoordhashsynchronisatie samen met federatieve verificatie als een verificatiemethode voor back-ups wanneer de primaire verificatiemethode niet meer beschikbaar is. Een voorbeeld hiervan is wanneer de on-premises servers niet beschikbaar zijn. Sommige grote bedrijfsorganisaties hebben een federatieoplossing nodig om meerdere internetinvallende punten te ondersteunen die zijn geconfigureerd met geo-DNS voor verificatieverzoeken met lage latentie.

* **Overwegingen**. Federatieve systemen vereisen doorgaans een grotere investering in on-premises infrastructuur. De meeste organisaties kiezen voor deze optie als ze al een on-premises federatie investering hebben. En als het een sterke zakelijke eis is om een single-identity provider te gebruiken. Federation is complexer om te bedienen en problemen op te lossen in vergelijking met cloudauthenticatieoplossingen.

Voor een niet-routeerbaar domein dat niet kan worden geverifieerd in Azure AD, hebt u extra configuratie nodig om de aanmelding van gebruikers-id's te implementeren. Deze vereiste wordt alternate login ID-ondersteuning genoemd. Zie [Alternatieve aanmeldings-id configureren](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) voor beperkingen en vereisten. Als u ervoor kiest om een externe multi-factor authenticatieprovider met federatie te gebruiken, moet u ervoor zorgen dat de provider WS-Trust ondersteunt om apparaten toe te staan lid te worden van Azure AD.

Raadpleeg [Federatieservers implementeren](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) voor implementatiestappen.

> [!NOTE]
> Wanneer u uw Azure AD-hybride identiteitsoplossing implementeert, moet u een van de ondersteunde topologieën van Azure AD Connect implementeren. Meer informatie over ondersteunde en niet-ondersteunde configuraties bij [Topologieën voor Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Architectuurdiagrammen

In de volgende diagrammen worden de architectuurcomponenten op hoog niveau beschreven die nodig zijn voor elke verificatiemethode die u gebruiken met uw azure AD-hybride identiteitsoplossing. Ze bieden een overzicht om u te helpen de verschillen tussen de oplossingen te vergelijken.

* Eenvoud van een wachtwoordhashsynchronisatieoplossing:

    ![Hybride azure AD-identiteit met wachtwoordhashsynchronisatie](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Agentvereisten voor pass-through-verificatie, met behulp van twee agents voor redundantie:

    ![Hybride Azure AD-identiteit met Pass-through-verificatie](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Onderdelen die nodig zijn voor federatie in uw perimeter en intern netwerk van uw organisatie:

    ![Hybride Azure AD-identiteit met federatieve verificatie](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Methoden vergelijken

|Overweging|Synchronisatie van wachtwoordhash + Naadloze SSO|Pass-through Authenticatie + Naadloze SSO|Federatie met AD FS|
|:-----|:-----|:-----|:-----|
|Waar vindt verificatie plaats?|In de cloud|In de cloud na een veilige verificatievan het wachtwoord met de on-premises verificatieagent|On-premises|
|Wat zijn de on-premises serververeisten buiten het inrichtingssysteem: Azure AD Connect?|Geen|Eén server voor elke extra verificatieagent|Twee of meer AD FS-servers<br><br>Twee of meer WAP-servers in het perimeter/DMZ-netwerk|
|Wat zijn de vereisten voor on-premises internet en netwerken buiten het inrichtingssysteem?|Geen|[Uitgaande internettoegang vanaf](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) de servers waarop verificatieagents worden uitgevoerd|[Binnenkomende internettoegang tot](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) WAP-servers in de perimeter<br><br>Binnenkomende netwerktoegang tot AD FS-servers vanaf WAP-servers in de perimeter<br><br>Taakverdeling voor netwerken|
|Is er een TLS/SSL-certificaatvereiste?|Nee|Nee|Ja|
|Is er een oplossing voor gezondheidsmonitoring?|Niet vereist|Agentstatus geleverd door [Azure Active Directory-beheercentrum](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health (Engelstalig)](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Krijgen gebruikers één aanmelding voor cloudbronnen van met een domein verbonden apparaten binnen het bedrijfsnetwerk?|Ja met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja|
|Welke aanmeldingstypen worden ondersteund?|UserPrincipalName + wachtwoord<br><br>Windows-geïntegreerde verificatie met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatieve aanmeldings-id](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + wachtwoord<br><br>Windows-geïntegreerde verificatie met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatieve aanmeldings-id](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + wachtwoord<br><br>sAMAccountName + wachtwoord<br><br>Windows-geïntegreerde verificatie<br><br>[Certificaat- en smartcardverificatie](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatieve aanmeldings-id](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Wordt Windows Hello for Business ondersteund?|[Sleutelvertrouwensmodel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Sleutelvertrouwensmodel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Vereist het functionele domeinniveau van Windows Server 2016*|[Sleutelvertrouwensmodel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Certificaatvertrouwensmodel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Wat zijn de multifactorauthenticatieopties?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Aangepaste besturingselementen met voorwaardelijke toegang*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Aangepaste besturingselementen met voorwaardelijke toegang*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA-server](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA van derden](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Aangepaste besturingselementen met voorwaardelijke toegang*](../../active-directory/conditional-access/controls.md)|
|Welke gebruikersaccountstatussen worden ondersteund?|Uitgeschakelde accounts<br>(tot 30 minuten vertraging)|Uitgeschakelde accounts<br><br>Account is vergrendeld<br><br>Account verlopen<br><br>Wachtwoord verlopen<br><br>Inloguren|Uitgeschakelde accounts<br><br>Account is vergrendeld<br><br>Account verlopen<br><br>Wachtwoord verlopen<br><br>Inloguren|
|Wat zijn de opties voor voorwaardelijke toegang?|[Voorwaardelijke toegang voor Azure AD, met Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Voorwaardelijke toegang voor Azure AD, met Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Voorwaardelijke toegang voor Azure AD, met Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS-claimregels](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Wordt het blokkeren van verouderde protocollen ondersteund?|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|u het logo, de afbeelding en de beschrijving op de aanmeldingspagina's aanpassen?|[Ja, met Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja, met Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Welke geavanceerde scenario's worden ondersteund?|[Slimme wachtwoorduitsluiting](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Rapporten over gelekte referenties, met Azure AD Premium P2](../../active-directory/reports-monitoring/concept-risk-events.md)|[Slimme wachtwoorduitsluiting](../../active-directory/authentication/howto-password-smart-lockout.md)|Multisite low-latency authenticatiesysteem<br><br>[AD FS extranet lock-out](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integratie met identiteitssystemen van derden](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Aangepaste besturingselementen in Azure AD Conditional Access ondersteunen momenteel geen apparaatregistratie.

## <a name="recommendations"></a>Aanbevelingen
Uw identiteitssysteem zorgt ervoor dat uw gebruikers toegang hebben tot cloud-apps en de zakelijke apps die u migreert en beschikbaar stelt in de cloud. Om geautoriseerde gebruikers productief en slecht te houden uit de gevoelige gegevens van uw organisatie, regelt verificatie de toegang tot apps.

Gebruik of schakel wachtwoordhashsynchronisatie in voor welke verificatiemethode u ook kiest, om de volgende redenen:

1. **Hoge beschikbaarheid en disaster recovery**. Pass-through Authentication en federation vertrouwen op on-premises infrastructuur. Voor pass-through-verificatie omvat de on-premises footprint de serverhardware en netwerken die de Pass-through Authentication-agents nodig hebben. Voor federatie is de on-premises voetafdruk nog groter. Het vereist servers in uw perimeternetwerk om proxyverificatieverzoeken en de interne federatieservers.

    Implementeer redundante servers om enkele storingspunten te voorkomen. Vervolgens worden verificatieverzoeken altijd onderhouden als een onderdeel uitvalt. Zowel pass-through authenticatie en federatie vertrouwen ook op domeincontrollers om te reageren op authenticatie verzoeken, die ook kan mislukken. Veel van deze componenten hebben onderhoud nodig om gezond te blijven. Storingen zijn waarschijnlijker wanneer onderhoud niet correct is gepland en geïmplementeerd. Voorkom uitval door wachtwoordhashsynchronisatie te gebruiken omdat de Microsoft Azure AD-cloudverificatieservice wereldwijd wordt geschaald en altijd beschikbaar is.

2. **On-premises uitval overleven**.  De gevolgen van een on-premises storing als gevolg van een cyber-aanval of ramp kan aanzienlijk zijn, variërend van reputatiemerkschade aan een verlamde organisatie niet in staat om te gaan met de aanval. Onlangs, veel organisaties waren het slachtoffer van malware-aanvallen, met inbegrip van gerichte ransomware, waardoor hun on-premises servers naar beneden te gaan. Wanneer Microsoft klanten helpt om met dit soort aanvallen om te gaan, ziet het twee categorieën organisaties:

   * Organisaties die eerder wachtwoordhashsynchronisatie hebben ingeschakeld, hebben hun verificatiemethode gewijzigd om wachtwoordhashsynchronisatie te gebruiken. Ze waren binnen een paar uur weer online. Door toegang tot e-mail via Office 365 te gebruiken, werkten ze aan het oplossen van problemen en toegang tot andere cloudgebaseerde workloads.

   * Organisaties die voorheen geen wachtwoordhashsynchronisatie hebben ingeschakeld, moesten hun toevlucht nemen tot niet-vertrouwde externe e-mailsystemen van consumenten voor communicatie om problemen op te lossen. In die gevallen duurde het weken om hun on-premises identiteitsinfrastructuur te herstellen, voordat gebruikers zich opnieuw konden aanmelden bij cloudgebaseerde apps.

3. **Identiteitsbescherming**. Een van de beste manieren om gebruikers in de cloud te beschermen is Azure AD Identity Protection met Azure AD Premium P2. Microsoft scant voortdurend het internet op lijsten van gebruikers en wachtwoorden die slechte acteurs verkopen en beschikbaar stellen op het dark web. Azure AD kan deze informatie gebruiken om te controleren of een van de gebruikersnamen en wachtwoorden in uw organisatie is aangetast. Daarom is het van cruciaal belang om synchronisatie van wachtwoordhash in te schakelen, ongeacht welke verificatiemethode u gebruikt, of het nu gaat om federatieve of doorgeefverificatie. Gelekte referenties worden gepresenteerd als een rapport. Gebruik deze informatie om gebruikers te blokkeren of te dwingen hun wachtwoorden te wijzigen wanneer ze proberen in te loggen met gelekte wachtwoorden.

## <a name="conclusion"></a>Conclusie

In dit artikel worden verschillende verificatieopties beschreven die organisaties kunnen configureren en implementeren om toegang tot cloud-apps te ondersteunen. Om te voldoen aan verschillende zakelijke, beveiligings- en technische vereisten, kunnen organisaties kiezen tussen wachtwoordhashsynchronisatie, Pass-through-verificatie en federatie.

Overweeg elke verificatiemethode. Voldoet de inspanning om de oplossing te implementeren en de ervaring van de gebruiker met het aanmeldingsproces aan uw bedrijfsvereisten? Evalueer of uw organisatie de geavanceerde scenario's en bedrijfscontinuïteitsfuncties van elke verificatiemethode nodig heeft. Evalueer ten slotte de overwegingen van elke verificatiemethode. Weerhoudt een van hen u ervan uw keuze uit te voeren?

## <a name="next-steps"></a>Volgende stappen

In de wereld van vandaag, bedreigingen zijn aanwezig 24 uur per dag en komen van overal. Implementeer de juiste verificatiemethode en het zal uw beveiligingsrisico's beperken en uw identiteit beschermen.

[Ga aan de slag](../../active-directory/fundamentals/get-started-azure-ad.md) met Azure AD en implementeer de juiste verificatieoplossing voor uw organisatie.

Als u overweegt om te migreren van federatieve naar cloudverificatie, leest u meer over [het wijzigen van de aanmeldingsmethode.](../../active-directory/hybrid/plan-connect-user-signin.md) Als u de migratie wilt plannen en implementeren, gebruikt u [deze projectimplementatieplannen](https://aka.ms/deploymentplans) of overweegt u de nieuwe functie [Gefaseerde implementatie](../../active-directory/hybrid/how-to-connect-staged-rollout.md) te gebruiken om federatieve gebruikers te migreren naar het gebruik van cloudverificatie in een gefaseerde aanpak.
