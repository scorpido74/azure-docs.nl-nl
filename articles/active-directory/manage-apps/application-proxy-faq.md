---
title: Veelgestelde vragen van Azure AD Application Proxy | Microsoft Documenten
description: Lees antwoorden op veelgestelde vragen (FAQ) over het gebruik van Azure AD Application Proxy om interne on-premises toepassingen te publiceren voor externe gebruikers.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 31587777ba22dd8b4cebf81f0ff98979bb30fade
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410722"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Veelgestelde vragen over Active Directory (Azure AD) Application Proxy

Op deze pagina worden veelgestelde vragen beantwoord over Azure Active Directory (Azure AD) Application Proxy.

## <a name="enabling-azure-ad-application-proxy"></a>Azure AD-toepassingsproxy inschakelen

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Welke licentie is vereist om Azure AD Application Proxy te gebruiken?

Als u Azure AD-toepassingsproxy wilt gebruiken, moet u over een Azure AD Premium P1- of P2-licentie beschikken. Zie [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/) voor meer informatie over licenties

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Waarom wordt de knop 'Toepassingsproxy inschakelen grijs weergegeven?

Zorg ervoor dat u ten minste een Azure AD Premium P1- of P2-licentie en een Azure AD Application Proxy Connector hebt geïnstalleerd. Nadat u uw eerste connector hebt geïnstalleerd, wordt de Azure AD Application Proxy-service automatisch ingeschakeld.

## <a name="connector-configuration"></a>Connectorconfiguratie

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Kunnen Application Proxy Connector-services worden uitgevoerd in een andere gebruikerscontext dan de standaardinstelling?

Nee, dit scenario wordt niet ondersteund. De standaardinstellingen zijn:

- Microsoft AAD Application Proxy Connector - WAPCSvc - Netwerkservice
- Microsoft AAD Application Proxy Connector Updater - WAPCUpdaterSvc - NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Mijn back-end applicatie wordt gehost op meerdere webservers en vereist gebruikerssessie persistentie (stickiness). Hoe kan ik sessiepersistentie bereiken? 

Zie Hoge [beschikbaarheid en taakverdeling van uw toepassingsproxyconnectors en -toepassingen](application-proxy-high-availability-load-balancing.md)voor aanbevelingen.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Kan ik een forward proxy-apparaat plaatsen tussen de connectorserver(s) en de back-end applicatieserver?

