---
title: Wilt u archiveren wat er nieuw is in Azure Active Directory? | Microsoft Docs
description: Wat is nieuw opmerkingen bij de release in het overzicht van de sectie van deze inhoudsset bevat zes maanden van de activiteit van. Na zes maanden worden de items verwijderd uit het belangrijkste artikel en in dit archiefartikel plaatsen.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4e2e25189b35f7d17ef42536aa7cd86ac92ec82
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185854"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Wilt u archiveren wat er nieuw is in Azure Active Directory?

De belangrijkste [nieuwe functies in azure Active Directory? release opmerkingen](whats-new.md) artikel bevat updates voor de afgelopen zes maanden, terwijl dit artikel alle oudere informatie bevat.

Wat is er nieuw in Azure Active Directory? release opmerkingen bevatten informatie over:

- De meest recente versies
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functies
- Plannen voor wijzigingen

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plannen voor wijziging: update van Application proxy-service om alleen TLS 1,2 te ondersteunen

**Type:** Plan voor wijziging  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Om u te helpen met onze krach tigste versleuteling, gaan we de toegang tot de toepassings proxy service beperken tot alleen TLS 1,2-protocollen. Deze beperking wordt eerst doorgevoerd naar klanten die al gebruikmaken van TLS 1,2-protocollen, zodat u de impact niet kunt zien. De afronding van de protocollen TLS 1,0 en TLS 1,1 is voltooid op 31 augustus 2019. Klanten die nog TLS 1,0 en TLS 1,1 gebruiken, ontvangen een geavanceerde kennisgeving om deze wijziging voor te bereiden.

Als u de verbinding met de service toepassings proxy tijdens deze wijziging wilt behouden, kunt u het beste controleren of uw combi Naties van client-server en browser-server zijn bijgewerkt voor het gebruik van TLS 1,2. We raden u ook aan om ervoor te zorgen dat alle client systemen die door uw werk nemers worden gebruikt, toegang hebben tot apps die zijn gepubliceerd via de Application proxy-service.

Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)voor meer informatie.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plannen voor wijziging: ontwerp updates zijn beschikbaar voor de toepassings galerie

**Type:** Plan voor wijziging  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

