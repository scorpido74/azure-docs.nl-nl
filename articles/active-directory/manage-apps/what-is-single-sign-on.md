---
title: Wat is eenmalige aanmelding van Azure?
description: Meer informatie over de werking van eenmalige aanmelding (SSO) met Azure Active Directory. Wanneer u SSO gebruikt, hoeven gebruikers geen wachtwoorden te onthouden voor elke toepassing. Wanneer u SSO gebruikt, kunt u tevens het accountbeheer vereenvoudigen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: 6f3c6351a7bcd87ae25dfae53cb17f634bbef146
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121507"
---
# <a name="what-is-single-sign-on-sso"></a>Wat is eenmalige aanmelding?

Eenmalige aanmelding betekent dat een gebruiker zich niet hoeft aan te melden bij elke toepassing die deze gebruikt. De gebruiker meldt zich eenmaal aan en deze referenties worden ook gebruikt voor andere apps.

Als u een eindgebruiker bent, geeft u waarschijnlijk niet veel om SSO-gegevens. U wilt alleen de apps gebruiken waarmee u productief kunt zijn zonder steeds uw wachtwoord te hoeven invoeren. U kunt uw apps vinden op: https://myapps.microsoft.com.
 
Als u een beheerder of IT-professional bent, leest u verder voor meer informatie over de implementatie van SSO in Azure.

## <a name="single-sign-on-basics"></a>Basisbeginselen voor eenmalige aanmelding
Eenmalige aanmelding biedt een grote stap vooruit in de manier waarop gebruikers zich aanmelden en toepassingen gebruiken. Verificatiesystemen op basis van eenmalige aanmelding worden vaak 'moderne verificatie' genoemd. Bekijk deze video als u wilt weten waarmee u eenmalige aanmelding mogelijk maakt.
> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="understanding-where-an-app-is-hosted"></a>Meer informatie over waar een app wordt gehost
De wijze waarop u eenmalige aanmelding voor een app implementeert, heeft veel te maken met de locatie waar de app wordt gehost. Het hosten is van belang vanwege de manier waarop netwerkverkeer wordt doorgestuurd om toegang te krijgen tot de app. Als een app wordt gehost en geopend via uw lokale netwerk, een zogeheten on-premises app, is het niet nodig dat gebruikers toegang hebben tot internet om de app te gebruiken. Als de app ergens anders wordt gehost, een zogeheten in de cloud gehoste app, moeten gebruikers toegang hebben tot internet om de app te kunnen gebruiken.

> [!TIP]
> Cloud-apps worden ook wel SaaS-apps (Software as a Service) genoemd. 

> [!TIP]
> De termen 'cloud' en 'internet' worden vaak door elkaar gebruikt. De reden hiervoor heeft te maken met netwerkdiagrammen. Het is gebruikelijk om grote computernetwerken in een diagram aan te duiden met een cloudvorm, omdat het niet haalbaar is om elk onderdeel te tekenen. Internet is het meest bekende netwerk en daarom is het eenvoudig om de termen door elkaar te gebruiken. Elk computernetwerk kan echter een cloud worden genoemd.

## <a name="choosing-a-single-sign-on-method"></a>Een methode voor eenmalige aanmelding kiezen

- **Bij eenmalige aanmelding** melden gebruikers zich eenmaal aan met één account om toegang te krijgen tot apparaten die lid zijn van een domein, bedrijfsresources, SaaS-toepassingen (Software as a Service) en web-toepassingen. Nadat de gebruiker zich heeft aangemeld, kan deze toepassingen starten vanuit de Office 365-portal of het MyApps-toegangsvenster van Azure AD. Beheerders kunnen het beheer van gebruikersaccounts centraal regelen en automatisch gebruikerstoegang tot toepassingen toevoegen of verwijderen op basis van groepslidmaatschap.

- **Zonder eenmalige aanmelding** moeten gebruikers toepassingsspecifieke wachtwoorden onthouden en zich bij elke toepassing aanmelden. IT-medewerkers moeten gebruikersaccounts maken en bijwerken voor elke toepassing, zoals Office 365, Box en Salesforce. Gebruikers moeten hun wachtwoorden onthouden en zijn meer tijd kwijt omdat ze zich bij elke toepassing moeten aanmelden.