Nee, dit scenario wordt niet ondersteund. Alleen de verbindings- en updateservices kunnen worden geconfigureerd om een forward proxy te gebruiken voor uitgaand verkeer naar Azure. Zie [Werken met bestaande on-premises proxyservers](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Wordt TLS-beëindiging (TLS/HTTPS-inspectie of versnelling) op het verkeer van de connectorservers naar Azure ondersteund?

De application proxy connector voert verificatie op basis van certificaten uit naar Azure. TLS Termination (TLS/HTTPS inspectie of acceleratie) breekt deze authenticatiemethode en wordt niet ondersteund. Verkeer van de connector naar Azure moet alle apparaten omzeilen die TLS-beëindiging uitvoeren.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Moet ik een speciaal account maken om de connector te registreren bij Azure AD Application Proxy?

Er is geen reden om. Elke globale beheerder of applicatiebeheerder-account werkt. De referenties die tijdens de installatie zijn ingevoerd, worden niet gebruikt na het registratieproces. In plaats daarvan wordt een certificaat uitgegeven aan de connector, die vanaf dat moment wordt gebruikt voor verificatie.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Hoe kan ik de prestaties van de Azure AD Application Proxy-connector controleren?

Er zijn Performance Monitor-tellers die samen met de connector zijn geïnstalleerd. Als u deze wilt weergeven:  

1. Selecteer **Start**, typ 'Perfmon' en druk op ENTER.
2. Selecteer **Prestatiemeter** en **+** klik op het groene pictogram.
3. Voeg de **Microsoft AAD Application Proxy Connector-tellers** toe die u wilt controleren.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Moet de Azure AD Application Proxy-connector zich op hetzelfde subnet bevinden als de bron?

De connector hoeft niet op hetzelfde subnet te staan. Het heeft echter naamomzetting (DNS, hosts-bestand) nodig voor de bron en de benodigde netwerkconnectiviteit (routering naar de bron, poorten die op de bron worden geopend, enz.). Zie [Overwegingen voor netwerktopologie bij het gebruik van Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Toepassingsconfiguratie

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Wat is de lengte van de standaard en "lange" back-end time-out? Kan de time-out worden verlengd?

De standaardlengte is 85 seconden. De "lange" instelling is 180 seconden. De time-outlimiet kan niet worden verlengd.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Hoe wijzig ik de bestemmingspagina die mijn toepassing laadt?

Vanaf de pagina Toepassingsregistraties u de URL van de startpagina wijzigen in de gewenste externe URL van de bestemmingspagina. De opgegeven pagina wordt geladen wanneer de toepassing wordt gestart vanuit Mijn apps of de Office 365-portal. Zie [Een aangepaste startpagina voor gepubliceerde apps instellen met Azure AD-toepassingsproxy voor configuratiestappen.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Kunnen alleen IIS-gebaseerde toepassingen worden gepubliceerd? Hoe zit het met webapplicaties die worden uitgevoerd op niet-Windows-webservers? Moet de connector worden geïnstalleerd op een server waarop IIS is geïnstalleerd?

Nee, er is geen IIS-vereiste voor aanvragen die worden gepubliceerd. U webtoepassingen publiceren die worden uitgevoerd op andere servers dan Windows Server. Het is echter mogelijk dat u geen pre-authenticatie gebruiken met een niet-Windows Server, afhankelijk van of de webserver Onderhandelen (Kerberos-verificatie) ondersteunt. IIS is niet vereist op de server waar de connector is geïnstalleerd.

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Wanneer moet ik de methode PrincipalsAllowedToDelegateToAccount gebruiken bij het instellen van Kerberos Constrained Delegation (KCD)?

De methode PrincipalsAllowedToDelegateToAccount wordt gebruikt wanneer connectorservers zich in een ander domein bevinden dan het webtoepassingsserviceaccount. Het vereist het gebruik van beperkte delegatie op basis van resources.
Als de verbindingsservers en het webtoepassingsserviceaccount zich in hetzelfde domein bevinden, u Active Directory- gebruikers en computers gebruiken om de delegatie-instellingen op elk van de connectormachineaccounts te configureren, zodat ze kunnen delegeren aan het doel SPN.

Als de connectorservers en het webtoepassingsserviceaccount zich in verschillende domeinen bevinden, wordt op resources gebaseerde delegatie gebruikt. De delegatiemachtigingen zijn geconfigureerd op de doelwebserver en het serviceaccount van de webtoepassing. Deze methode van Constrained Delegation is relatief nieuw. De methode is geïntroduceerd in Windows Server 2012, dat cross-domain delegatie ondersteunt door de eigenaar van de resource (webservice) te laten bepalen welke machine- en serviceaccounts eraan kunnen delegeren. Er is geen gebruikersinterface om te helpen bij deze configuratie, dus je moet PowerShell gebruiken.
Zie de whitepaper [Kerberos Constrained Delegation with Application Proxy](https://aka.ms/kcdpaper)voor meer informatie.

## <a name="pass-through-authentication"></a>Pass-through-verificatie

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Kan ik beleid voor voorwaardelijke toegang gebruiken voor toepassingen die zijn gepubliceerd met pass-through-verificatie?

Beleid voor voorwaardelijke toegang wordt alleen afgedwongen voor vooraf geverifieerde gebruikers in Azure AD. Pass-through-verificatie leidt niet tot Azure AD-verificatie, dus beleid voor voorwaardelijke toegang kan niet worden afgedwongen. Met pass-through-verificatie moet MFA-beleid worden geïmplementeerd op de on-premises server, indien mogelijk, of door pre-authenticatie in te schakelen met Azure AD Application Proxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Kan ik een webtoepassing publiceren met verificatievereiste voor clientcertificaaten?

Nee, dit scenario wordt niet ondersteund omdat Application Proxy TLS-verkeer beëindigt.  

## <a name="remote-desktop-gateway-publishing"></a>Extern bureaublad-gateway publiceren

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Hoe kan ik Extern bureaublad-gateway publiceren via Azure AD-toepassingsproxy?

Raadpleeg [Extern bureaublad publiceren met Azure AD-toepassingsproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Kan ik Kerberos Constrained Delegation (Single Sign-On - Windows Integrated Authentication) gebruiken in het publicatiescenario voor extern bureaublad-gateway?

Nee, dit scenario wordt niet ondersteund.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Mijn gebruikers gebruiken Internet Explorer 11 niet en het scenario voor vooraf verificatie werkt niet voor hen. Wordt dit verwacht?

Ja, het wordt verwacht. Het pre-authenticatiescenario vereist een ActiveX-besturingselement, dat niet wordt ondersteund in browsers van derden.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Wordt de HTML5 (Remote Desktop Web Client) ondersteund?

Nee, dit scenario wordt momenteel niet ondersteund. Volg ons [UserVoice-feedbackforum](https://aka.ms/aadapuservoice) voor updates over deze functie.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Nadat ik het pre-authenticatiescenario had geconfigureerd, realiseerde ik me dat de gebruiker twee keer moet verifiëren: eerst op het Azure AD-aanmeldingsformulier en vervolgens op het rdweb-aanmeldingsformulier. Wordt dit verwacht? Hoe kan ik dit reduceren tot één aanmelding?

Ja, het wordt verwacht. Als de computer van de gebruiker Azure AD is aangesloten, meldt de gebruiker zich automatisch aan bij Azure AD. De gebruiker hoeft zijn referenties alleen op het RDWeb-aanmeldingsformulier op te geven.

## <a name="sharepoint-publishing"></a>SharePoint-publicatie

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Hoe kan ik SharePoint publiceren via Azure AD Application Proxy?

Raadpleeg [Afstandsbediening voor SharePoint inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Kan ik de mobiele SharePoint-app (iOS/ Android) gebruiken om toegang te krijgen tot een gepubliceerde SharePoint-server?

De [mobiele SharePoint-app](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) biedt momenteel geen ondersteuning voor azure Active Directory-pre-verificatie.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Ad FS -publicatie (Active Directory Federation Services) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Kan ik Azure AD Application Proxy gebruiken als AD FS proxy (zoals Web Application Proxy)?

Nee. Azure AD Application Proxy is ontworpen om te werken met Azure AD en voldoet niet aan de vereisten om te fungeren als een AD FS-proxy.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Werkt WebSocket-ondersteuning voor andere toepassingen dan QlikSense?

Momenteel is websocket-protocolondersteuning nog steeds in openbare preview en werkt het mogelijk niet voor andere toepassingen. Sommige klanten hebben gemengd succes gehad met behulp van WebSocket-protocol met andere toepassingen. Als u dergelijke scenario's test, horen we graag uw resultaten. Stuur ons uw aadapfeedback@microsoft.comfeedback op .

Functies (Eventlogs, PowerShell en Extern bureaublad-services) in Windows Admin Center (WAC) of Remote Desktop Web Client (HTML5) werken momenteel niet via Azure AD Application Proxy.

## <a name="link-translation"></a>Koppelingsvertaling

### <a name="does-using-link-translation-affect-performance"></a>Heeft het gebruik van Link-vertaling invloed op de prestaties?

Ja. Linkvertaling beïnvloedt de prestaties. De Application Proxy-service scant de toepassing op hardcoded koppelingen en vervangt deze door hun respectievelijke, gepubliceerde externe URL's voordat deze aan de gebruiker worden gepresenteerd. 

Voor de beste prestaties raden we u aan identieke interne en externe URL's te gebruiken door [aangepaste domeinen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)te configureren. Als het gebruik van aangepaste domeinen niet mogelijk is, u de prestaties van de koppelingsvertaling verbeteren met de secure sign in-extensie mijn apps of microsoft edge-browser op mobiel. Zie [Hardcoded koppelingen omleiden voor apps die zijn gepubliceerd met Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Jokertekens

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Hoe gebruik ik wildcards om twee toepassingen met dezelfde aangepaste domeinnaam te publiceren, maar met verschillende protocollen, een voor HTTP en een voor HTTPS?

Dit scenario wordt niet direct ondersteund. Uw opties voor dit scenario zijn:

1. Publiceer zowel de HTTP- als de HTTPS-URL's als afzonderlijke toepassingen met een wildcard, maar geef elk van hen een ander aangepast domein. Deze configuratie werkt omdat ze verschillende externe URLS hebben.

2. Publiceer de HTTPS-URL via een wildcardtoepassing. Publiceer de HTTP-toepassingen afzonderlijk met behulp van deze PowerShell-cmdlets van application proxy:
   - [Application Proxy Application Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Beheer van toepassingsproxyconnector](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
