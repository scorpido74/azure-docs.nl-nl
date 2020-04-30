---
title: On-premises apps publiceren met Azure AD-toepassingsproxy
description: Begrijp waarom toepassings proxy moet worden gebruikt voor het extern publiceren van on-premises webtoepassingen aan externe gebruikers. Meer informatie over de architectuur van de toepassings proxy, connectors, verificatie methoden en beveiligings voordelen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7047dfd0f02ffe95dcacfdf4ddc014047a338513
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79481191"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Azure AD-toepassingsproxy gebruiken om on-premises apps te publiceren voor externe gebruikers

Azure Active Directory (Azure AD) biedt veel mogelijkheden voor het beveiligen van gebruikers, apps en gegevens in de Cloud en on-premises. Met name de functie Azure AD-toepassingsproxy kan worden geïmplementeerd door IT-professionals die extern on-premises webtoepassingen willen publiceren. Externe gebruikers die toegang nodig hebben tot interne apps, kunnen deze op een veilige manier benaderen.

De mogelijkheid om veilig toegang te krijgen tot interne apps buiten uw netwerk, is nog belang rijker in de moderne werk plek. Met scenario's zoals BYOD (neem uw eigen apparaat mee) en mobiele apparaten worden IT-professionals aangevraagd om te voldoen aan twee doel einden:

* Eind gebruikers in staat stellen om overal en altijd productief te zijn
* Bedrijfs middelen te allen tijde beveiligen

Veel organisaties geloven dat ze van invloed zijn op het beheer en ze kunnen beveiligen als er resources zijn die zich binnen de grenzen van hun bedrijfs netwerken bevinden. Maar in de huidige digitale werk plek is deze grens uitgebreid met beheerde mobiele apparaten en resources en services in de Cloud. U moet nu de complexiteit beheren van de identiteit en gegevens van uw gebruikers die zijn opgeslagen op hun apparaten en apps.

Misschien maakt u al gebruik van Azure AD om gebruikers in de cloud te beheren die toegang nodig hebben tot Office 365 en andere SaaS-toepassingen, evenals Web-apps die on-premises worden gehost. Als u Azure AD al hebt, kunt u deze gebruiken als één besturings vlak om naadloze en veilige toegang tot uw on-premises toepassingen mogelijk te maken. Of misschien bent u nog steeds een lift naar de Cloud. Als dat het geval is, kunt u beginnen met de overstap naar de Cloud door toepassings proxy te implementeren en de eerste stap te nemen in het bouwen van een sterke identiteits basis.

De lijst hieronder illustreert een aantal van de dingen die u kunt inschakelen door app proxy te implementeren in een hybride scenario:

* On-premises web-apps extern publiceren op een eenvoudige manier zonder een DMZ
* Ondersteuning voor eenmalige aanmelding (SSO) op apparaten, resources en apps in de Cloud en on-premises
* Multi-factor Authentication ondersteunen voor apps in de Cloud en on-premises
* Maak snel gebruik van Cloud functies met de beveiliging van de Microsoft Cloud
* Beheer van gebruikers accounts centraliseren
* Beheer van identiteit en beveiliging centraliseren
* Gebruikers toegang tot toepassingen automatisch toevoegen of verwijderen op basis van groepslid maatschap

In dit artikel wordt uitgelegd hoe u met Azure AD en toepassings proxy externe gebruikers een SSO-ervaring (eenmalige aanmelding) kunt geven. Gebruikers maken veilig verbinding met on-premises apps zonder VPN-of Dual-Home-servers en firewall regels. Dit artikel helpt u te begrijpen hoe toepassings proxy de mogelijkheden en beveiligings voordelen van de Cloud biedt voor uw on-premises webtoepassingen. Hierin worden ook de architectuur en topologieën beschreven die mogelijk zijn.

## <a name="remote-access-in-the-past"></a>Externe toegang in het verleden

