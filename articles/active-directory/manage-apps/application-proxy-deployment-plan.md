---
title: Een Azure Active Directory Application Proxy Deployment plannen
description: Een end-to-end handleiding voor het plannen van de implementatie van application proxy binnen uw organisatie
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330912"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Een Azure AD-toepassingsproxy-implementatie plannen

Azure Active Directory (Azure AD) Application Proxy is een veilige en kosteneffectieve oplossing voor externe toegang voor on-premises toepassingen. Het biedt een onmiddellijk overgangspad voor "Cloud First"-organisaties om toegang te beheren tot verouderde on-premises toepassingen die nog niet in staat zijn moderne protocollen te gebruiken. Zie [Wat is application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)voor aanvullende inleidende informatie.

Application Proxy wordt aanbevolen voor het geven van externe gebruikers toegang tot interne bronnen. Application Proxy vervangt de noodzaak van een VPN of reverse proxy voor deze remote access use cases. Het is niet bedoeld voor gebruikers die zich op het bedrijfsnetwerk bevinden. Deze gebruikers die Application Proxy gebruiken voor toegang tot intranet kunnen ongewenste prestatieproblemen ondervinden.

Dit artikel bevat de resources die u nodig hebt om Azure AD-toepassingsproxy te plannen, te bedienen en te beheren. 

## <a name="plan-your-implementation"></a>Uw implementatie plannen

In de volgende sectie vindt u een breed overzicht van de belangrijkste planningselementen die u instaat voor een efficiënte implementatie-ervaring. 

### <a name="prerequisites"></a>Vereisten

U moet aan de volgende voorwaarden voldoen voordat u met uw implementatie begint. U meer informatie zien over het instellen van uw omgeving, inclusief deze vereisten, in deze [zelfstudie.](application-proxy-add-on-premises-application.md)

* **Connectors:** Connectors zijn lichtgewicht agents die u implementeren op:
   * Fysieke hardware on-premises
   * Een VM gehost in elke hypervisor-oplossing
   * Een VM die wordt gehost in Azure om uitgaande verbinding met de service Application Proxy mogelijk te maken.

* Zie [Azure AD App Proxy Connectors begrijpen](application-proxy-connectors.md) voor een gedetailleerder overzicht.

     * Connectormachines [moeten voor TLS 1.2 zijn ingeschakeld](application-proxy-add-on-premises-application.md) voordat de connectoren worden geïnstalleerd.

     * Implementeer indien mogelijk connectors in [hetzelfde netwerk](application-proxy-network-topology.md) en segment als de back-end webserverservers. Het is het beste om connectors te implementeren nadat u een detectie van toepassingen hebt voltooid.
     * We raden aan dat elke connectorgroep ten minste twee connectoren heeft om hoge beschikbaarheid en schaal te bieden. Het hebben van drie connectoren is optimaal voor het geval u een machine op elk gewenst moment moet bedienen. Bekijk de tabel met de [verbindingscapaciteit](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) om te beslissen op welk type machine connectoren moet worden geïnstalleerd. Hoe groter de machine, hoe meer buffer en performant de connector zal zijn.

* **Netwerktoegangsinstellingen**: Azure AD Application Proxy connectors [maken verbinding met Azure via HTTPS (TCP Port 443) en HTTP (TCP Port 80)](application-proxy-add-on-premises-application.md). 

   * Beëindiging van connector TLS-verkeer wordt niet ondersteund en voorkomt dat connectors een beveiligd kanaal kunnen opzetten met hun respectievelijke Azure App Proxy-eindpunten.

   * Vermijd alle vormen van inline-inspectie op uitgaande TLS-communicatie tussen connectors en Azure. Interne inspectie tussen een connector en backend toepassingen is mogelijk, maar kan de gebruikerservaring verslechteren, en als zodanig wordt niet aanbevolen.

   * Load balancing van de connectoren zelf wordt ook niet ondersteund of zelfs noodzakelijk.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Belangrijke overwegingen voordat u Azure AD-toepassingsproxy configureert

Aan de volgende kernvereisten moet worden voldaan om Azure AD-toepassingsproxy te configureren en te implementeren.

*  **Azure onboarding:** Voordat de proxy van de toepassing wordt geïmplementeerd, moeten gebruikersidentiteiten worden gesynchroniseerd vanuit een on-premises directory of rechtstreeks worden gemaakt binnen uw Azure AD-tenants. Met identiteitssynchronisatie kan Azure AD gebruikers vooraf verifiëren voordat ze toegang krijgen tot door app-proxy gepubliceerde toepassingen en de benodigde gebruikers-id-informatie hebben om eenmalige aanmelding (SSO) uit te voeren.