Nieuwe wijzigingen in de gebruikers interface zijn afkomstig van het ontwerp van de **toevoegen vanuit het gebied Galerie** van de Blade **een toepassing toevoegen** . Met deze wijzigingen kunt u eenvoudig uw apps vinden die ondersteuning bieden voor automatische inrichting, OpenID Connect Connect, Security Assertion Markup Language (SAML) en een wacht woord voor eenmalige aanmelding (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plannen voor wijziging: het IP-adres van de MFA-server verwijderen uit het Office 365 IP-adres

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Het IP-adres van de MFA-server wordt verwijderd uit het [Office 365 IP-adres en de URL-webservice](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Als u momenteel afhankelijk bent van deze pagina's om uw firewall instellingen bij te werken, moet u ervoor zorgen dat u ook de lijst met IP-adressen die worden beschreven in de sectie **azure multi-factor Authentication-Server-firewall vereisten** van het artikel aan de slag [met Azure multi-factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Voor alleen app-tokens moet de client-app bestaan in de resource Tenant

**Type:** Vaste  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Op 26 juli 2019 hebben we gewijzigd hoe we alleen app-tokens bieden via de [client referenties verlenen](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Voorheen konden apps tokens krijgen om andere apps aan te roepen, ongeacht of de client-app zich in de Tenant bevindt. We hebben dit gedrag bijgewerkt, waardoor enkele Tenant bronnen, ook wel web-Api's genoemd, alleen kunnen worden aangeroepen door client-apps die voor komen in de resource-Tenant.

Als uw app zich niet in de resource Tenant bevindt, wordt er een fout bericht weer gegeven waarin staat dat `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` om dit probleem op te lossen, moet u de client app Service-Principal in de Tenant maken met behulp van het [beheerders toestemmings eindpunt](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) of [via Power shell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), zodat uw Tenant de app toestemming heeft gegeven om binnen de Tenant te werken.

Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)voor meer informatie.

> [!NOTE]
> Bestaande toestemming tussen de client en de API blijft niet vereist. Apps moeten nog steeds hun eigen autorisatie controles uitvoeren.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nieuwe aanmelding zonder wacht woord voor Azure AD met behulp van FIDO2-beveiligings sleutels

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Azure AD-klanten kunnen nu beleid instellen voor het beheren van FIDO2-beveiligings sleutels voor de gebruikers en groepen van hun organisatie. Eind gebruikers kunnen hun beveiligings sleutels ook zelf registreren, de sleutels gebruiken om zich aan te melden bij hun micro soft-accounts op websites, terwijl ze op FIDO apparaten werken, en aanmelden bij hun Azure AD-Windows 10-apparaten.

Zie voor meer informatie [aanmelden zonder wacht woord inschakelen voor Azure AD (preview)](/azure/active-directory/authentication/concept-authentication-passwordless) voor informatie die betrekking heeft op de beheerder en [Stel beveiligings informatie in voor het gebruik van een beveiligings sleutel (preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) voor informatie die aan de eind gebruiker is gerelateerd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-juli 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In juli 2019 zijn deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [helder patroon Omnichannel contact centrum](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [slimme Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access voor Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hele hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [schuine](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federatieve Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nieuwe Azure AD Domain Services servicetag voor de netwerk beveiligings groep

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services

Als u veel lijsten met IP-adressen en-bereiken niet meer wilt beheren, kunt u de nieuwe **AzureActiveDirectoryDomainServices** -netwerk servicetag gebruiken in uw Azure-netwerk beveiligings groep om inkomend verkeer naar uw Azure AD Domain Services subnet van het virtuele netwerk te beveiligen.

Zie [netwerk beveiligings groepen voor Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)voor meer informatie over deze nieuwe servicetag.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services van cruciaal belang maken door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD-domein service Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)voor meer informatie.

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nieuwe verificatie methoden Usage & Insights (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Self-service voor wacht woord opnieuw instellen  
**Product mogelijkheden:** & Rapportage controleren

Met de nieuwe verificatie methoden & Insights-rapporten kunt u beter begrijpen hoe functies zoals Azure Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden worden geregistreerd en gebruikt in uw organisatie, inclusief het aantal geregistreerde gebruikers voor elke functie, hoe vaak de selfservice voor wachtwoord herstel wordt gebruikt om wacht woorden opnieuw in te stellen, en op basis van welke methode het opnieuw instellen plaatsvindt.

Zie het gebruik van de [verificatie methoden & Insights (preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)voor meer informatie.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nieuwe beveiligings rapporten zijn beschikbaar voor alle Azure AD-beheerders (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Alle Azure AD-beheerders kunnen nu het vaandel selecteren boven aan bestaande beveiligings rapporten, zoals de gebruikers die zijn **gemarkeerd voor risico** rapport, om de nieuwe beveiligings ervaring te gebruiken, zoals wordt weer gegeven in de rapporten **Risk ante gebruikers** en **Risk ante aanmeldingen** . Na verloop van tijd worden alle beveiligings rapporten van oudere versies naar de nieuwe versies verplaatst, met de nieuwe rapporten die u de volgende aanvullende mogelijkheden bieden:

- Geavanceerd filteren en sorteren

- Bulk acties, zoals het negeren van gebruikers Risico's

- Bevestiging van aangetast of veilige entiteiten

- Risico status, waaronder: risico, genegeerd, hersteld en bevestigde schadelijke

Zie rapport met [Risk ante gebruikers](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) en [rapporten met Risk ante aanmeldingen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)voor meer informatie.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services van cruciaal belang maken door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD-domein service Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)voor meer informatie.

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nieuwe B2B direct Federation met behulp van SAML/WS-voeder (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C

Directe Federatie maakt het gemakkelijker voor u om te werken met partners waarvan de IT-beheerde identiteits oplossing niet Azure AD is, door te werken met identiteits systemen die ondersteuning bieden voor de SAML-of WS-voeder-standaarden. Nadat u een directe Federatie relatie met een partner hebt ingesteld, kan elke nieuwe gast gebruiker die u uit dat domein uitnodigt met behulp van hun bestaande organisatie account samen werken, waardoor de gebruikers ervaring voor uw gasten naadloos wordt.

Zie voor meer informatie [directe Federatie met AD FS en providers van derden voor gast gebruikers (preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federatieve Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nieuwe controle op dubbele groeps namen in de Azure AD-Portal

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

Wanneer u nu een groeps naam vanuit de Azure AD-Portal maakt of bijwerkt, wordt er een controle uitgevoerd om te zien of u een bestaande groeps naam in uw resource dupliceert. Als we bepalen dat de naam al wordt gebruikt door een andere groep, wordt u gevraagd om uw naam te wijzigen.

Zie [groepen beheren in de Azure AD-Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)voor meer informatie.

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD biedt nu ondersteuning voor statische query parameters in antwoord-Uri's (redirect)

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Azure AD-apps kunnen nu Uri's van antwoorden (omleiding) met statische query parameters registreren en gebruiken (bijvoorbeeld `https://contoso.com/oauth2?idp=microsoft`) voor OAuth 2,0-aanvragen. De statische query parameter is onderhevig aan de teken reeks die overeenkomt met antwoord-Uri's, net als elk ander deel van de antwoord-URI. Als er geen geregistreerde teken reeks is die overeenkomt met de door de URL gedecodeerde omleidings-URI, wordt de aanvraag geweigerd. Als de antwoord-URI wordt gevonden, wordt de gehele teken reeks gebruikt om de gebruiker te omleiden, inclusief de para meter static query.

Dynamische antwoord-Uri's zijn nog niet toegestaan omdat ze een beveiligings risico vertegenwoordigen en niet kunnen worden gebruikt om status informatie over een verificatie aanvraag te houden. Gebruik voor dit doel de para meter `state`.

Momenteel zijn de app-registratie schermen van de Azure Portal nog steeds blok keren query parameters. U kunt het app-manifest echter hand matig bewerken om query parameters toe te voegen en te testen in uw app. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)voor meer informatie.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Activiteiten Logboeken (MS Graph-Api's) voor Azure AD zijn nu beschikbaar via Power shell-cmdlets

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Met trots kondigen we aan dat Azure AD-activiteiten Logboeken (rapporten over controles en aanmeldingen) nu beschikbaar zijn via de Azure AD Power shell-module. U kunt voorheen uw eigen scripts maken met behulp van MS Graph API-eind punten en nu hebben we die mogelijkheid tot Power shell-cmdlets uitgebreid.

Zie [Azure AD Power shell-cmdlets voor rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)voor meer informatie over het gebruik van deze cmdlets.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Bijgewerkte filter besturings elementen voor controle-en aanmeld Logboeken in azure AD

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

De controle-en aanmeldings logboek rapporten zijn bijgewerkt, zodat u nu verschillende filters kunt Toep assen zonder ze als kolommen toe te voegen aan de rapport schermen. Daarnaast kunt u bepalen hoeveel filters u wilt weer geven op het scherm. Deze updates werken allemaal samen om uw rapporten gemakkelijker te kunnen lezen en zo meer te bereiken aan uw behoeften.

Zie [controle logboeken filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) en [aanmeldings activiteiten filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)voor meer informatie over deze updates.

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nieuwe riskDetections-API voor Microsoft Graph (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

We zijn blij met het aankondigen van de nieuwe riskDetections-API voor Microsoft Graph nu in open bare preview. U kunt deze nieuwe API gebruiken om een lijst weer te geven met de identiteits beveiliging van de gebruiker en de detectie van de aanmeldings Risico's van uw organisatie. U kunt deze API ook gebruiken om efficiënter een query uit te voeren op uw risico detecties, inclusief details over het detectie type, de status, het niveau en meer.

Zie de [referentie documentatie voor API voor risico detectie](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juni 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In juni 2019 hebben we deze 22 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [helper helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager voor Oracle Retail merchandising, Oracle Access Manager voor Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS voor People Soft, Oracle IDCS for JD Edwards

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>De real-time voortgang van de Azure AD-inrichtings service weer geven

**Type:** Gewijzigde functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

We hebben de Azure AD-inrichtings ervaring bijgewerkt zodat er een nieuwe voortgangs balk wordt weer gegeven waarin u kunt zien hoe ver u het proces voor het inrichten van gebruikers hebt. Deze bijgewerkte ervaring bevat ook informatie over het aantal gebruikers dat is ingericht tijdens de huidige cyclus, en hoeveel gebruikers tot nu toe zijn ingericht.

Zie [de status van gebruikers inrichten controleren](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)voor meer informatie.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>De huis stijl van het bedrijf wordt nu weer gegeven bij afmelden en fout schermen

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Azure AD is bijgewerkt, zodat de huis stijl van uw bedrijf nu wordt weer gegeven op de schermen voor afmelden en fout en op de aanmeldings pagina. U hoeft niets te doen om deze functie in te scha kelen. Azure AD maakt gewoon gebruik van de activa die u al hebt ingesteld in het **huis stijlgebied** van de Azure Portal.

Zie [huisstijl toevoegen aan de Azure Active Directory pagina's van uw organisatie](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)voor meer informatie over het instellen van de huis stijl van uw bedrijf.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>De Azure Multi-Factor Authentication-Server (MFA) is niet meer beschikbaar voor nieuwe implementaties

**Type:** Keur  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Met ingang van 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication in hun organisatie willen vereisen, moeten nu Azure Multi-Factor Authentication op basis van de Cloud gebruiken. Klanten die de MFA-server vóór 1 juli hebben geactiveerd, zien geen wijziging. U kunt nog steeds de nieuwste versie downloaden, toekomstige updates ophalen en activerings referenties genereren.

Zie [aan de slag met de Azure-multi-factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)voor meer informatie. Zie [een Cloud implementatie op basis van multi-factor Authentication Azure plannen](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)voor meer informatie over Azure multi-factor Authentication op basis van de Cloud.

---

## <a name="may-2019"></a>Mei 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Service wijziging: toekomstige ondersteuning voor alleen TLS 1,2-protocollen op de Application proxy-service

**Type:** Plan voor wijziging  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Om de beste versleuteling voor onze klanten te bieden, beperken we de toegang tot alleen TLS 1,2-protocollen op de Application proxy-service. Deze wijziging wordt geleidelijk doorgevoerd naar klanten die al alleen gebruikmaken van TLS 1,2-protocollen, zodat u geen wijzigingen ziet.

De afschaffing van TLS 1,0 en TLS 1,1 gebeurt op 31 augustus 2019, maar we bieden nog meer geavanceerde kennisgevingen, dus u hebt de tijd om deze wijziging voor te bereiden. Om deze wijziging voor te bereiden, moet u ervoor zorgen dat de combi Naties van client-server en browser server, inclusief alle clients die uw gebruikers gebruiken om toegang te krijgen tot apps die zijn gepubliceerd via toepassings proxy, worden bijgewerkt met het TLS 1,2-protocol om de verbinding met de toepassing te onderhouden Proxy service. Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)voor meer informatie.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Gebruik het rapport gebruik en inzichten om uw app-gerelateerde aanmeldings gegevens weer te geven

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu het rapport gebruik en inzichten gebruiken, dat zich bevindt in het gebied **bedrijfs toepassingen** van de Azure Portal, om een toepassings gerichte weer gave van uw aanmeldings gegevens te verkrijgen, met inbegrip van informatie over:

- Meestgebruikte apps voor uw organisatie

- Apps met de meeste mislukte aanmeldingen

- Meest voorkomende aanmeld fouten voor elke app

Zie voor meer informatie over deze functie [het rapport gebruik en inzichten in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatiseer uw gebruikers inrichting voor Cloud-apps met behulp van Azure AD

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** & Rapportage controleren

Volg deze nieuwe zelf studies om de Azure AD-inrichtings service te gebruiken voor het automatiseren van het maken, verwijderen en bijwerken van gebruikers accounts voor de volgende Cloud-apps:

- [Voldoen aan](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

U kunt deze nieuwe Dropbox- [zelf studie](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)ook volgen, die informatie bevat over het inrichten van groeps objecten.

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Beveiligde score voor identiteit is nu beschikbaar in azure AD (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

U kunt nu uw identiteits beveiligings postuur bewaken en verbeteren met behulp van de functie voor beveiligde scores voor identiteiten in azure AD. De functie voor het beveiligen van identiteiten maakt gebruik van één dash board waarmee u het volgende kunt doen:

- Objectief meet uw identiteits beveiligings postuur op basis van een score tussen 1 en 223.

- Uw verbeteringen voor identiteits beveiliging plannen

- Bekijk het succes van uw beveiligings verbeteringen

Zie [Wat is de identiteit beveiligde Score in azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)voor meer informatie over de functie voor identiteits beveiliging.

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nieuwe App-registraties-ervaring is nu beschikbaar (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Ontwikkelaars ervaring

De nieuwe [app-registraties](https://aka.ms/appregistrations) -ervaring is nu in algemene Beschik baarheid. Deze nieuwe ervaring omvat alle belang rijke functies van de Azure Portal en de portal voor toepassings registratie en verbetert deze.

- **Betere app-beheer.** In plaats van uw apps te zien op verschillende portals, kunt u nu al uw apps op één locatie bekijken.

- **Vereenvoudigde registratie van de app.** Van de verbeterde navigatie-ervaring tot de uitgebreide mogelijkheden voor het selecteren van machtigingen is het nu eenvoudiger om uw apps te registreren en te beheren.

- **Meer gedetailleerde informatie.** U kunt meer informatie vinden over uw app, inclusief Quick start-gidsen en meer.

Zie voor meer informatie [micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) en de [app-registraties-ervaring is nu algemeen beschikbaar.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) aankondiging van blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nieuwe mogelijkheden die beschikbaar zijn in de Risk ante gebruikers-API voor identiteits beveiliging

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Het is blij dat u de Risk ante gebruikers API kunt gebruiken om de risico geschiedenis van gebruikers op te halen, Risk ante gebruikers te sluiten en gebruikers te bevestigen dat ze zijn aangetast. Met deze wijziging kunt u de risico status van uw gebruikers efficiënter bijwerken en inzicht krijgen in hun risico geschiedenis.

Zie de [naslag documentatie voor Risk ante gebruikers-API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-mei 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In mei 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [real links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [deplayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [marketo Sales](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [Outsystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum werkplek](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webmethodes API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [levens](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)werk, [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Verbeterde functies voor het maken en beheren van groepen in de Azure AD-Portal

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

We hebben verbeteringen aangebracht in de groeps-gerelateerde ervaringen in de Azure AD-Portal. Met deze verbeteringen kunnen beheerders de lijsten met groepen, leden lijsten en aanvullende opties voor het maken beter beheren.

Verbeteringen zijn onder andere:

- Basis filtering op lidmaatschaps type en groeps type.

- Toevoeging van nieuwe kolommen, zoals bron-en e-mail adres.

- De mogelijkheid om meerdere groepen, leden en eigen aars lijsten te selecteren voor eenvoudige verwijdering.

- Mogelijkheid om een e-mail adres te kiezen en eigen aren toe te voegen tijdens het maken van de groep.

Zie [een basis groep maken en leden toevoegen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)voor meer informatie.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (algemene Beschik baarheid)

**Type:** Gewijzigde functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)voor meer informatie.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Er zijn nu Microsoft Graph API-eind punten beschikbaar voor Azure AD-activiteiten Logboeken (algemene Beschik baarheid)

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

We zijn blij met het aankondigen van de algemene Beschik baarheid van Microsoft Graph API-eind punten ondersteuning voor Azure AD-activiteiten Logboeken. Met deze versie kunt u nu versie 1,0 van zowel de Azure AD-audit logboeken en de Api's voor aanmeld Logboeken gebruiken.

Zie overzicht van de [API voor Azure AD-controle logboeken](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)voor meer informatie.

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Beheerders kunnen nu voorwaardelijke toegang gebruiken voor het gecombineerde registratie proces (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &  

Beheerders kunnen nu beleid voor voorwaardelijke toegang maken voor gebruik door de gecombineerde registratie pagina. Dit geldt ook voor het Toep assen van beleid om registratie in te stellen als:

- Gebruikers bevinden zich in een vertrouwd netwerk.

- Gebruikers zijn een laag aanmeldings risico.

- Gebruikers bevinden zich op een beheerd apparaat.

- Gebruikers komen overeen met de gebruiks voorwaarden van de organisatie.

Zie voor meer informatie over voorwaardelijke toegang en het opnieuw instellen van wacht woorden de [voorwaardelijke toegang voor het blog bericht over gecombineerde MFA-en wachtwoord herstel voor Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Zie [beleid voor voorwaardelijke toegang voor gecombineerde registratie](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)voor meer informatie over beleids regels voor voorwaardelijke toegang voor het gecombineerde registratie proces. Zie [Azure Active Directory functie gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie over de functie gebruiks voorwaarden van Azure AD.

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nieuwe Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van Azure AD Identity Protection

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Identity Protection  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van de bijgewerkte functie Azure AD Identity Protection. Deze nieuwe functionaliteit helpt bij het aanduiden van ongebruikelijke gebruikers activiteit voor een specifieke gebruiker of activiteit die consistent is met bekende aanvals patronen gebaseerd op de interne en externe informatie bronnen van micro soft.

Voor meer informatie over de vernieuwde versie van Azure AD Identity Protection raadpleegt u de [vier belangrijkste uitbrei dingen van de Azure AD Identity Protection nu in de open bare preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) -blog staan en [wat Azure Active Directory Identity Protection (vernieuwd)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) Knowledge. Voor meer informatie over de detectie van Azure AD Threat Intelligence raadpleegt u het artikel [Azure Active Directory Identity Protection risico detectie](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Het beheer van rechten van Azure AD is nu beschikbaar (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Identity governance  
**Product mogelijkheden:** Identity governance

Het beheer van rechten van Azure AD, nu in open bare preview, helpt klanten bij het delegeren van beheer van toegangs pakketten, waarmee wordt gedefinieerd hoe werk nemers en zakelijke partners toegang kunnen vragen, wie moet goed keuren en hoelang ze toegang hebben. Toegangs pakketten kunnen lidmaatschap beheren in azure AD en Office 365-groepen, roltoewijzingen in zakelijke toepassingen, en roltoewijzingen voor share point online-sites. Meer informatie over rechten beheer vindt u in het [overzicht van het beheer van rechten van Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Zie [Wat is Azure AD Identity governance?](../governance/identity-governance-overview.md)voor meer informatie over de breedte van Azure AD Identity governance functies, waaronder privileged Identity Management, toegangs beoordelingen en gebruiks voorwaarden.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)voor meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar in Azure Monitor (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Om uw feedback over visualisaties met de activiteiten logboeken van Azure AD te helpen aanpakken, introduceren we een nieuwe inzichten-functie in Log Analytics. Deze functie helpt u bij het verkrijgen van inzichten over uw Azure AD-resources door gebruik te maken van onze interactieve sjablonen, werkmappen genoemd. Deze vooraf gemaakte werkmappen kunnen Details bieden voor apps of gebruikers, en omvatten:

- **Aanmeldingen.** Biedt Details voor apps en gebruikers, waaronder aanmeldings locatie, het besturings systeem in gebruik of de browser-client en-versie, en het aantal geslaagde of mislukte aanmeldingen.

- **Verouderde verificatie en voorwaardelijke toegang.** Biedt Details voor apps en gebruikers met behulp van verouderde verificatie, met inbegrip van Multi-Factor Authentication gebruik dat wordt geactiveerd door het beleid voor voorwaardelijke toegang, apps die gebruikmaken van beleid voor voorwaardelijke toegang, enzovoort.

- **Analyse van mislukte aanmeldingen.** Helpt u te bepalen of uw aanmeldings fouten optreden als gevolg van een gebruikers actie, beleids problemen of uw infra structuur.

- **Aangepaste rapporten.** U kunt nieuwe werkmappen maken of bewerken om de inzichten-functie voor uw organisatie aan te passen.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-april 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In april 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Bank](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [afknot Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (op rollen gebaseerde SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nieuwe toegangs beoordelingen frequentie optie en selectie van meerdere rollen

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Identity governance

Met nieuwe updates in azure AD-toegangs beoordelingen kunt u het volgende doen:

- Wijzig de frequentie van uw toegangs beoordelingen in een **halfjaarlijkse**, naast de eerder bestaande opties wekelijks, maandelijks, elk kwar taal en jaarlijks.

- Selecteer meerdere Azure AD-en Azure-resource rollen bij het maken van één toegangs beoordeling. In dit geval worden alle rollen met dezelfde instellingen ingesteld en worden alle revisoren tegelijk op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)voor meer informatie over het maken van een toegangs beoordeling.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail waarschuwings systeem (en) worden overgezet, zodat er nieuwe e-mail gegevens worden verzonden voor sommige klanten

**Type:** Gewijzigde functie  
**Service categorie:** AD Sync  
**Product mogelijkheden:** Onafhankelijk

Azure AD Connect is bezig met het overstappen van onze e-mail waarschuwings systeem (s), waardoor sommige klanten een nieuwe e-mail afzender kunnen weer geven. U kunt dit oplossen door `azure-noreply@microsoft.com` toe te voegen aan de acceptatie lijst van uw organisatie of u kunt belang rijke waarschuwingen van uw Office 365, Azure of uw synchronisatie Services niet blijven ontvangen.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Wijzigingen in UPN-achtervoegsels zijn nu geslaagd tussen federatieve domeinen in Azure AD Connect

**Type:** Vaste  
**Service categorie:** AD Sync  
**Product mogelijkheden:** Onafhankelijk

U kunt nu het UPN-achtervoegsel van een gebruiker van een federatief domein wijzigen in een ander federatief domein in Azure AD Connect. Met deze oplossing wordt het FederatedDomainChangeError-fout bericht niet meer ervaren tijdens de synchronisatie cyclus of krijgt u een e-mail bericht met de melding ' kan dit object niet bijwerken in Azure Active Directory, omdat het kenmerk [ FederatedUser. UserPrincipalName] is niet geldig. Werk de waarde in uw lokale adreslijst Services bij.

Zie [probleemoplossings fouten tijdens de synchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)voor meer informatie.

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Verbeterde beveiliging met behulp van het beleid voor voorwaardelijke toegang op basis van app-beveiliging in azure AD (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Voorwaardelijke toegang op basis van app-beveiliging is nu beschikbaar via het beveiligings beleid voor het **vereisen van apps** . Dit nieuwe beleid helpt de beveiliging van uw organisatie te verbeteren door te voor komen dat:

- Gebruikers die toegang krijgen tot apps zonder een Microsoft Intune-licentie.

- Gebruikers kunnen geen Microsoft Intune beleid voor app-beveiliging ophalen.

- Gebruikers die toegang krijgen tot apps zonder een geconfigureerd Microsoft Intune beveiligings beleid voor apps.

Zie [app-beveiligings beleid vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)voor meer informatie.

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nieuwe ondersteuning voor eenmalige aanmelding voor Azure AD en voorwaardelijke toegang in micro soft Edge (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

We hebben onze Azure AD-ondersteuning voor micro soft Edge uitgebreid, met inbegrip van nieuwe ondersteuning voor eenmalige aanmelding voor Azure AD en voorwaardelijke toegang. Als u Microsoft Intune Managed Browser eerder hebt gebruikt, kunt u nu gebruikmaken van micro soft Edge.

Zie voor meer informatie over het instellen en beheren van uw apparaten en apps met behulp van voorwaardelijke toegang [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) en [goedgekeurde client-apps vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Voor meer informatie over het beheren van toegang met micro soft Edge met Microsoft Intune-beleid, Zie [Internet toegang beheren met een met Microsoft intune-beleid beveiligde browser](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Maart 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Het Framework voor identiteits ervaring en aangepaste beleids ondersteuning in Azure Active Directory B2C is nu beschikbaar (GA)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

U kunt nu aangepaste beleids regels maken in Azure AD B2C, met inbegrip van de volgende taken, die op schaal en onder onze Azure-SLA worden ondersteund:

- U maakt en uploadt gebruikers trajecten voor aangepaste authenticatie door aangepaste beleids regels te gebruiken.

- Beschrijf de door de gebruiker geplaatste stapsgewijze stap-voor-stap als uitwisseling tussen claim providers.

- Definieer voorwaardelijke vertakking in de reis van de gebruiker.

- Trans formatie en toewijzing van claims voor gebruik in realtime beslissingen en communicatie.

- Gebruik REST API-services in uw eigen trajecten voor de gebruikers met aangepaste verificatie. Bijvoorbeeld met e-mail providers, CRMs en bedrijfsspecifieke autorisatie systemen.

- Communiceren met id-providers die voldoen aan het OpenIDConnect-protocol. Bijvoorbeeld met multi tenant Azure AD, sociale-account providers of twee ledige verificatie providers.

Zie voor meer informatie over het maken van aangepaste beleids regels [voor ontwikkel aars voor aangepast beleid in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) en Lees [het blog bericht van Alex Simon, met inbegrip van](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)casestudy's.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-maart 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In 2019 maart hebben we deze 14 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [Medius flow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [controle systeem op basis van uitleg](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool prestatie zaken](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizonten](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [taa](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nieuwe Zscaler-en Atlassian-inrichtings connectors in de Azure AD Gallery-maart 2019

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden

Automatisch maken, bijwerken en verwijderen van gebruikers accounts voor de volgende apps:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler twee](https://aka.ms/ZscalerTwoProvisioning), [Zscaler drie](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Uw verwijderde Office 365-groepen herstellen en beheren in de Azure AD-Portal

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

U kunt nu uw verwijderde Office 365-groepen bekijken en beheren via de Azure AD-Portal. Met deze wijziging kunt u zien welke groepen beschikbaar zijn om te herstellen, en kunt u alle groepen die niet nodig zijn voor uw organisatie permanent verwijderen.

Zie [verlopen of verwijderde groepen herstellen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)voor meer informatie.

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Eenmalige aanmelding is nu beschikbaar voor Azure AD SAML-beveiligde on-premises apps via toepassings proxy (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

U kunt nu een SSO-ervaring (eenmalige aanmelding) bieden voor on-premises, met SAML geverifieerde apps en externe toegang tot deze apps via toepassings proxy. Zie voor meer informatie over het instellen van SAML SSO met uw on-premises apps [SAML eenmalige aanmelding voor on-premises toepassingen met toepassings proxy (preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Client-apps in de aanvraag lussen worden onderbroken om de betrouw baarheid en gebruikers ervaring te verbeteren

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Client-apps kunnen honderden dezelfde aanmeldings aanvragen binnen korte tijd onjuist uitgeven. Deze aanvragen, ongeacht of ze wel of niet zijn gelukt, nemen alle bijdragen aan een slechte gebruikers ervaring en verhoogde workloads voor de IDP, verg root de latentie voor alle gebruikers en verminderen de beschik baarheid van de IDP.

Deze update verstuurt een `invalid_grant` fout: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` naar client-apps die dubbele aanvragen meerdere keren worden uitgevoerd gedurende een korte periode, buiten het bereik van de normale werking. Client-apps die dit probleem ondervinden, moeten een interactieve prompt weer geven, waarbij de gebruiker zich opnieuw moet aanmelden. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)voor meer informatie over deze wijziging en hoe u uw app kunt herstellen als deze fout optreedt.

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nieuwe audit logboeken gebruikers ervaring nu beschikbaar

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

We hebben een nieuwe Azure AD- **controle logboek** pagina gemaakt waarmee u zowel de Lees baarheid als de manier waarop u naar uw gegevens zoekt, kunt verbeteren. Als u de nieuwe pagina **controle logboeken** wilt weer geven, selecteert u **controle logboeken** in het gedeelte **activiteit** van Azure AD.

![Nieuwe pagina controle logboeken, met voorbeeld gegevens](media/whats-new/audit-logs-page.png)

Zie voor meer informatie over de nieuwe pagina **controle logboeken** [activiteiten controleren in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nieuwe waarschuwingen en richt lijnen om te voor komen dat onbedoelde beheerders niet-geconfigureerde beleids regels voor voorwaardelijke toegang kunnen worden vergrendeld

**Type:** Gewijzigde functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Om te voor komen dat beheerders zichzelf via onjuist geconfigureerde beleids regels voor voorwaardelijke toegang per ongeluk kunnen vergren delen, hebben we nieuwe waarschuwingen en bijgewerkte richt lijnen gemaakt in de Azure Portal. Zie [Wat zijn service afhankelijkheden in azure Active Directory voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)voor meer informatie over de nieuwe richt lijnen.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Betere gebruiks voorwaarden voor eind gebruikers op mobiele apparaten

**Type:** Gewijzigde functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer

We hebben onze bestaande gebruiks voorwaarden bijgewerkt om te helpen bij het verbeteren van de manier waarop u de gebruiks voorwaarden op een mobiel apparaat bekijkt en ermee akkoord gaat. U kunt nu in-en uitzoomen, terugkeren, de informatie downloaden en hyper links selecteren. Zie [Azure Active Directory functie gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)voor meer informatie over de bijgewerkte gebruiks voorwaarden.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nieuwe ervaring voor het downloaden van Azure AD-activiteiten logboeken beschikbaar

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu grote hoeveel heden activiteiten logboeken rechtstreeks downloaden vanuit het Azure Portal. Met deze update kunt u het volgende doen:

- Down load Maxi maal 250.000 rijen.

- Ontvang een melding wanneer de down load is voltooid.

- Pas de bestands naam aan.

- Bepaal de indeling van de uitvoer, ofwel JSON of CSV.

Voor meer informatie over deze functie raadpleegt [u Quick Start: een controle rapport downloaden met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Belang rijke wijziging: updates voor evaluatie van voor waarden door Exchange ActiveSync (EAS)

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Access Control

We zijn bezig met het bijwerken van de manier waarop Exchange ActiveSync (EAS) de volgende voor waarden evalueert:

- Gebruikers locatie, op basis van land, regio of IP-adres

- Aanmeldings risico

- Platform apparaat

Als u deze voor waarden in uw beleid voor voorwaardelijke toegang eerder hebt gebruikt, is het mogelijk dat het probleem van de voor waarde wordt gewijzigd. Als u bijvoorbeeld eerder de voor waarde voor de gebruikers locatie in een beleid hebt gebruikt, kan het beleid worden overgeslagen op basis van de locatie van uw gebruiker.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configureerbaar Azure AD SAML-token versleuteling (open bare preview) 

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

U kunt nu elke ondersteunde SAML-app configureren voor het ontvangen van versleutelde SAML-tokens. Wanneer Azure AD is geconfigureerd en gebruikt met een app, worden de verzonden SAML-bevestigingen versleuteld met behulp van een open bare sleutel die is verkregen van een certificaat dat is opgeslagen in azure AD.

Zie [Azure AD SAML-token versleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie over het configureren van uw SAML-token versleuteling.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Een toegangs beoordeling maken voor groepen of apps met behulp van Azure AD-toegangs beoordelingen

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Beheer

U kunt nu meerdere groepen of apps opnemen in één Azure AD-toegangs beoordeling voor groepslid maatschap of app-toewijzing. Toegangs beoordelingen met meerdere groepen of apps worden ingesteld met dezelfde instellingen en alle opgenomen revisoren worden tegelijkertijd op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) voor meer informatie over hoe u een toegangs beoordeling maakt met behulp van Azure AD-toegangs beoordelingen

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-februari 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In februari 2019 zijn deze 27 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Euromonitor Pass Port](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), Port [stack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), permission click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisch](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [delen van een droom](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webmethodes integratie Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [kennis Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [ou campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Peri Scope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud door Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Uitgebreide registratie van gecombineerde MFA/SSPR

**Type:** Gewijzigde functie  
**Service categorie:** Self-service voor wacht woord opnieuw instellen  
**Product mogelijkheden:** Gebruikers verificatie

Als reactie op feedback van klanten hebben we de preview-ervaring voor de gecombineerde MFA-SSPR-registratie verbeterd, waardoor uw gebruikers hun beveiligings gegevens voor zowel MFA als SSPR sneller kunnen registreren. 

**Voer de volgende stappen uit om de verbeterde ervaring voor uw gebruikers vandaag in te scha kelen:**

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder en ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster te beheren**. 

2. In de **gebruikers die de preview-functies voor het registreren en beheren van beveiligings gegevens – optie voor het vernieuwen kunnen gebruiken** , kiest u de functies voor een **geselecteerde groep gebruikers** of voor **alle gebruikers**.

In de komende weken verwijderen we de mogelijkheid om de oude gecombineerde registratie Preview voor MFA/SSPR voor tenants die nog niet zijn ingeschakeld, in te scha kelen.

**Voer de volgende stappen uit om te controleren of het besturings element wordt verwijderd voor uw Tenant:**

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder en ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster te beheren**.  

2. Als de **gebruikers die de optie preview-functies voor het registreren en beheren van beveiligings gegevens hebben** , zijn ingesteld op **geen**, wordt de optie verwijderd uit uw Tenant.

Ongeacht of u eerder de oude gecombineerde MFA/SSPR registratie preview-ervaring voor gebruikers hebt ingeschakeld, wordt de oude ervaring op een later tijdstip uitgeschakeld. Daarom wordt u zo snel mogelijk geadviseerd om over te stappen op de nieuwe, verbeterde ervaring.

Voor meer informatie over de verbeterde registratie-ervaring raadpleegt [u de leuke verbeteringen van de Azure AD gecombineerde MFA-en registratie-ervaring voor het opnieuw instellen van wacht woorden](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Beleids beheer ervaring voor gebruikers stromen bijgewerkt

**Type:** Gewijzigde functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

We hebben het proces voor het maken en beheren van beleid voor gebruikers stromen bijgewerkt (voorheen bekend als ingebouwde beleids regels). Deze nieuwe ervaring is nu de standaard instelling voor al uw Azure AD-tenants.

U kunt aanvullende feedback en suggesties bieden met behulp van de pictogrammen glim lach of frons in het gebied **Feedback verzenden** boven aan het portal scherm.

Voor meer informatie over de nieuwe beleids beheer-ervaring raadpleegt u de [Azure AD B2C nu java script customization en veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Specifieke versie van pagina-elementen kiezen die wordt opgegeven door Azure AD B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

U kunt nu een specifieke versie van de pagina-elementen kiezen die door Azure AD B2C worden verschaft. Door een specifieke versie te selecteren, kunt u de updates testen voordat ze op een pagina worden weer gegeven en kunt u voorspel bare gedrag ophalen. Daarnaast kunt u ervoor kiezen om specifieke pagina versies af te dwingen om Java script-aanpassingen toe te staan. Als u deze functie wilt inschakelen, gaat u naar de pagina **Eigenschappen** in uw gebruikers stromen.

Voor meer informatie over het kiezen van specifieke versies van pagina-elementen raadpleegt u de [Azure AD B2C nu java script customization en veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Configureer bare vereisten voor het wacht woord voor de eind gebruiker voor B2C (GA)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

U kunt nu de wachtwoord complexiteit van uw organisatie instellen voor uw eind gebruikers, in plaats van uw systeem eigen Azure AD-wachtwoord beleid te gebruiken. Op de Blade **Eigenschappen** van uw gebruikers stromen (voorheen bekend als uw ingebouwde beleids regels) kunt u een wachtwoord complexiteit van **eenvoudig** of **sterk**kiezen, of u kunt een **aangepaste** set vereisten maken.

Zie [complexiteits vereisten configureren voor wacht woorden in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)voor meer informatie over de configuratie van de vereisten voor wachtwoord complexiteit.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nieuwe standaard sjablonen voor aangepaste merk authenticatie-ervaringen

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

U kunt onze nieuwe standaard sjablonen op de Blade **pagina-indelingen** van uw gebruikers stromen (voorheen bekend als ingebouwde beleids regels) gebruiken om een aangepaste merk bare verificatie-ervaring voor uw gebruikers te maken.

Voor meer informatie over het gebruik van de sjablonen raadpleegt u [Azure AD B2C nu java script-aanpassing en nog veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samen werking met authenticatie met eenmalige verificatie (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C

We hebben eenmalige wachtwoord verificatie (OTP) geïntroduceerd voor B2B-gast gebruikers die niet kunnen worden geverifieerd via andere manieren, zoals Azure AD, een Microsoft-account (MSA) of Google Federatie. Deze nieuwe verificatie methode betekent dat gast gebruikers geen nieuwe Microsoft-account hoeven te maken. Een gast gebruiker kan in plaats daarvan een uitnodiging inwisselen of toegang tot een gedeelde bron aanvragen om een tijdelijke code aan een e-mail adres te verzenden. Met deze tijdelijke code kan de gast gebruiker zich blijven aanmelden.

Zie voor meer informatie [e-mail One-time wachtwoord code verificatie (preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) en de blog. [Azure AD maakt delen en samen werking naadloos mogelijk voor elke gebruiker met een account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nieuwe instellingen voor Azure AD-toepassingsproxy cookie

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

We hebben drie nieuwe cookie-instellingen geïntroduceerd, die beschikbaar zijn voor uw apps die zijn gepubliceerd via toepassings proxy:

- **Cookie met alleen HTTP gebruiken.** Hiermee stelt u de **HTTPOnly** -vlag in voor de toegangs-en sessie cookies van de toepassings proxy. Het inschakelen van deze instelling biedt extra veiligheids voordelen, zoals het voor komen van het kopiëren of wijzigen van cookies via scripting aan de client zijde. U wordt aangeraden deze vlag in te scha kelen ( **Ja**) om de extra voor delen te bepalen.

- **Gebruik beveiligde cookie.** Hiermee stelt u de **beveiligde** vlag in voor de toegangs-en sessie cookies van de toepassings proxy. Het inschakelen van deze instelling biedt extra veiligheids voordelen door ervoor te zorgen dat cookies alleen worden verzonden via TLS-beveiligde kanalen, zoals HTTPS. U wordt aangeraden deze vlag in te scha kelen ( **Ja**) om de extra voor delen te bepalen.

- **Permanente cookie gebruiken.** Hiermee voor komt u dat de toegang tot cookies verloopt wanneer de webbrowser is gesloten. Deze cookies duren voor de levens duur van het toegangs token. De cookies worden echter opnieuw ingesteld als de verloop tijd wordt bereikt of als de gebruiker de cookie hand matig verwijdert. We raden u aan om de standaard instelling **Nee**te laten, alleen de instelling in te scha kelen voor oudere apps die geen cookies delen tussen processen.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)voor meer informatie over de nieuwe cookies.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-januari 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In januari 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talen palet](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco paraplu](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [verval herinnering](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CORPTAX](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)belopend, [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNEXUS SSO system](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [aren voor Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 voor Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [IDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Ga naar](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)de, [Korn veer Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nieuwe Azure AD Identity Protection verbeteringen (open bare preview)

**Type:** Gewijzigde functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

We zijn enthousiast dat we de volgende uitbrei dingen hebben toegevoegd aan de aanbieding van Azure AD Identity Protection open bare preview, waaronder:

- Een bijgewerkte en meer geïntegreerde gebruikers interface

- Aanvullende Api's

- Verbeterde risico analyse via machine learning

- Uitlijning op het hele product voor Risk ante gebruikers en Risk ante aanmeldingen

Zie [Wat is Azure Active Directory Identity Protection (vernieuwd)?](https://aka.ms/IdentityProtectionDocs) voor meer informatie over de uitbrei dingen. voor meer informatie en om je mening te delen via de prompts in het product.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nieuwe app-vergrendelings functie voor de app Microsoft Authenticator op iOS-en Android-apparaten

**Type:** Nieuwe functie  
**Service categorie:** App Microsoft Authenticator  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Als u uw eenmalige wachtwoord code, app-informatie en app-instellingen veiliger wilt laten, kunt u de functie voor het vergren delen van apps inschakelen in de app Microsoft Authenticator. Wanneer u app Lock inschakelt, wordt u gevraagd om te verifiëren met uw pincode of biometrisch telkens wanneer u de Microsoft Authenticator-app opent.

Zie de [Veelgestelde vragen over de Microsoft Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)voor meer informatie.

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Export mogelijkheden voor verbeterde Azure AD Privileged Identity Management (PIM)

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management

Privileged Identity Management (PIM) beheerders kunnen nu alle actieve en in aanmerking komende roltoewijzingen voor een specifieke resource exporteren, die roltoewijzingen voor alle onderliggende resources bevat. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en ze moest roltoewijzingen voor elke specifieke resource exporteren.

Zie [activiteit en controle geschiedenis voor Azure-resource rollen weer geven in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)voor meer informatie.

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gebruikers die zijn verwijderd uit het synchronisatie bereik, worden niet meer overgeschakeld naar alleen-Cloud accounts

**Type:** Vaste  
**Service categorie:** Gebruikers beheer  
**Product mogelijkheden:** Uitvoermap

>[!Important]
>We hebben uw frustratie gehoord en begrepen door deze oplossing. Daarom hebben we deze wijziging tot nu toe gewijzigd, zodat u de oplossing gemakkelijker kunt implementeren in uw organisatie.

Er is een fout opgelost waarbij de vlag DirSyncEnabled van een gebruiker onjuist wordt overgeschakeld naar **False** wanneer het object Active Directory Domain Services (AD DS) is uitgesloten van het synchronisatie bereik en vervolgens is verplaatst naar de Prullenbak in azure ad tijdens de volgende synchronisatie cyclus. Als gevolg van deze oplossing geldt dat als de gebruiker is uitgesloten van het synchronisatie bereik en daarna wordt hersteld vanuit Azure AD recycle bin, het gebruikers account blijft gesynchroniseerd vanuit on-premises AD, zoals verwacht, en niet kan worden beheerd in de Cloud omdat de bron van de autoriteit (SoA) blijft on-premises AD.

Voorafgaand aan deze oplossing is er een probleem opgetreden toen de vlag DirSyncEnabled is overgeschakeld naar onwaar. Er is een onjuiste indruk gegeven dat deze accounts zijn geconverteerd naar objecten in de Cloud en dat de accounts in de Cloud kunnen worden beheerd. De accounts behouden echter nog steeds hun SoA als on-premises en alle gesynchroniseerde eigenschappen (schaduw kenmerken) die afkomstig zijn van on-premises AD. Deze voor waarde heeft meerdere problemen ondervonden in azure AD en andere Cloud werkbelastingen (zoals Exchange Online) die verwacht worden deze accounts te behandelen als gesynchroniseerd vanuit AD, maar nu fungeren als alleen Cloud accounts.

Op dit moment is de enige manier om een gesynchroniseerd-van-AD-account echt te converteren naar een alleen-Cloud account door DirSync op Tenant niveau uit te scha kelen, waarmee een back-end-bewerking wordt geactiveerd om de SoA over te dragen. Voor dit type SoA-wijziging is (maar is niet beperkt tot) het schoonmaken van alle on-premises verwante kenmerken (zoals LastDirSyncTime-en schaduw kenmerken) en het verzenden van een signaal naar andere Cloud werkbelastingen om het desbetreffende object te converteren naar een alleen-Cloud account .

Deze oplossing verhindert daarom directe updates van het kenmerk ImmutableID van een gebruiker die is gesynchroniseerd vanuit AD, wat in sommige scenario's in het verleden was vereist. Het ImmutableID van een object in azure AD, zoals de naam al aangeeft, is bedoeld om onveranderbaar te zijn. Nieuwe functies die in Azure AD Connect Health zijn geïmplementeerd en Azure AD Connect-synchronisatie-client, zijn beschikbaar om deze scenario's te verhelpen:

- **Grootschalige ImmutableID-update voor veel gebruikers in een gefaseerde benadering**
  
  U moet bijvoorbeeld een langdurige AD DS tussen forest-migratie uitvoeren. Oplossing: gebruik Azure AD Connect om het **bron anker te configureren** en kopieer, wanneer de gebruiker migreert, de bestaande ImmutableID-waarden van Azure AD naar het kenmerk MS-DS-Consistency-GUID van de lokale AD DS gebruiker van het nieuwe forest. Zie [using MS-DS-ConsistencyGuid als source Anchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)voor meer informatie.

- **Grootschalige ImmutableID-updates voor veel gebruikers per foto**

  Tijdens de implementatie Azure AD Connect u bijvoorbeeld een fout gemaakt en nu moet u het kenmerk source Anchor wijzigen. Oplossing: Schakel DirSync uit op Tenant niveau en wis alle ongeldige ImmutableID-waarden. Zie Directory-synchronisatie uitschakelen [voor Office 365](/office365/enterprise/turn-off-directory-synchronization)voor meer informatie.

- **Een on-premises gebruiker opnieuw koppelen aan een bestaande gebruiker in azure AD** Een gebruiker die opnieuw is gemaakt in AD DS genereert bijvoorbeeld een duplicaat in het Azure AD-account in plaats van het opnieuw te koppelen aan een bestaand Azure AD-account (zwevend object). Oplossing: gebruik Azure AD Connect Health in de Azure Portal om de bron-anker-ImmutableID opnieuw toe te wijzen. Zie voor meer informatie [zwevend object scenario](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Belang rijke wijziging: updates voor het schema voor controle en aanmeldings logboeken via Azure Monitor

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Momenteel publiceren we de audit-en aanmeldings logboek stromen via Azure Monitor, zodat u de logboek bestanden naadloos kunt integreren met uw SIEM-hulpprogram ma's of met Log Analytics. Op basis van uw feedback en in de voor bereiding van de algemene Beschik baarheid van deze functie, worden de volgende wijzigingen aangebracht in het schema. Deze schema wijzigingen en de bijbehorende documentatie-updates worden uitgevoerd door de eerste week van januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nieuwe velden in het audit schema
Er wordt een nieuw **bewerkings type** veld toegevoegd om het type bewerking te bieden dat wordt uitgevoerd op de resource. Bijvoorbeeld: **toevoegen**, **bijwerken**of **verwijderen**.

#### <a name="changed-fields-in-the-audit-schema"></a>Gewijzigde velden in het audit schema
De volgende velden worden gewijzigd in het controle schema:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|Category|Dit is het veld **service naam** . Nu is het veld **controle categorieën** . De naam van de **service naam** is gewijzigd in het veld **loggedByService** .|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Self-service voor wacht woord opnieuw instellen</li></ul>|<ul><li>Gebruikersbeheer</li><li>Groepsbeheer</li><li>App-beheer</li></ul>|
|targetResources|Bevat **TargetResourceType** op het hoogste niveau.|&nbsp;|<ul><li>Beleid</li><li>App</li><li>Gebruiker</li><li>Groep</li></ul>|
|loggedByService|Geeft de naam van de service die het audit logboek heeft gegenereerd.|Null|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Self-service voor wachtwoord opnieuw instellen</li></ul>|
|Resultaat|Levert het resultaat van de audit Logboeken. Voorheen werd dit opgesomd, maar de werkelijke waarde wordt nu weer gegeven.|<ul><li>0</li><li>1</li></ul>|<ul><li>Geslaagd</li><li>Fout</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Gewijzigde velden in het aanmeldings schema
De volgende velden worden gewijzigd in het aanmeldings schema:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Dit is het **conditionalaccessPolicies** -veld. Nu is het veld **appliedConditionalAccessPolicies** .|Geen wijziging|Geen wijziging|
|conditionalAccessStatus|Hiermee wordt het resultaat van de status van het beleid voor voorwaardelijke toegang weer geven bij het aanmelden. Voorheen werd dit opgesomd, maar de werkelijke waarde wordt nu weer gegeven.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Geslaagd</li><li>Fout</li><li>Niet toegepast</li><li>Uitgeschakeld</li></ul>|
|appliedConditionalAccessPolicies: resultaat|Hiermee wordt het resultaat van de individuele status van het beleid voor voorwaardelijke toegang weer geven bij het aanmelden. Voorheen werd dit opgesomd, maar de werkelijke waarde wordt nu weer gegeven.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Geslaagd</li><li>Fout</li><li>Niet toegepast</li><li>Uitgeschakeld</li></ul>|

Zie [het schema voor Azure AD-controle logboeken interpreteren in azure monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema) voor meer informatie over het schema.

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Verbeteringen op het gebied van identiteits beveiliging in het machine learning model met toezicht en de risico Score-engine

**Type:** Gewijzigde functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Risico scores

Verbeteringen aan de gebruikers-en aanmeldings risico beoordelings engine voor identiteits beveiliging kunnen helpen de nauw keurigheid en dekking van de gebruikers Risico's te verbeteren. Beheerders kunnen merken dat het risico niveau van de gebruiker niet langer rechtstreeks is gekoppeld aan het risico niveau van specifieke detecties en dat er een toename is in het aantal en het niveau van Risk ante aanmeldings gebeurtenissen.

Risico detecties worden nu geëvalueerd door het machine learning model met toezicht, waarmee gebruikers Risico's worden berekend met behulp van aanvullende functies van de aanmeldingen van de gebruiker en een patroon van detecties. Op basis van dit model kan de beheerder gebruikers met hoge risico scores vinden, zelfs als detecties die aan die gebruiker zijn gekoppeld, een laag of gemiddeld risico hebben. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Beheerders kunnen hun eigen wacht woord opnieuw instellen met behulp van de Microsoft Authenticator-app (open bare preview)

**Type:** Gewijzigde functie  
**Service categorie:** Self-service voor wacht woord opnieuw instellen  
**Product mogelijkheden:** Gebruikers verificatie

Azure AD-beheerders kunnen nu hun eigen wacht woord opnieuw instellen met behulp van de Microsoft Authenticator app-meldingen of een code van een mobiele verificator-app of-hardware-token. Beheerders kunnen nu twee van de volgende methoden gebruiken om hun eigen wacht woord opnieuw in te stellen:

- App-melding Microsoft Authenticator

- Andere Mobile Authenticator-app/hardware-token code

- Email

- Telefoonoproep

- Sms-bericht

Voor meer informatie over het gebruik van de Microsoft Authenticator-app om wacht woorden opnieuw in te stellen, Zie [selfservice voor wachtwoord herstel van Azure AD-mobiele app en SSPR (preview-versie)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nieuwe rol van Azure AD-Cloud apparaat-beheerder (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Apparaatregistratie en-beheer  
**Product mogelijkheden:** Toegangs beheer

Beheerders kunnen gebruikers toewijzen aan de nieuwe rol van de beheerder van het Cloud apparaat voor het uitvoeren van beheer taken voor de Cloud. Gebruikers met de rol Administrators van Cloud apparaten kunnen apparaten in azure AD inschakelen, uitschakelen en verwijderen, en kunnen Windows 10 BitLocker-sleutels (indien aanwezig) in de Azure Portal lezen.

Zie [beheerders rollen toewijzen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor meer informatie over rollen en machtigingen.

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Uw apparaten beheren met de nieuwe tijds tempel van de activiteit in azure AD (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Apparaatregistratie en-beheer  
**Product mogelijkheden:** Levenscyclus beheer van apparaten

Na verloop van tijd moet u de apparaten van uw organisatie in azure AD vernieuwen en buiten gebruik stellen om te voor komen dat er verouderde apparaten in uw omgeving worden uitgevoerd. Om u te helpen bij dit proces, worden uw apparaten nu door Azure AD bijgewerkt met een nieuwe tijds tempel van de activiteit, zodat u de levens cyclus van uw apparaat kunt beheren.

Zie [How to: de verouderde apparaten beheren in azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices) voor meer informatie over het ophalen en gebruiken van deze tijds tempel.

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Beheerders kunnen vereisen dat gebruikers de gebruiks voorwaarden voor elk apparaat accepteren

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer
 
Beheerders kunnen nu de optie **gebruikers moeten toestemming geven op elk apparaat** , zodat uw gebruikers de gebruiks voorwaarden kunnen accepteren op elk apparaat dat ze gebruiken in uw Tenant.

Zie de [sectie met gebruiks voorwaarden per apparaat van de functie Azure Active Directory gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)voor meer informatie.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Beheerders kunnen een gebruiks voorwaarden configureren om te verlopen op basis van een terugkerend schema

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer
 

Beheerders kunnen de optie voor het **verstrijken van verlopen** nu inschakelen om een gebruiks voorwaarden te laten verlopen voor al uw gebruikers op basis van uw opgegeven terugkerende planning. De planning kan jaarlijks, tweejaarlijkse, elk kwar taal of maandelijks zijn. Nadat de gebruiks voorwaarden verlopen zijn, moeten gebruikers deze opnieuw accepteren.

Zie de [sectie gebruiks voorwaarden toevoegen van de functie Azure Active Directory gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)voor meer informatie.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Beheerders kunnen een gebruiks voorwaarden configureren om te verlopen op basis van de planning van elke gebruiker

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer

Beheerders kunnen nu een duur opgeven die de gebruiker nodig heeft om een gebruiks voorwaarden opnieuw te accepteren. Beheerders kunnen bijvoorbeeld opgeven dat gebruikers elke 90 dagen een gebruiks voorwaarden opnieuw moeten accepteren.

Zie de [sectie gebruiks voorwaarden toevoegen van de functie Azure Active Directory gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)voor meer informatie.
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nieuwe Azure AD Privileged Identity Management (PIM) e-mails voor Azure Active Directory rollen

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Klanten die Azure AD Privileged Identity Management (PIM) gebruiken, kunnen nu een wekelijks overzicht van e-mail ontvangen, met inbegrip van de volgende informatie over de afgelopen zeven dagen:

- Overzicht van de meest geschikte en permanente roltoewijzingen

- Aantal gebruikers die rollen activeren

- Aantal gebruikers dat is toegewezen aan rollen in PIM

- Aantal gebruikers dat is toegewezen aan rollen buiten PIM

- Het aantal gebruikers dat permanent is gemaakt in PIM

Zie [e-mail meldingen in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)voor meer informatie over PIM en de beschik bare e-mail meldingen.

---

### <a name="group-based-licensing-is-now-generally-available"></a>Op groep gebaseerde licentie verlening is nu algemeen beschikbaar

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Uitvoermap

De open bare preview-versie van een groeps licentie is niet beschikbaar en is nu algemeen verkrijgbaar. Als onderdeel van deze algemene release hebben we deze functie uitgebreidere schaalbaar en hebben ze de mogelijkheid voor het opnieuw verwerken van op groepen gebaseerde licentie toewijzingen voor één gebruiker en de mogelijkheid om op groep gebaseerde licentie verlening te gebruiken met Office 365 E3/a3-licenties.

Zie [Wat is op groep gebaseerde licentie verlening in azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) voor meer informatie over groeps licenties?

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-november 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In november 2018 hebben deze 26 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [oneindig campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial) [, Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy voor Business Central 365](https://accounting.zenegy.com/), [Everbridge member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex apps-klassieke test ](https://test.plexonline.com/signon), [Plex apps – klassiek](https://www.plexonline.com/signon), [Plex apps-UX test](https://test.cloud.plex.com/sso), [Plex apps – UX](https://cloud.plex.com/sso), [Plex apps – iam](https://accounts.plex.com/), [Craft producten-Childcare records, aanwezigheid, & systeem voor financiële tracking](https://getcrafts.ca/craftsregistration) 

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Logboeken in Azure AD werkt nu met Azure Log Analytics (preview-versie)

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

We zijn trots te kunnen aankondigen dat u nu uw Azure AD-logboeken naar Azure Log Analytics doorsturen kunt. Deze functie meest gevraagde helpt u nog beter toegang geven tot analytics voor uw bedrijf, bewerkingen, en beveiliging, evenals een manier om u te helpen bij het beheren van uw infrastructuur. Zie voor meer informatie de [Azure Active Directory activiteiten Logboeken in Azure log Analytics nu beschikbaar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nieuwe federatieve Apps beschikbaar in de galerie van Azure AD-app - oktober 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In oktober 2018, hebben we deze 14 nieuwe apps met Federatie ondersteuning aan de app-galerie toegevoegd:

[Mijn toekennings punten](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler drie](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [beoordeeld](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services-e-mailmeldingen

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services

Azure AD Domain Services biedt waarschuwingen in Azure portal over onjuiste configuraties of problemen met uw beheerde domein. Deze waarschuwingen bevatten stapsgewijze handleidingen, zodat u de problemen kunt oplossen kunt zonder dat contact opnemen met ondersteuning.

Met ingang van oktober, zal het mogelijk om aan te passen van de meldingsinstellingen voor uw beheerde domein dus wanneer er nieuwe waarschuwingen optreden, een e-mailbericht is verzonden naar een aangewezen groep mensen, hoeft u niet voortdurend controleren van de portal voor updates.

Zie [meldings instellingen in azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)voor meer informatie.

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal ondersteunt het gebruik van het domein ForceDelete API voor aangepaste domeinen verwijderen 

**Type:** Gewijzigde functie  
**Service categorie:** Directory beheer  
**Product mogelijkheden:** Uitvoermap

We zijn trots aan u kunt nu het domein ForceDelete API gebruiken om te verwijderen van uw aangepaste domeinnamen asynchroon naam van referenties, zoals gebruikers, groepen en apps van uw aangepaste domeinnaam (contoso.com) terug naar de naam van de initiële standaard-domein ( Contoso.onmicrosoft.com).

Deze wijziging kunt u snel uw aangepaste domeinnamen als uw organisatie de naam niet meer gebruikt, of te verwijderen als u nodig hebt voor het gebruik van de domeinnaam met een andere Azure AD.

Zie [een aangepaste domein naam verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)voor meer informatie.

---

## <a name="september-2018"></a>September 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Bijgewerkte machtigingen voor beheerdersrollen voor dynamische groepen

**Type:** Vaste  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

We hebben een probleem is opgelost, zodat de specifieke beheerdersrollen nu kunnen maken en bijwerken van dynamisch-lidmaatschapregels, zonder dat de eigenaar van de groep.

De rollen zijn:

- Globale beheerder

- InTune-beheerder

- Gebruikers beheerder

Zie [een dynamische groep maken en de status controleren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) voor meer informatie

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Configuratie-instellingen voor vereenvoudigde eenmalige aanmelding voor sommige apps van derden

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We realiseren ons dat instellen van eenmalige aanmelding (SSO) voor Software als een Service (SaaS)-apps kunnen lastig zijn vanwege de unieke aard van de configuratie van apps. We hebben een vereenvoudigde configuratie-ervaring voor het automatisch vullen van de SSO-configuratie-instellingen voor de volgende externe SaaS-apps gemaakt:

- Zendesk

- ArcGis Online

- Jamf Pro

Ga naar de pagina **Azure Portal** > **SSO-configuratie** voor de app om te beginnen met het gebruik van deze sessie met één klik. Zie voor meer informatie [SaaS-toepassings integratie met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure van de Active Directory - waar bevindt uw gegevens zich? pagina

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** GoLocal

Selecteer de regio van uw bedrijf in de **Azure Active Directory-waar** bevindt zich uw gegevens pagina om te zien welk Azure-Data Center uw Azure AD-gegevens in rust heeft voor alle Azure AD-Services. U kunt de gegevens filteren op specifieke Azure AD-services voor de regio van uw bedrijf.

Als u toegang wilt krijgen tot deze functie en voor meer informatie, Zie [Azure Active Directory-waar bevindt zich uw gegevens](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nieuw implementatieplan beschikbaar voor het deelvenster Mijn apps-toegang

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** SSO

Bekijk het nieuwe implementatie plan dat beschikbaar is voor het toegangs venster mijn apps (https://aka.ms/deploymentplans).
Het deelvenster Mijn Apps toegang biedt gebruikers met één plek om te zoeken en toegang tot hun apps. De portal bevat ook gebruikers met selfservice-mogelijkheden, zoals aanvragen van toegang tot apps en -groepen of beheren van toegang tot deze resources namens anderen.

Zie [Wat is de portal mijn apps?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) voor meer informatie.

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nieuw tabblad Probleemoplossing en ondersteuning op de pagina met logboeken van aanmeldingen in Azure Portal

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Het nieuwe tabblad **probleem oplossing en ondersteuning** op de pagina **aanmeldingen** van het Azure Portal, is bedoeld om beheerders te helpen bij het oplossen van problemen met betrekking tot Azure AD-aanmeldingen. Dit nieuwe tabblad bevat de fout code, het fout bericht en aanbevelingen voor herstel (indien van toepassing) om het probleem op te lossen. Als u het probleem niet kunt oplossen, geeft u ook een nieuwe manier om een ondersteunings ticket te maken met behulp van de functie **kopiëren naar klem bord** , waarmee de velden **aanvraag-id** en **datum (UTC)** voor het logboek bestand in uw ondersteunings ticket worden ingevuld.  

![Aanmeld logboeken met het nieuwe tabblad](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Verbeterde ondersteuning voor aangepaste uitbreidingseigenschappen gebruikt om regels voor dynamisch lidmaatschap te maken

**Type:** Gewijzigde functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

Met deze update kunt u nu klikken op de koppeling **aangepaste extensie eigenschappen ophalen** van de opbouw functie voor de groep voor dynamische gebruikers, uw unieke App-ID invoeren en de volledige lijst met aangepaste extensie-eigenschappen ontvangen die moeten worden gebruikt bij het maken van een dynamische lidmaatschaps regel voor gebruikers. Deze lijst kan ook worden vernieuwd om op te halen van alle nieuwe aangepaste extensie-eigenschappen voor die app.

Voor meer informatie over het gebruik van aangepaste uitbreidings eigenschappen voor dynamische lidmaatschaps regels raadpleegt u de eigenschappen van de [extensie en aangepaste extensie](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

De volgende apps zijn te vinden op de lijst met [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Ga voor meer informatie naar:

- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nieuwe ondersteuning voor selfservice voor wachtwoordherstel op het vergrendelingsscherm van Windows 7/8/8.1

**Type:** Nieuwe functie  
**Service categorie:** SSPR  
**Product mogelijkheden:** Gebruikers verificatie

Nadat u deze nieuwe functie hebt ingesteld, krijgen gebruikers een koppeling te zien om hun wacht woord opnieuw in te stellen vanaf het **vergrendelings** scherm van een apparaat met Windows 7, Windows 8 of Windows 8,1. Door te klikken op de koppeling, wordt de gebruiker geleid door de dezelfde stroom voor wachtwoord opnieuw instellen via de webbrowser.

Zie [How to enable password reset from Windows 7, 8 en 8,1 (](https://aka.ms/ssprforwindows78) Engelstalig) voor meer informatie.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Kennisgeving van wijziging: autorisatiecodes kunnen niet langer opnieuw worden gebruikt 

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Vanaf 15 November 2018, Azure AD wordt niet meer accepteren van eerder gebruikte verificatie codes voor apps. Deze wijziging in de beveiliging zorgt ervoor dat Azure AD in overeenstemming met de OAuth-specificatie brengen en worden afgedwongen op de v1- en v2-eindpunten.

Als uw app wordt gebruikgemaakt van autorisatiecodes om op te halen van tokens voor meerdere bronnen, raden wij u gebruik van de code om op te halen van een vernieuwingstoken en gebruikt vervolgens die vernieuwingstoken om te verkrijgen van aanvullende tokens voor andere resources. Autorisatiecodes kunnen slechts eenmaal worden gebruikt, maar vernieuwen van tokens kunnen meerdere keren worden gebruikt in meerdere resources. Een app waarmee wordt geprobeerd om een verificatiecode op te geven tijdens de OAuth-codestroom opnieuw te gebruiken krijgt een foutmelding invalid_grant zijn.

Zie voor deze en andere protocollen gerelateerde wijzigingen [de volledige lijst met nieuwe functies voor verificatie](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Er zijn nieuwe federatieve apps beschikbaar in de app-galerie voor Azure AD - september 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In September 2018, hebben we deze 16 nieuwe apps met Federatie ondersteuning aan de app-galerie toegevoegd:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [tegemoetkomen aan wervings software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [sneeuw](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [racks Pace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO voor Azure, surveymonkey, [bijeenroeping](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="support-for-additional-claims-transformations-methods"></a>Ondersteuning voor aanvullende claimstransformatiemethoden

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We hebben nieuwe claim transformatie methoden, ToLower () en ToUpper () geïntroduceerd, die kunnen worden toegepast op SAML-tokens van de configuratie pagina voor **eenmalige aanmelding** op basis van SAML.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) voor meer informatie.

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Bijgewerkte configuratie-UI voor SAML-apps (preview-versie)

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

Als onderdeel van onze bijgewerkt op basis van SAML appconfiguratie-UI krijgt u het:

- Een bijgewerkte scenario-ervaring voor het configureren van uw apps op basis van SAML.

- Meer zichtbaarheid over wat er ontbreekt of is onjuist in uw configuratie.

- De mogelijkheid om toe te voegen meerdere e-mailadressen voor melding over verlopen certificaat.

- Nieuwe claim transformatie methoden, ToLower() en ToUpper() en meer.

- Een manier voor het uploaden van uw eigen token handtekeningcertificaat voor apps in uw onderneming.

- Een manier om in te stellen de NameID-indeling voor SAML-apps, en een manier om in te stellen de NameID-waarde als uitbreidingen van de Directory.

Als u deze bijgewerkte weer gave wilt inschakelen, klikt u op de koppeling **onze nieuwe ervaring proberen** vanaf de bovenkant van de pagina voor **eenmalige aanmelding** . Zie [zelf studie: eenmalige aanmelding op basis van SAML configureren voor een toepassing met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)voor meer informatie.

---

## <a name="august-2018"></a>Augustus 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Wijzigingen in Azure Active Directory-IP-adresbereiken

**Type:** Plan voor wijziging  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Onafhankelijk

We introduceren grotere IP-adresbereiken naar Azure AD, wat betekent dat als u Azure AD-IP-adresbereiken voor uw firewalls, routers of Netwerkbeveiligingsgroepen hebt geconfigureerd, moet u deze bijwerken. We doorvoeren deze update zodat u uw firewall, router of Network Security groepen IP-bereik configuraties opnieuw wijzigen hoeft wanneer Azure AD nieuwe eindpunten toevoegt. 

Netwerkverkeer wordt verplaatst naar deze nieuwe bereiken in de volgende twee maanden. Als u wilt doorgaan met de service niet wordt onderbroken, moet u deze bijgewerkte waarden toevoegen aan uw IP-adressen voor 10 September 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Het is raadzaam de oude IP-adresbereiken niet verwijderen tot al uw netwerkverkeer is verplaatst naar de nieuwe bereiken. Zie [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken voor updates over de verplaatsing en om te leren wanneer u de oude bereiken kunt verwijderen.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Kennisgeving van wijziging: autorisatiecodes kunnen niet langer opnieuw worden gebruikt 

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Vanaf 15 November 2018, Azure AD wordt niet meer accepteren van eerder gebruikte verificatie codes voor apps. Deze wijziging in de beveiliging zorgt ervoor dat Azure AD in overeenstemming met de OAuth-specificatie brengen en worden afgedwongen op de v1- en v2-eindpunten.

Als uw app wordt gebruikgemaakt van autorisatiecodes om op te halen van tokens voor meerdere bronnen, raden wij u gebruik van de code om op te halen van een vernieuwingstoken en gebruikt vervolgens die vernieuwingstoken om te verkrijgen van aanvullende tokens voor andere resources. Autorisatiecodes kunnen slechts eenmaal worden gebruikt, maar vernieuwen van tokens kunnen meerdere keren worden gebruikt in meerdere resources. Een app waarmee wordt geprobeerd om een verificatiecode op te geven tijdens de OAuth-codestroom opnieuw te gebruiken krijgt een foutmelding invalid_grant zijn.

Zie voor deze en andere protocollen gerelateerde wijzigingen [de volledige lijst met nieuwe functies voor verificatie](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Geconvergeerd beveiligingsinformatiebeheer voor selfservice voor wachtwoordherstel en Multi-Factor Authentication

**Type:** Nieuwe functie  
**Service categorie:** SSPR  
**Product mogelijkheden:** Gebruikers verificatie
 
Deze nieuwe functie helpt mensen hun beveiligingsgegevens (zoals, telefoonnummer, mobiele app, enzovoort) beheren voor SSPR en MFA in een enkele locatie en ervaring; in vergelijking met op eerder, waar dit is gedaan in twee verschillende locaties.

Deze ervaring geconvergeerde werkt ook voor mensen met behulp van SSPR of MFA. Bovendien, als uw organisatie niet van MFA of SSPR-registratie afdwingen, kunt mensen nog steeds registreren MFA of SSPR info beveiligingsmethoden toegestaan door uw organisatie van de portal mijn Apps.

Dit is een opt-in voor openbare preview-versie. Beheerders kunnen inschakelen op de nieuwe ervaring (indien gewenst) voor een geselecteerde groep of voor alle gebruikers in een tenant. Voor meer informatie over de geconvergeerde ervaring raadpleegt u de [geconvergeerde ervaring blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nieuwe instelling Alleen HTTP-cookies in apps voor de Azure AD-toepassingsproxy

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Er is een nieuwe instelling met de naam **http-only cookies** in uw toepassings proxy-apps. Deze instelling biedt extra beveiliging door de vlag HTTPOnly opnemen in de HTTP-antwoordheader voor beide Application Proxy toegangs- en sessiebeleid cookies, toegang aan de cookie van een client-side-script stoppen en verder te voorkomen dat bewerkingen zoals kopiëren of het wijzigen van de cookie. Hoewel deze vlag nog niet eerder zijn gebruikt, zijn uw cookies altijd versleuteld en verzonden met behulp van een SSL-verbinding om u te helpen beschermen tegen verkeerde wijzigingen.

Deze instelling is niet compatibel is met apps met behulp van ActiveX-besturingselementen, zoals Extern bureaublad. Als u bent in dit geval is, wordt u aangeraden dat u deze instelling uitschakelen.

Zie [toepassingen publiceren met Azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)voor meer informatie over de instelling voor alleen HTTP-cookies.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) voor Azure-resources ondersteunt resourcestypen voor beheergroepen

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Instellingen voor het activeren en de toewijzing van Just-In-Time kunnen nu worden toegepast op beheergroep-resourcetypen, net zoals u al voor abonnementen, resourcegroepen en Resources (zoals virtuele machines, App Services en meer doet). Bovendien kan iedereen met een rol waarmee de toegang als beheerder voor een beheergroep detecteren en beheren van die resource in PIM.

Zie [Azure-resources detecteren en beheren met privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources) voor meer informatie over PIM-en Azure-resources.
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Toegang tot toepassingen (preview-versie) biedt sneller toegang tot de Azure AD-portal

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Vandaag de dag bij het activeren van een rol met PIM, deze kan meer dan 10 minuten duren voordat de machtigingen voor het van kracht. Als u kiest voor toegang tot toepassingen, dat zich momenteel in openbare preview, beheerders hebben toegang tot de Azure AD-portal zodra de activeringsaanvraag is voltooid.

Toegang tot de toepassing ondersteunt momenteel alleen de Azure AD portal-ervaring en de Azure-resources. Zie [Wat is Azure AD privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) voor meer informatie over PIM en toegang tot toepassingen.
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Er zijn nieuwe federatieve apps beschikbaar in de app-galerie voor Azure AD - augustus 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In augustus 2018, hebben we deze 16 nieuwe apps met Federatie ondersteuning aan de app-galerie toegevoegd:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [BridgeLine](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial)niet-gebonden [, saus Labs-mobiele en webtests](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [META netwerken connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [manier van werken](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (per Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-onkosten rapporten](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 live chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Systeemeigen Tableau-ondersteuning is nu beschikbaar in Azure AD-toepassingsproxy

**Type:** Gewijzigde functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Met de update van de OpenID Connect voor het verlenen van OAuth 2.0-Code-protocol voor het protocol van onze vooraf-verificatie hebt u niet langer geen aanvullende instellingen voor het gebruik van Tableau met Application Proxy. Deze wijziging protocol helpt ook bij de toepassingsproxy betere ondersteuning bieden voor moderne apps met behulp van alleen HTTP-omleidingen, die vaak worden ondersteund in JavaScript en HTML-codes.

Voor meer informatie over onze systeem eigen ondersteuning voor tableau raadpleegt u [Azure AD-toepassingsproxy nu met native tableau-ondersteuning](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nieuwe ondersteuning om Google toe te voegen als een id-provider voor B2B-gastgebruikers in Azure Active Directory (preview-versie)

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C

Door het instellen van Federatie met Google in uw organisatie, kunt u uitgenodigde Gmail gebruikers zich laten uw gedeelde apps en resources met behulp van hun bestaande Google-account, zonder te hoeven maken van een persoonlijk Microsoft-Account (MSA's) of een Azure AD-account.

Dit is een opt-in voor openbare preview-versie. Zie [Google als een id-provider voor B2B-gast gebruikers toevoegen](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)voor meer informatie over Google Federation.

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Verbeteringen voor e-mailmeldingen van Azure Active Directory

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Azure Active Directory (Azure AD) e-mailberichten functie nu het ontwerp van een bijgewerkte, evenals wijzigingen in het e-mailadres afzender en de weergavenaam van de afzender, wanneer verzonden vanuit de volgende services:
 
- Azure AD-Toegangsbeoordelingen
- Azure AD Connect Health 
- Azure AD-identiteitsbeveiliging 
- Azure AD Privileged Identity Management
- Enterprise-App verloopt certificaat meldingen
- Servicemeldingen voor inrichting van Enterprise-App
 
De e-mailmeldingen worden verzonden van de volgende e-mailadres en de weergavenaam:

- E-mail adres: azure-noreply@microsoft.com
- Weergavenaam: Microsoft Azure
 
Zie [e-mail meldingen in azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832)voor een voor beeld van een aantal nieuwe e-mail ontwerpen en meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-activiteitenlogboeken zijn nu beschikbaar via Azure Monitor

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

De Azure AD-activiteitenlogboeken zijn nu beschikbaar in openbare preview-versie van de Azure Monitor (van Azure-platform hele monitoring-service). Azure Monitor biedt u met een langetermijnbewaarperiode en naadloze integratie, naast deze verbeteringen:

- Langetermijnretentie van uw logboekbestanden routering naar uw eigen Azure storage-account.

- Naadloze SIEM-integratie, zonder dat u hoeft te schrijven of te onderhouden aangepaste scripts.

- Naadloze integratie met uw eigen aangepaste oplossingen, analysehulpprogramma's of oplossingen van incidentbeheer.

Voor meer informatie over deze nieuwe mogelijkheden raadpleegt u onze blog [Azure AD-activiteiten Logboeken in azure monitor Diagnostics is nu beschikbaar in de open bare preview-versie](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) en in onze documentatie [Azure Active Directory activiteiten logboeken in azure monitor (preview)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informatie over voorwaardelijke toegang toegevoegd aan het Azure AD-aanmeld rapport

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Deze update kunt u zien welke beleidsregels worden geëvalueerd wanneer een gebruiker zich aanmeldt, samen met het resultaat van het beleid. Daarnaast bevat het rapport nu het type van de client-app die wordt gebruikt door de gebruiker, zodat u oudere protocolverkeer kunt identificeren. Rapport vermeldingen kunnen nu ook worden gezocht naar een correlatie-ID, die kan worden gevonden in het foutbericht van de gebruiker gerichte en kan worden gebruikt om te identificeren en oplossen van de overeenkomende aanmeldingsaanvraag.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Verouderde verificaties weergeven via logboeken met aanmeldingsactiviteiten

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren
 
Met de introductie van het veld **client-app** in de logboeken voor aanmeldings activiteiten kunnen klanten nu zien welke gebruikers oudere verificaties gebruiken. Klanten hebben toegang tot deze gegevens via de aanmeldingen MS Graph API of via de aanmeld activiteiten Logboeken in de Azure AD-Portal, waar u het besturings element voor **client-apps** kunt gebruiken om te filteren op verouderde verificaties. Bekijk de documentatie voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Er zijn nieuwe federatieve apps beschikbaar in de galerie met apps van Azure AD - juli 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In juli 2018, hebben we deze 16 nieuwe apps met Federatie ondersteuning aan de app-galerie toegevoegd:

[Innovatie hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bepaalde beheer-SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC staging [, IPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screen cast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified leslokaal, [Eli onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar externe ondersteuning](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [denkers webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow-connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [vaardig heden basis](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nieuwe SaaS-app-integraties voor het inrichten van gebruikers - juli 2018

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
Azure AD kunt u het maken, onderhoud en verwijderen van gebruikers-id's in SaaS-toepassingen, zoals Dropbox, Salesforce, ServiceNow en automatiseren. Voor juli 2018, hebben we ondersteuning voor de volgende toepassingen in de galerie van Azure AD-app inrichten van gebruikers toegevoegd:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonus](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Zie [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial)voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure AD-galerie.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health voor synchronisatie - een eenvoudigere manier om synchronisatiefouten met zwevende en dubbele kenmerken te herstellen

**Type:** Nieuwe functie  
**Service categorie:** AD Connect  
**Product mogelijkheden:** & Rapportage controleren
 
Azure AD Connect Health introduceert herstel van self-service te markeren en synchronisatiefouten oplossen. Deze functie Hiermee lost u dubbel kenmerk synchronisatiefouten en correcties van objecten die zijn zwevende van Azure AD. Deze diagnose biedt de volgende voordelen:

- De taalinstelling van de synchronisatiefouten dubbel kenmerk, bieden specifieke oplossingen

- Van toepassing is een oplossing voor Azure AD-scenario's, het oplossen van fouten in één stap toegewezen

- Er is geen upgrade of de configuratie is in te schakelen en gebruik deze functie vereist

Zie voor meer informatie [problemen met dubbele kenmerken vaststellen en oplossen](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visualupdates voor de aanmeldingservaring van Azure AD en van MSA

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD  
**Product mogelijkheden:** Gebruikers verificatie

We hebben de gebruikersinterface voor de ervaring van Microsoft online services aanmelden, zoals bijgewerkt voor Office 365 en Azure. Deze wijziging stelt de schermen, minder rommelige en eenvoudiger. Zie voor meer informatie over deze wijziging de [aanstaande verbeteringen in de blog van Azure AD-aanmeld ervaring](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nieuwe versie van Azure AD Connect - juli 2018

**Type:** Gewijzigde functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

De meest recente versie van Azure AD Connect bevat: 

- Oplossingen voor problemen en ondersteuning-updates 

- Algemene beschikbaarheid van de integratie Ping federeren

- Updates voor de nieuwste SQL 2012-client 

Zie [Azure AD Connect: release geschiedenis](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) van de versie voor meer informatie over deze update.

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Updates voor de gebruiks voorwaarden van de gebruikers interface van de eind gebruiker

**Type:** Gewijzigde functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer

De acceptatie van de tekenreeks in de gebruikersinterface van de eindgebruiker gebruiksvoorwaarden worden bijgewerkt.

**Huidige tekst.** Voor toegang tot resources [tenantName], moet u de gebruiksvoorwaarden accepteren.<br>**Nieuwe tekst.** Voor toegang tot [tenantName]-resource, moet u de gebruiksvoorwaarden lezen.

**Huidige tekst:** Als u ervoor kiest om te accepteren, moet u akkoord gaan met alle bovenstaande gebruiks voorwaarden.<br>**Nieuwe tekst:** Klik op accepteren om te bevestigen dat u de gebruiks voorwaarden hebt gelezen en begrepen.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Pass-through-verificatie ondersteunt verouderde protocollen en toepassingen

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Nu Pass through-verificatie biedt ondersteuning voor verouderde protocollen en -apps. De volgende beperkingen zijn nu volledig ondersteund:

- Gebruikersaanmeldingen voor verouderde Office clienttoepassingen, Office 2010 en Office 2013, zonder moderne verificatie.

- Toegang tot de agenda te delen en beschikbaarheidsgegevens in hybride omgevingen voor Office 2010 alleen.

- Gebruikersaanmeldingen tot Skype voor bedrijven-clienttoepassingen zonder moderne verificatie.

- Gebruikersaanmeldingen naar PowerShell versie 1.0.

- Het Apple Device Enrollment Program (DEP) van Apple, met behulp van de iOS-Configuratieassistent. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Geconvergeerd beveiligingsinformatiebeheer voor selfservice voor wachtwoordherstel en Multi-Factor Authentication

**Type:** Nieuwe functie  
**Service categorie:** SSPR  
**Product mogelijkheden:** Gebruikers verificatie

Deze nieuwe functie kan gebruikers hun beveiligingsgegevens (bijvoorbeeld telefoonnummer, e-mailadres, mobiele app, enzovoort) voor selfservice voor wachtwoordherstel (SSPR) en multi-factor Authentication (MFA) in één ervaring beheren. Niet meer hebben gebruikers de dezelfde beveiligingsgegevens registreren voor SSPR en MFA in twee verschillende ervaringen. Deze nieuwe ervaring is ook van toepassing op gebruikers die Self-service voor Wachtwoordherstel of MFA hebben.

Als een organisatie geen MFA-of SSPR-registratie afdwingt, kunnen gebruikers hun beveiligings gegevens registreren via de portal **mijn apps** . Van daaruit kunnen gebruikers zich registreren ingeschakeld voor MFA of SSPR methoden. 

Dit is een opt-in voor openbare preview-versie. Beheerders kunnen inschakelen op de nieuwe ervaring (indien gewenst) voor een geselecteerde groep van gebruikers of alle gebruikers in een tenant.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>De app Microsoft Authenticator gebruiken om uw identiteit te verifiëren wanneer u uw wachtwoord opnieuw instelt

**Type:** Gewijzigde functie  
**Service categorie:** SSPR  
**Product mogelijkheden:** Gebruikers verificatie

Deze functie kunt niet-beheerders hun identiteit te verifiëren bij het herstellen van een wachtwoord met behulp van een melding of de code van de Microsoft Authenticator (of een andere verificator-app). Nadat beheerders inschakelen dit self-service voor wachtwoord opnieuw instellen van methode, gebruikers die zich hebben geregistreerd een mobiele app via aka.ms/mfasetup of aka.ms/setupsecurityinfo kunnen hun mobiele app gebruiken als een verificatiemethode tijdens hun wachtwoord opnieuw instellen.

Mobiele app-meldingen kan alleen worden ingeschakeld als onderdeel van een beleid dat is vereist twee methoden voor uw wachtwoord opnieuw instellen.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Kennisgeving te wijzigen: Security-oplossing voor de autorisatiestroom gedelegeerde voor apps met behulp van Logboeken API van Azure AD-activiteit

**Type:** Plan voor wijziging  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Vanwege onze sterkere beveiligings handhaving hebben we een wijziging aangebracht in de machtigingen voor apps die gebruikmaken van een gedelegeerde autorisatie stroom om toegang te krijgen tot de [api's van Azure AD-activiteiten logboeken](https://aka.ms/aadreportsapi). Deze wijziging gaat in op **26 juni 2018**.

Als een van uw apps in Azure AD-activiteit Log-API's gebruikt, volg deze stappen om te controleren of dat de app niet wordt beëindigd nadat de wijziging gebeurt.

**Uw app-machtigingen bijwerken**

1. Meld u aan bij de Azure Portal, selecteer **Azure Active Directory**en selecteer vervolgens **app-registraties**.
2. Selecteer uw app die gebruikmaakt van de API voor Azure AD-activiteiten logboeken, selecteer **instellingen**, selecteer **vereiste machtigingen**en selecteer vervolgens de **Windows Azure Active Directory** -API.
3. Schakel in het gebied **gedelegeerde machtigingen** van de Blade **toegang inschakelen** het selectie vakje in naast **Directory gegevens lezen** en selecteer vervolgens **Opslaan**.
4. Selecteer **machtigingen verlenen**en selecteer vervolgens **Ja**.
    
    >[!Note]
    >U moet een globale beheerder om machtigingen aan de app te verlenen.

Zie het gedeelte [machtigingen verlenen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) van de vereisten voor toegang tot het Azure AD Reporting API-artikel voor meer informatie.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Verbinding maken met Azure AD-services voor het PCI DSS-compliance TLS-instellingen configureren

**Type:** Nieuwe functie  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Onafhankelijk

Transport Layer Security (TLS) is een protocol waarmee privacy en integriteit tussen twee communicerende toepassingen en is het meest geïmplementeerde beveiligingsprotocol tegenwoordig gebruikt.

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft vastgesteld dat vroege versies van TLS en Secure Sockets Layer (SSL) moeten worden uitgeschakeld in het voor deel van het inschakelen van nieuwe en veiligere app-protocollen, met naleving vanaf **30 juni 2018**. Deze wijziging betekent dat als u verbinding met Azure AD-services maken en voldoen aan PCI DSS-beleid moet, moet u de TLS 1.0 uitschakelen. Er zijn meerdere versies van TLS beschikbaar, maar TLS 1.2 is de meest recente versie die beschikbaar zijn voor Azure Active Directory-Services. Wij raden verplaatsen rechtstreeks naar TLS 1.2 voor combinaties van zowel de client/server en de browser of de server.

Verouderde browsers ondersteunen mogelijk niet de nieuwere TLS-versies, zoals TLS 1.2. Als u wilt zien welke versies van TLS door uw browser worden ondersteund, gaat u naar de [QUALYS SSL Labs](https://www.ssllabs.com/) -site en klikt u op **uw browser testen**. We raden u upgraden naar de nieuwste versie van uw webbrowser en bij voorkeur inschakelen alleen TLS 1.2.

**TLS 1,2 inschakelen, per browser**

- **Micro soft Edge en Internet Explorer (beide worden ingesteld met Internet Explorer)**

    1. Open Internet Explorer, selecteer **extra** > **Internet opties** > **Geavanceerd**.
    2. Selecteer in het gebied **beveiliging** de optie **TLS 1,2 gebruiken**en selecteer vervolgens **OK**.
    3. Alle browservensters sluiten en opnieuw starten van Internet Explorer. 

- **Google Chrome**

    1. Open Google Chrome, typ *Chrome://Settings/* in de adres balk en druk op **Enter**.
    2. Vouw de **Geavanceerde** opties uit, ga naar het gebied **systeem** en selecteer **proxy-instellingen openen**.
    3. Selecteer in het vak **Internet eigenschappen** het tabblad **Geavanceerd** , ga naar het gebied **beveiliging** , selecteer **TLS 1,2 gebruiken**en selecteer vervolgens **OK**.
    4. Alle browservensters sluiten en opnieuw starten van Google Chrome.

- **Mozilla Firefox**

    1. Open Firefox, typ *about: config* in de adres balk en druk op **Enter**.
    2. Zoek naar de term *TLS*en selecteer vervolgens de vermelding **Security. TLS. version. Max** .
    3. Stel de waarde in op **3** om te zorgen dat de browser Maxi maal versie TLS 1,2 gebruikt en selecteer **OK**.

        >[!NOTE]
        >Versie 60,0 van Firefox ondersteunt TLS 1,3, dus u kunt ook de waarde Security. TLS. version. Max instellen op **4**.

    4. Sluit alle browservensters en Mozilla Firefox opnieuw.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nieuwe federatieve Apps beschikbaar in de galerie van Azure AD-app - juni 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In juni 2018, hebben we deze 15 nieuwe apps met Federatie ondersteuning aan de app-galerie toegevoegd:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), het [afwikkelen van muziek](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), het [SAML 1,1-token ingeschakelde LOB-app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [supersfeer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [endpoint backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TONICDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [share point on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [topcx Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [chronischex](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD. 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-wachtwoordbeveiliging is beschikbaar in openbare preview

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Gebruikers verificatie

Azure AD-wachtwoord Protection gebruiken om u te helpen voorkomen gemakkelijk geraden wachtwoorden van uw omgeving. Deze wachtwoorden elimineren kunt u het risico van inbreuk op basis van een wachtwoord spray type aanval.

Met name kunt Azure AD-wachtwoord Protection u:

- Beveiligen van uw organisatie-accounts in zowel Azure AD en Windows Server Active Directory (AD). 
- Hiermee stopt u uw gebruikers met een wachtwoord op een lijst van de meest gebruikte wachtwoorden meer dan 500 en meer dan 1 miljoen tekens vervanging varianten van deze wachtwoorden.
- Beheren van Azure AD-wachtwoord beveiliging vanaf één locatie in de Azure AD-portal voor zowel Azure AD en on-premises Windows Server AD.

Zie voor meer informatie over Azure AD-wachtwoord beveiliging [het elimineren van ongeldige wacht woorden in uw organisatie](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe sjabloon voor beleid voor voorwaardelijke toegang van alle gasten gemaakt tijdens het maken van de gebruiksrecht overeenkomst

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer

Tijdens het maken van de gebruiks voorwaarden wordt ook een nieuwe beleids sjabloon voor voorwaardelijke toegang gemaakt voor alle gasten en alle apps. Deze nieuwe beleidssjabloon is van toepassing de zojuist gemaakte gebruiksvoorwaarden, stroomlijnen het maken en het afdwingen van proces voor gasten.

Zie [Azure Active Directory gebruiksvoorwaarden functie](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe aangepaste beleids sjabloon voor voorwaardelijke toegang die is gemaakt tijdens het maken van de gebruiksrecht overeenkomst

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Beheer

Tijdens het maken van de gebruiks voorwaarden wordt ook een nieuwe ' aangepaste beleids sjabloon voor voorwaardelijke toegang gemaakt. Met deze nieuwe beleids sjabloon kunt u de gebruiks voorwaarden maken en vervolgens direct naar de Blade voor het maken van beleid voor voorwaardelijke toegang gaan, zonder dat u hand matig door de portal hoeft te navigeren.

Zie [Azure Active Directory gebruiksvoorwaarden functie](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nieuwe en uitgebreide richtlijnen over het implementeren van Azure multi-factor Authentication

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
We hebben nieuwe Stapsgewijze instructies over het implementeren van Azure multi-factor Authentication (MFA) in uw organisatie uitgebracht.

Als u de MFA-implementatie handleiding wilt weer geven, gaat u naar de hand leiding voor [identiteits implementatie](https://aka.ms/DeploymentPlans) opslag plaats op github. Als u feedback wilt geven over de implementatie handleidingen, gebruikt u het [feedback formulier voor het implementatie plan](https://aka.ms/deploymentplanfeedback). Als u vragen hebt over de implementatie handleidingen, kunt u contact met ons opnemen via [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD gedelegeerd beheer van apps rollen in openbare preview zijn

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** Access Control

Beheerders kunt nu beheertaken delegeren zonder de globale beheerdersrol toewijzen. De nieuwe functies en mogelijkheden zijn:

- **Nieuwe standaard Azure AD-beheerders rollen:**

    - **Toepassings beheerder.** De mogelijkheid voor het beheren van alle aspecten van alle apps, met inbegrip van de registratie, SSO-instellingen, app-toewijzingen en licentieverlening, App proxy-instellingen en toestemming verleent (behalve aan Azure AD-resources).

    - **Cloud toepassings beheerder.** Verleent alle van de mogelijkheden van de beheerder van de toepassing, behalve App proxy omdat het niet mogelijk om toegang tot on-premises.

    - **Application Developer.** Biedt de mogelijkheid om app-registraties te maken, zelfs als de optie **gebruikers toestaan apps te registreren** is uitgeschakeld.

- **Eigendom (per app-registratie en per bedrijfs-app instellen, vergelijkbaar met het groeps eigenaars proces:**
 
    - **Eigenaar van app-registratie.** Hebben de mogelijkheid voor het beheren van alle aspecten van die eigendom zijn app-registratie, met inbegrip van het app-manifest en meer eigenaren toe te voegen.

    - **Eigenaar van de Enter prise-app.** Hebben de mogelijkheid om u te veel aspecten van eigendom zakelijke apps, inclusief SSO-instellingen, app-toewijzingen en toestemming beheren (met uitzondering van Azure AD-resources).

Voor meer informatie over open bare preview-versie, zie de [open bare preview-functie voor Azure AD-gedelegeerde toepassingen.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Zie [beheerders rollen toewijzen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)voor meer informatie over rollen en machtigingen.

---

## <a name="may-2018"></a>Mei 2018

### <a name="expressroute-support-changes"></a>Ondersteuning voor ExpressRoute-wijzigingen

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Onafhankelijk  

Software as a Service biedt, zoals Azure Active Directory (Azure AD) zijn ontworpen voor het beste werkt door te gaan rechtstreeks via het Internet, zonder ExpressRoute of andere persoonlijke VPN-tunnels. Daarom zullen we op **1 augustus 2018**geen ondersteuning meer bieden voor ExpressRoute voor Azure AD-Services met behulp van open bare Azure-peering en Azure-Community's in micro soft-peering. Alle services die beïnvloed door deze wijziging mogelijk ziet u Azure AD-verkeer geleidelijk verschuiving van ExpressRoute met Internet.

Terwijl we onze ondersteuning, wijzigt ook weten we er zijn nog steeds situaties waarin u mogelijk wilt gebruiken een toegewezen set met circuits voor uw verificatieverkeer. Als gevolg hiervan blijft Azure AD voor de ondersteuning van per-tenant IP-bereikbeperkingen met behulp van ExpressRoute- en -services al op het Microsoft-peering met de community 'Andere Online Office 365-services'. Als uw services worden beïnvloed, maar u ExpressRoute nodig hebt, moet u het volgende doen:

- **Als u gebruikmaakt van open bare Azure-peering.** Ga naar micro soft-peering en meld u aan voor de **andere Office 365 Online Services (12076:5100)-** community. Zie het artikel [een openbaar peering naar micro soft-peering verplaatsen](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) voor meer informatie over het verplaatsen van open bare Azure-peering naar micro soft-peering.

- **Als u gebruikmaakt van micro soft-peering.** Meld u aan voor de **andere Office 365 online service-Community (12076:5100)** . Zie de [sectie ondersteuning voor BGP-community's](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) van het artikel ExpressRoute Routing Requirements (Engelstalig) voor meer informatie over routerings vereisten.

Als u speciale circuits moet blijven gebruiken, moet u contact opnemen met uw micro soft-account team over het verkrijgen van toestemming voor het gebruik van de **andere Office 365 online service-Community (12076:5100)** . De MS Office beheerde controleteam controleert of u of u deze circuits moet en zorg ervoor dat u inzicht in de technische gevolgen te houden. Niet-gemachtigde abonnementen bij het maken van routefilters voor Office 365 ontvangt een foutbericht weergegeven. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph-API's voor beheerscenario's voor de gebruiksvoorwaarden

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
We hebben Microsoft Graph-Api's toegevoegd voor de beheer bewerking van Azure AD-gebruiks voorwaarden. U kunt het object voor waarden van het gebruik maken, bijwerken en verwijderen.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD-multitenant-eindpunt toevoegen als een id-provider in Azure AD B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Met behulp van aangepaste beleidsregels, kunt u de algemene Azure AD-eindpunt toevoegen als een id-provider in Azure AD B2C. Hiermee kunt u dat een single point of vermelding voor alle Azure AD-gebruikers die bij uw toepassingen aanmeldt zich. Zie [Azure Active Directory B2C: gebruikers toestaan zich aan te melden bij een multi tenant Azure ad-id-provider met behulp van aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)voor meer informatie.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Interne URL's gebruikt voor toegang tot apps vanaf elke locatie met de extensie mijn Apps, aanmelding en de Azure AD-toepassingsproxy

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** SSO
 
Gebruikers kunnen nu toegang tot toepassingen via de interne URL's ook buiten uw bedrijfsnetwerk met behulp van de mijn Apps beveiligde aanmelding-extensie voor Azure AD. Dit werkt met elke toepassing die u hebt gepubliceerd met behulp van Azure AD-toepassingsproxy in elke browser waarop ook de Browseruitbreiding van het toegangsvenster geïnstalleerd. De functionaliteit van URL-omleiding is automatisch ingeschakeld wanneer een gebruiker meldt zich aan bij de extensie. De uitbrei ding is beschikbaar voor down loads van [micro soft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)en [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - gegevens in Europa voor Europa-klanten

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** GoLocal

Klanten in Europa vereisen hun gegevens om te blijven in Europa en niet gerepliceerd buiten de Europese datacenters voor vergadering privacy en de Europese wetgeving. Dit [artikel](https://go.microsoft.com/fwlink/?linkid=872328) bevat de specifieke details over welke identiteits gegevens worden opgeslagen in Europa en geeft ook informatie over informatie die buiten Europese data centers zal worden opgeslagen. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nieuwe gebruikers inrichten van SaaS-app integraties - mei 2018

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
Azure AD kunt u het maken, onderhoud en verwijderen van gebruikers-id's in SaaS-toepassingen, zoals Dropbox, Salesforce, ServiceNow en automatiseren. Voor mei 2018, hebben we ondersteuning voor de volgende toepassingen in de galerie van Azure AD-app inrichten van gebruikers toegevoegd:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Hoek steen op OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Zie [https://aka.ms/appstutorial](https://aka.ms/appstutorial)voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure AD-galerie.

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-toegangsbeoordelingen van groepen en toegang tot de app biedt nu terugkerende beoordelingen

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Beheer
 
Toegangsbeoordeling voor groepen en apps is nu algemeen beschikbaar als onderdeel van Azure AD Premium P2.  Beheerders zich toegangsbeoordelingen van groepslidmaatschappen en -toewijzingen van de toepassing automatisch wordt herhaald met regelmatige intervallen, zoals maandelijkse of driemaandelijkse configureren.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-activiteitenlogboeken (aanmeldingen en audit) zijn nu beschikbaar zijn via MS Graph

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren
 
Azure AD activiteitenlogboeken, waaronder, aanmeldingen en auditlogboeken, zijn nu beschikbaar zijn via MS Graph. We hebben twee eindpunten via MS Graph voor toegang tot deze logboeken weergegeven. Bekijk onze [documenten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) voor programmatische toegang tot Azure AD Reporting api's om aan de slag te gaan. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Verbeteringen aan het inwisselen van de B2B-ervaring en een organisatie verlaten

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C

**Just-in-time-inwisseling:** Nadat u een resource met een gast-API hebt gedeeld, hoeft u geen speciale e-mail met een uitnodiging te verzenden. In de meeste gevallen de gastgebruiker hebben toegang tot de resource en zien wat de ervaring inwisselen just-in-tijd worden uitgevoerd. Er is geen meer impact vanwege gemiste e-mailberichten. Niet meer wordt gevraagd uw gastgebruikers 'Hebt u klikken op de koppeling inwisselen die het systeem naar u verzonden?'. Dit betekent dat zodra SPO maakt gebruik van de manager van de uitnodiging – bewolkt bijlagen de dezelfde standaard-URL voor alle gebruikers, interne en externe – in elke status van inschrijving hebben kunnen.

**Moderne aflossings ervaring:** Geen pagina meer gesplitste scherm opname. Gebruikers zien een moderne toestemming geven ervaring met de privacyverklaring van de uitnodigende organisatie, net als voor apps van derden.

**Gast gebruikers kunnen de organisatie verlaten:** Zodra de relatie van een gebruiker met een organisatie is afgelopen, kunnen ze zelf de organisatie verlaten. Aanroepen niet meer van de uitnodigende organisatie admin 'verwijderd', niet meer verhogen ondersteuningstickets.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nieuwe federatieve Apps beschikbaar in de galerie van Azure AD-app - mei 2018

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In mei 2018, hebben we deze 18 nieuwe apps met Federatie ondersteuning aan onze app-galerie toegevoegd:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), openspoel, [Arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [koppel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nieuwe stapsgewijze implementatiehandleidingen voor Azure Active Directory

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Uitvoermap
 
Nieuwe stapsgewijze richt lijnen voor het implementeren van Azure Active Directory (Azure AD), waaronder selfservice voor wachtwoord herstel (SSPR), eenmalige aanmelding (SSO), voorwaardelijke toegang (CA), app-proxy, gebruikers inrichting, Active Directory Federation Services (ADFS) aan Pass-Through-verificatie (PTA) en ADFS to Password Hash Sync (PHS).

Als u de implementatie handleidingen wilt weer geven, gaat u naar de [hand leidingen voor identiteits implementatie](https://aka.ms/DeploymentPlans) opslag plaats op github. Als u feedback wilt geven over de implementatie handleidingen, gebruikt u het [feedback formulier voor het implementatie plan](https://aka.ms/deploymentplanfeedback). Als u vragen hebt over de implementatie handleidingen, kunt u contact met ons opnemen via [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Zakelijke toepassingen zoeken: meer Apps laden

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Problemen met het vinden van uw toepassingen / service-principals? We hebben de mogelijkheid om te laden meer toepassingen in uw zakelijke toepassingen op de lijst met alle toepassingen toegevoegd. Standaard, laten we zien 20 toepassingen. U kunt nu klikken, **meer laden** om extra toepassingen weer te geven. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>De versie van AADConnect een openbare preview-versie van de integratie met PingFederate bevat, mei belangrijke beveiligingsupdates, veel oplossingen voor problemen en nieuwe geweldige nieuwe hulpprogramma's voor probleemoplossing. 

**Type:** Gewijzigde functie  
**Service categorie:** AD Connect  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De versie van AADConnect een openbare preview-versie van de integratie met PingFederate bevat, mei belangrijke beveiligingsupdates, veel oplossingen voor problemen en nieuwe geweldige nieuwe hulpprogramma's voor probleemoplossing. U vindt [hier](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)de release opmerkingen.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-toegangsbeoordelingen: automatisch toepassen

**Type:** Gewijzigde functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Beheer

Toegangsbeoordelingen voor groepen en apps zijn nu algemeen beschikbaar als onderdeel van Azure AD Premium P2. Een beheerder kan configureren zodat de wijzigingen van de revisor automatisch aan die groep of de app worden toegepast als de toegangsbeoordeling is voltooid. De beheerder kan ook opgeven wat gebeurt er met blijvende toegang van de gebruiker als revisoren niet reageren, toegang verwijderen, toegang houden of systeem aanbevelingen ophalen. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-tokens kunnen niet meer worden geretourneerd met behulp van de query-response_mode voor nieuwe apps. 

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Apps die zijn gemaakt op of na 25 april 2018 kunnen geen **id_token** meer aanvragen met behulp van de **query** response_mode.  Hiermee wordt Azure AD-inline met de OIDC-specificaties en helpt de kwetsbaarheid van uw apps te beperken.  Apps die zijn gemaakt vóór 25 april 2018, worden niet geblokkeerd voor het gebruik van de **query** response_mode met een response_type van **id_token**.  De geretourneerde fout, bij het aanvragen van een id_token van AAD, is **AADSTS70007: ' query ' is geen ondersteunde waarde van ' response_mode ' bij het aanvragen van een token**.

Het **fragment** en **form_post** response_modes blijven werken: bij het maken van nieuwe toepassings objecten (bijvoorbeeld voor het gebruik van de app-proxy), moet u ervoor zorgen dat een van deze response_modes wordt gebruikt voordat een nieuwe toepassing wordt gemaakt.  

---
 
## <a name="april-2018"></a>April 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C Access Token zijn algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C 

U hebt nu toegang tot Web-API's beveiligd door Azure AD B2C met-toegangstokens. De functie wordt verplaatst van de openbare preview-versie voor algemene beschikbaarheid. De gebruikersinterface-ervaring voor het configureren van Azure AD B2C-toepassingen en web-API's, is verbeterd en zijn andere kleine verbeteringen aangebracht.
 
Zie [Azure AD B2C: toegangs tokens aanvragen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)voor meer informatie.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Configuratie voor eenmalige aanmelding voor toepassingen op basis van SAML testen

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

Bij het configureren van SAML gebaseerde SSO-toepassingen, u kunt de integratie van op de configuratiepagina testen. Als er een fout opgetreden tijdens het aanmelden, kunt u de fout in de test-ervaring bieden en Azure AD biedt u stappen voor het oplossen van dit specifieke probleem.

Ga voor meer informatie naar:

- [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps) (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)
- [Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD-gebruiks voorwaarden hebben nu per gebruiker rapporteert

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht
 
Beheerders kunnen nu selecteert u een bepaalde gebruiksvoorwaarden en zien alle gebruikers die hebben ingestemd met de gebruiksvoorwaarden en wat datum/tijd het heeft plaatsgevonden.

Zie de [functie gebruiks voorwaarden van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riskante IP voor beveiliging met AD FS extranet accountvergrendeling 

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** & Rapportage controleren

Connect Health nu ondersteunt de mogelijkheid voor het detecteren van IP adressen die groter zijn dan een drempel voor mislukte U/P aanmeldingen op basis van per uur of dagelijks. De mogelijkheden van deze functie zijn:

- Uitgebreide rapport weergeven met de IP-adres en het aantal mislukte aanmeldingen die zijn gegenereerd op basis van per uur/dag met aanpasbare drempelwaarde.
- Waarschuwingen op basis van e-mailbericht weergegeven wanneer een specifiek IP-adres heeft de drempelwaarde van mislukte U/P aanmeldingen op basis van per uur/dag overschreden.
- Een downloadoptie om een gedetailleerde analyse van de gegevens

Zie [riskive IP Report](https://aka.ms/aadchriskyip)(Engelstalig) voor meer informatie.

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Eenvoudig app-configuratie met de van metagegevensbestand of URL

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

Op de pagina van de Enterprise-toepassingen kunnen beheerders een SAML-metagegevensbestand als u wilt configureren op basis van SAML-aanmelding voor AAD-galerie en niet toepassing uploaden.

Bovendien kunt u URL voor federatieve metagegevens van Azure AD-toepassing voor het configureren van eenmalige aanmelding met de betreffende toepassing.

Zie [eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassings galerie bevinden](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)voor meer informatie.

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD gebruiksrechtovereenkomst gebruiken nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht
 

Azure AD-gebruiks voorwaarden zijn verplaatst van de open bare preview naar algemeen beschikbaar.

Zie de [functie gebruiks voorwaarden van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Uitnodigingen voor B2B-gebruikers van bepaalde organisaties toestaan of blokkeren

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 

U kunt nu opgeven welke partnerorganisaties die u wilt delen en samenwerken met in Azure AD B2B-samenwerking. Om dit te doen, kunt u lijst maken met specifieke toestaan of weigeren van domeinen. Wanneer een domein is geblokkeerd met behulp van deze mogelijkheden, kunnen werknemers niet meer uitnodigingen verzenden naar mensen in dat domein.

Zo kunt u voor het beheren van toegang tot uw resources tijdens het inschakelen van een goede ervaring voor goedgekeurde gebruikers.

Deze functie voor B2B-samen werking is beschikbaar voor alle Azure Active Directory klanten en kan worden gebruikt in combi natie met Azure AD Premium functies zoals voorwaardelijke toegang en identiteits beveiliging voor gedetailleerdere controle van wanneer en hoe externe zakelijke gebruikers zich aanmelden in en toegang krijgen.

Zie [uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)voor meer informatie.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps beschikbaar in Azure AD app-galerie

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In April 2018, hebben we deze 13 nieuwe apps met Federatie ondersteuning aan onze app-galerie toegevoegd:

Criterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [organigram Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta performance monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [stroomx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), schap, [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>GRANT B2B-gebruikers in Azure AD-toegang tot uw on-premises toepassingen (preview-versie)

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C

Als een organisatie die gebruikmaakt van functionaliteit voor samenwerking in Azure Active Directory (Azure AD) B2B uitnodigen van gastgebruikers van partnerorganisaties koppelt aan uw Azure AD, kunt u nu deze B2B-gebruikers toegang bieden tot on-premises toepassingen. Deze on-premises toepassingen kunnen verificatie op basis van SAML of geïntegreerde Windows-verificatie (IWA) gebruiken met Kerberos-beperkte delegatie (KCD).

Zie [B2B-gebruikers in azure AD-toegang verlenen aan uw on-premises toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)voor meer informatie.

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Zelfstudies over integratie van eenmalige aanmelding ophalen uit de Azure-Marketplace

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product capaciteit:** integratie van derden

Als een toepassing die in de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) wordt vermeld, ondersteuning biedt voor eenmalige aanmelding op basis van SAML, kunt u op **Get it nu** de zelf studie voor integratie koppelen die aan die toepassing is gekoppeld. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snellere prestaties van Azure AD automatisch gebruikers inrichten voor SaaS-toepassingen

**Type:** Gewijzigde functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
Voorheen klanten die gebruikmaken van de inrichting van connectors voor SaaS-toepassingen (bijvoorbeeld Salesforce, ServiceNow en vak) Azure Active Directory-gebruiker kunnen traag als hun Azure AD-tenants die meer dan 100.000 gecombineerde gebruikers en groepen, en ze zijn toewijzingen van gebruikers en groepen gebruiken om te bepalen welke gebruikers moeten worden ingericht.

Op 2 April 2018, zijn belangrijke prestatieverbeteringen geïmplementeerd op de Azure AD-inrichtingsservice die aanzienlijk verminderen de hoeveelheid tijd die nodig is om uit te voeren van de initiële synchronisatie tussen Azure Active Directory- en doel SaaS-toepassingen.

Als gevolg hiervan, veel klanten had initiële synchronisaties met apps die het aantal dagen duurde of nooit uitgevoerd, kunt u nu binnen een paar minuten of uren zijn voltooid.

Zie [Wat gebeurt er tijdens het inrichten?](/azure//active-directory/app-provisioning/how-provisioning-works) voor meer informatie.

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Self-service voor wachtwoord opnieuw instellen van Windows 10-vergrendelingsscherm voor hybride Azure AD verbonden computers

**Type:** Gewijzigde functie  
**Service categorie:** Self-service voor wacht woord opnieuw instellen  
**Product mogelijkheden:** Gebruikers verificatie
 
De functie voor Windows 10 SSPR nu ondersteuning voor machines die zijn toegevoegd aan hybrid Azure AD join is bijgewerkt. Deze functie is beschikbaar in Windows 10 RS4 kunnen gebruikers hun wachtwoord van het vergrendelingsscherm van een Windows 10-computer. Deze functie kunnen gebruikmaken van gebruikers die zijn ingeschakeld en geregistreerd voor selfservice voor wachtwoord opnieuw instellen.

Zie [Azure AD-wacht woord opnieuw instellen in het aanmeldings scherm](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)voor meer informatie.

---

## <a name="march-2018"></a>Maart 2018
 
### <a name="certificate-expire-notification"></a>Certificaat verloopt melding

**Type:** Vaste  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Azure AD verzendt een melding wanneer een certificaat voor een galerie of toepassing buiten de galerie is bijna verlopen. 

Sommige gebruikers is niet ontvangen van meldingen voor bedrijfstoepassingen die zijn geconfigureerd voor SAML gebaseerde eenmalige aanmelding. Dit probleem is opgelost. Azure AD stuurt de melding voor certificaten dat verloopt binnen 7, 30 en 60 dagen. U bent kunnen zien van dit evenement dat in de auditlogboeken. 

Ga voor meer informatie naar:

- [Certificaten beheren voor federatieve eenmalige aanmelding in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Rapporten van activiteiten controleren in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- en GitHub-id-providers in Azure AD B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
U kunt nu Twitter of GitHub toevoegen als een id-provider in Azure AD B2C. Twitter wordt verplaatst van de openbare preview-versie voor algemene beschikbaarheid. GitHub is in openbare preview-versie wordt uitgebracht.

Zie [Wat is Azure AD B2B-samen werking?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)voor meer informatie.
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Browser toegang beperken met Intune Managed Browser met voorwaardelijke toegang op basis van een toepassing voor Azure AD voor iOS en Android

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
**Nu beschikbaar in de open bare preview.**

**INTUNE Managed browser SSO:** Uw werk nemers kunnen eenmalige aanmelding gebruiken voor systeem eigen clients (zoals micro soft Outlook) en de Intune Managed Browser voor alle apps die zijn verbonden met Azure AD.

**Ondersteuning voor voorwaardelijke toegang intune Managed browser:** U kunt werk nemers nu de intune Managed browser gebruiken met behulp van beleid voor voorwaardelijke toegang op basis van toepassingen.

Meer informatie hierover vindt u in onze [blog post](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Ga voor meer informatie naar:

- [Voorwaardelijke toegang op basis van toepassingen instellen](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed browser-beleid configureren](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App-Proxy-Cmdlets in algemene beschikbaarheid van Powershell-Module

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control
 
Ondersteuning voor toepassingsproxy-cmdlets is nu in de Powershell-Module voor algemene beschikbaarheid. Dit vereist dat u op de hoogte te blijven van Powershell-modules - als u meer dan een jaar achter, sommige cmdlets worden mogelijk niet meer werken. 

Zie [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)voor meer informatie.
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 systeemeigen clients worden ondersteund door een naadloze eenmalige aanmelding met een niet-interactieve protocol

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Gebruiker met behulp van Office 365 systeemeigen clients (versie 16.0.8730.xxxx en hoger) ophalen van een op de achtergrond aanmelding met naadloze eenmalige aanmelding. Deze ondersteuning wordt geleverd door de toevoeging een niet-interactieve protocol (WS-Trust) naar Azure AD.

Zie [Hoe werkt aanmelden op een systeem eigen client met naadloze SSO?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work) voor meer informatie.
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Gebruikers krijgen een op de achtergrond aanmelding, met naadloze eenmalige aanmelding, als een toepassing aanmeldingsaanvragen naar Azure AD-tenant-eindpunten verzendt

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Gebruikers krijgen een stille aanmelding, met naadloze SSO, als een toepassing (bijvoorbeeld `https://contoso.sharepoint.com`) aanmeldings aanvragen verzendt naar de Tenant eindpunten van Azure AD, dat wil zeggen, `https://login.microsoftonline.com/contoso.com/<..>` of `https://login.microsoftonline.com/<tenant_ID>/<..>` in plaats van het gemeen schappelijke eind punt van Azure AD (`https://login.microsoftonline.com/common/<...>`).

Zie [Azure Active Directory naadloze eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)voor meer informatie. 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Moet slechts één Azure AD-URL, in plaats van twee URL's eerder, toevoegen aan gebruikers Intranet zone-instellingen voor de implementatie van naadloze eenmalige aanmelding

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Als u naadloze SSO wilt implementeren voor uw gebruikers, moet u slechts één Azure AD-URL toevoegen aan de intranet zone-instellingen van de gebruikers met behulp van groeps beleid in Active Directory: `https://autologon.microsoftazuread-sso.com`. Voorheen moesten klanten om toe te voegen twee URL's.

Zie [Azure Active Directory naadloze eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)voor meer informatie. 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve Apps beschikbaar in Azure AD app-galerie

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In maart 2018, hebben we deze 15 nieuwe apps met Federatie ondersteuning aan onze app-galerie toegevoegd:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), assistent van FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enter prise server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD. 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM voor Azure-Resources is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Als u van Azure AD Privileged Identity Management voor directory-rollen gebruikmaakt, kunt u nu gebruiken de tijdelijke toegang en de mogelijkheden van de toewijzing van PIM voor Azure-Resource-rollen, zoals abonnementen, resourcegroepen, virtuele Machines en een andere bron die wordt ondersteund door Azure Resource Manager. Meervoudige verificatie afdwingen bij het activeren van rollen Just-In-Time en plannen van activeringen in combinatie met windows goedgekeurde wijzigen. Deze versie wordt bovendien niet beschikbaar tijdens de openbare preview, met inbegrip van een bijgewerkte gebruikersinterface, goedkeuringswerkstromen en de mogelijkheid om verlopen rollen vernieuwen uit te breiden rollen verloopt binnenkort verbeteringen toegevoegd.

Zie [PIM voor Azure-resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) voor meer informatie
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Toevoegen van optionele Claims voor uw apps-tokens (openbare preview)

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Uw Azure AD-app kunt nu aanvraag aangepaste of optioneel claims in JWTs of SAML-tokens.  Dit zijn de claims over de gebruiker of de tenant die niet zijn opgenomen in het token wordt vanwege beperkingen grootte of toepasselijkheid standaard.  Dit is momenteel in openbare preview voor Azure AD-apps op de v1.0 en v2.0-eindpunten.  Zie de documentatie voor meer informatie over welke claims kunnen worden toegevoegd en hoe u uw toepassingsmanifest om aan te vragen deze te bewerken.  

Zie [optionele claims in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)voor meer informatie.
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD biedt ondersteuning voor PKCE voor veiliger OAuth-stromen

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Azure AD-documenten zijn bijgewerkt voor ondersteuning voor PKCE, waardoor meer beveiligde communicatie tijdens de stroom voor het verlenen van OAuth 2.0-autorisatiecode opmerking.  Zowel S256 als tekst zonder opmaak code_challenges worden ondersteund op de v1.0 en v2.0-eindpunten. 

Zie [een autorisatie code aanvragen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)voor meer informatie. 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Ondersteuning voor het inrichten van alle gebruiker kenmerkwaarden beschikbaar in de Workday Get_Workers-API

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
De openbare preview van inkomende inrichten van Workday naar Active Directory en Azure AD nu ondersteunt de mogelijkheid om op te halen en de inrichting van alle waarden van kenmerken beschikbaar in de Workday Get_Workers-API. Dit biedt ondersteuning voor honderden extra standard wordt toegevoegd en aangepaste kenmerken die verzonden met de eerste versie van de Workday inbound provisioning connector.

Zie [de lijst met gebruikers kenmerken van workday aanpassen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes) voor meer informatie.

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Groepslidmaatschap wijzigen van dynamisch naar statisch, en vice versa

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking
 
Het is mogelijk om te wijzigen hoe lidmaatschap in een groep wordt beheerd. Dit is handig als u behouden van de dezelfde naam en de ID in het systeem wilt, zodat alle bestaande verwijzingen naar de groep nog steeds geldig zijn. het maken van een nieuwe groep zorgde deze verwijzingen worden bijgewerkt.
Het Azure AD-beheercentrum ter ondersteuning van deze functionaliteit is bijgewerkt. Klanten kunnen bestaande groepen nu converteren van een dynamisch lidmaatschap toegewezen lidmaatschap en vice versa. De bestaande PowerShell-cmdlets zijn ook nog steeds beschikbaar.

Zie voor meer informatie [dynamische lidmaatschaps regels voor groepen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Verbeterde afmelding gedrag met naadloze eenmalige aanmelding

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Eerder, zelfs als gebruikers wordt expliciet afgemeld bij een toepassing die wordt beveiligd door Azure AD, ze zou worden automatisch aangemeld terug met behulp van naadloze eenmalige aanmelding als ze toegang probeert te krijgen van een Azure AD-toepassing opnieuw binnen het bedrijfsnetwerk vanaf hun apparaten toegevoegd aan een domein. Met deze wijziging, wordt Meld u af ondersteund.  Hiermee kunnen gebruikers kiezen voor de dezelfde of verschillende Azure AD-account aan te melden in met, in plaats van dat automatisch wordt aangemeld bij het gebruik van naadloze eenmalige aanmelding.

Zie [Azure Active Directory naadloze eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) voor meer informatie
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Application Proxy Connector versie 1.5.402.0 die zijn uitgebracht

**Type:** Gewijzigde functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De versie van deze connector wordt geleidelijk tot en met November wordt geïmplementeerd. Deze nieuwe versie van de connector bevat de volgende wijzigingen:

- De connector nu stelt domein niveau cookies in plaats daarvan subdomein niveau. Dit zorgt ervoor dat een soepeler SSO-ervaring en voorkomt u redundante verificatie wordt gevraagd.
- Ondersteuning voor gesegmenteerde codering aanvragen
- Verbeterde connector statuscontrole 
- Verschillende oplossingen voor problemen en stabiliteitsverbeteringen

Zie [Wat is Azure AD-toepassingsproxy-connectors](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)? voor meer informatie.
 
---

## <a name="february-2018"></a>Februari 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Verbeterde navigatie voor het beheren van gebruikers en groepen

**Type:** Plan voor wijziging  
**Service categorie:** Directory beheer  
**Product mogelijkheden:** Uitvoermap

De navigatie-ervaring voor het beheren van gebruikers en groepen is gestroomlijnd. U kunt nu rechtstreeks aan de lijst met alle gebruikers, met eenvoudiger toegang tot de lijst met verwijderde gebruikers uit de directory-overzicht navigeren. U kunt ook rechtstreeks aan de lijst met alle groepen, eenvoudiger toegang tot de beheerinstellingen van de groep uit de directory-overzicht navigeren. En ook de overzichtspagina voor de directory, u kunt zoeken naar een gebruiker, groep, bedrijfstoepassing of app-registratie. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Beschikbaarheid van aanmeldingen en audit rapporten in Microsoft Azure uitgevoerd door 21Vianet (Azure China 21Vianet)

**Type:** Nieuwe functie  
**Service categorie:** Azure Stack  
**Product mogelijkheden:** & Rapportage controleren

Azure AD-activiteit log-rapporten zijn nu beschikbaar in Microsoft Azure, bediend door 21Vianet (Azure China 21Vianet) exemplaren. De volgende logboeken zijn opgenomen:

- **Activiteiten logboeken voor aanmeldingen** : bevat alle aanmeld logboeken die zijn gekoppeld aan uw Tenant.

- **Audit logboeken voor selfservice wacht woorden** : bevat alle SSPR-controle Logboeken.

- **Controle logboeken voor Directory beheer** : bevat alle controle logboeken met betrekking tot Directory beheer, zoals gebruikers beheer, app-beheer en anderen.

Met deze logboeken krijgt u inzicht in hoe uw omgeving presteert. Met de gegevens kunt u het volgende doen:

- Bepalen hoe uw apps en services worden gebruikt door uw gebruikers.

- Oplossen van problemen te voorkomen dat uw gebruikers van het ontvangen van hun werk afmaken.

Zie [Azure Active Directory Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)(Engelstalig) voor meer informatie over het gebruik van deze rapporten.

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>'Rapportlezer'-rol (niet-beheerdersrol) gebruiken om Azure AD-activiteitenrapporten weer te geven

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Als onderdeel van feedback van klanten om in te schakelen van niet-beheerdersrollen toegang hebben tot Azure AD-activiteit zich aanmeldt, is voorzien van de mogelijkheid voor gebruikers die zich in de rol "Lezer rapport" voor toegang tot aanmeldingen en Audit-activiteit in de Azure-portal, evenals met onze Graph API's. 

Zie [Azure Active Directory Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)(Engelstalig) voor meer informatie over het gebruik van deze rapporten. 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-claim die beschikbaar zijn als gebruikerskenmerk en gebruikers-id

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
U kunt werk **nemers** configureren als de gebruikers-id en het gebruikers kenmerk voor leden gebruikers en B2B-gasten in op SAML gebaseerde aanmeldings toepassingen vanuit de gebruikers interface van de bedrijfs toepassing.

Zie voor meer informatie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Vereenvoudigd Toepassingsbeheer met jokertekens in Azure AD-toepassingsproxy

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Gebruikers verificatie
 
Implementatie van toepassingen te vereenvoudigen en reduceren uw administratieve overhead, ondersteunen we nu de mogelijkheid voor het publiceren van toepassingen die gebruikmaken van jokertekens. U kunt voor het publiceren van een jokertekentoepassing, volgt u de standard-toepassing publiceren stroom maar een jokerteken gebruiken in de interne en externe URL's.

Zie voor meer informatie [joker tekens toepassingen in de Azure Active Directory toepassings proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Er zijn nieuwe cmdlets voor de ondersteuning van de configuratie van de toepassingsproxy

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Onafhankelijk

De nieuwste versie van de Preview van AzureAD PowerShell-module bevat nieuwe cmdlets waarmee klanten het configureren van Application Proxy-toepassingen met behulp van PowerShell.

De nieuwe-cmdlets zijn: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Er zijn nieuwe cmdlets voor de ondersteuning van de configuratie van groepen

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Onafhankelijk

De nieuwste versie van de AzureAD PowerShell-module bevat cmdlets voor het beheren van groepen in Azure AD. Deze cmdlets waren eerder beschikbaar in de AzureADPreview-module en zijn nu toegevoegd aan de module AzureAD

De groep-cmdlets die nu release voor algemene beschikbaarheid zijn zijn: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Er is een nieuwe versie van Azure AD Connect beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** AD Sync  
**Product mogelijkheden:** Onafhankelijk
 
Azure AD Connect is de gewenste hulpprogramma voor het synchroniseren van gegevens tussen Azure AD en lokale gegevensbronnen, met inbegrip van Windows Server Active Directory en LDAP.

>[!Important]
>Deze versie introduceert schema en de synchronisatie regel wijzigingen. De Azure AD Connect-synchronisatieservice wordt een volledige importeerbewerking en een volledige synchronisatie stappen geactiveerd na een upgrade. Zie voor meer informatie over hoe u dit gedrag kunt wijzigen de [volledige synchronisatie uitstellen na de upgrade](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Deze release bevat de volgende updates en wijzigingen:

**Opgeloste problemen**

- Los tijdvenster op achtergrondtaken voor pagina partities filteren wanneer u overschakelt naar de volgende pagina.

- Een opgelost waardoor toegangsfout tijdens de aangepaste actie ConfigDB.

- Een opgelost om te herstellen van de time-out van de sql-verbinding.

- Een bug opgelost waarbij certificaten met jokertekens SAN controle van vereisten mislukt.

- Een opgelost die ervoor zorgt miiserver.exe crashes tijdens het exporteren van AAD-connector dat.

- Een bug opgelost waar een onjuist wachtwoord poging uitgevoerd veroorzaakt de AAD DC aangemeld verbinding maken met de wizard configuratie te wijzigen

**Nieuwe functies en verbeteringen**
 
- Toepassingstelemetrie - beheerders kunnen overschakelen naar deze klasse van gegevens in-of uitschakelen.

- Statusgegevens van Azure AD - beheerders moeten Ga naar de health-portal voor het beheren van de health-instellingen. Zodra de service-beleid is gewijzigd, wordt de agents lezen en deze toepassen.

- Apparaat terugschrijven Configuratieacties en een voortgangsbalk voor de initialisatie van de pagina toegevoegd.

- Verbeterde algemene diagnostische gegevens met HTML-rapport en de volledige gegevens verzamelen in een ZIP-tekst / HTML-rapport.

- Verbeterde betrouwbaarheid van Automatische upgrade en extra telemetrie om te controleren of de status van de server kan worden bepaald toegevoegd.

- Machtigingen die beschikbaar zijn beperkt tot beschermde accounts in AD Connector-account. Voor nieuwe installaties van de wizard beperkt de machtigingen die accounts met bevoegdheden hebben op de MSOL-account na het maken van de MSOL-account. De wijzigingen die invloed hebben op installaties van snelle en aangepaste installaties met account automatisch maken.

- Het installatieprogramma niet verplicht SA-bevoegdheden op een schone installatie van AADConnect gewijzigd.

- Nieuwe hulpprogramma voor het oplossen van synchronisatieproblemen met voor een specifiek object. Op dit moment controleert het hulpprogramma voor de volgende zaken:

    - UserPrincipalName komt niet overeen tussen gesynchroniseerde gebruikersobject en het gebruikersaccount in Azure AD-Tenant.
  
    - Als het object is uitgesloten voor synchronisatie vanwege domein filteren
  
    - Als het object is uitgesloten voor synchronisatie vanwege een organisatie-eenheid (OE) filteren

- Nieuwe hulpprogramma voor het synchroniseren van de huidige wachtwoord-hash die zijn opgeslagen in de on-premises Active Directory voor een specifiek gebruikersaccount. Het hulpprogramma is niet vereist voor een wachtwoordwijziging. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Toepassingen die Intune-app-beveiliging beleid ondersteunen dat is toegevoegd voor gebruik met voorwaardelijke toegang op basis van Azure AD-toepassing

**Type:** Gewijzigde functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Er zijn meer toepassingen toegevoegd die ondersteuning bieden voor voorwaardelijke toegang op basis van toepassingen. Nu krijgt u toegang tot Office 365 en andere Azure AD verbonden cloudapps met behulp van deze goedgekeurde client-apps.

De volgende toepassingen worden toegevoegd aan het einde van februari:

- Microsoft Power BI

- Microsoft Launcher

- Facturering van Microsoft

Ga voor meer informatie naar:

- [Vereiste voor client-app vereist](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Gebruiksvoorwaarden update voor de mobiele ervaring 

**Type:** Gewijzigde functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht

Wanneer de gebruiks voorwaarden worden weer gegeven, kunt u nu klikken op **problemen met weer gave? Klik hier**. Op deze koppeling te klikken, opent de gebruiksvoorwaarden zelf op uw apparaat. Ongeacht de tekengrootte in het document of het schermformaat van het apparaat, kunt u inzoomen en het document lezen. 

---
 
## <a name="january-2018"></a>Januari 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve Apps beschikbaar in Azure AD app-galerie 

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In januari 2018 zijn de volgende nieuwe apps met ondersteuning voor identiteitsfederatie in de app-galerie toegevoegd:

[IBM-Openpaginas](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust privacy management-software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory en [betrouw baarheid netvoor delen](https://go.microsoft.com/fwlink/?linkid=864701).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD. 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Meld u aan met extra risico gedetecteerd

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Het inzicht dat u krijgt bij een gedetecteerde risico detectie is gekoppeld aan uw Azure AD-abonnement. Met de Azure AD Premium P2-editie krijgt u de meest gedetailleerde informatie over alle onderliggende detecties.

Met de Azure AD Premium P1 Edition worden detecties die niet onder uw licentie vallen, weer gegeven als het aanmelden van de risico detectie met een extra risico gedetecteerd.

Zie [Azure Active Directory-risico detectie](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)voor meer informatie.
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-toepassingen uit de toegangsdeelvensters van de eindgebruiker verbergen

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** SSO

U kunt nu beter beheren hoe Office 365-toepassingen weergegeven op de toegangsdeelvensters van uw gebruikers via een nieuwe gebruikersinstelling. Deze optie is nuttig voor het verminderen van het aantal apps in de toegangsdeelvensters van een gebruiker als u liever alleen Office-apps weergeven in de Office-portal. De instelling bevindt zich in de **gebruikers instellingen** en is voorzien van een label, **gebruikers kunnen alleen Office 365-apps zien in de Office 365-Portal**.

Zie [een toepassing verbergen van gebruikers ervaring in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)voor meer informatie.

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Naadloze Meld u aan bij de apps die zijn ingeschakeld voor eenmalige aanmelding wachtwoord rechtstreeks vanuit de URL van app 

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** SSO

De Apps in mijn browser-extensie is nu beschikbaar via een handig hulpmiddel waarmee u beschikt over de mijn Apps van eenmalige aanmelding over mogelijkheid als snelkoppeling in uw browser. Na het installeren ziet van de gebruiker een pictogram wafel in hun browser waarmee ze snel toegang tot apps. Gebruikers kunnen nu profiteren van:

- De mogelijkheid rechtstreeks aanmelden bij wachtwoord-eenmalige aanmelding op basis van apps van de aanmeldingspagina van de app
- Een app met behulp van de functie snelle zoekactie starten
- Snelkoppelingen naar onlangs gebruikte apps van de extensie
- De uitbrei ding is beschikbaar voor micro soft Edge, Chrome en Firefox.
 
Zie voor meer informatie [mijn apps beveiligde aanmeldings extensie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Beheer van Azure AD-ervaring in de klassieke Azure Portal is buiten gebruik gesteld

**Type:** Keur   
**Service categorie:** Azure AD  
**Product mogelijkheden:** Uitvoermap

Vanaf 8 januari 2018, het beheer van Azure AD-ervaring in de klassieke Azure portal is buiten gebruik gesteld. Dit heeft plaatsgevonden in combinatie met het buiten gebruik stellen van de klassieke Azure portal zelf. In de toekomst moet u het [Azure AD-beheer centrum](https://aad.portal.azure.com) gebruiken voor al uw op portals gebaseerd beheer van Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>De PhoneFactor-web-portal is buiten gebruik gesteld

**Type:** Keur  
**Service categorie:** Azure AD  
**Product mogelijkheden:** Uitvoermap
 
Vanaf 8 januari 2018 de PhoneFactor-web-portal is buiten gebruik gesteld. Deze portal is gebruikt voor het beheer van MFA-server, maar deze functies zijn verplaatst naar de Azure-portal op portal.azure.com. 

De MFA-configuratie bevindt zich op: **Azure Active Directory \> MFA-server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Afschaffen van Azure AD-rapporten

**Type:** Keur  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** Beheer van identiteits levenscyclus  


Met de algemene beschikbaarheid van de nieuwe Azure Active Directory Administration console en de nieuwe API's nu beschikbaar voor zowel de activiteit en de beveiliging van rapporten, het rapport API's onder '/ rapporten'-eindpunt is buiten gebruik gesteld vanaf het einde van 31 December 2017.

**Wat is er beschikbaar?**

Als onderdeel van de overgang naar de nieuwe beheerconsole, 2 nieuwe API's beschikbaar gemaakt voor het ophalen van Azure AD-activiteitenlogboeken. De nieuwe set API's biedt uitgebreidere filteren en sorteren van functionaliteit naast het leveren van uitgebreide controle- en aanmeldingsactiviteiten. De gegevens die eerder via de beveiligings rapporten beschikbaar zijn, kunnen nu worden geopend via de API voor risico detectie van identiteits beveiliging in Microsoft Graph.

Ga voor meer informatie naar:

- [Aan de slag met de API voor Azure Active Directory rapportage](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Gebruiksvoorwaarden in het toegangsvenster

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht
 
U kunt nu gaat u naar het toegangsvenster en weergeven van de gebruiksvoorwaarden die u eerder hebt geaccepteerd.

Volg deze stappen:

1. Ga naar de [MyApps-Portal](https://myapps.microsoft.com)en meld u aan.

2. Selecteer uw naam in de rechter bovenhoek en selecteer vervolgens **profiel** in de lijst. 

3. Selecteer in uw **profiel** **gebruiks voorwaarden bekijken**. 

4. Nu kunt u de gebruiksvoorwaarden bekijken die u hebt geaccepteerd. 

Zie de [functie Azure AD-gebruiks voorwaarden (preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nieuwe Azure Active Directory-aanmeldingservaring

**Type:** Nieuwe functie  
**Service categorie:** Azure AD  
**Product mogelijkheden:** Gebruikers verificatie
 
De Azure AD en Microsoft-account-identiteitssysteem gebruikersinterfaces zijn opnieuw ontworpen zodat ze beschikken over een consistent uiterlijk geven. Bovendien verzamelt de Azure AD-aanmeldingspagina de naam van de gebruiker eerst, gevolgd door de referentie in een tweede scherm.

Zie voor meer informatie [de nieuwe Azure AD-aanmeldings ervaring is nu beschikbaar als open bare preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Minder prompts voor aanmelden: een nieuwe "aangemeld blijven'-ervaring voor aanmelding bij Azure AD

**Type:** Nieuwe functie  
**Service categorie:** Azure AD  
**Product mogelijkheden:** Gebruikers verificatie
 
Het selectie vakje **aangemeld blijven** op de aanmeldings pagina van Azure AD is vervangen door een nieuwe prompt die wordt weer gegeven nadat u zich hebt geverifieerd. 

Als u **Ja** op deze vraag reageert, geeft de service u een permanent vernieuwings token. Dit gedrag is hetzelfde als wanneer u het selectie vakje **aangemeld blijven** in de oude ervaring hebt ingeschakeld. Voor federatieve tenants ziet dit bericht nadat u met de federatieve service verifiëren.

Zie voor meer informatie [minder aanmeldings prompts: de nieuwe ervaring ' aangemeld blijven ' voor Azure AD is in Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Configuratie om te vereisen dat de gebruiksvoorwaarden worden uitgebreid alvorens deze te accepteren toevoegen

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht
 
Een optie voor beheerders moeten hun gebruikers om uit te breiden de gebruiksvoorwaarden voorafgaand aan de voorwaarden te accepteren.

Selecteer aan **of** **uit** om gebruikers te verplichten de gebruiks voorwaarden uit te breiden. Met de instelling **on** moeten gebruikers de gebruiks voorwaarden weer geven voordat ze deze kunnen accepteren.

Zie de [functie Azure AD-gebruiks voorwaarden (preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Scoped activering voor in aanmerking komende roltoewijzingen

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Roltoewijzingen in aanmerking komende Azure-resource met minder autonomie dan de oorspronkelijke toewijzing standaardwaarden activeren kunt u binnen het bereik activering. Een voorbeeld is als u als eigenaar van een abonnement in uw tenant toegewezen bent. Met een bereik-activering, kunt u de rol van eigenaar voor maximaal vijf resources die zich in het abonnement (zoals resourcegroepen en virtuele machines) activeren. Bereik van de activering kan de mogelijkheid van de uitvoering van ongewenste wijzigingen om essentiële Azure-resources te beperken.

Zie [Wat is Azure AD privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)voor meer informatie.
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nieuwe federatieve apps in de Azure AD-app-galerie

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In December 2017, hebben we de ondersteuning van deze nieuwe apps met Federatie naar onze app-galerie toegevoegd:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital Store](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CYBSAFE, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image Works](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [Mobile Iron Azure AD Integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO voor bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO voor bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD. 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Goedkeuringswerkstromen voor Azure AD-maprollen

**Type:** Gewijzigde functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Werkstroom voor goedkeuring voor Azure AD-maprollen is algemeen beschikbaar.

Met de werkstroom voor goedkeuring, bevoegde rol beheerders kunnen vereisen dat leden van een in aanmerking komende-rol op aanvraag voor rolactivering voordat ze de bevoorrechte rol kunnen gebruiken. Meerdere gebruikers en groepen kunnen worden gedelegeerd goedkeuring verantwoordelijkheden. Leden van een in aanmerking komende rol ontvangen meldingen wanneer goedkeuring is voltooid en hun rol actief is.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Pass through-verificatie: Skype voor bedrijven-ondersteuning

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Nu Pass through-verificatie ondersteunt gebruikersaanmeldingen tot Skype voor bedrijven-clienttoepassingen die ondersteuning bieden voor moderne verificatie, waaronder online en hybride topologieën. 

Zie voor meer informatie [Skype voor bedrijven-topologieën die worden ondersteund door moderne verificatie](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Updates voor Azure AD Privileged Identity Management voor Azure RBAC (preview)

**Type:** Gewijzigde functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Met het vernieuwen van de openbare preview van Azure AD Privileged Identity Management (PIM) voor op rollen gebaseerd toegangsbeheer (RBAC) kunt u nu:

* Just Enough Administration gebruiken.
* Goedkeuring van de resource-rollen activeren.
* Plan een toekomstige activering van een rol die moet worden goedgekeurd voor beide Azure AD en Azure RBAC-rollen.
 
Zie [privileged Identity Management voor Azure-resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)voor meer informatie.

---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control service buiten gebruik stellen

**Type:** Plan voor wijziging  
**Service categorie:** Access Control-service  
**Product mogelijkheden:** Access Control-service 

Azure Active Directory Access Control (ook wel bekend als de Access Control service) wordt in latere 2018 beëindigd. Meer informatie, waaronder gedetailleerde plan en hulp bij de migratie op hoog niveau zijn, beschikbaar in de komende weken. U kunt opmerkingen achterlaten op deze pagina met vragen over de Access Control-service en een teamlid zal deze beantwoorden.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Browsertoegang beperken tot de Intune Managed Browser 

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

U kunt browsertoegang tot Office 365 en andere Azure AD verbonden cloud-apps beperken met behulp van de Intune Managed Browser als een goedgekeurde app. 

U kunt nu de volgende voor waarde configureren voor voorwaardelijke toegang op basis van een toepassing:

**Client-apps:** Browser

**Wat is het effect van de wijziging?**

Vandaag de dag toegang geblokkeerd wanneer u deze voorwaarde gebruiken. Wanneer de Preview-versie beschikbaar is, wordt alle toegang tot het gebruik van de toepassing van de beheerde browser vereist. 

Zoeken naar deze mogelijkheid en meer informatie in toekomstige blogs en release-opmerkingen. 

Zie [voorwaardelijke toegang in azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)voor meer informatie.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

De volgende apps zijn te vinden op de lijst met [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Micro soft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Ga voor meer informatie naar:

- [Vereiste voor client-app vereist](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Gebruiksvoorwaarden van ondersteuning voor meerdere talen

**Type:** Nieuwe functie    
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht

Beheerders kunnen nu nieuwe gebruiksvoorwaarden die meerdere PDF-documenten bevatten maken. U kunt deze PDF-documenten met een bijbehorende taal kunt labelen. Gebruikers worden weergegeven van het PDF-bestand met de overeenkomende taal op basis van hun voorkeuren. Als er geen overeenkomst is, wordt de standaardtaal weergegeven.

---
 
### <a name="real-time-password-writeback-client-status"></a>Status van de client realtime wachtwoord terugschrijven

**Type:** Nieuwe functie  
**Service categorie:** Self-service voor wacht woord opnieuw instellen  
**Product mogelijkheden:** Gebruikers verificatie

U kunt nu de status van uw on-premises wachtwoord-Write-backclient bekijken. Deze optie is beschikbaar in de sectie **on-premises integratie** van de pagina [wacht woord opnieuw instellen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Als er problemen met de verbinding met uw on-premises Write-backclient zijn, ziet u een foutmelding krijgen dat beschikt u over:

- Informatie over waarom u geen verbinding met uw on-premises Write-backclient.
- Een koppeling naar de documentatie die u helpt bij het oplossen van het probleem. 

Zie [on-premises integratie](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)voor meer informatie.

---

### <a name="azure-ad-app-based-conditional-access"></a>Voorwaardelijke toegang op basis van Azure AD-app 
 
**Type:** Nieuwe functie  
**Service categorie:** Azure AD  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

U kunt nu de toegang tot Office 365 en andere met Azure AD verbonden Cloud-apps beperken tot [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) die ondersteuning bieden voor het intune-beveiligings beleid voor apps met behulp van [voorwaardelijke toegang op basis van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Intune beveiligingsbeleid voor apps worden gebruikt voor het configureren en beveiligen van bedrijfsgegevens op deze clienttoepassingen.

Door [app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) te combi neren met op [apparaten gebaseerd](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) beleid voor voorwaardelijke toegang, hebt u de flexibiliteit om gegevens te beveiligen voor persoonlijke en Bedrijfs apparaten.

De volgende voor waarden en besturings elementen zijn nu beschikbaar voor gebruik met voorwaardelijke toegang op basis van apps:

**Ondersteunde platform voorwaarde**

- iOS
- Android

**Voor waarde voor client-apps**

- Mobiele apps en bureaubladclients

**Toegangsbeheer**

- Goedgekeurde client-apps vereisen

Zie [voorwaardelijke toegang op basis van apps voor Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)voor meer informatie.
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-apparaten in de Azure-portal beheren

**Type:** Nieuwe functie  
**Service categorie:** Apparaatregistratie en-beheer  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

Nu kunt u uw apparaten die zijn verbonden met Azure AD en de activiteiten met betrekking tot apparaat op één plek. Er is een nieuwe beheerervaring voor het beheren van uw apparaat-id's en instellingen in de Azure portal. In deze release kunt u het volgende doen:

- Bekijk alle apparaten die beschikbaar zijn voor voorwaardelijke toegang in azure AD.
- Eigenschappen, waaronder uw hybride Azure AD gekoppelde apparaten.
- BitLocker-sleutels voor uw Azure AD join-apparaten zoeken, beheren van uw apparaat met Intune en meer.
- Apparaat met betrekking tot Azure AD-instellingen beheren.

Zie [apparaten beheren met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)voor meer informatie.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Ondersteuning voor macOS als een platform voor voorwaardelijke toegang van Azure AD 

**Type:** Nieuwe functie    
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging 

U kunt macOS in uw beleid voor voorwaardelijke toegang van Azure AD nu opnemen (of uitsluiten). Met de toevoeging van macOS om de ondersteunde apparaatplatformen, kunt u het volgende doen:

- **Registreer en beheer macOS-apparaten met behulp van intune.** Net als bij andere platformen, zoals iOS en Android, een bedrijf portal toepassing beschikbaar is voor macOS geïntegreerde inschrijvingen doen. De nieuwe bedrijfsportal-app voor macOS kunt u een apparaat met Intune inschrijven en Registreer het bij Azure AD.
- **Zorg ervoor dat macOS-apparaten voldoen aan het nalevings beleid van uw organisatie dat is gedefinieerd in intune.** In Intune in Azure portal kunt kunt u nu instellen nalevingsbeleid voor macOS-apparaten. 
- **Beperk de toegang tot toepassingen in azure AD tot alleen compatibele macOS-apparaten.** Het ontwerp van het beleid voor voorwaardelijke toegang heeft macOS als een afzonderlijke platform optie. Nu kunt u macOS-specifiek beleid voor voorwaardelijke toegang ontwerpen voor de doel toepassing die in Azure is ingesteld.

Ga voor meer informatie naar:

- [Een apparaatnalevingsbeleid maken voor macOS-apparaten in Intune](https://aka.ms/macoscompliancepolicy)
- [Voorwaardelijke toegang in azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Network Policy Server-extensie voor Azure multi-factor Authentication 

**Type:** Nieuwe functie    
**Service categorie:**  Multi-factor Authentication  
**Product mogelijkheden:** Gebruikers verificatie

De Network Policy Server-extensie voor Azure multi-factor Authentication wordt cloud-gebaseerde multi-factor Authentication-mogelijkheden toegevoegd aan uw infrastructuur voor verificatie met behulp van uw bestaande servers. Met de extensie voor Network Policy Server, kunt u telefonische oproepen, SMS-bericht of verificatie via de telefoon-app kunt toevoegen aan uw bestaande verificatiestroom. U hoeft niet te installeren, configureren en onderhouden van nieuwe servers. 

Deze extensie is bedoeld voor organisaties die verbindingen virtual private network beveiligen willen zonder de Azure multi-factor Authentication-Server te implementeren. De Network Policy Server-extensie als een adapter tussen RADIUS- en cloud-gebaseerde Azure multi-factor Authentication fungeert voor een tweede factor-verificatie voor federatieve of gesynchroniseerde gebruikers.

Zie [uw bestaande Network Policy Server-infra structuur integreren met Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)voor meer informatie.
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Herstellen of verwijderde gebruikers permanent verwijderen

**Type:** Nieuwe functie    
**Service categorie:** Gebruikers beheer  
**Product mogelijkheden:** Uitvoermap 

In het beheercentrum van Azure AD kunt u nu:

- Een verwijderde gebruiker herstellen. 
- Een gebruiker permanent verwijdert.

**Om het uit te proberen:**

1. Selecteer in het Azure AD-beheer centrum [alle gebruikers](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) in de sectie **beheren** . 

2. Selecteer **onlangs verwijderde gebruikers**in de lijst **weer geven** . 

3. Selecteer een of meer onlangs verwijderde gebruikers, en vervolgens ofwel herstellen of deze definitief verwijderen.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app
 
**Type:** Gewijzigde functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

De volgende apps zijn toegevoegd aan de lijst met [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Ga voor meer informatie naar:

- [Vereiste voor client-app vereist](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>De besturings elementen ' OR ' gebruiken in een beleid voor voorwaardelijke toegang 

**Type:** Gewijzigde functie    
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging
 
U kunt nu ' of ' (een van de geselecteerde besturings elementen vereisen) voor besturings elementen voor voorwaardelijke toegang gebruiken. U kunt deze functie gebruiken om beleid te maken met 'of' tussen de besturingselementen voor toegang. Bijvoorbeeld, kunt u deze functie om een beleid waarvoor een gebruiker zich aanmeldt met behulp van multi-factor Authentication 'of' op een compatibel apparaat te maken.

Zie [besturings elementen in voorwaardelijke toegang tot Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)voor meer informatie.
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregatie van real-time risico detecties

**Type:** Gewijzigde functie    
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

In Azure AD Identity Protection worden alle real-time-risico detecties die afkomstig zijn van hetzelfde IP-adres op een bepaalde dag, nu geaggregeerd voor elk type risico detectie. Met deze wijziging wordt het volume aan risico detecties beperkt zonder dat er wijzigingen in de gebruikers beveiliging worden weer gegeven.

De onderliggende realtime detectie werkt telkens wanneer de gebruiker zich aanmeldt. Als u een aanmeldingsrisico beveiligingsbeleid instellen voor multi-factor Authentication of de toegang blokkeert hebt, is het nog steeds geactiveerd tijdens elke riskante aanmelding.
 
---
 
## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Afschaffen van Azure AD-rapporten

**Type:** Plan voor wijziging  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** Beheer van identiteits levenscyclus  

De Azure-portal beschikt u over:

- Een nieuwe Azure AD-beheerconsole.
- Nieuwe API's voor rapporten van activiteit en beveiliging.
 
Vanwege deze nieuwe mogelijkheden, het rapport API's onder het eindpunt/Reports zijn buiten gebruik gesteld op 10 December 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detectie van het veld voor automatische aanmelding

**Type:** Vaste   
**Service categorie:** Mijn apps  
**Product mogelijkheden:** Eenmalige aanmelding  

Azure AD biedt ondersteuning voor detectie van het veld voor automatische aanmelding voor toepassingen die een gebruiker HTML-veld naam en het wachtwoord weergeven. Deze stappen worden beschreven in [het automatisch vastleggen van aanmeldings velden voor een toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). U kunt deze mogelijkheid vinden door een *niet-galerie* toepassing toe te voegen op de pagina **bedrijfs toepassingen** in de [Azure Portal](https://aad.portal.azure.com). Daarnaast kunt u de modus voor **eenmalige aanmelding** op deze nieuwe toepassing configureren voor **eenmalige aanmelding op basis van wacht woorden**, een web-URL opgeven en de pagina vervolgens opslaan.
 
Deze functionaliteit is vanwege een serviceprobleem tijdelijk uitgeschakeld. Het probleem is opgelost en de detectie van het veld voor automatische aanmelding weer beschikbaar is.

---

### <a name="new-multi-factor-authentication-features"></a>Nieuwe functies van multi-factor Authentication

**Type:** Nieuwe functie  
**Service categorie:** Multi-factor Authentication  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging  

Multi-factor authentication (MFA) is een essentieel onderdeel van uw organisatie te beschermen. Als u meer geavanceerde referenties en de ervaring biedt een naadloze ervaring, zijn de volgende functies toegevoegd: 

- Resultaten van de basis van meerdere factoren uitdaging zijn rechtstreeks geïntegreerd in het Azure AD aanmelden rapport, waaronder programmatische toegang tot de MFA-resultaten.
- De configuratie van MFA dieper is geïntegreerd in de configuratie van de Azure AD-ervaring in Azure portal.

Met deze preview-versie, MFA-beheer en rapportage een geïntegreerde deel uitmaken van de belangrijkste Azure AD-configuratie-ervaring. U kunt nu de portal beheerfunctionaliteit MFA in de Azure AD-ervaring beheren.

Zie [referentie voor MFA-rapportage in de Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)voor meer informatie. 

---

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

**Type:** Nieuwe functie  
**Service categorie:** Gebruiksvoorwaarden  
**Product mogelijkheden:** Acht  

U kunt Azure AD gebruiksrechtovereenkomst gebruiken om gegevens te presenteren, zoals relevante disclaimers voor juridische vereisten of nalevingsvereisten voor gebruikers.

U kunt Azure AD gebruiksrechtovereenkomst gebruiken in de volgende scenario's:

- Algemene gebruiksrechtovereenkomst voor alle gebruikers in uw organisatie
- Specifieke gebruiksrechtovereenkomst op basis van kenmerken van een gebruiker (bijvoorbeeld artsen versus verpleegkundigen) of binnenlandse versus werknemers, uitgevoerd door dynamische groepen
- Specifieke gebruiksrechtovereenkomst gebruiken voor toegang tot indrukwekkende business-apps, zoals Salesforce

Zie [gebruiks voorwaarden voor Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="enhancements-to-privileged-identity-management"></a>Verbeteringen voor Privileged Identity Management

**Type:** Nieuwe functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management  

U kunt met Azure AD Privileged Identity Management kunt beheren, controleren en toegang tot Azure-resources (preview) binnen uw organisatie te controleren:

- Abonnementen
- Resourcegroepen
- Virtuele machines 

Alle resources in Azure portal die gebruikmaken van de functionaliteit voor Azure RBAC kunnen profiteren van alle beveiligings- en functionaliteit voor het beheer die Azure AD Privileged Identity Management te bieden heeft.

Zie [privileged Identity Management voor Azure-resources](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)voor meer informatie.

---

### <a name="access-reviews"></a>Toegangsbeoordelingen

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Acht  

Organisaties kunnen toegangsbeoordelingen (preview) gebruiken op efficiënte wijze groepslidmaatschappen en toegang tot bedrijfstoepassingen beheren: 

- U kunt toegang voor gastgebruikers opnieuw certificeren door gebruik te maken van toegangsbeoordelingen van hun toegang tot toepassingen en lidmaatschappen van groepen. Revisoren kunnen efficiënt kunnen beslissen of het om toe te staan van gasten toegang op basis van de inzichten geleverd door de toegangsbeoordelingen blijvende.
- Met toegangsbeoordelingen kunt u de toegang van werknemers voor toepassingen en groepslidmaatschappen opnieuw certificeren.

U kunt de controles van toegangsbeoordelingen verzamelen in programma's die relevant zijn voor uw organisatie, om beoordelingen bij te houden voor naleving of risicogevoelige toepassingen.

Zie [Azure AD Access revisies](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)(Engelstalig) voor meer informatie.

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Toepassingen van derden van mijn Apps en het startprogramma voor Office 365 verbergen

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** Eenmalige aanmelding  

U kunt nu beter apps beheren die worden weer gegeven op de portals van uw gebruikers via een nieuwe eigenschap voor het verbergen van een **app** . U kunt apps om in gevallen waar de app-tegels voor back-end-services of dubbele tegels en overbodige items gebruikers-app voor ruimtevaartuigen weergegeven te verbergen. De wissel knop bevindt zich in de sectie **Eigenschappen** van de app van derden en is **zichtbaar voor de gebruiker?** U kunt ook een app via een programma via PowerShell verbergen. 

Zie [een toepassing van een derde partij verbergen in de gebruikers ervaring van Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)voor meer informatie. 


**Wat is er beschikbaar?**

 Als onderdeel van de overgang naar de nieuwe beheerconsole, twee nieuwe API's voor het ophalen van Azure AD-activiteit logboeken beschikbaar zijn. De nieuwe set API's biedt uitgebreidere filteren en sorteren van functionaliteit naast het leveren van uitgebreide controle- en aanmeldingsactiviteiten. De gegevens die eerder via de beveiligings rapporten beschikbaar zijn, kunnen worden geopend via de API voor risico detectie van identiteits beveiliging in Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix voor Identity Manager

**Type:** Gewijzigde functie  
**Service categorie:** Identiteits beheer  
**Product mogelijkheden:** Beheer van identiteits levenscyclus  

Een hotfix totaliseren-pakket (build 4.4.1642.0) is beschikbaar vanaf 25 September 2017 voor Identity Manager 2016 Service Pack 1. Dit pakket totaliseren:

- Lost problemen op en voegt verbeteringen toe.
- Is een cumulatieve update die wordt vervangen door alle Identity Manager 2016 Service Pack 1-updates maximaal build 4.4.1459.0 voor Identity Manager 2016. 
- Vereist dat u beschikt over Identity Manager 2016 4.4.1302.0 bouwen. 

Zie hotfixcombinatiepakket [(build 4.4.1642.0) is beschikbaar voor Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562)voor meer informatie. 

---
