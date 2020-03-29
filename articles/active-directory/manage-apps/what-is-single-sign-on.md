---
title: Eén aanmelding bij toepassingen - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het kiezen van één aanmeldingsmethode bij het configureren van toepassingen in Azure Active Directory (Azure AD). Gebruik één aanmelding zodat gebruikers geen wachtwoorden voor elke toepassing hoeven te onthouden en het beheer van accountbeheer te vereenvoudigen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f46bcf412403d8f911e484e12a9d1f421b1666f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366067"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Eenmalige aanmelding bij toepassingen in Azure Active Directory

Eenmalige aanmelding (SSO) voegt beveiliging en gemak toe wanneer gebruikers zich aanmelden bij toepassingen in Azure Active Directory (Azure AD). In dit artikel worden de enkele aanmeldingsmethoden beschreven en u de meest geschikte SSO-methode kiezen bij het configureren van uw toepassingen.

- **Met één aanmelding**melden gebruikers zich eenmaal aan met één account om toegang te krijgen tot met een domein verbonden apparaten, bedrijfsbronnen, software as a service (SaaS) applicaties en webapplicaties. Na het aanmelden kan de gebruiker toepassingen starten via de Office 365-portal of het access-panel van Azure AD MyApps. Beheerders kunnen gebruikersaccountbeheer centraliseren en automatisch gebruikerstoegang tot toepassingen toevoegen of verwijderen op basis van groepslidmaatschap.

- **Zonder eenmalige aanmelding**moeten gebruikers toepassingsspecifieke wachtwoorden onthouden en zich aanmelden bij elke toepassing. IT-personeel moet gebruikersaccounts maken en bijwerken voor elke toepassing, zoals Office 365, Box en Salesforce. Gebruikers moeten hun wachtwoorden onthouden en de tijd besteden om zich bij elke toepassing aan te melden.

## <a name="choosing-a-single-sign-on-method"></a>Een enkele aanmeldingsmethode kiezen

Er zijn verschillende manieren om een toepassing voor eenmalige aanmelding te configureren. Het kiezen van één aanmeldingsmethode is afhankelijk van de manier waarop de toepassing is geconfigureerd voor verificatie.

- Cloudtoepassingen kunnen OpenID Connect, OAuth, SAML, methoden op basis van wachtwoorden, gekoppelde of uitgeschakelde methoden gebruiken voor eenmalige aanmelding. 
- On-premises toepassingen kunnen gebruik maken van methoden op basis van een wachtwoord, geïntegreerde Windows-verificatie, op kopteksten gebaseerde, gekoppelde of uitgeschakelde methoden voor eenmalige aanmelding. De on-premises keuzes werken wanneer toepassingen zijn geconfigureerd voor Application Proxy.

Met dit stroomdiagram u bepalen welke enkele aanmeldingsmethode het beste is voor uw situatie.