Voorheen was uw besturings vlak voor de bescherming van interne bronnen van aanvallers, terwijl de toegang door externe gebruikers is vergemakkelijkt in de DMZ of het perimeter netwerk. Maar de VPN-en reverse proxy-oplossingen die zijn geïmplementeerd in het DMZ dat door externe clients wordt gebruikt voor toegang tot bedrijfs bronnen, zijn niet geschikt voor de Cloud wereld. Ze lijden doorgaans aan de volgende nadelen:

* Hardwarekosten
* Beveiliging beheren (patches, poorten bewaken, enz.)
* Gebruikers aan de rand verifiëren
* Gebruikers verifiëren bij webservers in het perimeter netwerk
* VPN-toegang onderhouden voor externe gebruikers met de distributie en configuratie van VPN-client software. Houd ook rekening met servers die lid zijn van een domein in de DMZ, die kwetsbaar kunnen zijn voor aanvallen van buitenaf.

In de huidige Cloud-eerste wereld is Azure AD het meest geschikt om te bepalen wie en wat in uw netwerk wordt weer gegeven. Azure AD-toepassingsproxy integreert met moderne verificatie en Cloud technologieën, zoals SaaS-toepassingen en identiteits providers. Met deze integratie kunnen gebruikers overal toegang krijgen tot apps. Deze app is niet alleen geschikt voor de digitale werk plek van de huidige omgeving, maar is veiliger dan VPN-en reverse-proxy oplossingen en eenvoudiger te implementeren. Externe gebruikers hebben toegang tot uw on-premises toepassingen op dezelfde manier als de toegang tot O365 en andere SaaS-apps die zijn geïntegreerd met Azure AD. U hoeft uw toepassing niet te wijzigen of bij te werken om te kunnen werken met een toepassingsproxy. Bovendien hoeft u voor app-proxy geen inkomende verbindingen te openen via uw firewall. Met app proxy kunt u deze eenvoudigweg instellen en verg eten.

## <a name="the-future-of-remote-access"></a>De toekomst van externe toegang

In de huidige digitale werk plek werken gebruikers overal met meerdere apparaten en apps. De enige constante is de gebruikers-id. Daarom is het gebruik van de mogelijkheden voor [identiteits beheer van Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) vandaag de eerste stap voor het beveiligen van het netwerk. Een model dat gebruikmaakt van identiteit als uw besturings vlak, bestaat doorgaans uit de volgende onderdelen:

* Een id-provider om gebruikers en gebruikers informatie bij te houden.
* Device Directory voor het onderhouden van een lijst met apparaten die toegang hebben tot bedrijfs bronnen. Deze map bevat de bijbehorende apparaatgegevens (bijvoorbeeld type apparaat, integriteit enz.).
* De beleids evaluatie service om te bepalen of een gebruiker en apparaat voldoen aan het beleid dat is vastgelegd door beveiligings beheerders.
* De mogelijkheid om toegang tot resources van de organisatie toe te kennen of te weigeren.

Met toepassings proxy houdt Azure AD de gebruikers bij die toegang moeten hebben tot web-apps die on-premises en in de Cloud moeten worden gepubliceerd. Het biedt een centraal beheer punt voor die apps. Hoewel dit niet vereist is, is het raadzaam om ook voorwaardelijke toegang van Azure AD in te scha kelen. Door voor waarden te definiëren voor de manier waarop gebruikers toegang kunnen verifiëren en verkrijgen, zorgt u ervoor dat de juiste mensen toegang hebben tot toepassingen.

**Opmerking:** Het is belang rijk om te begrijpen dat Azure AD-toepassingsproxy is bedoeld als een VPN-of omgekeerde proxy vervanging voor zwervende (of externe) gebruikers die toegang nodig hebben tot interne bronnen. Het is niet bedoeld voor interne gebruikers in het bedrijfs netwerk. Interne gebruikers die onnodig een toepassings proxy gebruiken, kunnen onverwachte en ongewenste prestatie problemen veroorzaken.