Er zijn verschillende manieren om een toepassing te configureren voor eenmalige aanmelding. Het kiezen van een methode voor eenmalige aanmelding is afhankelijk van de wijze waarop de toepassing is geconfigureerd voor verificatie.

- Cloud-toepassingen kunnen gebruikmaken van de volgende methoden voor eenmalige aanmelding: OpenID Connect, OAuth, SAML, op een wachtwoord gebaseerd, gekoppeld of uitgeschakeld. 
- On-premises toepassingen kunnen gebruikmaken van de volgende methoden voor eenmalige aanmelding: op een wachtwoord gebaseerd, geïntegreerde Windows-verificatie, op headers gebaseerd, gekoppeld of uitgeschakeld. De on-premises opties kunnen worden toegepast wanneer toepassingen zijn geconfigureerd voor Application Proxy.

U kunt aan de hand van dit stroomdiagram bepalen welke methode voor eenmalige aanmelding het geschiktst is voor uw situatie.

![Beslissingsstroomdiagram voor de methode voor eenmalige aanmelding](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

In de volgende tabel vindt u een overzicht van de methoden voor eenmalige aanmelding en koppelingen voor meer informatie.

| Methode voor eenmalige aanmelding | Toepassingstypen | Wanneer gebruikt u dit? |
| :------ | :------- | :----- |
| [OpenID Connect en OAuth](#openid-connect-and-oauth) | cloud en on-premises | Gebruik OpenID Connect en OAuth bij het ontwikkelen van een nieuwe toepassing. Dit protocol vereenvoudigt de configuratie van toepassingen, biedt gebruiksvriendelijke SDK's en stelt uw toepassing in staat om MS Graph te gebruiken.
| [SAML](#saml-sso) | cloud en on-premises | Kies indien mogelijk SAML voor bestaande toepassingen die niet gebruikmaken van OpenID Connect of OAuth. SAML kan worden gebruikt voor toepassingen waarbij de verificatie wordt uitgevoerd aan de hand van een van de SAML-protocollen.|
| [Op basis van een wachtwoord](#password-based-sso) | cloud en on-premises | Kies voor eenmalige aanmelding op basis van een wachtwoord wanneer de verificatie voor de toepassing wordt uitgevoerd met een gebruikersnaam en wachtwoord. Bij eenmalige aanmelding op basis van een wachtwoord is beveiligde toepassingswachtwoordopslag en -herhaling mogelijk door middel van een webbrowserextensie of mobiele app. Deze methode maakt gebruik van de bestaande aanmeldingsprocedure van de toepassing, maar stelt een beheerder in staat om de wachtwoorden te beheren. |
| [Gekoppeld](#linked-sign-on) | cloud en on-premises | Kies voor een gekoppelde aanmelding wanneer de toepassing is geconfigureerd voor eenmalige aanmelding in een andere id-providerservice. Bij deze optie wordt geen eenmalige aanmelding toegevoegd aan de toepassing. Voor de toepassing is echter mogelijk al een eenmalige aanmelding geïmplementeerd met behulp van een andere service, zoals Active Directory Federation Services.|
| [Uitgeschakeld](#disabled-sso) | cloud en on-premises | Kies voor een uitgeschakelde eenmalige aanmelding wanneer de app niet gereed is om te worden geconfigureerd voor eenmalige aanmelding. Deze modus is de standaardinstelling bij het maken van de app.|
| [Geïntegreerde Windows-verificatie](#integrated-windows-authentication-iwa-sso) | alleen on-premises | Kies eenmalige aanmelding met geïntegreerde Windows-verificatie voor toepassingen die gebruikmaken van [geïntegreerde Windows-verificatie](/aspnet/web-api/overview/security/integrated-windows-authentication) of claimbewuste toepassingen. Voor geïntegreerde Windows-verificatie maken de Application Proxy-connectors gebruik van beperkte Kerberos-delegering om gebruikers te verifiëren voor de toepassing. |
| [Op basis van headers](#header-based-sso) | alleen on-premises | Gebruik eenmalige aanmelding op basis van headers wanneer de toepassing headers gebruikt voor verificatie. Voor eenmalige aanmelding op basis van headers is PingAccess voor Azure AD vereist. Application Proxy gebruikt Azure AD om de gebruiker te verifiëren en geeft vervolgens het verkeer door via de connectorservice.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect en OAuth

Gebruik bij het ontwikkelen van nieuwe toepassingen moderne protocollen, zoals OpenID Connect en OAuth, om de beste eenmalige aanmelding voor uw app op meerdere platformen te realiseren. Met OAuth kunnen gebruikers of beheerders [toestemming geven](configure-user-consent.md) voor beveiligde resources, zoals [Microsoft Graph](/graph/overview). We bieden [SDK's](../develop/reference-v2-libraries.md) voor uw app die eenvoudig kunnen worden aangepast en daarnaast kan [Microsoft Graph](/graph/overview) worden gebruikt door uw app.

Zie voor meer informatie:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Handleiding voor ontwikkelaars van Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>Eenmalige aanmelding op basis van SAML

Met **eenmalige aanmelding op basis van SAML** voert Azure AD een verificatie voor de toepassing uit met behulp van het Azure AD-account van de gebruiker. Azure AD geeft de aanmeldingsgegevens door aan de toepassing via een verbindingsprotocol. Met eenmalige aanmelding op basis van SAML kunt u gebruikers toewijzen aan specifieke toepassingsrollen op basis van de regels die u in uw SAML-claims definieert.

Kies voor eenmalige aanmelding op basis van SAML wanneer de toepassing dit ondersteunt.

Eenmalige aanmelding op basis van SAML wordt ondersteund voor toepassingen die gebruikmaken van de volgende protocollen:

- SAML 2.0
- Webservices-federatie

Zie [Eenmalige aanmelding op basis van SAML configureren](configure-saml-single-sign-on.md) als u een SaaS-toepassing wilt configureren voor eenmalige aanmelding op basis van SAML. Daarnaast bestaat er voor veel SaaS-toepassingen (Software as a Service) een [toepassingsspecifieke zelfstudie](../saas-apps/tutorial-list.md) waarin de configuratie voor eenmalige aanmelding op basis van SAML stapsgewijs wordt beschreven.

Als u een toepassing voor WS-Federation wilt configureren, volgt u dezelfde richtlijnen om de toepassing te configureren voor eenmalige aanmelding op basis van SAML. In de stap waarin de toepassing wordt geconfigureerd voor gebruik van Azure AD, moet u de aanmeldings-URL van Azure AD voor het eindpunt van WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed` vervangen.

Raadpleeg [Eenmalige aanmelding op basis van SAML voor on-premises toepassingen met Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md) als u een on-premises toepassing wilt configureren voor eenmalige aanmelding op basis van SAML.

Zie [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md) voor meer informatie over het SAML-protocol.

## <a name="password-based-sso"></a>Eenmalige aanmelding op basis van een wachtwoord

Bij aanmelding op basis van een wachtwoord melden gebruikers zich aan bij de toepassing met een gebruikersnaam en wachtwoord wanneer ze deze voor de eerste keer gaan gebruiken. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord voor de toepassing.

Eenmalige aanmelding op basis van een wachtwoord maakt gebruik van de bestaande verificatieprocedure die door de toepassing wordt geboden. Wanneer u eenmalige aanmelding op basis van een wachtwoord inschakelt voor een toepassing, worden gebruikersnamen en wachtwoorden voor de toepassing door Azure AD verzameld en beveiligd. Gebruikersreferenties worden versleuteld opgeslagen in de map.

Kies voor eenmalige aanmelding op basis van wachtwoord wanneer:

- Een toepassing geen ondersteuning biedt voor het protocol van eenmalige aanmelding op basis van SAML.
- Verificatie voor een toepassing wordt uitgevoerd met een gebruikersnaam en wachtwoord in plaats van toegangstokens en headers.

>[!NOTE]
>U geen beleid voor voorwaardelijke toegang of meervoudige verificatie kunt toepassen voor eenmalige aanmelding op basis van een wachtwoord.

Eenmalige aanmelding op basis van een wachtwoord wordt ondersteund voor alle cloud-toepassingen die een HTML-aanmeldingspagina hebben. De gebruiker kan een van de volgende browsers gebruiken:

- Internet Explorer 11 in Windows 7 of hoger
   > [!NOTE]
   > Internet Explorer heeft beperkte ondersteuning en ontvangt geen nieuwe software-updates meer. Microsoft Edge is de aanbevolen browser.

- Microsoft Edge in Windows 10 Anniversary Edition of hoger
- Microsoft Edge voor iOS en Android
- Intune Managed Browser
- Chrome in Windows 7 of hoger en in macOS X of hoger
- Firefox 26.0 of hoger in Windows XP SP2 of hoger en in macOS X 10.6 of hoger

Zie [Eenmalige aanmelding op basis van een wachtwoord configureren](configure-password-single-sign-on-non-gallery-applications.md) als u een cloudtoepassing wilt configureren voor eenmalige aanmelding op basis van een wachtwoord.

Raadpleeg [Wachtwoordkluis voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-password-vaulting.md) als u een on-premises toepassing wilt configureren voor eenmalige aanmelding via Application Proxy

### <a name="how-authentication-works-for-password-based-sso"></a>Hoe verificatie wordt toegepast voor eenmalige aanmelding op basis van een wachtwoord

Als u een gebruiker wilt verifiëren voor een toepassing, haalt Azure AD de referenties van de gebruiker op uit de directory en voert deze in op de aanmeldingspagina van de toepassing.  Azure AD geeft de gebruikersreferenties veilig door via een webbrowserextensie of een mobiele app. Op deze manier kan een beheerder gebruikersreferenties beheren en hoeven gebruikers hun wachtwoord niet te onthouden.

> [!IMPORTANT]
> De referenties worden niet weergegeven voor de gebruiker tijdens de geautomatiseerde aanmeldingsprocedure. De referenties kunnen echter wel worden weergegeven met behulp van webfoutopsporingsprogramma's. Gebruikers en beheerders moeten hetzelfde beveiligingsbeleid volgen alsof referenties rechtstreeks door de gebruiker zijn ingevoerd.

### <a name="managing-credentials-for-password-based-sso"></a>Referenties voor eenmalige aanmelding op basis van een wachtwoord beheren

Wachtwoorden voor elke toepassing kunnen worden beheerd door de Azure AD-beheerder of door de gebruikers.

Wanneer de Azure AD-beheerder de referenties beheert, is het volgende van toepassing:  

- De gebruiker hoeft de gebruikersnaam en het wachtwoord niet opnieuw in te stellen of te onthouden. De gebruiker kan toegang krijgen tot de toepassing door erop te klikken in hun toegangsvenster of via een geleverde koppeling.
- De beheerder kan beheertaken uitvoeren voor de referenties. De beheerder kan bijvoorbeeld de toegang tot de toepassing bijwerken op basis van lidmaatschappen van gebruikersgroepen en de status van de werknemers.
- De beheerder kan beheerdersreferenties gebruiken om toegang te bieden tot toepassingen die worden gedeeld door veel gebruikers. De beheerder kan bijvoorbeeld toestaan dat iedereen die toegang heeft tot een toepassing toegang heeft tot een socialemediatoepassing of een toepassing voor het delen van documenten.

Wanneer de eindgebruiker de referenties beheert, is het volgende van toepassing:

- Gebruikers kunnen hun wachtwoorden beheren door deze naar behoefte bij te werken of te verwijderen.
- Beheerders kunnen nog steeds nieuwe referenties voor de toepassing instellen.

## <a name="linked-sign-on"></a>Gekoppelde aanmelding
Met gekoppelde aanmelding kan Azure AD eenmalige aanmelding bieden voor een toepassing die al is geconfigureerd voor eenmalige aanmelding in een andere service. De gekoppelde toepassing kan worden weergegeven voor eindgebruikers in de Office 365-portal of MyApps-portal van Azure AD. Een gebruiker kan bijvoorbeeld een toepassing starten die is geconfigureerd voor eenmalige aanmelding in Active Directory Federation Services 2.0 (AD FS) van de Office 365-portal. Aanvullende rapportage is ook beschikbaar voor gekoppelde toepassingen die worden gestart vanuit de Office 365-portal of de MyApps-portal van Azure AD. Zie [Gekoppelde aanmelding configureren](configure-linked-sign-on.md) als u voor een toepassing gekoppelde aanmelding wilt configureren.

### <a name="linked-sign-on-for-application-migration"></a>Gekoppelde aanmelding voor de migratie van toepassingen

Een gekoppelde aanmelding kan een consistente gebruikerstoepassing bieden tijdens het migreren van toepassingen gedurende een bepaalde periode. Als u toepassingen migreert naar Azure Active Directory, kunt u een gekoppelde aanmelding gebruiken om snel koppelingen te publiceren naar alle toepassingen die u wilt migreren.  Gebruikers kunnen alle koppelingen vinden in de [MyApps-portal](../user-help/active-directory-saas-access-panel-introduction.md) of het [programma voor het starten van toepassingen van Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Gebruikers weten niet dat ze toegang hebben tot een gekoppelde toepassing of een gemigreerde toepassing.  

Wanneer een gebruiker eenmaal is geverifieerd met een gekoppelde toepassing, moet er een accountrecord worden gemaakt voordat de eindgebruiker toegang via eenmalige aanmelding wordt geboden. Het inrichten van deze accountrecord kan automatisch plaatsvinden of kan handmatig door een beheerder worden uitgevoerd.

>[!NOTE]
>U kunt geen beleid voor voorwaardelijke toegang of meervoudige verificatie toepassen op een gekoppelde toepassing. Dit komt doordat een gekoppelde toepassing geen functionaliteit voor eenmalige aanmelding biedt via Azure AD. Wanneer u een gekoppelde toepassing configureert, voegt u simpelweg een koppeling toe die wordt weergegeven in het programma voor het starten van toepassingen of in de MyApps-portal. 

## <a name="disabled-sso"></a>Uitgeschakelde eenmalige aanmelding

In de modus Uitgeschakeld wordt eenmalige aanmelding niet gebruikt voor de app. Wanneer eenmalige aanmelding is uitgeschakeld, moeten gebruikers mogelijk twee keer worden geverifieerd. Eerst moeten gebruikers zich verifiëren bij Azure AD en vervolgens melden ze zich aan bij de toepassing.

Pas de modus voor uitgeschakelde eenmalige aanmelding toe in de volgende situaties:

- Als u deze toepassing nog niet kunt integreren met de eenmalige aanmelding van Azure AD, of
- Als u andere aspecten van de toepassing test, of
- Als een beveiligingslaag voor een on-premises toepassing waarvoor gebruikers zich niet hoeven te verifiëren. In de modus Uitgeschakeld moet de gebruiker zich verifiëren.

Houd er rekening mee dat als u de toepassing hebt geconfigureerd voor eenmalige aanmelding op basis van SAML die door de serviceprovider wordt gestart en u de modus voor eenmalige aanmelding wijzigt in Uitgeschakeld, gebruikers zich nog steeds buiten de MyApps-portal kunnen aanmelden bij de toepassing. Als u dit wilt bereiken, moet u [de mogelijkheid uitschakelen voor gebruikers om zich aan te melden](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Eenmalige aanmelding met geïntegreerde Windows-verificatie

[Application Proxy](application-proxy.md) biedt eenmalige aanmelding voor toepassingen die gebruikmaken van [geïntegreerde Windows-verificatie](/aspnet/web-api/overview/security/integrated-windows-authentication) of claimbewuste toepassingen. Als uw toepassing gebruikmaakt van geïntegreerde Windows-verificatie, voert Application Proxy een verificatie uit voor de toepassing met behulp van beperkte Kerberos-delegatie. Voor een claimbewuste toepassing die Azure Active Directory vertrouwt, kan eenmalige aanmelding worden gebruikt omdat de gebruiker al is geverifieerd door Azure AD.

Kies voor de modus voor eenmalige aanmelding met geïntegreerde Windows-verificatie als u eenmalige aanmelding wilt gebruiken voor een on-premises toepassing die wordt geverifieerd met geïntegreerde Windows-verificatie.

Raadpleeg [Beperkte Kerberos-delegering voor eenmalige aanmelding bij uw toepassingen met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) als u een on-premises toepassing wilt configureren voor geïntegreerde Windows-verificatie.

### <a name="how-single-sign-on-with-kcd-works"></a>Hoe eenmalige aanmelding met beperkte Kerberos-delegering werkt
In dit diagram wordt de stroom weergegeven voor een gebruiker die toegang wil krijgen tot een on-premises toepassing die gebruikmaakt van geïntegreerde Windows-verificatie.

![Stroomdiagram voor Microsoft Azure AD-verificatie](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. De gebruiker voert de URL in om toegang te krijgen tot de on-premises toepassing via Application Proxy.
1. Application Proxy leidt de aanvraag om naar Azure AD-verificatieservices om vooraf een verificatie uit te voeren. Op dit punt past Azure AD de betreffende verificatie en het relevante verificatiebeleid, zoals meervoudige verificatie, toe. Als de gebruiker is gevalideerd, wordt door Azure AD een token gemaakt en verzonden naar de gebruiker.
1. De gebruiker geeft het token door aan Application Proxy.
1. Application Proxy valideert het token en haalt de user principal name (UPN) op uit het token. Vervolgens worden de aanvraag, de UPN en de SPN-naam (Service Principal Name) verzonden naar de connector via een dubbel geverifieerd beveiligd kanaal.
1. De connector maakt gebruik van onderhandeling voor beperkte Kerberos-delegering met de on-premises AD en imiteert de gebruiker om een Kerberos-token voor de toepassing te krijgen.
1. Active Directory verzendt het Kerberos-token voor de toepassing naar de connector.
1. De connector verzendt de oorspronkelijke aanvraag naar de toepassingsserver met behulp van het Kerberos-token dat is ontvangen van AD.
1. De toepassing stuurt het antwoord naar de connector en dit wordt vervolgens teruggestuurd naar de Application Proxy-service en uiteindelijk naar de gebruiker.

## <a name="header-based-sso"></a>Eenmalige aanmelding op basis van headers

Eenmalige aanmelding op basis van headers kan worden gebruikt voor toepassingen die HTTP-headers gebruiken voor de verificatie. Deze aanmeldingsmethode maakt gebruik van een externe verificatieservice met de naam PingAccess. Een gebruiker hoeft zich alleen te verifiëren bij Azure AD.

Kies voor een eenmalige aanmelding op basis van headers wanneer Application Proxy en PingAccess zijn geconfigureerd voor de toepassing.

Raadpleeg [Verificatie op basis van headers voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-with-ping-access.md) als u een verificatie op basis van headers wilt configureren.

### <a name="what-is-pingaccess-for-azure-ad"></a>Wat is PingAccess voor Azure AD?

Wanneer gebruikers PingAccess voor Azure AD gebruiken, kunnen ze via eenmalige aanmelding toegang krijgen tot toepassingen waarin headers worden gebruikt voor verificatie. Application Proxy behandelt deze toepassingen hetzelfde als andere, waarbij Azure AD wordt gebruikt om de toegang te verifiëren en vervolgens het verkeer door te geven via de connectorservice. Nadat de verificatie is uitgevoerd, zet de PingAccess-service het Azure AD-toegangstoken om in een headerindeling die naar de toepassing wordt verzonden.

De gebruikers merken hier niets van wanneer ze zich aanmelden om uw bedrijfstoepassingen te gebruiken. Ze kunnen nog steeds vanaf elke locatie op elk apparaat werken. De Application Proxy-connectors sturen extern verkeer door naar alle toepassingen, waarbij ze automatisch de taakverdeling blijven uitvoeren.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hoe kan ik een licentie voor PingAccess krijgen?

Omdat deze werkwijze mogelijk wordt gemaakt door een samenwerking tussen Azure AD en PingAccess, hebt u licenties nodig voor beide services. Azure AD Premium-abonnementen omvatten echter een basislicentie voor PingAccess die kan worden gebruikt voor maximaal twintig toepassingen. Als u meer dan twintig toepassingen op basis van headers wilt publiceren, kunt u een aanvullende licentie verkrijgen via PingAccess.

Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
