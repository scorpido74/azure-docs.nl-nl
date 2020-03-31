---
title: On-premises apps publiceren met Azure AD Application Proxy
description: Begrijpen waarom u Application Proxy wilt gebruiken om on-premises webtoepassingen extern te publiceren naar externe gebruikers. Meer informatie over application proxy-architectuur, connectors, verificatiemethoden en beveiligingsvoordelen.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79481191"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Azure AD-toepassingsproxy gebruiken om on-premises apps voor externe gebruikers te publiceren

Azure Active Directory (Azure AD) biedt veel mogelijkheden voor het beveiligen van gebruikers, apps en gegevens in de cloud en on-premises. De functie Azure AD Application Proxy kan met name worden geïmplementeerd door IT-professionals die externe webtoepassingen op locatie willen publiceren. Externe gebruikers die toegang nodig hebben tot interne apps, kunnen deze vervolgens op een veilige manier openen.

De mogelijkheid om veilig toegang te krijgen tot interne apps van buiten uw netwerk wordt nog kritischer op de moderne werkplek. Met scenario's zoals BYOD (Bring Your Own Device) en mobiele apparaten worden IT-professionals uitgedaagd om twee doelen te bereiken:

* Stel eindgebruikers in staat om altijd en overal productief te zijn
* Bedrijfsmiddelen te allen tijde beschermen

Veel organisaties geloven dat ze in controle en beschermd wanneer middelen bestaan binnen de grenzen van hun bedrijfsnetwerken. Maar in de huidige digitale werkplek is die grens uitgebreid met beheerde mobiele apparaten en middelen en services in de cloud. Nu moet u de complexiteit beheren van het beschermen van de identiteit en gegevens van uw gebruikers die zijn opgeslagen op hun apparaten en apps.

Misschien gebruikt u Azure AD al om gebruikers in de cloud te beheren die toegang moeten krijgen tot Office 365 en andere SaaS-toepassingen, evenals web-apps die on-premises worden gehost. Als u al Azure AD hebt, u het gebruiken als één besturingsvlak om naadloze en veilige toegang tot uw on-premises toepassingen mogelijk te maken. Of misschien overweeg je nog steeds een verhuizing naar de cloud. Als dat zo is, u uw reis naar de cloud beginnen door Application Proxy te implementeren en de eerste stap te zetten naar het opbouwen van een sterke identiteitsbasis.

Hoewel niet volledig, de onderstaande lijst illustreert een aantal van de dingen die u inschakelen door de uitvoering van App Proxy in een hybride coëxistentie scenario:

* On-premises web-apps extern publiceren op een vereenvoudigde manier zonder een DMZ
* Ondersteuning voor single sign-on (SSO) op apparaten, resources en apps in de cloud en on-premises
* Ondersteuning voor multi-factor authenticatie voor apps in de cloud en on-premises
* Maak snel gebruik van cloudfuncties met de beveiliging van de Microsoft Cloud
* Beheer van gebruikersaccounts centraliseren
* Centraliseren controle op identiteit en veiligheid
* Automatisch gebruikerstoegang toevoegen of verwijderen tot toepassingen op basis van groepslidmaatschap

In dit artikel wordt uitgelegd hoe Azure AD en Application Proxy externe gebruikers een enkele sso-ervaring (sign-on) bieden. Gebruikers maken veilig verbinding met on-premises apps zonder vpn- of dual-homed servers en firewallregels. Dit artikel helpt u te begrijpen hoe Application Proxy de mogelijkheden en beveiligingsvoordelen van de cloud naar uw on-premises webtoepassingen brengt. Het beschrijft ook de architectuur en topologieën die mogelijk zijn.

## <a name="remote-access-in-the-past"></a>Toegang op afstand in het verleden