![Beslissingsstroomdiagram voor eenmalige aanmeldingsmethode](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

In de volgende tabel worden de enkele aanmeldingsmethoden samengevat en wordt koppelingen naar meer details gemaakt.

| Methode voor eenmalig aanmelden | Toepassingstypen | Wanneer gebruikt u dit? |
| :------ | :------- | :----- |
| [OpenID Connect en OAuth](#openid-connect-and-oauth) | alleen in de cloud | Gebruik OpenID Connect en OAuth bij het ontwikkelen van een nieuwe toepassing. Dit protocol vereenvoudigt de toepassingsconfiguratie, heeft gebruiksvriendelijke SDK's en stelt uw toepassing in staat MS Graph te gebruiken.
| [SAML](#saml-sso) | cloud en on-premises | Kies SAML waar mogelijk voor bestaande toepassingen die geen Gebruik maken van OpenID Connect of OAuth. SAML werkt voor toepassingen die zich verifiëren met een van de SAML-protocollen.|
| [Op basis van een wachtwoord](#password-based-sso) | cloud en on-premises | Kies op basis van een wachtwoord wanneer de toepassing zich verifieert met gebruikersnaam en wachtwoord. Met een enkele aanmelding op basis van wachtwoorden met wachtwoord u veilige opslag en herhaling van het wachtwoord van toepassingen mogelijk maken met behulp van een webbrowserextensie of mobiele app. Deze methode maakt gebruik van het bestaande aanmeldingsproces dat door de toepassing wordt geleverd, maar stelt een beheerder in staat om de wachtwoorden te beheren. |
| [Gekoppelde](#linked-sign-on) | cloud en on-premises | Kies gekoppelde aanmelding wanneer de toepassing is geconfigureerd voor eenmalige aanmelding in een andere service voor identiteitsprovider. Met deze optie wordt geen enkele aanmelding aan de toepassing toegevoegd. De toepassing kan echter al eenmalige aanmelding hebben geïmplementeerd met behulp van een andere service, zoals Active Directory Federation Services.|
| [Handicap](#disabled-sso) | cloud en on-premises | Kies uitgeschakelde eenmalige aanmelding wanneer de app nog niet klaar is om te worden geconfigureerd voor eenmalige aanmelding. Deze modus is de standaardinstelling wanneer u de app maakt.|
| [Geïntegreerde Windows-verificatie (IWA)](#integrated-windows-authentication-iwa-sso) | alleen on-premises | Kies IWA single sign-on voor toepassingen die gebruik maken van [Integrated Windows Authentication (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)of claimsbewuste toepassingen. Voor IWA gebruiken de connectors voor toepassingsproxy Kerberos Constrained Delegation (KCD) om gebruikers te verifiëren voor de toepassing. |
| [Op kopteksten gebaseerd](#header-based-sso) | alleen on-premises | Gebruik eenmalige aanmelding op basis van kopteksten wanneer de toepassing kopteksten gebruikt voor verificatie. Voor eenmalige aanmelding op basis van kopteksten is PingAccess voor Azure AD vereist. Application Proxy gebruikt Azure AD om de gebruiker te verifiëren en vervolgens verkeer door de connectorservice te leiden.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect en OAuth

Gebruik bij het ontwikkelen van nieuwe toepassingen moderne protocollen zoals OpenID Connect en OAuth om de beste single sign-on ervaring voor uw app op meerdere apparaatplatforms te bereiken. OAuth stelt gebruikers of beheerders in staat om toestemming te [verlenen](configure-user-consent.md) voor beveiligde bronnen zoals [Microsoft Graph.](/graph/overview) We bieden eenvoudig te gebruiken [SDK's](../develop/reference-v2-libraries.md) voor uw app en bovendien is uw app klaar om [Microsoft Graph](/graph/overview)te gebruiken.

Zie voor meer informatie:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft identity platform developer's guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>Eenmalige aanmelding op basis van SAML

Met **SAML single sign-on**verifieert Azure AD de toepassing met behulp van het Azure AD-account van de gebruiker. Azure AD communiceert de aanmeldingsgegevens naar de toepassing via een verbindingsprotocol. Met SAML-gebaseerde enkele aanmelding u gebruikers toewijzen aan specifieke toepassingsrollen op basis van regels die u definieert in uw SAML-claims.

Kies SAML-gebaseerde eenmalige aanmelding wanneer de toepassing dit ondersteunt.

Saml-gebaseerde single sign-on wordt ondersteund voor toepassingen die gebruik maken van een van de volgende protocollen:

- SAML 2.0
- Webservices-federatie

Zie [SAML-gebaseerde aanmelding configureren](configure-single-sign-on-non-gallery-applications.md)als u een SaaS-toepassing voor SAML-gebaseerde eenmalige aanmelding wilt configureren. Ook hebben veel Software as a Service (SaaS) toepassingen een [toepassingsspecifieke zelfstudie](../saas-apps/tutorial-list.md) die u door de configuratie voor SAML-gebaseerde enkele aanmelding zet.

Als u een toepassing voor WS-Federation wilt configureren, volgt u dezelfde richtlijnen voor het configureren van de toepassing voor SAML-gebaseerde eenmalige aanmelding, zie [SAML-gebaseerde eenmalige aanmelding configureren.](configure-single-sign-on-non-gallery-applications.md) In de stap om de toepassing te configureren om Azure AD te gebruiken, moet u `https://login.microsoftonline.com/<tenant-ID>/wsfed`de URL van azure AD-aanmelding voor het eindpunt van de WS-Federation vervangen.

Zie [SAML single-sign-on voor on-premises toepassingen met Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md)als u een on-premises toepassing voor SAML-gebaseerde eenmalige aanmelding wilt configureren.

Zie [Saml-protocol met één aanmelding voor](../develop/single-sign-on-saml-protocol.md)meer informatie over het SAML-protocol .

## <a name="password-based-sso"></a>SSO op basis van wachtwoorden

Met op een wachtwoord gebaseerde aanmelding melden gebruikers zich bij de toepassing met een gebruikersnaam en wachtwoord wanneer ze er voor het eerst toegang toe hebben. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord aan de toepassing.

Met een enkele aanmelding op basis van wachtwoorden wordt gebruikgenomen van het bestaande verificatieproces dat door de toepassing wordt geleverd. Wanneer u een malige aanmelding voor een toepassing met wachtwoord inschakelt, verzamelt en slaat Azure AD gebruikersnamen en wachtwoorden voor de toepassing veilig op. Gebruikersreferenties worden in een versleutelde status in de map opgeslagen.

Kies eenenkele aanmelding op basis van een wachtwoord wanneer:

- Een toepassing biedt geen ondersteuning voor saml-protocol voor aanmelding.
- Een toepassing verifieert met een gebruikersnaam en wachtwoord in plaats van toegangstokens en kopteksten.

Eenmalige aanmelding op basis van wachtwoorden wordt ondersteund voor elke cloudtoepassing met een html-gebaseerde aanmeldingspagina. De gebruiker kan een van de volgende browsers gebruiken:

- Internet Explorer 11 op Windows 7 of hoger
   > [!NOTE]
   > Internet Explorer biedt beperkte ondersteuning en ontvangt geen nieuwe software-updates meer. Microsoft Edge is de aanbevolen browser.

- Microsoft Edge op Windows 10 Anniversary Edition of hoger
- Microsoft Edge voor iOS en Android
- Intune Managed Browser
- Chrome op Windows 7 of hoger en op MacOS X of hoger
- Firefox 26.0 of hoger op Windows XP SP2 of hoger, en op Mac OS X 10.6 of hoger

Zie [Eenmalige aanmelding](configure-password-single-sign-on-non-gallery-applications.md)voor wachtwoorden configureren als u een cloudtoepassing wilt configureren voor een enkele aanmelding op basis van een wachtwoord.

Zie [Wachtwoordkluisen voor eenmalige aanmelding met toepassingsproxy](application-proxy-configure-single-sign-on-password-vaulting.md) voor een on-premises toepassing configureren voor eenmalige aanmelding

### <a name="how-authentication-works-for-password-based-sso"></a>Hoe verificatie werkt voor sso op basis van wachtwoorden

Als u een gebruiker wilt verifiëren voor een toepassing, haalt Azure AD de referenties van de gebruiker uit de map en voert deze in op de aanmeldingspagina van de toepassing.  Azure AD geeft de gebruikersreferenties veilig door via een webbrowserextensie of mobiele app. Met dit proces kan een beheerder gebruikersreferenties beheren en hoeven gebruikers hun wachtwoord niet te onthouden.

> [!IMPORTANT]
> De referenties worden versluierd van de gebruiker tijdens het geautomatiseerde aanmeldingsproces. De referenties zijn echter vindbaar met behulp van webfoutfouttools. Gebruikers en beheerders moeten hetzelfde beveiligingsbeleid volgen alsof referenties rechtstreeks door de gebruiker zijn ingevoerd.

### <a name="managing-credentials-for-password-based-sso"></a>Referenties beheren voor sso op basis van wachtwoorden

Wachtwoorden voor elke toepassing kunnen worden beheerd door de Azure AD-beheerder of door de gebruikers.

Wanneer de Azure AD-beheerder de referenties beheert:  

- De gebruiker hoeft de gebruikersnaam en het wachtwoord niet opnieuw in te stellen of te onthouden. De gebruiker heeft toegang tot de applicatie door erop te klikken in het toegangspaneel of via een meegeleverde link.
- De beheerder kan beheertaken uitvoeren op de referenties. De beheerder kan bijvoorbeeld de toegang tot toepassingen bijwerken op basis van lidmaatschappen van gebruikersgroepen en de status van werknemers.
- De beheerder kan beheerdersreferenties gebruiken om toegang te bieden tot toepassingen die door veel gebruikers worden gedeeld. De beheerder kan bijvoorbeeld iedereen die toegang heeft tot een toepassing toegang geven tot een toepassing voor het delen van sociale media of het delen van documenten.

Wanneer de eindgebruiker de referenties beheert:

- Gebruikers kunnen hun wachtwoorden beheren door ze te updaten of te verwijderen als dat nodig is.
- Beheerders kunnen nog steeds nieuwe referenties instellen voor de toepassing.

## <a name="linked-sign-on"></a>Gekoppelde aanmelding
Met gekoppelde aanmelding kan Azure AD één aanmelding bieden bij een toepassing die al is geconfigureerd voor eenmalige aanmelding in een andere service. De gekoppelde toepassing kan worden weergegeven aan eindgebruikers in de Office 365-portal of azure AD MyApps-portal. Een gebruiker kan bijvoorbeeld een toepassing starten die is geconfigureerd voor eenmalige aanmelding in Active Directory Federation Services 2.0 (AD FS) vanuit de Office 365-portal. Aanvullende rapportage is ook beschikbaar voor gekoppelde toepassingen die worden gestart vanuit de Office 365-portal of de Azure AD MyApps-portal. Zie [Gekoppeldaanmelden](configure-linked-sign-on.md)configureren als u een toepassing voor gekoppelde aanmelding wilt configureren.

### <a name="linked-sign-on-for-application-migration"></a>Gekoppelde aanmelding voor toepassingsmigratie

Gekoppelde aanmelding kan een consistente gebruikerservaring bieden terwijl u toepassingen over een bepaalde periode migreert. Als u toepassingen migreert naar Azure Active Directory, u gekoppelde aanmelding gebruiken om snel koppelingen te publiceren naar alle toepassingen die u wilt migreren.  Gebruikers kunnen alle koppelingen vinden in de [MyApps-portal](../user-help/active-directory-saas-access-panel-introduction.md) of het [Office 365-toepassingslauncher.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) Gebruikers weten niet dat ze toegang hebben tot een gekoppelde toepassing of een gemigreerde toepassing.  

Zodra een gebruiker is geverifieerd met een gekoppelde toepassing, moet een accountrecord worden gemaakt voordat de eindgebruiker één aanmeldingstoegang krijgt. Het inrichten van deze accountrecord kan automatisch plaatsvinden of handmatig optreden door een beheerder.

## <a name="disabled-sso"></a>UitgeschakeldE SSO

Uitgeschakelde modus betekent dat eenmalige aanmelding niet wordt gebruikt voor de toepassing. Wanneer eenmalige aanmelding is uitgeschakeld, moeten gebruikers mogelijk twee keer verifiëren. Eerst verifiëren gebruikers bij Azure AD en melden ze zich vervolgens aan bij de toepassing.

Gebruik de modus Uitgeschakelde aanmelding:

- Als u niet klaar bent om deze toepassing te integreren met Azure AD-aanmelding, of
- Als u andere aspecten van de toepassing test, of
- Als beveiligingslaag voor een on-premises toepassing waarvoor gebruikers zich niet hoeven te verifiëren. Als uitgeschakeld, moet de gebruiker zich verifiëren.

Houd er rekening mee dat als u de toepassing voor de door SP geïnitieerde SAML-gebaseerde eenmalige aanmelding hebt geconfigureerd en u de SSO-modus wijzigt om uit te schakelen, dit gebruikers niet zal weerhouden om zich buiten de MyApps-portal te ondertekenen bij de toepassing. Om dit te bereiken, moet u [de mogelijkheid voor gebruikers om zich aan te melden uitschakelen](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Geïntegreerde Windows-verificatie (IWA) SSO

[Application Proxy](application-proxy.md) biedt single sign-on (SSO) aan toepassingen die [geïntegreerde Windows-verificatie (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)of claimsbewuste toepassingen gebruiken. Als uw toepassing IWA gebruikt, verifieert Application Proxy de toepassing met behulp van Kerberos Constrained Delegation (KCD). Voor een claimbewuste toepassing die azure Active Directory vertrouwt, werkt eenmalige aanmelding omdat de gebruiker al is geverifieerd met Azure AD.

Kies de modus Geïntegreerde Windows-verificatie om eenmalige aanmelding te bieden bij een on-premises app die zich verifieert met IWA.

Zie [Kerberos Constrained Delegation voor eenmalige aanmelding bij uw toepassingen met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)als u een on-premises app voor IWA wilt configureren.

### <a name="how-single-sign-on-with-kcd-works"></a>Hoe single sign-on met KCD werkt
In dit diagram wordt de stroom uitgelegd wanneer een gebruiker toegang heeft tot een on-premises toepassing die IWA gebruikt.

![Microsoft Azure AD-verificatiestroomdiagram](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. De gebruiker voert de URL in om toegang te krijgen tot de on-premises applicatie via Application Proxy.
1. Application Proxy leidt het verzoek door naar Azure AD-verificatieservices naar preauthenticeren. Op dit moment past Azure AD alle toepasselijke verificatie- en autorisatiebeleidsregels toe, zoals multifactorauthenticatie. Als de gebruiker is gevalideerd, maakt Azure AD een token en stuurt het naar de gebruiker.
1. De gebruiker geeft het token door aan Application Proxy.
1. Application Proxy valideert het token en haalt de User Principal Name (UPN) uit het token op. Vervolgens wordt het verzoek, de UPN en de Service Principal Name (SPN) naar de connector verzenden via een dubbel geverifieerd beveiligd kanaal.
1. De connector maakt gebruik van Kerberos Constrained Delegation (KCD) onderhandeling met de on-premises AD, als de gebruiker om een Kerberos token te krijgen om de toepassing.
1. Active Directory stuurt het Kerberos-token voor de toepassing naar de connector.
1. De connector stuurt het oorspronkelijke verzoek naar de toepassingsserver met behulp van het Kerberos-token dat het van AD heeft ontvangen.
1. De toepassing stuurt het antwoord naar de connector, die vervolgens wordt teruggestuurd naar de Application Proxy-service en ten slotte naar de gebruiker.

## <a name="header-based-sso"></a>SSO op basis van kopteksten

Op kopteksten gebaseerde aanmeldingswerken voor toepassingen die HTTP-headers gebruiken voor verificatie. Deze aanmeldingsmethode maakt gebruik van een verificatieservice van derden genaamd PingAccess. Een gebruiker hoeft alleen te verifiëren aan Azure AD.

Kies eenenkele aanmelding op basis van koptekst wanneer Application Proxy en PingAccess zijn geconfigureerd voor de toepassing.

Zie Verificatie op basis [van koptekst en voor één aanmelding met toepassingsproxy](application-proxy-configure-single-sign-on-with-ping-access.md)voor het configureren van verificatie op basis van koptekst.

### <a name="what-is-pingaccess-for-azure-ad"></a>Wat is PingAccess voor Azure AD?

Met PingAccess voor Azure AD kunnen gebruikers toegang krijgen tot toepassingen die kopteksten gebruiken voor verificatie. Application Proxy behandelt deze toepassingen als alle andere, met behulp van Azure AD om toegang te verifiëren en vervolgens het passeren van verkeer via de connectorservice. Nadat verificatie heeft plaatsgevonden, vertaalt de PingAccess-service het Azure AD-toegangstoken naar een kopindeling die naar de toepassing wordt verzonden.

Uw gebruikers zullen niets anders merken wanneer ze zich aanmelden om uw bedrijfstoepassingen te gebruiken. Ze kunnen nog steeds overal op elk apparaat werken. De connectors van Application Proxy leiden extern verkeer naar alle toepassingen en ze blijven automatisch het saldo laden.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hoe krijg ik een licentie voor PingAccess?

Aangezien dit scenario wordt aangeboden via een partnerschap tussen Azure AD en PingAccess, hebt u licenties nodig voor beide services. Azure AD Premium-abonnementen bevatten echter een basis-PingAccess-licentie die maximaal 20 toepassingen dekt. Als u meer dan 20 op kopteksten gebaseerde toepassingen wilt publiceren, u een extra licentie verkrijgen van PingAccess.

Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md)voor meer informatie.

## <a name="related-articles"></a>Verwante artikelen:
* [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)
* [SAML-gebaseerde aanmelding configureren](configure-single-sign-on-non-gallery-applications.md)
* [Eenmalige aanmelding op basis van wachtwoorden configureren op](configure-password-single-sign-on-non-gallery-applications.md)
* [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)
* [Inleiding tot het beheren van toegang tot toepassingen](what-is-access-management.md)
* Downloadkoppeling: [Implementatieplan voor één aanmelding](https://aka.ms/SSODeploymentPlan).
