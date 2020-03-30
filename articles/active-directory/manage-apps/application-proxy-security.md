---
title: Beveiligingsoverwegingen voor Azure AD-toepassingsproxy | Microsoft Documenten
description: Dekt beveiligingsoverwegingen voor het gebruik van Azure AD-toepassingsproxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481225"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Beveiligingsoverwegingen voor toegang tot apps op afstand met Azure AD-toepassingsproxy

In dit artikel worden de onderdelen uitgelegd die werken om uw gebruikers en toepassingen veilig te houden wanneer u Azure Active Directory Application Proxy gebruikt.

In het volgende diagram ziet u hoe Azure AD veilige externe toegang tot uw on-premises toepassingen mogelijk maakt.

 ![Diagram met beveiligde externe toegang via Azure AD-toepassingsproxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Beveiligingsvoordelen

Azure AD Application Proxy biedt de volgende beveiligingsvoordelen:

### <a name="authenticated-access"></a>Geverifieerde toegang 

Als u ervoor kiest om Azure Active Directory-voorverificatie te gebruiken, hebben alleen geverifieerde verbindingen toegang tot uw netwerk.

Azure AD Application Proxy is afhankelijk van de Azure AD Security Token Service (STS) voor alle verificatie.  Preauthentication blokkeert van nature een aanzienlijk aantal anonieme aanvallen, omdat alleen geverifieerde identiteiten toegang hebben tot de back-endtoepassing.

Als u Passthrough als uw preauthenticatiemethode kiest, krijgt u dit voordeel niet. 

### <a name="conditional-access"></a>Voorwaardelijke toegang

Pas uitgebreidere beleidsbesturingselementen toe voordat verbindingen met uw netwerk tot stand worden gebracht.

Met [Voorwaardelijke toegang](../conditional-access/overview.md)u beperkingen definiëren voor welk verkeer toegang heeft tot uw back-endtoepassingen. U beleid maken dat aanmeldingen beperkt op basis van locatie, sterkte van verificatie en gebruikersrisicoprofiel.

U voorwaardelijke toegang ook gebruiken om beleid voor meervoudige verificatie te configureren en een andere beveiligingslaag toe te voegen aan uw gebruikersverificaties. Bovendien kunnen uw toepassingen ook worden doorgestuurd naar Microsoft Cloud App Security via Azure AD Conditional Access om realtime bewaking en besturingselementen te bieden, via [toegangs-](https://docs.microsoft.com/cloud-app-security/access-policy-aad) en [sessiebeleid](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Verkeersbeëindiging

Al het verkeer wordt beëindigd in de cloud.

Omdat Azure AD Application Proxy een reverse-proxy is, wordt al het verkeer naar back-endtoepassingen beëindigd bij de service. De sessie kan alleen worden hersteld met de back-endserver, wat betekent dat uw back-endservers niet worden blootgesteld aan direct HTTP-verkeer. Deze configuratie betekent dat u beter beschermd bent tegen gerichte aanvallen.

### <a name="all-access-is-outbound"></a>Alle toegang is uitgaand 

U hoeft geen binnenkomende verbindingen met het bedrijfsnetwerk te openen.

Application Proxy-connectors gebruiken alleen uitgaande verbindingen met de Azure AD Application Proxy-service, wat betekent dat er geen firewallpoorten hoeven te worden geopend voor binnenkomende verbindingen. Traditionele proxy's vereisten een perimeternetwerk (ook bekend als *DMZ,* *gedemilitariseerde zone*of *gescreend subnet)* en toegestaan toegang tot niet-geverifieerde verbindingen aan de netwerkrand. Dit scenario vereiste investeringen in firewallproducten voor webtoepassingen om verkeer te analyseren en het milieu te beschermen. Met Application Proxy hebt u geen perimeternetwerk nodig omdat alle verbindingen uitgaand zijn en plaatsvinden via een beveiligd kanaal.

Zie [Azure AD Application Proxy connectors begrijpen](application-proxy-connectors.md)voor meer informatie over connectors.

### <a name="cloud-scale-analytics-and-machine-learning"></a>Cloud-scale analytics en machine learning 

Profiteer van geavanceerde beveiliging.

Omdat het onderdeel is van Azure Active Directory, kan Application Proxy gebruikmaken van [Azure AD Identity Protection,](../active-directory-identityprotection.md)met gegevens van het Microsoft Security Response Center en Digital Crimes Unit. Samen identificeren we proactief gecompromitteerde accounts en bieden we bescherming tegen risicovolle aanmeldingen. We houden rekening met tal van factoren om te bepalen welke aanmeldingspogingen een hoog risico zijn. Deze factoren omvatten het markeren van geïnfecteerde apparaten, anonymizing netwerken, en atypische of onwaarschijnlijke locaties.

Veel van deze rapporten en gebeurtenissen zijn al beschikbaar via een API voor integratie met uw SIEM-systemen (Security Information and Event Management).

### <a name="remote-access-as-a-service"></a>Externe toegang als service

U hoeft zich geen zorgen te maken over het onderhouden en patchen van on-premises servers.

Ongepatchte software is nog steeds goed voor een groot aantal aanvallen. Azure AD Application Proxy is een internet-schaal service die Microsoft bezit, zodat u altijd de nieuwste beveiligingspatches en upgrades.

Om de beveiliging van toepassingen die zijn gepubliceerd door Azure AD Application Proxy te verbeteren, blokkeren we webcrawlerrobots om uw toepassingen te indexeren en te archiveren. Elke keer dat een webcrawlerrobot de instellingen van de robot voor een gepubliceerde app probeert `User-agent: * Disallow: /`op te halen, antwoordt Application Proxy met een robots.txt-bestand met daarin .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS-beveiligingsservice

Toepassingen die worden gepubliceerd via Application Proxy zijn beschermd tegen DDoS-aanvallen (Distributed Denial of Service). **Azure DDoS-beveiliging** is een service die wordt aangeboden met het Azure-platform om uw Azure-bronnen te beschermen tegen denial of service-aanvallen. De **Basic** Basic-servicelaag wordt automatisch ingeschakeld, waardoor altijd verkeersbewaking en real-time-beperking van veelvoorkomende aanvallen op netwerkniveau wordt geboden. Er is ook een **standaardlaag** beschikbaar, die extra mitigatiemogelijkheden biedt die specifiek zijn afgestemd op Azure Virtual Network-bronnen. Zie overzicht [van Azure DDoS Protection Standard voor](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)meer informatie.

## <a name="under-the-hood"></a>Onderhuids

Azure AD-toepassingsproxy bestaat uit twee delen:

* De cloudservice: deze service wordt uitgevoerd in Azure en is waar de externe client-/gebruikersverbindingen worden gemaakt.
* [De on-premises connector](application-proxy-connectors.md): de connector luistert naar aanvragen van de Azure AD Application Proxy-service en verwerkt verbindingen met de interne toepassingen als on-premises component: een on-premises component. 

Er wordt een stroom tussen de connector en de application proxy-service ingesteld wanneer:

* De connector wordt als eerste ingesteld.
* De connector haalt configuratiegegevens uit de application proxy-service.
* Een gebruiker heeft toegang tot een gepubliceerde toepassing.

>[!NOTE]
>Alle communicatie vindt plaats via TLS, en ze komen altijd uit bij de connector naar de Application Proxy-service. De service is alleen uitgaand.

De connector gebruikt een clientcertificaat om te verifiëren aan de application proxy-service voor bijna alle gesprekken. De enige uitzondering op dit proces is de eerste installatiestap, waarbij het clientcertificaat wordt opgesteld.

### <a name="installing-the-connector"></a>De connector installeren

Wanneer de connector voor het eerst is ingesteld, vinden de volgende stroomgebeurtenissen plaats:

1. De connectorregistratie voor de service gebeurt als onderdeel van de installatie van de connector. Gebruikers wordt gevraagd hun Azure AD-beheerdersreferenties in te voeren.Het token dat met deze verificatie is verkregen, wordt vervolgens gepresenteerd aan de Azure AD Application Proxy-service.
2. De application proxy-service evalueert het token. Het controleert of de gebruiker een bedrijfsbeheerder in de tenant is.Als de gebruiker geen beheerder is, wordt het proces beëindigd.
3. De connector genereert een clientcertificaataanvraag en geeft deze, samen met het token, door aan de application proxy-service. De service verifieert op zijn beurt het token en ondertekent de aanvraag voor clientcertificaat.
4. De connector gebruikt het clientcertificaat voor toekomstige communicatie met de application proxy-service.
5. De connector voert een eerste pull van de systeemconfiguratiegegevens uit de service uit met behulp van het clientcertificaat en is nu klaar om aanvragen aan te nemen.

### <a name="updating-the-configuration-settings"></a>De configuratie-instellingen bijwerken

Wanneer de application proxy-service de configuratie-instellingen bijwerkt, vinden de volgende stroomgebeurtenissen plaats:

1. De connector maakt verbinding met het configuratieeindpunt binnen de application proxy-service met behulp van het clientcertificaat.
2. Nadat het clientcertificaat is gevalideerd, retourneert de service Application Proxy configuratiegegevens naar de connector (bijvoorbeeld de verbindingsgroep waarvan de connector deel moet uitmaken).
3. Als het huidige certificaat meer dan 180 dagen oud is, genereert de connector een nieuwe certificaataanvraag, waarmee het clientcertificaat om de 180 dagen effectief wordt bijgewerkt.

### <a name="accessing-published-applications"></a>Toegang tot gepubliceerde toepassingen

Wanneer gebruikers toegang krijgen tot een gepubliceerde toepassing, vinden de volgende gebeurtenissen plaats tussen de application proxy-service en de application proxy-connector:

1. De service verifieert de gebruiker voor de app
2. De service plaatst een aanvraag in de connectorwachtrij
3. Een connector verwerkt de aanvraag vanuit de wachtrij
4. De connector wacht op een antwoord
5. De service streamt gegevens naar de gebruiker

Voor meer informatie over wat er gebeurt in elk van deze stappen, blijven lezen.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. De service verifieert de gebruiker voor de app

Als u de app hebt geconfigureerd om Passthrough als verificatiemethode te gebruiken, worden de stappen in deze sectie overgeslagen.

Als u de app hebt geconfigureerd om vooraf te verifiëren met Azure AD, worden gebruikers doorgestuurd naar de Azure AD STS om te verifiëren en vinden de volgende stappen plaats:

1. Toepassingsproxy controleert op eventuele vereisten voor voorwaardelijke toegang-beleidsregels voor de specifieke toepassing. Deze stap zorgt ervoor dat de gebruiker is toegewezen aan de toepassing. Als verificatie in twee stappen vereist is, vraagt de verificatiereeks de gebruiker om een tweede verificatiemethode.

2. Nadat alle controles zijn verstreken, geeft de Azure AD STS een ondertekend token voor de toepassing uit en leidt de gebruiker terug naar de Application Proxy-service.

3. Application Proxy controleert of het token is uitgegeven aan de juiste toepassing. Het voert ook andere controles uit, zoals ervoor zorgen dat het token is ondertekend door Azure AD en dat het nog steeds in het geldige venster is.

4. Application Proxy stelt een versleutelde verificatiecookie in om aan te geven dat verificatie naar de toepassing heeft plaatsgevonden. De cookie bevat een verlooptijdstempel die is gebaseerd op het token van Azure AD en andere gegevens, zoals de gebruikersnaam waarop de verificatie is gebaseerd. De cookie is versleuteld met een privésleutel die alleen bekend is bij de Application Proxy-service.

5. Application Proxy leidt de gebruiker terug naar de oorspronkelijk gevraagde URL.

Als een deel van de preauthenticatiestappen mislukt, wordt het verzoek van de gebruiker geweigerd en wordt de gebruiker een bericht weergegeven dat de bron van het probleem aangeeft.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. De service plaatst een aanvraag in de connectorwachtrij

Connectors houden een uitgaande verbinding open voor de application proxy-service. Wanneer er een aanvraag binnenkomt, worden de aanvraag in de wachtrijen op een van de geopende verbindingen voor de connector die moet worden opgehaald.

Het verzoek bevat items uit de toepassing, zoals de aanvraagkoppen, gegevens van de versleutelde cookie, de gebruiker die het verzoek doet en de aanvraag-id. Hoewel gegevens van de versleutelde cookie worden verzonden met het verzoek, is de verificatiecookie zelf niet.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. De connector verwerkt de aanvraag vanuit de wachtrij. 

Op basis van de aanvraag voert Application Proxy een van de volgende acties uit:

* Als het verzoek een eenvoudige bewerking is (er zijn bijvoorbeeld geen gegevens in de behuizing zoals bij een RESTful *GET-aanvraag),* maakt de connector een verbinding met de doelinterne bron en wacht vervolgens op een antwoord.

* Als de aanvraag gegevens in de hoofdtekst heeft (bijvoorbeeld een RESTful *POST-bewerking),* maakt de connector een uitgaande verbinding met behulp van het clientcertificaat met de instantie Application Proxy. Het maakt deze verbinding om de gegevens op te vragen en een verbinding met de interne bron te openen. Nadat de aanvraag van de connector is ontvangen, begint de service Application Proxy inhoud van de gebruiker te accepteren en worden gegevens doorsturen naar de connector. De connector stuurt de gegevens op zijn beurt door naar de interne bron.

#### <a name="4-the-connector-waits-for-a-response"></a>4. De connector wacht op een reactie.

Nadat het verzoek en de overdracht van alle inhoud naar de back-end is voltooid, wacht de connector op een antwoord.

Nadat de connector een antwoord heeft ontvangen, maakt deze een uitgaande verbinding met de application proxy-service, om de headergegevens terug te sturen en de retourgegevens te streamen.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. De service streamt gegevens naar de gebruiker. 

Enige verwerking van de toepassing kan hier plaatsvinden. Als u Application Proxy hebt geconfigureerd om kop- of URL's in uw toepassing te vertalen, gebeurt die verwerking zo nodig tijdens deze stap.


## <a name="next-steps"></a>Volgende stappen

[Overwegingen voor netwerktopologie bij het gebruik van Azure AD-toepassingsproxy](application-proxy-network-topology.md)

[Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