Voorheen was uw besturingsvlak voor het beschermen van interne bronnen tegen aanvallers terwijl het vergemakkelijken van de toegang door externe gebruikers zich in het DMZ- of perimeternetwerk bevond. Maar de VPN- en reverse proxy-oplossingen die worden geïmplementeerd in de DMZ die door externe clients worden gebruikt om toegang te krijgen tot bedrijfsbronnen, zijn niet geschikt voor de cloudwereld. Ze hebben meestal last van de volgende nadelen:

* Hardwarekosten
* Beveiliging behouden (patchen, bewaken van poorten, enz.)
* Gebruikers aan de rand authenticeren
* Gebruikers authenticeren naar webservers in het perimeternetwerk
* Het onderhouden van VPN-toegang voor externe gebruikers met de distributie en configuratie van VPN-clientsoftware. Ook het onderhouden van domein-aangesloten servers in de DMZ, die kwetsbaar kunnen zijn voor aanvallen van buitenaf.

In de huidige cloud-first wereld is Azure AD het meest geschikt om te bepalen wie en wat er in uw netwerk terechtkomt. Azure AD Application Proxy integreert met moderne authenticatie en cloudgebaseerde technologieën, zoals SaaS-toepassingen en identiteitsproviders. Deze integratie stelt gebruikers in staat om overal toegang te krijgen tot apps. App Proxy is niet alleen geschikter voor de digitale werkplek van vandaag, het is ook veiliger dan VPN- en reverse proxy-oplossingen en gemakkelijker te implementeren. Externe gebruikers hebben toegang tot uw on-premises toepassingen op dezelfde manier als ze toegang hebben tot O365 en andere SaaS-apps die zijn geïntegreerd met Azure AD. U hoeft uw toepassing niet te wijzigen of bij te werken om te kunnen werken met een toepassingsproxy. Bovendien vereist App Proxy niet dat u binnenkomende verbindingen opent via uw firewall. Met App Proxy, je gewoon instellen en vergeten.

## <a name="the-future-of-remote-access"></a>De toekomst van toegang op afstand

In de huidige digitale werkplek werken gebruikers overal met meerdere apparaten en apps. De enige constante is de identiteit van de gebruiker. Daarom is de eerste stap naar een beveiligd netwerk vandaag de dag om de [mogelijkheden voor identiteitsbeheer van Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) te gebruiken als uw beveiligingscontrolevlak. Een model dat identiteit als controlevlak gebruikt, bestaat meestal uit de volgende componenten:

* Een identiteitsprovider om gebruikers en gebruikersgerelateerde informatie bij te houden.
* Apparaatmap om een lijst bij te houden van apparaten die toegang hebben tot bedrijfsbronnen. Deze map bevat overeenkomstige apparaatinformatie (bijvoorbeeld type apparaat, integriteit enz.).
* Beleidsevaluatieservice om te bepalen of een gebruiker en apparaat voldoen aan het beleid dat is ingesteld door beveiligingsbeheerders.
* De mogelijkheid om toegang tot organisatiebronnen te verlenen of te weigeren.

Met Application Proxy houdt Azure AD gebruikers bij die toegang nodig hebben tot web-apps die on-premises en in de cloud zijn gepubliceerd. Het biedt een centraal beheerpunt voor die apps. Hoewel dit niet nodig is, wordt u aangeraden ook voorwaardelijke toegang tot Azure AD in te schakelen. Door voorwaarden te definiëren voor de manier waarop gebruikers zich verifiëren en toegang krijgen, zorgt u er verder voor dat de juiste mensen toegang hebben tot toepassingen.

**Let op:** Het is belangrijk om te begrijpen dat Azure AD Application Proxy is bedoeld als een VPN of reverse proxy vervanging voor roaming (of externe) gebruikers die toegang nodig hebben tot interne bronnen. Het is niet bedoeld voor interne gebruikers op het bedrijfsnetwerk. Interne gebruikers die application proxy onnodig gebruiken, kunnen onverwachte en ongewenste prestatieproblemen veroorzaken.