![Azure Active Directory en al uw apps](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Een overzicht van de werking van de app proxy

Toepassings proxy is een Azure AD-service die u configureert in de Azure Portal. U kunt hiermee een extern open bare HTTP/HTTPS-URL-eind punt publiceren in de Azure-Cloud, die verbinding maakt met een interne toepassings server-URL in uw organisatie. Deze on-premises web-apps kunnen worden geïntegreerd met Azure AD ter ondersteuning van eenmalige aanmelding. Eind gebruikers hebben dan toegang tot on-premises web-apps op dezelfde manier als ze toegang hebben tot Office 365 en andere SaaS-apps.

Onderdelen van deze functie omvatten de Application proxy-service, die wordt uitgevoerd in de Cloud, de connector van de toepassings proxy, een licht gewicht agent die wordt uitgevoerd op een on-premises server en Azure AD, de ID-provider. Alle drie de onderdelen werken samen om de gebruiker te voorzien van een eenmalige aanmelding om toegang te krijgen tot on-premises webtoepassingen.

Nadat u zich hebt aangemeld, hebben externe gebruikers toegang tot on-premises webtoepassingen met behulp van een vertrouwde URL of het [MyApps-toegangs venster](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) van hun bureau blad of IOS/Mac-apparaten. App proxy kan bijvoorbeeld externe toegang en eenmalige aanmelding bieden voor Extern bureaublad, share point-sites, tableau, Qlik, Outlook op internet en LOB-toepassingen (line-of-Business).

![Architectuur van Azure AD-toepassingsproxy](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Verificatie

Er zijn verschillende manieren om een toepassing te configureren voor eenmalige aanmelding en de methode die u selecteert, is afhankelijk van de verificatie die door uw toepassing wordt gebruikt. Toepassings proxy biedt ondersteuning voor de volgende typen toepassingen:

* Webtoepassingen
* Web-Api's die u beschikbaar wilt maken voor uitgebreide toepassingen op verschillende apparaten
* Toepassingen die worden gehost achter een Extern bureaublad-gateway
* Uitgebreide client-apps die zijn geïntegreerd met de Active Directory Authentication Library (ADAL)

App proxy werkt met apps die gebruikmaken van het volgende systeem eigen verificatie Protocol:

* **[Geïntegreerde Windows-verificatie (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Voor IWA gebruiken de Application proxy-connectors Kerberos-beperkte delegering (KCD) om gebruikers te verifiëren bij de Kerberos-toepassing.

App proxy biedt ook ondersteuning voor de volgende verificatie protocollen met integratie van derden of in specifieke configuratie scenario's:

* [**Verificatie op basis van een header**](application-proxy-configure-single-sign-on-with-ping-access.md). Deze aanmeldings methode maakt gebruik van een verificatie service van derden met de naam PingAccess en wordt gebruikt wanneer de toepassing headers gebruikt voor verificatie. In dit scenario wordt verificatie afgehandeld door PingAccess.
* [**Verificatie op basis van formulieren of wacht woorden**](application-proxy-configure-single-sign-on-password-vaulting.md). Met deze verificatie methode melden gebruikers zich aan bij de toepassing met een gebruikers naam en wacht woord, de eerste keer dat ze deze gebruiken. Na de eerste aanmelding levert Azure AD de gebruikers naam en het wacht woord voor de toepassing. In dit scenario wordt verificatie verwerkt door Azure AD.
* [**SAML-verificatie**](application-proxy-configure-single-sign-on-on-premises-apps.md). Eenmalige aanmelding op basis van SAML wordt ondersteund voor toepassingen die gebruikmaken van de protocollen SAML 2,0 of WS-Federation. Met eenmalige aanmelding via SAML verifieert Azure AD bij de toepassing met behulp van het Azure AD-account van de gebruiker.

Zie voor meer informatie over ondersteunde methoden [één aanmeldings methode kiezen](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Beveiligings voordelen

De oplossing voor externe toegang die wordt aangeboden door de toepassings proxy en Azure AD biedt ondersteuning voor verschillende beveiligings voordelen die klanten kunnen gebruiken, met inbegrip van:

* **Geverifieerde toegang**. Toepassings proxy is het meest geschikt voor het publiceren van toepassingen met [verificatie vooraf](application-proxy-security.md#authenticated-access) om ervoor te zorgen dat alleen geverifieerde verbindingen in uw netwerk worden bereikt. Voor toepassingen die zijn gepubliceerd met verificatie vooraf, mag er geen verkeer worden door gegeven aan uw on-premises omgeving via de app-proxy service, zonder een geldig token. Verificatie vooraf, door de aard van de authenticatie, blokkeert een aanzienlijk aantal gerichte aanvallen, omdat alleen geverifieerde identiteiten toegang hebben tot de back-end-toepassing.
* **Voorwaardelijke toegang**. Geavanceerde beleids regels kunnen worden toegepast voordat verbindingen met uw netwerk tot stand worden gebracht. Met voorwaardelijke toegang kunt u beperkingen definiëren voor het verkeer dat u toestaat uw back-end-toepassing te bereiken. U maakt beleids regels die aanmeldingen beperken op basis van locatie, sterkte van verificatie en gebruikers risico profiel. Als voorwaardelijke toegang wordt ontwikkeld, worden er meer besturings elementen toegevoegd om extra beveiliging te bieden, zoals integratie met Microsoft Cloud App Security (MCAS). Met MCAS-integratie kunt u een on-premises toepassing voor [realtime-bewaking](application-proxy-integrate-with-microsoft-cloud-application-security.md) configureren door gebruik te maken van voorwaardelijke toegang om sessies in realtime te bewaken en te beheren op basis van beleid voor voorwaardelijke toegang.
* **Beëindiging van verkeer**. Al het verkeer naar de back-end-toepassing wordt beëindigd bij de service toepassings proxy in de Cloud terwijl de sessie opnieuw tot stand is gebracht met de back-endserver. Deze verbindings strategie houdt in dat uw back-endservers niet worden blootgesteld aan direct HTTP-verkeer. Ze zijn beter beschermd tegen specifieke DoS-aanvallen (Denial of service), omdat uw firewall niet is aangevallen.
* **Alle toegang is uitgaand**. De Application proxy-connectors gebruiken alleen uitgaande verbindingen met de Application proxy-service in de Cloud via de poorten 80 en 443. Zonder binnenkomende verbindingen is het niet nodig om firewall poorten te openen voor binnenkomende verbindingen of onderdelen in de DMZ. Alle verbindingen zijn uitgaand en via een beveiligd kanaal.
* **Security Analytics en op basis van machine learning (ml)**. Omdat het een deel is van Azure Active Directory, kan toepassings proxy gebruikmaken van [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) ( [Premium P2-licentie](https://azure.microsoft.com/pricing/details/active-directory/)vereist). Azure AD Identity Protection is een combi natie van beveiligings informatie voor machine learning met gegevensfeeds van de [digitale misdrijven eenheid](https://news.microsoft.com/stories/cybercrime/index.html) van micro soft en het [micro soft Security Response Center](https://www.microsoft.com/msrc) om gemanipuleerde accounts proactief te identificeren. Identiteits beveiliging biedt real-time beveiliging van aanmeldingen met een hoog risico. Het houdt rekening met factoren zoals toegang vanaf geïnfecteerde apparaten, via anoniem-netwerken, of op ongewoone en onwaarschijnlijke locaties om het risico profiel van een sessie te verg Roten. Dit risico profiel wordt gebruikt voor realtime-beveiliging. Veel van deze rapporten en gebeurtenissen zijn al beschikbaar via een API voor integratie met uw SIEM-systemen.

* **Externe toegang als een service**. U hoeft zich geen zorgen te maken over het onderhouden en patchen van on-premises servers om externe toegang in te scha kelen. Toepassings proxy is een Internet Scale-service die micro soft eigenaar is, zodat u altijd de meest recente beveiligings patches en upgrades krijgt. Niet-gepatched software-accounts voor een groot aantal aanvallen. Op basis van het ministerie van binnenlandse-beveiliging is een aantal [van 85 procent van de beoogde aanvallen voor komen](https://www.us-cert.gov/ncas/alerts/TA15-119A). Met dit service model hoeft u de zware servers niet meer te beheren en zo nodig een patch uit te voeren.

* **Integratie van intune**. Met intune wordt bedrijfs verkeer onafhankelijk van persoonlijk verkeer gerouteerd. Met toepassings proxy zorgt u ervoor dat het bedrijfs verkeer wordt geverifieerd. [Toepassings proxy en de intune Managed browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) mogelijkheid kunnen ook samen worden gebruikt om externe gebruikers veilig toegang te bieden tot interne websites van Ios-en Android-apparaten.

### <a name="roadmap-to-the-cloud"></a>Route kaart naar de Cloud

Een ander belang rijk voor deel van het implementeren van toepassings proxy is Azure AD uitbreiden naar uw on-premises omgeving. De implementatie van de app-proxy is in feite een belang rijke stap bij het verplaatsen van uw organisatie en apps naar de Cloud. Door over te stappen naar de Cloud en te gaan van on-premises verificatie, vermindert u de on-premises footprint en gebruikt u de mogelijkheden van Azure AD-identiteits beheer als uw besturings vlak. Met minimale of geen updates voor bestaande toepassingen, hebt u toegang tot Cloud mogelijkheden zoals eenmalige aanmelding, multi-factor Authentication en Centraal beheer. Het installeren van de benodigde onderdelen voor een app-proxy is een eenvoudig proces om een RAS-Framework tot stand te brengen. En als u overstapt naar de Cloud, hebt u toegang tot de nieuwste functies, updates en functionaliteit van Azure AD, zoals hoge Beschik baarheid en herstel na nood gevallen.

Voor meer informatie over het migreren van uw apps naar Azure AD raadpleegt u het technisch document over het [migreren van uw toepassingen naar Azure Active Directory](https://aka.ms/migrateapps/whitepaper) .

## <a name="architecture"></a>Architectuur

In het volgende diagram ziet u hoe de Azure AD-verificatie services en toepassings proxy samen werken om eenmalige aanmelding te bieden voor on-premises toepassingen voor eind gebruikers.

![Azure AD-toepassingsproxy-verificatie stroom](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Nadat de gebruiker toegang tot de toepassing heeft verkregen via een eind punt, wordt de gebruiker omgeleid naar de aanmeldings pagina van Azure AD. Als u beleid voor voorwaardelijke toegang hebt geconfigureerd, worden er op dit moment specifieke voor waarden gecontroleerd om ervoor te zorgen dat u voldoet aan de beveiligings vereisten van uw organisatie.
2. Na een geslaagde aanmelding stuurt Azure AD een token naar het client apparaat van de gebruiker.
3. De client stuurt het token naar de Application proxy-service, waarmee de user principal name (UPN) en de SPN (Security Principal Name) van het token worden opgehaald.
4. Toepassings proxy stuurt de aanvraag door, die wordt opgehaald door de Application proxy- [connector](application-proxy-connectors.md).
5. De connector voert aanvullende authenticatie uit namens de gebruiker (*optioneel afhankelijk van de verificatie methode*), vraagt het interne eind punt van de toepassings server aan en verzendt de aanvraag naar de on-premises toepassing.
6. Het antwoord van de toepassings server wordt via de connector naar de Application proxy-service verzonden.
7. Het antwoord wordt verzonden vanuit de toepassings proxy service naar de gebruiker.

|**Component**|**Beschrijving**|
|:-|:-|
|Eindpunt|Het eind punt is een URL of [eind gebruikers Portal](end-user-experiences.md). Gebruikers kunnen toepassingen en buiten uw netwerk bereiken door toegang te krijgen tot een externe URL. Gebruikers in uw netwerk hebben toegang tot de toepassing via een URL of een portal voor eind gebruikers. Wanneer gebruikers naar een van deze eind punten gaan, verifiëren ze in azure AD en sturen ze vervolgens via de connector naar de on-premises toepassing.|
|Azure AD|Azure AD voert de verificatie uit met behulp van de Tenant Directory die is opgeslagen in de Cloud.|
|Application proxy-service|Deze toepassings proxy service wordt uitgevoerd in de Cloud als onderdeel van Azure AD. Hiermee wordt het aanmeldings token van de gebruiker aan de Application proxy-connector door gegeven. Met de toepassings proxy worden alle toegankelijke headers voor de aanvraag doorgestuurd en worden de headers volgens het bijbehorende protocol ingesteld op het IP-adres van de client. Als de inkomende aanvraag voor de proxy al die header heeft, wordt het client-IP-adres toegevoegd aan het einde van de door komma's gescheiden lijst die de waarde van de header is.|
|Application proxy-connector|De connector is een licht gewicht agent die wordt uitgevoerd op een Windows-Server in uw netwerk. De connector beheert de communicatie tussen de service toepassings proxy in de Cloud en de on-premises toepassing. De connector gebruikt alleen uitgaande verbindingen. u hoeft geen binnenkomende poorten te openen of niets in de DMZ te plaatsen. De connectors zijn stateless en halen indien nodig gegevens uit de Cloud. Zie [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)voor meer informatie over connectors, zoals hoe ze worden geladen en geverifieerd.|
|Active Directory (AD)|Active Directory on-premises wordt uitgevoerd om verificatie voor domein accounts uit te voeren. Wanneer eenmalige aanmelding is geconfigureerd, communiceert de connector met AD voor het uitvoeren van aanvullende verificatie vereist.|
|On-premises toepassing|Ten slotte heeft de gebruiker toegang tot een on-premises toepassing.|

Azure AD-toepassingsproxy bestaat uit de Cloud service voor toepassings proxy en een on-premises connector. De connector luistert naar aanvragen van de Application proxy-service en verwerkt de verbindingen met de interne toepassingen. Het is belang rijk te weten dat alle communicatie plaatsvindt via TLS en altijd afkomstig is van de connector voor de Application proxy-service. Dat wil zeggen dat de communicatie alleen uitgaand is. De connector gebruikt een client certificaat voor de verificatie van de Application proxy-service voor alle aanroepen. De enige uitzonde ring op de verbindings beveiliging is de eerste Setup-stap waarin het client certificaat is ingesteld. Bekijk de toepassings proxy [onder de schermen](application-proxy-security.md#under-the-hood) voor meer informatie.

### <a name="application-proxy-connectors"></a>Application proxy-connectors

[Application proxy-connectors](application-proxy-connectors.md) zijn Lightweight-agents die on-premises zijn geïmplementeerd en die de uitgaande verbinding met de Application proxy-service in de Cloud vergemakkelijken. De connectors moeten worden geïnstalleerd op een Windows-Server die toegang heeft tot de back-end-toepassing. Gebruikers maken verbinding met de Cloud service van de app proxy waarmee hun verkeer via de connectors naar de apps wordt gerouteerd, zoals hieronder wordt weer gegeven.

![Azure AD-toepassingsproxy-netwerk verbindingen](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Setup en registratie tussen een connector en de app-proxy service worden als volgt uitgevoerd:

1. De IT-beheerder opent poorten 80 en 443 voor uitgaand verkeer en biedt toegang tot verschillende Url's die nodig zijn voor de connector, de app-proxy service en Azure AD.
2. De beheerder meldt zich aan bij de Azure Portal en voert een uitvoerbaar bestand uit om de connector te installeren op een on-premises Windows-Server.
3. De connector begint met Luis teren naar de proxy service van de app.
4. De beheerder voegt de on-premises toepassing toe aan Azure AD en configureert instellingen, zoals de Url's die gebruikers nodig hebben om verbinding te maken met hun apps.

Zie [een Azure AD-toepassingsproxy-implementatie plannen](application-proxy-deployment-plan.md)voor meer informatie.

Het is raadzaam om altijd meerdere connectors te implementeren voor redundantie en schaal. De connectors, in combi natie met de-service, nemen alle taken met hoge Beschik baarheid in en kunnen dynamisch worden toegevoegd of verwijderd. Telkens wanneer een nieuwe aanvraag arriveert, wordt deze doorgestuurd naar een van de beschik bare connectors. Wanneer een connector wordt uitgevoerd, blijft deze actief wanneer deze verbinding maakt met de service. Als een connector tijdelijk niet beschikbaar is, reageert deze niet op dit verkeer. Ongebruikte connectors zijn gelabeld als inactief en worden verwijderd na 10 dagen van inactiviteit.

Connectors vragen de server ook om te bepalen of er een nieuwere versie van de connector is. Hoewel u een hand matige update kunt uitvoeren, worden connectors automatisch bijgewerkt zolang de toepassings proxy Connector Updater service wordt uitgevoerd. Voor tenants met meerdere connectors bedoelt de automatische updates één connector per keer in elke groep om uitval tijd in uw omgeving te voor komen.

**Opmerking**: u kunt de [pagina versie geschiedenis](application-proxy-release-version-history.md) van de toepassings proxy controleren om te worden gewaarschuwd wanneer er updates zijn uitgebracht door u te abonneren op de RSS-feed.

Elke toepassings proxy connector wordt toegewezen aan een [connector groep](application-proxy-connector-groups.md). Connectors in dezelfde connector groep fungeren als één eenheid voor hoge Beschik baarheid en taak verdeling. U kunt nieuwe groepen maken, Connect oren aan hen toewijzen in de Azure Portal en vervolgens specifieke connectors toewijzen voor specifieke toepassingen. Het wordt aanbevolen om ten minste twee connectors in elke connector groep te hebben voor hoge Beschik baarheid.

Connector groepen zijn handig wanneer u de volgende scenario's moet ondersteunen:

* Geografische apps publiceren
* Toepassings segmentatie/-isolatie
* Publiceren van web-apps die in de Cloud of on-premises worden uitgevoerd

Zie [netwerk topologie overwegingen bij het gebruik van Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md)voor meer informatie over het kiezen van het installeren van uw connectors en het optimaliseren van uw netwerk.

## <a name="other-use-cases"></a>Andere use cases

Tot nu toe hebben we gestreefd naar het gebruik van toepassings proxy om on-premises apps extern te publiceren, terwijl eenmalige aanmelding wordt ingeschakeld voor al uw Cloud-en on-premises apps. Er zijn echter andere use cases voor app proxy. Deze omvatten:

* **Rest-Api's veilig publiceren**. Wanneer u bedrijfs logica of Api's uitvoert die on-premises of op virtuele machines in de cloud worden uitgevoerd, biedt toepassings proxy een openbaar eind punt voor API-toegang. Met API endpoint Access kunt u verificatie en autorisatie beheren zonder dat er binnenkomende poorten zijn vereist. Het biedt extra beveiliging via Azure AD Premium functies, zoals multi-factor Authentication en voorwaardelijke toegang op basis van apparaten voor Desk tops, iOS, MAC en Android-apparaten met intune. Zie voor meer informatie [over het inschakelen van systeem eigen client toepassingen om te communiceren met proxy toepassingen](application-proxy-configure-native-client-application.md) en [een API te beveiligen met behulp van OAuth 2,0 met Azure Active Directory en API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Extern bureaublad-services** **(RDS)**. Voor standaard-RDS-implementaties is open binnenkomende verbindingen vereist. De RDS- [implementatie met toepassings proxy](application-proxy-integrate-with-remote-desktop-services.md) heeft echter een permanente uitgaande verbinding van de server waarop de connector service wordt uitgevoerd. Op deze manier kunt u meer toepassingen aan eind gebruikers aanbieden door on-premises toepassingen te publiceren via Extern bureaublad-services. U kunt ook de kwets baarheid van de implementatie verminderen met een beperkt aantal verificaties in twee stappen en voorwaardelijke toegangs controles voor RDS.
* **Publiceer toepassingen die verbinding maken met behulp van Websockets**. Ondersteuning met [Qlik Sense](application-proxy-qlik.md) is in open bare preview en wordt in de toekomst uitgebreid naar andere apps.
* **Schakel systeem eigen client toepassingen in om te communiceren met proxy toepassingen**. U kunt Azure AD-toepassingsproxy gebruiken om web-apps te publiceren, maar dit kan ook worden gebruikt voor het publiceren van [systeem eigen client toepassingen](application-proxy-configure-native-client-application.md) die zijn geconfigureerd met de Azure AD-verificatie bibliotheek (ADAL). Systeem eigen client toepassingen verschillen van web-apps omdat ze zijn geïnstalleerd op een apparaat, terwijl web apps via een browser worden geopend.

## <a name="conclusion"></a>Conclusie

De manier waarop we werken en de hulpprogram ma's die we gebruiken, worden snel gewijzigd. Met meer werk nemers die hun eigen apparaten gebruiken om te werken en het gebruik van de software-as-a-Service (SaaS)-toepassingen, de manier waarop organisaties hun gegevens kunnen beheren en beveiligen, moeten ook worden gegroeid. Bedrijven worden niet meer alleen op hun eigen wanden bediend, beveiligd door een Moat die de randen van het kader omsluit. Gegevens worden verplaatst naar meer locaties dan ooit--in zowel on-premises als in de cloud omgeving. Deze evolutie heeft bijgedragen tot meer productiviteit en mogelijkheid van gebruikers om samen te werken, maar maakt het beveiligen van gevoelige gegevens ook moeilijker.

Of u nu Azure AD gebruikt voor het beheren van gebruikers in een hybride scenario of als u geïnteresseerd bent in het starten van uw reis naar de Cloud, het implementeren van Azure AD-toepassingsproxy kan u helpen de grootte van uw on-premises footprint te verkleinen door externe toegang als een service te bieden.

Organisaties moeten vandaag nog profiteren van app proxy om te profiteren van de volgende voor delen:

* On-premises apps extern publiceren zonder de overhead voor het onderhouden van traditionele VPN-of andere on-premises oplossingen voor webpublicaties en DMZ benadering
* Eenmalige aanmelding voor alle toepassingen, zijn Office 365 of andere SaaS-apps en met inbegrip van on-premises toepassingen
* Cloud schaal beveiliging waarbij Azure AD gebruikmaakt van Office 365-telemetrie om onbevoegde toegang te voor komen
* Integratie van intune om ervoor te zorgen dat bedrijfs verkeer wordt geverifieerd
* Beheer van Gebruikersaccountbeheer
* Automatische updates om ervoor te zorgen dat u over de nieuwste beveiligings patches beschikt
* Nieuwe functies als deze worden vrijgegeven; de meest recente ondersteuning voor eenmalige SAML-aanmelding en een nauw keuriger beheer van toepassings cookies

## <a name="next-steps"></a>Volgende stappen

* Zie [een azure AD-toepassingsproxy-implementatie plannen](application-proxy-deployment-plan.md)voor meer informatie over het plannen, gebruiken en beheren van Azure AD-toepassingsproxy.
* Zie aan de slag [met Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)als u een live demo wilt plannen of een gratis proef versie van 90 dagen wilt ontvangen voor evaluatie.