* **Vereisten voor voorwaardelijke toegang**: We raden het gebruik van Application Proxy niet aan voor intranettoegang, omdat dit latentie toevoegt die gevolgen heeft voor gebruikers. We raden u aan Application Proxy te gebruiken met pre-authenticatie en voorwaardelijke toegangsbeleidsregels voor externe toegang vanaf internet.  Een benadering om voorwaardelijke toegang te bieden voor intranetgebruik is het moderniseren van toepassingen, zodat ze rechtstreeks kunnen verifiëren met AAD. Raadpleeg [Bronnen voor het migreren van toepassingen naar AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) voor meer informatie. 

* **Servicelimieten**: Om te beschermen tegen overconsumptie van resources door individuele tenants zijn er beperkingslimieten per toepassing en tenant ingesteld. Als u deze limieten wilt bekijken, verwijzen we naar [Azure AD-servicelimieten en -beperkingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Deze beperkingslimieten zijn gebaseerd op een benchmark die ver boven het normale gebruiksvolume ligt en biedt voldoende buffer voor een meerderheid van de implementaties.

* **Openbaar certificaat:** Als u aangepaste domeinnamen gebruikt, moet u een TLS/SSL-certificaat aanschaffen. Afhankelijk van uw organisatorische vereisten kan het verkrijgen van een certificaat enige tijd duren en raden we u aan het proces zo vroeg mogelijk te starten. Azure Application Proxy ondersteunt standaard-, [wildcard-](application-proxy-wildcard.md)of SAN-certificaten. Zie Aangepaste [domeinen configureren met Azure AD Application Proxy](application-proxy-configure-custom-domain.md)voor meer informatie.

* **Domeinvereisten**: Voor eenmalige aanmelding voor uw gepubliceerde toepassingen met Kerberos Constrained Delegation (KCD) moet de server waarop de connector en de server waarop de app wordt uitgevoerd, domein worden samengevoegd en deel uitmaken van hetzelfde domein of vertrouwende domeinen.
Zie [KCD voor eenmalige aanmelding](application-proxy-configure-single-sign-on-with-kcd.md) met Application Proxy voor gedetailleerde informatie over het onderwerp. De verbindingsservice wordt uitgevoerd in de context van het lokale systeem en mag niet zijn geconfigureerd om een aangepaste identiteit te gebruiken.

* **DNS-records voor URL's**

   * Voordat u aangepaste domeinen in Application Proxy gebruikt, moet u een CNAME-record maken in openbare DNS, zodat clients de aangepaste gedefinieerde externe URL kunnen oplossen naar het vooraf gedefinieerde adres van de toepassingsproxy. Als u geen CNAME-record maakt voor een toepassing die een aangepast domein gebruikt, voorkomt u dat externe gebruikers verbinding maken met de toepassing. De stappen die nodig zijn om CNAME-records toe te voegen, kunnen variëren van DNS-provider tot provider, dus leer hoe u [DNS-records en recordsets beheert met behulp van de Azure-portal.](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)

   * Op dezelfde manier moeten connectorhosts de interne URL van toepassingen die worden gepubliceerd, kunnen oplossen.

* **Administratieve rechten en rollen**

   * **Voor de installatie** van connectoren zijn lokale beheerdersrechten vereist voor de Windows-server waarop deze wordt geïnstalleerd. Het vereist ook een minimum van een *rol van toepassingsbeheerder* om de verbindingsinstantie te verifiëren en te registreren bij uw Azure AD-tenant. 

   * **Voor het publiceren en beheer van toepassingen** is de rol *Toepassingsbeheerder* vereist. Toepassingsbeheerders kunnen alle toepassingen in de map beheren, inclusief registraties, SSO-instellingen, gebruikers- en groepstoewijzingen en licenties, instellingen voor toepassingsproxy en toestemming. Het verleent geen de mogelijkheid om voorwaardelijke toegang te beheren. De rol *CloudApplication Administrator* heeft alle mogelijkheden van de toepassingsbeheerder, behalve dat het beheer van toepassingsproxy-instellingen niet mogelijk is.

* **Licenties:** Application Proxy is beschikbaar via een Azure AD Premium-abonnement. Raadpleeg de [pagina Azure Active Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/) voor een volledige lijst met licentieopties en -functies.  

### <a name="application-discovery"></a>Detectie van toepassingen

Stel een inventaris samen van alle toepassingen in het bereik die worden gepubliceerd via Application Proxy door de volgende informatie te verzamelen:

| Informatietype| Informatie om te verzamelen |
|---|---|
| Servicetype| Bijvoorbeeld: SharePoint, SAP, CRM, Custom Web Application, API |
| Toepassingsplatform | Bijvoorbeeld: Windows IIS, Apache op Linux, Tomcat, NGINX |
| Lidmaatschap van domein| Volledig gekwalificeerde domeinnaam van de webserver (FQDN) |
| Toepassingslocatie | Waar de webserver of farm zich in uw infrastructuur bevindt |
| Interne toegang | De exacte URL die wordt gebruikt bij het intern openen van de toepassing. <br> Als een boerderij, welk type van load balancing is in gebruik? <br> Of de toepassing inhoud haalt uit andere bronnen dan zichzelf.<br> Bepaal of de toepassing via WebSockets werkt. |
| Externe toegang | De leverancieroplossing waaraan de toepassing al extern is blootgesteld. <br> De URL die u wilt gebruiken voor externe toegang. Als SharePoint moet u ervoor zorgen dat Alternatieve toegangstoewijzingen zijn geconfigureerd volgens [deze richtlijnen](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Als dit niet het zo is, moet u externe URL's definiëren. |
| Openbaar certificaat | Als u een aangepast domein gebruikt, aanschaft u een certificaat met een overeenkomstige onderwerpnaam. als er een certificaat bestaat, moet u het serienummer en de locatie noteren van waaruit het kan worden verkregen. |
| Verificatietype| Het type verificatie dat wordt ondersteund door de toepassingsondersteuning, zoals Basic, Windows Integration Authentication, forms-based, header-based en claims. <br>Als de toepassing is geconfigureerd om onder een specifiek domeinaccount te worden uitgevoerd, noteert u de FQDN (Fully Qualified Domain Name) van het serviceaccount.<br> Als SAML-gebaseerd, de id en antwoord URL's. <br> Als de leverancier op basis van kopteksten en specifieke vereisten voor het verwerken van verificatietype. |
| Naam van de connectorgroep | De logische naam voor de groep connectoren die zal worden aangewezen om de leiding en SSO te leveren aan deze backend applicatie. |
| Toegang tot gebruikers/groepen | De gebruikers of gebruikersgroepen die externe toegang tot de toepassing krijgen. |
| Aanvullende eisen | Let op eventuele aanvullende externe toegangs- of beveiligingsvereisten die moeten worden meegenomen in het publiceren van de toepassing. |

U deze [spreadsheet voor toepassingsinventaris](https://aka.ms/appdiscovery) downloaden om uw apps te inventariseren.

### <a name="define-organizational-requirements"></a>Organisatorische vereisten definiëren

Hieronder volgen gebieden waarvoor u de zakelijke vereisten van uw organisatie moet definiëren. Elk gebied bevat voorbeelden van vereisten

 **Toegang**

* Externe gebruikers met domeinsamengevoegde of Azure AD-apparaten gebruikers kunnen veilig toegang krijgen tot gepubliceerde toepassingen met naadloze single sign-on (SSO).

* Externe gebruikers met goedgekeurde persoonlijke apparaten hebben veilig toegang tot gepubliceerde toepassingen, mits ze zijn ingeschreven bij MFA en de Microsoft Authenticator-app op hun mobiele telefoon hebben geregistreerd als verificatiemethode.

**Governance** 

* Beheerders kunnen de levenscyclus van gebruikerstoewijzingen definiëren en bewaken naar toepassingen die zijn gepubliceerd via Application Proxy.

**Beveiliging**

* Alleen gebruikers die via groepslidmaatschap of individueel aan toepassingen zijn toegewezen, hebben toegang tot deze toepassingen.

**Prestaties**

* Er is geen verslechtering van de prestaties van toepassingen in vergelijking met de toegang tot toepassing van het interne netwerk.

**Gebruikerservaring**

* Gebruikers zijn zich bewust van hoe ze toegang krijgen tot hun toepassingen door gebruik te maken van bekende bedrijfs-URL's op elk apparaatplatform.

**Controleren**
* Beheerders kunnen gebruikerstoegangsactiviteiten controleren.


### <a name="best-practices-for-a-pilot"></a>Aanbevolen procedures voor een pilot

Bepaal de hoeveelheid tijd en moeite die nodig is om één enkele toepassing volledig in opdracht te geven voor toegang op afstand met Single sign-on (SSO). Doe dit door een pilot uit te voeren die rekening houdt met de eerste detectie, publicatie en algemene tests. Het gebruik van een eenvoudige IIS-gebaseerde webapplicatie die al vooraf is geconfigureerd voor Integrated Windows Authentication (IWA) zou helpen bij het vaststellen van een basislijn, omdat deze instelling minimale inspanning vereist om met succes externe toegang en SSO te besturen.

De volgende ontwerpelementen moeten het succes van uw pilotimplementatie direct in een productietenant vergroten.  

**Connectorbeheer**:  

* Connectors spelen een belangrijke rol bij het leveren van de on-premises leiding aan uw toepassingen. Het gebruik van de **standaardconnectorgroep** is voldoende voor het eerste testen van gepubliceerde toepassingen voordat ze in productie worden genomen. Met succes geteste toepassingen kunnen vervolgens worden verplaatst naar productieconnectorgroepen.

**Applicatiebeheer**:

* Uw personeelsbestand onthoudt waarschijnlijk dat een externe URL bekend en relevant is. Vermijd het publiceren van uw toepassing met behulp van onze vooraf gedefinieerde msappproxy.net of onmicrosoft.com achtervoegsels. Geef in plaats daarvan een vertrouwd geverifieerd domein op het hoogste niveau, vooraf bevestigd met een logische hostnaam zoals *intranet.<customers_domain>.com*.

* Beperk de zichtbaarheid van het pictogram van de pilottoepassing tot een pilotgroep door het startpictogram te verbergen dat de Azure MyApps-portal vormt. Wanneer u klaar bent voor productie, u de app richten op de desbetreffende doelgroep, hetzij in dezelfde pre-productietenant, hetzij door de toepassing ook in uw productietenant te publiceren.

**Instellingen voor één aanmelding:** sommige SSO-instellingen hebben specifieke afhankelijkheden die tijd kunnen kosten om in te stellen, dus voorkom vertragingen bij het beheren van wijzigingen door ervoor te zorgen dat afhankelijkheden van tevoren worden aangepakt. Dit omvat domeinverbindingsconnectorhosts om SSO uit te voeren met Kerberos Constrained Delegation (KCD) en het verzorgen van andere tijdrovende activiteiten. Bijvoorbeeld een PING Access-exemplaar instellen, als u SSO op basis van koptekstnodig hebt.

**TLS Tussen Connector Host en Target Application**: Beveiliging staat voorop, dus TLS tussen de connectorhost en doeltoepassingen moet altijd worden gebruikt. Vooral als de webtoepassing is geconfigureerd voor op formulieren gebaseerde verificatie (FBA), omdat gebruikersreferenties vervolgens effectief worden verzonden in duidelijke tekst.

**Implementeren stapsgewijs en test elke stap**. Voer basisfunctionele tests uit na het publiceren van een toepassing om ervoor te zorgen dat aan alle gebruikers- en bedrijfsvereisten wordt voldaan door de onderstaande aanwijzingen te volgen:

1. Algemene toegang tot de webtoepassing testen en valideren met pre-authenticatie uitgeschakeld.
2. Als dit is gelukt, schakelt u pre-authenticatie in en wijst u gebruikers en groepen toe. Test en valideer de toegang.
3. Voeg vervolgens de SSO-methode voor uw toepassing toe en test opnieuw om de toegang te valideren.
4. Pas afhankelijke toegang en MFA-beleid naar behoefte toe. Test en valideer de toegang.

**Hulpmiddelen voor probleemoplossing:** Bij het oplossen van problemen moet u altijd beginnen met het valideren van de toegang tot de gepubliceerde toepassing vanuit de browser op de connectorhost en controleren of de toepassing functioneert zoals verwacht. Hoe eenvoudiger uw installatie, hoe gemakkelijker de hoofdoorzaak te bepalen, dus overweeg om problemen te reproduceren met een minimale configuratie, zoals het gebruik van slechts een enkele connector en geen SSO. In sommige gevallen kunnen webdebugging-tools zoals Telerik's Fiddler onmisbaar blijken om toegangs- of inhoudsproblemen op te lossen in toepassingen die via een proxy worden geopend. Fiddler kan ook fungeren als een proxy om te helpen bij het traceren en debuggen verkeer voor mobiele platforms zoals iOS en Android, en vrijwel alles dat kan worden geconfigureerd om route via een proxy. Zie de [handleiding voor probleemoplossing](application-proxy-troubleshoot.md) voor meer informatie.

## <a name="implement-your-solution"></a>Uw oplossing implementeren

### <a name="deploy-application-proxy"></a>Toepassingsproxy implementeren

De stappen voor het implementeren van uw toepassingsproxy worden in deze zelfstudie behandeld [voor het toevoegen van een on-premises toepassing voor externe toegang.](application-proxy-add-on-premises-application.md) Als de installatie niet is gelukt, selecteert u **Toepassingsproxy oplossen** in de portal of gebruikt u de handleiding voor probleemoplossing [voor problemen met het installeren van de Application Proxy Agent Connector](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Sollicitaties publiceren via Application Proxy

Het publiceren van toepassingen gaat ervan uit dat u aan alle vereisten hebt voldaan en dat u verschillende connectors hebt die als geregistreerd en actief worden weergegeven op de pagina Application Proxy.

U ook toepassingen publiceren met [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Hieronder vindt u enkele aanbevolen procedures die u volgen bij het publiceren van een toepassing:

* **Connectorgroepen gebruiken:** wijs een verbindingsgroep toe die is aangewezen voor het publiceren van elke desbetreffende toepassing. We raden aan dat elke connectorgroep ten minste twee connectoren heeft om hoge beschikbaarheid en schaal te bieden. Het hebben van drie connectoren is optimaal voor het geval u een machine op elk gewenst moment moet bedienen. Zie Daarnaast Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van verbindingsgroepen om te zien hoe u connectorgroepen ook gebruiken om uw connectors te [segmenteren](application-proxy-connector-groups.md) op netwerk of locatie.

* **Backend-toepassingstime-out instellen:** deze instelling is handig in scenario's waarin de toepassing mogelijk meer dan 75 seconden nodig heeft om een clienttransactie te verwerken. Bijvoorbeeld wanneer een client een query verzendt naar een webtoepassing die fungeert als een front-end naar een database. De front-end stuurt deze query naar de back-end database server en wacht op een antwoord, maar tegen de tijd dat het een antwoord ontvangt, de client kant van het gesprek time-out. Als u de time-out instelt op Long, biedt u 180 seconden om langere transacties te voltooien.

* **Geschikte cookietypen gebruiken**

   * **HTTP-Only Cookie**: Biedt extra beveiliging door Application Proxy de HTTPOnly-vlag te laten opnemen in HTTP-reactiekoppen voor set-cookie.HTTP-Only Cookie : Provides additional security by having Application Proxy include the HTTPOnly flag in set-cookie HTTP response headers. Deze instelling helpt exploits zoals cross-site scripting (XSS) te beperken. Laat deze set aan Nee over voor clients/gebruikersagents die wel toegang nodig hebben tot de sessiecookie. Rdp/MTSC-client die verbinding maakt met een Extern bureaublad-gateway die is gepubliceerd via App Proxy.

   * **Beveiligde cookie:** Wanneer een cookie is ingesteld met het kenmerk Secure, zal de gebruikersagent (client-side app) de cookie alleen opnemen in HTTP-verzoeken als het verzoek wordt verzonden via een door TLS beveiligd kanaal. Dit helpt het risico te beperken dat een cookie wordt gecompromitteerd via duidelijke tekstkanalen, dus moet worden ingeschakeld.

   * **Persistent Cookie**: Hiermee kan de sessiecookie van Application Proxy blijven bestaan tussen browsersluitingen door geldig te blijven totdat deze verloopt of wordt verwijderd. Wordt gebruikt voor scenario's waarin een uitgebreide toepassing, zoals office, toegang krijgt tot een document binnen een gepubliceerde webtoepassing, zonder dat de gebruiker opnieuw wordt gevraagd om verificatie. Schakel echter voorzichtig in, omdat persistente cookies uiteindelijk een dienst het risico kunnen geven van ongeautoriseerde toegang, indien deze niet worden gebruikt in combinatie met andere veredelingscontroles. Deze instelling mag alleen worden gebruikt voor oudere toepassingen die geen cookies tussen processen kunnen delen. Het is beter om uw toepassing bij te werken om het delen van cookies tussen processen te verwerken in plaats van deze instelling te gebruiken.

* **URL's vertalen in kopteksten:** U schakelt dit in voor scenario's waarin interne DNS niet kan worden geconfigureerd om overeen te komen met de openbare naamruimte van de organisatie(a.k.a Split DNS). Tenzij voor uw toepassing de oorspronkelijke hostheader in het clientverzoek vereist is, laat u deze waarde in op Ja. Het alternatief is om de connector de FQDN in de interne URL te laten gebruiken voor het routeren van het werkelijke verkeer en de FQDN in de externe URL, als de host-header. In de meeste gevallen moet dit alternatief de toepassing als normaal laten functioneren, wanneer deze op afstand wordt geopend, maar uw gebruikers verliezen de voordelen van het hebben van een matching binnen & externe URL.

* **URL's vertalen in toepassingsinstantie:** Schakel koppelingsvertaling van toepassingsinstantie in voor een app wanneer u wilt dat de koppelingen van die app worden vertaald in antwoorden terug naar de client. Als deze functie is ingeschakeld, biedt deze functie een poging om alle interne koppelingen te vertalen die App Proxy in HTML- en CSS-antwoorden vindt die naar clients worden geretourneerd. Het is handig bij het publiceren van apps die harde absolute of NetBIOS-shortnamekoppelingen in de inhoud bevatten, of apps met inhoud die naar andere on-premises toepassingen linkt.

Voor scenario's waarin een gepubliceerde app naar andere gepubliceerde apps verwijst, schakelt u linkvertaling in voor elke toepassing, zodat u controle hebt over de gebruikerservaring op het niveau per app.

Stel dat u drie toepassingen hebt gepubliceerd via Application Proxy die allemaal met elkaar worden gekoppeld: voordelen, onkosten en reizen, plus een vierde app, Feedback, die niet wordt gepubliceerd via Application Proxy.

![Afbeelding 1](media/App-proxy-deployment-plan/link-translation.png)

Wanneer u koppelingsvertaling inschakelt voor de App Voordelen, worden de koppelingen naar Onkosten en Reizen doorgestuurd naar de externe URL's voor die apps, zodat gebruikers die toegang hebben tot de toepassingen van buiten het bedrijfsnetwerk er toegang toe hebben. Koppelingen van onkosten en reis terug naar voordelen werken niet omdat linkvertaling niet is ingeschakeld voor deze twee apps. De koppeling naar feedback wordt niet omgeleid omdat er geen externe URL is, zodat gebruikers die de App Voordelen gebruiken, geen toegang hebben tot de feedback-app van buiten het bedrijfsnetwerk. Zie gedetailleerde informatie over [linkvertaling en andere omleidingsopties.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Toegang tot uw toepassing

Er bestaan verschillende opties voor het beheren van toegang tot door app-proxy gepubliceerde bronnen, dus kies de meest geschikte voor uw opgegeven scenario en schaalbaarheidsbehoeften. Veelvoorkomende benaderingen zijn: het gebruik van on-premises groepen die worden gesynchroniseerd via Azure AD Connect, het maken van dynamische groepen in Azure AD op basis van gebruikerskenmerken, met behulp van selfservicegroepen die worden beheerd door een broneigenaar, of een combinatie van al deze. Bekijk de gekoppelde bronnen voor de voordelen van elk.

De meest rechttoe rechtaan manier om gebruikers toegang te geven tot een toepassing gaat naar de opties **Gebruikers en Groepen** vanuit het linkerdeelvenster van uw gepubliceerde toepassing en het rechtstreeks toewijzen van groepen of individuen.

![Foto 24](media/App-proxy-deployment-plan/add-user.png)

U gebruikers ook toestaan om zelfservicetoegang tot uw toepassing toe te staan door een groep toe te wijst waarvan ze momenteel geen lid zijn en de self-serve-opties te configureren.

![Foto 25](media/App-proxy-deployment-plan/allow-access.png)

Als dit is ingeschakeld, kunnen gebruikers zich aanmelden bij de MyApps-portal en toegang aanvragen en automatisch worden goedgekeurd en toegevoegd aan de al toegestane selfservicegroep, of goedkeuring nodig hebben van een aangewezen goedkeurder.

Gastgebruikers kunnen ook worden [uitgenodigd om toegang te krijgen tot interne toepassingen die via Application Proxy worden gepubliceerd via Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)

Voor on-premises toepassingen die normaal gesproken anoniem toegankelijk zijn en waarvoor geen verificatie vereist is, u de optie in de **eigenschappen**van de toepassing uitschakelen.

![Foto 26](media/App-proxy-deployment-plan/assignment-required.png)


Als u deze optie instelt op Nee, kunnen gebruikers de on-premises toepassing zonder machtigingen openen via Azure AD App Proxy, dus wees voorzichtig.

Zodra uw toepassing is gepubliceerd, moet deze toegankelijk zijn door de [https://myapps.microsoft.com](https://myapps.microsoft.com/)externe URL in een browser of door het pictogram te typen op .

### <a name="enable-pre-authentication"></a>Vooraf verificatie inschakelen

Controleer of uw toepassing toegankelijk is via Application Proxy die toegang krijgt tot deze via de externe URL. 

1. Navigeer naar **Azure Active Directory** > **Enterprise-toepassingen** > **Alle toepassingen** en kies de app die u wilt beheren.

2. Selecteer **Toepassingsproxy**.

3. Gebruik in het veld **Pre-authentication** de vervolgkeuzelijst om **Azure Active Directory**te selecteren en selecteer **Opslaan**.

Als vooraf verificatie is ingeschakeld, daagt Azure AD gebruikers eerst uit voor verificatie en als eenmalige aanmelding is geconfigureerd, controleert de back-endtoepassing de gebruiker ook voordat toegang tot de toepassing wordt verleend. Als u de pre-authenticatiemodus wijzigt van Passthrough naar Azure AD, configureert u ook de externe URL met HTTPS, zodat elke toepassing die in eerste instantie voor HTTP is geconfigureerd, nu wordt beveiligd met HTTPS.

### <a name="enable-single-sign-on"></a>Eén aanmelding inschakelen

SSO biedt de best mogelijke gebruikerservaring en beveiliging omdat gebruikers zich slechts één keer hoeven aan te melden bij toegang tot Azure AD. Zodra een gebruiker vooraf is geverifieerd, wordt SSO uitgevoerd door de Application Proxy-connector die namens de gebruiker authentiek is voor de on-premises toepassing. De backend applicatie verwerkt de login alsof het de gebruiker zelf. 

Als u de optie **Passthrough kiest,** kunnen gebruikers toegang krijgen tot de gepubliceerde toepassing zonder dat ze zich ooit hoeven te verifiëren bij Azure AD.

Het uitvoeren van SSO is alleen mogelijk als Azure AD de gebruiker kan identificeren die toegang tot een bron aanvraagt, dus uw toepassing moet zijn geconfigureerd om gebruikers vooraf te verifiëren met Azure AD nadat sso kan functioneren, anders worden de SSO-opties uitgeschakeld.

Lees [Enkele aanmelding voor toepassingen in Azure AD](what-is-single-sign-on.md) om u te helpen bij het kiezen van de meest geschikte SSO-methode bij het configureren van uw toepassingen.

###  <a name="working-with-other-types-of-applications"></a>Werken met andere soorten toepassingen

Azure AD Application Proxy kan ook toepassingen ondersteunen die zijn ontwikkeld om onze Azure AD Authentication Library[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)of Microsoft Authentication Library[(MSAL)](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)te gebruiken. Het ondersteunt native client-apps door azure AD-tokens te gebruiken die zijn ontvangen in de header-informatie van clientaanvragen om vooraf verificatie uit te voeren namens de gebruikers.

Lees [publicatie van native en mobile client apps](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) en [claims-gebaseerde toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) om meer te weten te komen over beschikbare configuraties van Application Proxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Voorwaardelijke toegang gebruiken om de beveiliging te versterken

Toepassingsbeveiliging vereist een geavanceerde set beveiligingsmogelijkheden die kunnen beschermen tegen en reageren op complexe bedreigingen on-premises en in de cloud. Aanvallers krijgen meestal toegang tot het bedrijfsnetwerk via zwakke, standaard- of gestolen gebruikersreferenties.  Microsoft-identiteitsgestuurde beveiliging vermindert het gebruik van gestolen referenties door zowel bevoorrechte als niet-bevoorrechte identiteiten te beheren en te beschermen.

De volgende mogelijkheden kunnen worden gebruikt om Azure AD-toepassingsproxy te ondersteunen:

* Afhankelijke toegang voor gebruikers en locaties: houd gevoelige gegevens beschermd door de toegang van gebruikers te beperken op basis van geolocatie of een IP-adres met [locatiegebaseerdbeleid voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Voorwaardelijke toegang op basis van apparaten: zorg ervoor dat apparaten die alleen zijn ingeschreven, goedgekeurd en compatibel zijn, toegang hebben tot bedrijfsgegevens met [op apparaten gebaseerde voorwaardelijke toegang.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)

* Afhankelijke toegang voor toepassingen: werk hoeft niet te stoppen wanneer een gebruiker zich niet in het bedrijfsnetwerk bevindt. [Beveiligde toegang tot bedrijfscloud- en on-premises apps](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) en behoud de controle met Voorwaardelijke toegang.

* Voorwaardelijke toegang op basis van risico's: bescherm uw gegevens tegen kwaadwillende hackers met een [op risico's gebaseerd conditional access-beleid](https://www.microsoft.com/cloud-platform/conditional-access) dat kan worden toegepast op alle apps en alle gebruikers, zowel on-premises als in de cloud.

* Azure AD Access-paneel: met uw Application Proxy-service geïmplementeerd en toepassingen die veilig zijn gepubliceerd, bieden u uw gebruikers een eenvoudige hub om al hun toepassingen te ontdekken en te openen. Verhoog de productiviteit met selfservicemogelijkheden, zoals de mogelijkheid om toegang tot nieuwe apps en groepen aan te vragen of de toegang tot deze bronnen namens anderen te beheren, via het [Toegangspaneel.](https://aka.ms/AccessPanelDPDownload)

## <a name="manage-your-implementation"></a>Uw implementatie beheren

### <a name="required-roles"></a>Vereiste rollen

Microsoft pleit voor het principe van het verlenen van zo min mogelijk bevoegdheden om benodigde taken uit te voeren met Azure AD. [Bekijk de verschillende Azure-rollen die beschikbaar zijn](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) en kies de juiste om aan de behoeften van elke persona te voldoen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en verwijderd nadat de implementatie is voltooid.

| Zakelijke rol| Zakelijke taken| Azure AD-rollen |
|---|---|---|
| Helpdeskbeheerder | Meestal beperkt tot in aanmerking komende problemen van eindgebruikers en het uitvoeren van beperkte taken, zoals het wijzigen van wachtwoorden van gebruikers, het ongeldig maken van vernieuwingstokens en het controleren van de servicestatus. | Helpdeskbeheerder |
| Identiteitsbeheerder| Lees Azure AD-aanmeldingsrapporten en controlelogboeken om gerelateerde problemen met app-proxy te debuggen.| Beveiligingslezer |
| Toepassingseigenaar| Alle aspecten van bedrijfstoepassingen, toepassingsregistraties en proxy-instellingen voor toepassingen maken en beheren.| Toepassingsbeheerder |
| Infrastructuurbeheerder | Certificaatrollover-eigenaar | Toepassingsbeheerder |

Het minimaliseren van het aantal mensen dat toegang heeft tot beveiligde informatie of bronnen zal helpen bij het verminderen van de kans dat een kwaadwillende acteur ongeautoriseerde toegang krijgt, of een geautoriseerde gebruiker die per ongeluk een gevoelige bron beïnvloedt. 
 
Gebruikers moeten echter nog steeds dagelijkse bevoorrechte bewerkingen uitvoeren, dus het afdwingen van op just-in-time (JIT) gebaseerd [privileged Identity Management-beleid](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) om on-demand bevoorrechte toegang tot Azure-bronnen te bieden en Azure AD is onze aanbevolen aanpak voor het effectief beheren van beheervan beheertoegang en auditing.

### <a name="reporting-and-monitoring"></a>Rapportage en bewaking

Azure AD biedt aanvullende inzichten in het toepassingsgebruik en de operationele status van uw organisatie via [auditlogboeken en rapporten.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Application Proxy maakt het ook heel eenvoudig om connectors van de Azure AD-portal en Windows Event Logs te controleren.

#### <a name="application-audit-logs"></a>Auditlogboeken van toepassingen

Deze logboeken bieden gedetailleerde informatie over aanmeldingen bij toepassingen die zijn geconfigureerd met Application Proxy en het apparaat en de gebruiker die toegang heeft tot de toepassing. [Controlelogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) bevinden zich in de Azure-portal en in [audit-API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) voor export. Daarnaast zijn er ook rapporten over [gebruik en inzichten](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) beschikbaar voor uw toepassing.

#### <a name="application-proxy-connector-monitoring"></a>Proxyconnectorbewaking voor toepassingen

De connectoren en de service zorgen voor alle taken met hoge beschikbaarheid. U de status van uw connectors controleren vanaf de pagina Proxy van toepassingen in de Azure AD-portal. Zie Inzicht in Azure [AD Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)voor meer informatie over connectoronderhoud.

![Voorbeeld: Azure AD Application Proxy connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-gebeurtenislogboeken en prestatiemeteritems

Connectors hebben zowel beheerders- als sessielogboeken. De beheerderslogboeken bevatten belangrijke gebeurtenissen en hun fouten. De sessielogboeken bevatten alle transacties en hun verwerkingsgegevens. Logboeken en tellers bevinden zich in Windows-gebeurtenislogboeken voor meer [informatie: Inzicht in Azure AD Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Volg deze [zelfstudie om gebeurtenislogboekgegevensbronnen in Azure Monitor te configureren.](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)

### <a name="troubleshooting-guide-and-steps"></a>Handleiding en stappen voor probleemoplossing

Meer informatie over veelvoorkomende problemen en hoe u deze oplossen met onze handleiding voor [het oplossen van](application-proxy-troubleshoot.md) foutmeldingen. 

De volgende artikelen hebben betrekking op veelvoorkomende scenario's die ook kunnen worden gebruikt om handleidingen voor probleemoplossing voor uw ondersteuningsorganisatie te maken. 

* [Probleem bij weergeven app-pagina](application-proxy-page-appearance-broken-problem.md)
* [Applicatie laden duurt te lang](application-proxy-page-load-speed-problem.md)
* [Koppelingen op de toepassingspagina werken niet](application-proxy-page-links-broken-problem.md)
* [Welke poorten moet ik openen voor mijn app?](application-proxy-connectivity-ports-how-to.md)
* [Geen werkende connector in een connectorgroep voor mijn app](application-proxy-connectivity-no-working-connector.md)
* [Configureren in beheerportal](application-proxy-config-how-to.md)
* [Eenmalige aanmelding voor mijn app configureren](application-proxy-config-sso-how-to.md)
* [Probleem bij het maken van een app in de beheerportal](application-proxy-config-problem.md)
* [Kerberos-beperkte overdracht configureren](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configureren met PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [De fout 'Kan deze zakelijke toepassing niet openen'](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probleem bij het installeren van de connector voor de toepassingsproxyagent](application-proxy-connector-installation-problem.md)
* [Aanmeldingsprobleem](application-sign-in-problem-on-premises-application-proxy.md)