![Azure Active Directory en al uw apps](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Een overzicht van hoe App Proxy werkt

Application Proxy is een Azure AD-service die u configureert in de Azure-portal. Hiermee u een extern openbaar HTTP/HTTPS URL-eindpunt publiceren in de Azure Cloud, dat verbinding maakt met een URL van een interne toepassingsserver in uw organisatie. Deze on-premises webapps kunnen worden geïntegreerd met Azure AD om eenmalige aanmelding te ondersteunen. Eindgebruikers hebben dan toegang tot on-premises webapps op dezelfde manier als ze toegang hebben tot Office 365 en andere SaaS-apps.

Onderdelen van deze functie zijn de Application Proxy-service, die wordt uitgevoerd in de cloud, de Application Proxy-connector, een lichtgewicht agent die wordt uitgevoerd op een on-premises server, en Azure AD, dat is de identiteitsprovider. Alle drie de componenten werken samen om de gebruiker één aanmeldingservaring te bieden voor on-premises webapplicaties.

Na het aanmelden hebben externe gebruikers toegang tot on-premises webapplicaties via een bekende URL of het [MyApps-toegangspaneel](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) vanaf hun desktop- of iOS/MAC-apparaten. App Proxy kan bijvoorbeeld externe toegang en single sign-on bieden voor Extern bureaublad, SharePoint-sites, Tableau, Qlik, Outlook on the web en LOB-toepassingen (line-of-business).

![Azure AD-toepassingsproxy-architectuur](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Er zijn verschillende manieren om een toepassing voor één aanmelding te configureren en de methode die u selecteert, is afhankelijk van de verificatie die uw toepassing gebruikt. Application Proxy ondersteunt de volgende typen toepassingen:

* Webtoepassingen
* Web API's die u wilt blootstellen aan rijke toepassingen op verschillende apparaten
* Toepassingen die worden gehost achter een Extern bureaublad-gateway
* Uitgebreide client-apps die zijn geïntegreerd met de Active Directory Authentication Library (ADAL)

App Proxy werkt met apps die het volgende native verificatieprotocol gebruiken:

* **[Geïntegreerde Windows-verificatie (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Voor IWA gebruiken de connectors voor toepassingsproxy Kerberos Constrained Delegation (KCD) om gebruikers te verifiëren bij de Kerberos-toepassing.

App Proxy ondersteunt ook de volgende verificatieprotocollen met integratie door derden of in specifieke configuratiescenario's:

* [**Verificatie op basis van kopteksten**](application-proxy-configure-single-sign-on-with-ping-access.md). Deze aanmeldingsmethode maakt gebruik van een verificatieservice van derden genaamd PingAccess en wordt gebruikt wanneer de toepassing kopteksten gebruikt voor verificatie. In dit scenario wordt verificatie afgehandeld door PingAccess.
* [**Verificatie op basis van formulieren of wachtwoorden**](application-proxy-configure-single-sign-on-password-vaulting.md). Met deze verificatiemethode melden gebruikers zich aan bij de toepassing met een gebruikersnaam en wachtwoord wanneer ze er voor het eerst toegang toe hebben. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord aan de toepassing. In dit scenario wordt verificatie afgehandeld door Azure AD.
* [**SAML-verificatie**](application-proxy-configure-single-sign-on-on-premises-apps.md). Saml-gebaseerde single sign-on wordt ondersteund voor toepassingen die saml 2.0- of WS-Federation-protocollen gebruiken. Met SAML single sign-on verifieert Azure AD de toepassing met behulp van het Azure AD-account van de gebruiker.

Zie [Een enkele aanmeldingsmethode kiezen](what-is-single-sign-on.md#choosing-a-single-sign-on-method)voor meer informatie over ondersteunde methoden.

### <a name="security-benefits"></a>Beveiligingsvoordelen

De oplossing voor externe toegang die wordt aangeboden door Application Proxy en Azure AD ondersteunen verschillende beveiligingsvoordelen waar klanten gebruik van kunnen maken, waaronder:

* **Geverifieerde toegang**. Application Proxy is het meest geschikt om toepassingen met [pre-authenticatie](application-proxy-security.md#authenticated-access) te publiceren om ervoor te zorgen dat alleen geverifieerde verbindingen uw netwerk raken. Voor toepassingen die met pre-authenticatie zijn gepubliceerd, mag er geen verkeer door de App Proxy-service naar uw on-premises omgeving gaan, zonder een geldig token. Pre-authenticatie blokkeert van nature een aanzienlijk aantal gerichte aanvallen, omdat alleen geverifieerde identiteiten toegang hebben tot de backend-toepassing.
* **Voorwaardelijke toegang**. Uitgebreidere beleidsbesturingselementen kunnen worden toegepast voordat verbindingen met uw netwerk worden gemaakt. Met Voorwaardelijke toegang u beperkingen definiëren voor het verkeer dat u toestaat om uw backend-toepassing te raken. U maakt beleid dat aanmeldingen beperkt op basis van locatie, sterkte van verificatie en gebruikersrisicoprofiel. Naarmate voorwaardelijke toegang evolueert, worden er meer besturingselementen toegevoegd om extra beveiliging te bieden, zoals integratie met Microsoft Cloud App Security (MCAS). Met MCAS-integratie u een on-premises toepassing configureren voor [realtime bewaking](application-proxy-integrate-with-microsoft-cloud-application-security.md) door voorwaardelijke toegang te gebruiken om sessies in realtime te controleren en te beheren op basis van beleid voor voorwaardelijke toegang.
* **Verkeersbeëindiging**. Al het verkeer naar de backend-toepassing wordt beëindigd bij de Application Proxy-service in de cloud, terwijl de sessie opnieuw wordt ingesteld met de backendserver. Deze verbindingsstrategie betekent dat uw backendservers niet worden blootgesteld aan direct HTTP-verkeer. Ze zijn beter beschermd tegen gerichte DoS -aanvallen (denial-of-service) omdat uw firewall niet wordt aangevallen.
* **Alle toegang is uitgaand.** De Application Proxy-connectors gebruiken alleen uitgaande verbindingen met de Application Proxy-service in de cloud via poorten 80 en 443. Als er geen inkomende verbindingen zijn, is het niet nodig om firewallpoorten te openen voor binnenkomende verbindingen of componenten in de DMZ. Alle verbindingen zijn uitgaand en via een beveiligd kanaal.
* **Security Analytics en Machine Learning (ML) gebaseerde intelligentie**. Omdat het onderdeel is van Azure Active Directory, kan Application Proxy gebruikmaken van [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (waarvoor Premium [P2-licenties](https://azure.microsoft.com/pricing/details/active-directory/)vereist zijn). Azure AD Identity Protection combineert machine learning security intelligence met datafeeds van microsoft's [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) en Microsoft Security Response [Center](https://www.microsoft.com/msrc) om proactief gecompromitteerde accounts te identificeren. Identity Protection biedt real-time bescherming tegen risicovolle aanmeldingen. Het houdt rekening met factoren zoals toegang en toegang vanaf geïnfecteerde apparaten, via anonymizing netwerken, of vanaf atypische en onwaarschijnlijke locaties om het risicoprofiel van een sessie te verhogen. Dit risicoprofiel wordt gebruikt voor real-time bescherming. Veel van deze rapporten en gebeurtenissen zijn al beschikbaar via een API voor integratie met uw SIEM-systemen.

* **Toegang op afstand als service.** U hoeft zich geen zorgen te maken over het onderhouden en patchen van on-premises servers om externe toegang mogelijk te maken. Application Proxy is een internet schaal service die Microsoft bezit, zodat u altijd de nieuwste beveiligingspatches en upgrades. Ongepatchte software is nog steeds goed voor een groot aantal aanvallen. Volgens het Ministerie van Binnenlandse Veiligheid, maar liefst [85 procent van de gerichte aanvallen zijn te voorkomen](https://www.us-cert.gov/ncas/alerts/TA15-119A). Met dit servicemodel hoeft u niet meer de zware last van het beheer van uw edge-servers te dragen en te klauteren om ze te patchen als dat nodig is.

* **Intune integratie**. Met Intune wordt zakelijk verkeer apart van persoonlijk verkeer omgeleid. Application Proxy zorgt ervoor dat het bedrijfsverkeer wordt geverifieerd. [Application Proxy en de Intune Managed Browser-mogelijkheid](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) kunnen ook samen worden gebruikt om externe gebruikers in staat te stellen veilig toegang te krijgen tot interne websites vanaf iOS- en Android-apparaten.

### <a name="roadmap-to-the-cloud"></a>Roadmap naar de cloud

Een ander belangrijk voordeel van het implementeren van Application Proxy is het uitbreiden van Azure AD naar uw on-premises omgeving. In feite is het implementeren van App Proxy een belangrijke stap in het verplaatsen van uw organisatie en apps naar de cloud. Door naar de cloud te gaan en niet over te stappen op on-premises verificatie, vermindert u uw on-premises voetafdruk en gebruikt u de mogelijkheden voor identiteitsbeheer van Azure AD als uw besturingsvlak. Met minimale of geen updates voor bestaande toepassingen hebt u toegang tot cloudmogelijkheden zoals single sign-on, multi-factor authenticatie en centraal beheer. Het installeren van de benodigde componenten voor App Proxy is een eenvoudig proces voor het opzetten van een remote access framework. En door naar de cloud te gaan, hebt u toegang tot de nieuwste Azure AD-functies, -updates en -functionaliteit, zoals hoge beschikbaarheid en het herstel na noodgevallen.

Zie het whitepaper migreren van uw toepassingen [naar Azure Active Directory voor](https://aka.ms/migrateapps/whitepaper) meer informatie over het migreren van uw apps naar Azure AD.

## <a name="architecture"></a>Architectuur

In het volgende diagram wordt in het algemeen uitgelegd hoe Azure AD-verificatieservices en Application Proxy samenwerken om on-premises toepassingen aan eindgebruikers te bieden.

![Verificatiestroom azure AD-toepassingproxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Nadat de gebruiker de toepassing via een eindpunt heeft geopend, wordt de gebruiker doorgestuurd naar de aanmeldingspagina van Azure AD. Als u beleid voor voorwaardelijke toegang hebt geconfigureerd, worden op dit moment specifieke voorwaarden gecontroleerd om ervoor te zorgen dat u voldoet aan de beveiligingsvereisten van uw organisatie.
2. Na een succesvolle aanmelding stuurt Azure AD een token naar het clientapparaat van de gebruiker.
3. De client stuurt het token naar de Application Proxy-service, die de gebruikersnaam (UPN) en de beveiligingsnaam (SPN) uit het token haalt.
4. Application Proxy stuurt de aanvraag door, die wordt opgehaald door de Application [Proxy-connector](application-proxy-connectors.md).
5. De connector voert eventuele aanvullende verificatie uit die namens de gebruiker vereist is (*Optioneel afhankelijk van de verificatiemethode*), vraagt het interne eindpunt van de toepassingsserver en stuurt het verzoek naar de on-premises toepassing.
6. Het antwoord van de toepassingsserver wordt via de connector naar de application proxy-service verzonden.
7. Het antwoord wordt verzonden vanuit de Application Proxy-service naar de gebruiker.

|**Component**|**Beschrijving**|
|:-|:-|
|Eindpunt|Het eindpunt is een URL of een [portal voor eindgebruikers.](end-user-experiences.md) Gebruikers kunnen applicaties buiten uw netwerk bereiken door toegang te krijgen tot een externe URL. Gebruikers binnen uw netwerk hebben toegang tot de toepassing via een URL of een portal voor eindgebruikers. Wanneer gebruikers naar een van deze eindpunten gaan, verifiëren ze zich in Azure AD en worden ze vervolgens via de connector doorgestuurd naar de on-premises toepassing.|
|Azure AD|Azure AD voert de verificatie uit met de tenantmap die in de cloud is opgeslagen.|
|Application Proxy-service|Deze application proxy-service wordt uitgevoerd in de cloud als onderdeel van Azure AD. Het doorgeeft het aanmeldingstoken van de gebruiker aan de Application Proxy Connector. Application Proxy stuurt alle toegankelijke headers op de aanvraag door en stelt de headers in volgens het protocol, naar het IP-adres van de client. Als de binnenkomende aanvraag voor de proxy al die koptekst heeft, wordt het IP-adres van de client toegevoegd aan het einde van de door komma's gescheiden lijst die de waarde van de koptekst is.|
|Toepassingsproxyconnector|De connector is een lichtgewicht agent die wordt uitgevoerd op een Windows Server in uw netwerk. De connector beheert de communicatie tussen de Application Proxy-service in de cloud en de on-premises toepassing. De connector maakt alleen gebruik van uitgaande verbindingen, zodat u geen binnenkomende poorten hoeft te openen of iets in de DMZ hoeft te plaatsen. De connectors zijn stateloos en halen informatie uit de cloud als dat nodig is. Zie [Azure AD Application Proxy connectors](application-proxy-connectors.md)begrijpen voor meer informatie over connectors, zoals hoe ze load-balance en authenticeren.|
|Active Directory (AD)|Active Directory wordt on-premises uitgevoerd om verificatie voor domeinaccounts uit te voeren. Wanneer eenmalige aanmelding is geconfigureerd, communiceert de connector met AD om eventuele extra verificatie uit te voeren die vereist is.|
|On-premises toepassing|Ten slotte heeft de gebruiker toegang tot een on-premises toepassing.|

Azure AD Application Proxy bestaat uit de cloud-gebaseerde Application Proxy-service en een on-premises connector. De connector luistert naar aanvragen van de Application Proxy-service en verwerkt verbindingen met de interne toepassingen. Het is belangrijk op te merken dat alle communicatie plaatsvindt via TLS, en altijd afkomstig zijn van de connector naar de Application Proxy-service. Dat wil zeggen, communicatie is alleen uitgaand. De connector gebruikt een clientcertificaat om te verifiëren bij de application proxy-service voor alle aanroepen. De enige uitzondering op de verbindingsbeveiliging is de eerste installatiestap waarbij het clientcertificaat wordt ingesteld. Zie de Application Proxy [Onder de motorkap](application-proxy-security.md#under-the-hood) voor meer details.

### <a name="application-proxy-connectors"></a>Proxyconnectors voor toepassingen

[Application Proxy-connectors](application-proxy-connectors.md) zijn lichtgewicht agents die on-premises worden geïmplementeerd en die de uitgaande verbinding met de Application Proxy-service in de cloud vergemakkelijken. De connectors moeten worden geïnstalleerd op een Windows Server die toegang heeft tot de backend-toepassing. Gebruikers maken verbinding met de App Proxy-cloudservice die hun verkeer via de onderstaande connectors naar de apps leidt.

![Azure AD Application Proxy-netwerkverbindingen](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Het instellen en registreren tussen een connector en de app-proxyservice wordt als volgt uitgevoerd:

1. De IT-beheerder opent poorten 80 en 443 voor uitgaand verkeer en geeft toegang tot verschillende URL's die nodig zijn voor de connector, de App Proxy-service en Azure AD.
2. De beheerder meldt zich aan bij de Azure-portal en voert een uitvoerbare uit om de connector op een on-premises Windows-server te installeren.
3. De connector begint te "luisteren" naar de App Proxy-service.
4. De beheerder voegt de on-premises toepassing toe aan Azure AD en configureert instellingen zoals de URL's die gebruikers nodig hebben om verbinding te maken met hun apps.

Zie [Een Azure AD Application Proxy-implementatie plannen voor](application-proxy-deployment-plan.md)meer informatie.

Het wordt aanbevolen dat u altijd meerdere connectors implementeert voor redundantie en schaal. De connectors zorgen, in combinatie met de service, voor alle taken met hoge beschikbaarheid en kunnen dynamisch worden toegevoegd of verwijderd. Elke keer dat er een nieuw verzoek binnenkomt, wordt deze doorgestuurd naar een van de beschikbare connectoren. Wanneer een connector wordt uitgevoerd, blijft deze actief wanneer deze verbinding maakt met de service. Als een connector tijdelijk niet beschikbaar is, reageert deze niet op dit verkeer. Ongebruikte connectors worden getagd als inactief en verwijderd na 10 dagen inactiviteit.

Connectors peilen ook de server om erachter te komen of er een nieuwere versie van de connector is. Hoewel u een handmatige update uitvoeren, worden connectors automatisch bijgewerkt zolang de application proxy connector updater-service wordt uitgevoerd. Voor tenants met meerdere connectors is de automatische update gericht op één connector tegelijk in elke groep om downtime in uw omgeving te voorkomen.

**Opmerking:** U de [pagina Versiegeschiedenis](application-proxy-release-version-history.md) van de toepassingsproxy controleren om op de hoogte te worden gesteld wanneer updates zijn uitgebracht door u te abonneren op de RSS-feed.

Elke toepassingsproxyconnector is toegewezen aan een [connectorgroep](application-proxy-connector-groups.md). Connectoren in dezelfde connectorgroep fungeren als één eenheid voor hoge beschikbaarheid en taakverdeling. U nieuwe groepen maken, er connectors aan toewijzen in de Azure-portal en vervolgens specifieke connectors toewijzen om specifieke toepassingen te serveren. Het wordt aanbevolen om ten minste twee connectoren in elke connectorgroep te hebben voor hoge beschikbaarheid.

Connectorgroepen zijn handig wanneer u de volgende scenario's moet ondersteunen:

* Geografische app-publicatie
* Toepassingssegmentatie/isolatie
* Web-apps publiceren die in de cloud of on-premises worden uitgevoerd

Zie [Overwegingen voor netwerktopologie bij het gebruik van Azure Active Directory Application Proxy](application-proxy-network-topology.md)voor meer informatie over het kiezen waar u uw connectors wilt installeren en uw netwerk optimaliseren.

## <a name="other-use-cases"></a>Andere use cases

Tot nu toe hebben we ons gericht op het gebruik van Application Proxy om on-premises apps extern te publiceren, terwijl we eenmalig aanmelden in al uw cloud- en on-premises apps inschakelen. Er zijn echter andere use cases voor App Proxy die het vermelden waard zijn. Deze omvatten:

* **Publiceer veilig REST API's**. Wanneer u bedrijfslogica of API's hebt die on-premises worden uitgevoerd of gehost op virtuele machines in de cloud, biedt Application Proxy een openbaar eindpunt voor API-toegang. Api-eindpunttoegang u verificatie en autorisatie beheren zonder inkomende poorten nodig te hebben. Het biedt extra beveiliging via Azure AD Premium-functies, zoals multi-factor authenticatie en apparaatgebaseerde voorwaardelijke toegang voor desktops, iOS, MAC en Android-apparaten die Intune gebruiken. Zie [Native clienttoepassingen inschakelen om te communiceren met proxytoepassingen](application-proxy-configure-native-client-application.md) en [Een API beveiligen met OAuth 2.0 met Azure Active Directory en API-beheer](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)voor meer informatie.
* **Extern bureaublad-services** **(RDS)**. Standaard RDS-implementaties vereisen open binnenkomende verbindingen. De [RDS-implementatie met Application Proxy](application-proxy-integrate-with-remote-desktop-services.md) heeft echter een permanente uitgaande verbinding van de server waarop de connectorservice wordt uitgevoerd. Op deze manier u meer toepassingen aanbieden aan eindgebruikers door on-premises toepassingen te publiceren via Remote Desktop Services. U ook het aanvalsoppervlak van de implementatie verkleinen met een beperkte set verificatie in twee stappen en besturingselementen voor voorwaardelijke toegang tot RDS.
* **Publiceer toepassingen die verbinding maken via WebSockets.** Ondersteuning met [Qlik Sense](application-proxy-qlik.md) is in Public Preview en zal in de toekomst worden uitgebreid naar andere apps.
* **Native clienttoepassingen inschakelen om te communiceren met proxytoepassingen.** U Azure AD Application Proxy gebruiken om web-apps te publiceren, maar het kan ook worden gebruikt om [native clienttoepassingen](application-proxy-configure-native-client-application.md) te publiceren die zijn geconfigureerd met de Azure AD Authentication Library (ADAL). Native clienttoepassingen verschillen van web-apps omdat ze op een apparaat zijn geïnstalleerd, terwijl web-apps worden geopend via een browser.

## <a name="conclusion"></a>Conclusie

De manier waarop we werken en de tools die we gebruiken veranderen snel. Met meer medewerkers die hun eigen apparaten aan het werk brengen en het alomtegenwoordige gebruik van Software-as-a-Service (SaaS) applicaties, moet ook de manier waarop organisaties hun gegevens beheren en beveiligen evolueren. Bedrijven opereren niet langer alleen binnen hun eigen muren, beschermd door een gracht die hun grens omringt. Gegevens worden naar meer locaties dan ooit tevoren verplaatst - in zowel on-premises als cloudomgevingen. Deze evolutie heeft bijgedragen tot het verhogen van de productiviteit van gebruikers en het vermogen om samen te werken, maar het maakt ook het beschermen van gevoelige gegevens uitdagender.

Of u azure AD momenteel gebruikt om gebruikers te beheren in een hybride coëxistentiescenario of geïnteresseerd bent in het starten van uw reis naar de cloud, het implementeren van Azure AD Application Proxy kan helpen de grootte van uw on-premises voetafdruk te verkleinen door op afstand toegang als een dienst.

Organisaties moeten vandaag beginnen met het gebruik van App Proxy om te profiteren van de volgende voordelen:

* Publiceer on-premises apps extern zonder de overhead die gepaard gaat met het onderhouden van traditionele VPN of andere on-premises webpublishingoplossingen en DMZ-benadering
* Eenmalig aanmelden voor alle toepassingen, of het nu Office 365- of andere SaaS-apps zijn en met on-premises toepassingen
* Beveiliging van cloudschaal waarbij Azure AD office 365-telemetrie gebruikt om ongeautoriseerde toegang te voorkomen
* Intune-integratie om ervoor te zorgen dat bedrijfsverkeer wordt geverifieerd
* Centralisatie van gebruikersaccountbeheer
* Automatische updates om ervoor te zorgen dat u over de nieuwste beveiligingspatches beschikt
* Nieuwe functies als ze worden vrijgegeven; de meest recente ondersteuning voor SAML single sign-on en meer gedetailleerd beheer van applicatiecookies

## <a name="next-steps"></a>Volgende stappen

* Zie [Een Azure AD Application Proxy-implementatie plannen](application-proxy-deployment-plan.md)voor informatie over het plannen, bedienen en beheren van Azure AD Application Proxy.
* Zie [Aan de slag met Enterprise Mobility + Beveiliging](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)om een live demo te plannen of een gratis proefperiode van 90 dagen te krijgen voor evaluatie.
