---
title: Wat is nieuw? Releaseopmerkingen - Azure Active Directory | Microsoft Docs
description: Ontdek wat er nieuw in Azure Active Directory, zoals de meest recente release-opmerkingen worden bekende problemen, oplossingen voor problemen, afgeschafte functies en toekomstige wijzigingen.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93327091d0065a67d441dd56cadd83f4f7d2d1f2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970400"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` en te ![plakken: in uw](./media/whats-new/feed-icon-16x16.png) RSS feed reader pictogram feed lezer.

Azure AD ontvangt verbeteringen regelmatig. Als u wilt bijhouden met de meest recente ontwikkelingen, vindt in dit artikel u informatie over:

- De meest recente versies
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functies
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt, dus regelmatig bezoekt. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in het [Archief voor wat er nieuw is in azure Active Directory](whats-new-archive.md).

---

## <a name="august-2019"></a>2019 augustus

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET negeert de gedeelde cache van MSAL.NET voor namens een scenario

**Type:** Vast  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Verificatie van de gebruiker

Vanaf Azure AD Authentication Library (ADAL.NET) versie 5.0.0-Preview moeten app-ontwikkel aars [één cache per account serialiseren voor web-apps en Web-api's](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Anders is het mogelijk dat sommige scenario's die gebruikmaken van de [namen van de stroom](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), samen met een aantal `UserAssertion`specifieke gebruiks cases, een verhoogde bevoegdheid hebben. Om dit beveiligings probleem te voor komen, negeert ADAL.NET nu de micro soft-verificatie bibliotheek voor dotnet (MSAL.NET) gedeelde cache voor namens een scenario.

Zie [Azure Active Directory beveiligingslek met betrekking tot misbruik van bevoegdheden voor verificatie bibliotheek](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)voor meer informatie over dit probleem.

---

## <a name="july-2019"></a>2019 juli

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Wijziging plannen: Update van Application proxy-service om alleen TLS 1,2 te ondersteunen

**Type:** Plan voor wijziging  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Om u te helpen met onze krach tigste versleuteling, gaan we de toegang tot de toepassings proxy service beperken tot alleen TLS 1,2-protocollen. Deze beperking wordt eerst doorgevoerd naar klanten die al gebruikmaken van TLS 1,2-protocollen, zodat u de impact niet kunt zien. De afronding van de protocollen TLS 1,0 en TLS 1,1 is voltooid op 31 augustus 2019. Klanten die nog TLS 1,0 en TLS 1,1 gebruiken, ontvangen een geavanceerde kennisgeving om deze wijziging voor te bereiden.

Als u de verbinding met de service toepassings proxy tijdens deze wijziging wilt behouden, kunt u het beste controleren of uw combi Naties van client-server en browser-server zijn bijgewerkt voor het gebruik van TLS 1,2. We raden u ook aan om ervoor te zorgen dat alle client systemen die door uw werk nemers worden gebruikt, toegang hebben tot apps die zijn gepubliceerd via de Application proxy-service.

Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)voor meer informatie.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Wijziging plannen: Er zijn ontwerp updates beschikbaar voor de toepassings galerie

**Type:** Plan voor wijziging  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** SSO

Nieuwe wijzigingen in de gebruikers interface zijn afkomstig van het ontwerp van de **toevoegen vanuit het gebied Galerie** van de Blade **een toepassing toevoegen** . Met deze wijzigingen kunt u eenvoudig uw apps vinden die ondersteuning bieden voor automatische inrichting, OpenID Connect Connect, Security Assertion Markup Language (SAML) en een wacht woord voor eenmalige aanmelding (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Wijziging plannen: Het IP-adres van de MFA-server verwijderen uit het Office 365 IP-adres

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Het IP-adres van de MFA-server wordt verwijderd uit het [Office 365 IP-adres en de URL-webservice](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Als u momenteel afhankelijk bent van deze pagina's voor het bijwerken van uw firewall instellingen, moet u ervoor zorgen dat u ook de lijst met IP-adressen die worden beschreven in de sectie **vereisten van Azure multi-factor Authentication-Server-firewall** van het gedeelte aan de slag [ met het Azure Multi-Factor Authentication-server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) -artikel.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Voor alleen app-tokens moet de client-app bestaan in de resource Tenant

**Type:** Vast  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Verificatie van de gebruiker

Op 26 juli 2019 hebben we gewijzigd hoe we alleen app-tokens bieden via de [client referenties verlenen](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Voorheen konden apps tokens krijgen om andere apps aan te roepen, ongeacht of de client-app zich in de Tenant bevindt. We hebben dit gedrag bijgewerkt, waardoor enkele Tenant bronnen, ook wel web-Api's genoemd, alleen kunnen worden aangeroepen door client-apps die voor komen in de resource-Tenant.

Als uw app zich niet in de resource-Tenant bevindt, wordt er een fout bericht `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` weer gegeven waarin wordt vermeld, om dit probleem op te lossen, moet u de client app Service-Principal in de Tenant maken met behulp van het uitstemmings eindpunt van de [beheerder](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) of [via Power shell ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), waardoor uw Tenant toestemming heeft gegeven voor het uitvoeren van de app in de Tenant.

Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)voor meer informatie.

> [!NOTE]
> Bestaande toestemming tussen de client en de API blijft niet vereist. Apps moeten nog steeds hun eigen autorisatie controles uitvoeren.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nieuwe aanmelding zonder wacht woord voor Azure AD met behulp van FIDO2-beveiligings sleutels

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Verificatie van de gebruiker

Azure AD-klanten kunnen nu beleid instellen voor het beheren van FIDO2-beveiligings sleutels voor de gebruikers en groepen van hun organisatie. Eind gebruikers kunnen hun beveiligings sleutels ook zelf registreren, de sleutels gebruiken om zich aan te melden bij hun micro soft-accounts op websites, terwijl ze op FIDO apparaten werken, en zich aanmelden bij hun Azure AD-gekoppelde Windows 10-apparaten.

Zie voor meer informatie [aanmelden zonder wacht woord inschakelen voor Azure AD (preview)](/azure/active-directory/authentication/concept-authentication-passwordless) voor informatie die betrekking heeft op de beheerder en [Stel beveiligings informatie in voor het gebruik van een beveiligings sleutel (preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) voor informatie die aan de eind gebruiker is gerelateerd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-juli 2019

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** Integratie met toepassing van derden

In juli 2019 zijn deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [helder patroon Omnichannel contact centrum](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [slimme Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hele hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [schuine](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
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

Als u veel lijsten met IP-adressen en-bereiken niet meer wilt beheren, kunt u de nieuwe **AzureActiveDirectoryDomainServices** -netwerk servicetag gebruiken in uw Azure-netwerk beveiligings groep om inkomend verkeer naar uw Azure AD Domain Services virtuele server te beveiligen subnet van het netwerk.

Zie [netwerk beveiligings groepen voor Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group)voor meer informatie over deze nieuwe servicetag.

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
**Service categorie:** Selfservice voor wachtwoord opnieuw instellen  
**Product mogelijkheden:** & Rapportage controleren

Met de nieuwe verificatie methoden & Insights-rapporten kunt u beter begrijpen hoe functies zoals Azure Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden worden geregistreerd en gebruikt in uw organisatie, inclusief het aantal geregistreerde gebruikers voor elke functie, hoe vaak de selfservice voor wachtwoord herstel wordt gebruikt om wacht woorden opnieuw in te stellen, en op basis van welke methode het opnieuw instellen plaatsvindt.

Zie het gebruik van de [verificatie methoden & Insights (preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)voor meer informatie.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nieuwe beveiligings rapporten zijn beschikbaar voor alle Azure AD-beheerders (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Identity Protection  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Alle Azure AD-beheerders kunnen nu het vaandel selecteren boven aan bestaande beveiligings rapporten, zoals de gebruikers die zijn **gemarkeerd voor risico** rapport, om de nieuwe beveiligings ervaring te gebruiken, zoals wordt weer gegeven in de rapporten **Risk ante gebruikers** en **Risk ante** aanmeldingen. . Na verloop van tijd worden alle beveiligings rapporten van oudere versies naar de nieuwe versies verplaatst, met de nieuwe rapporten die u de volgende aanvullende mogelijkheden bieden:

- Geavanceerd filteren en sorteren

- Bulk acties, zoals het negeren van gebruikers Risico's

- Bevestiging van aangetast of veilige entiteiten

- Risico status, waaronder: Risico, genegeerd, hersteld en bevestigde schade

Zie rapport met [Risk ante gebruikers](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) en [rapporten met Risk ante](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)aanmeldingen voor meer informatie.

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
**Service categorie:** B2B  
**Product mogelijkheden:** B2B/B2C

Directe Federatie maakt het gemakkelijker voor u om te werken met partners waarvan de IT-beheerde identiteits oplossing niet Azure AD is, door te werken met identiteits systemen die ondersteuning bieden voor de SAML-of WS-voeder-standaarden. Nadat u een directe Federatie relatie met een partner hebt ingesteld, kan elke nieuwe gast gebruiker die u uit dat domein uitnodigt met behulp van hun bestaande organisatie account samen werken, waardoor de gebruikers ervaring voor uw gasten naadloos wordt.

Zie voor meer informatie [directe Federatie met AD FS en providers van derden voor gast gebruikers (preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
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
**Service categorie:** Groepsbeheer  
**Product mogelijkheden:** Samenwerking

Wanneer u nu een groeps naam vanuit de Azure AD-Portal maakt of bijwerkt, wordt er een controle uitgevoerd om te zien of u een bestaande groeps naam in uw resource dupliceert. Als we bepalen dat de naam al wordt gebruikt door een andere groep, wordt u gevraagd om uw naam te wijzigen.

Zie [groepen beheren in de Azure AD-Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)voor meer informatie.

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD biedt nu ondersteuning voor statische query parameters in antwoord-Uri's (redirect)

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Verificatie van de gebruiker

Azure AD-apps kunnen nu uri's voor beantwoorden (omleiden) registreren en gebruiken met statische query parameters ( `https://contoso.com/oauth2?idp=microsoft`bijvoorbeeld) voor OAuth 2,0-aanvragen. De statische query parameter is onderhevig aan de teken reeks die overeenkomt met antwoord-Uri's, net als elk ander deel van de antwoord-URI. Als er geen geregistreerde teken reeks is die overeenkomt met de door de URL gedecodeerde omleidings-URI, wordt de aanvraag geweigerd. Als de antwoord-URI wordt gevonden, wordt de gehele teken reeks gebruikt om de gebruiker te omleiden, inclusief de para meter static query.

Dynamische antwoord-Uri's zijn nog niet toegestaan omdat ze een beveiligings risico vertegenwoordigen en niet kunnen worden gebruikt om status informatie over een verificatie aanvraag te houden. Voor dit doel gebruikt u de `state` para meter.

Momenteel zijn de app-registratie schermen van de Azure Portal nog steeds blok keren query parameters. U kunt het app-manifest echter hand matig bewerken om query parameters toe te voegen en te testen in uw app. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)voor meer informatie.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Activiteiten Logboeken (MS Graph-Api's) voor Azure AD zijn nu beschikbaar via Power shell-cmdlets

**Type:** Nieuwe functie  
**Service categorie:** Rapportage  
**Product mogelijkheden:** & Rapportage controleren

Met trots kondigen we aan dat Azure AD-activiteiten Logboeken (rapporten over controles en aanmeldingen) nu beschikbaar zijn via de Azure AD Power shell-module. U kunt voorheen uw eigen scripts maken met behulp van MS Graph API-eind punten en nu hebben we die mogelijkheid tot Power shell-cmdlets uitgebreid.

Zie [Azure AD Power shell-cmdlets voor rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)voor meer informatie over het gebruik van deze cmdlets.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Bijgewerkte filter besturings elementen voor controle-en aanmeld Logboeken in azure AD

**Type:** Gewijzigde functie  
**Service categorie:** Rapportage  
**Product mogelijkheden:** & Rapportage controleren

De controle-en aanmeldings logboek rapporten zijn bijgewerkt, zodat u nu verschillende filters kunt Toep assen zonder ze als kolommen toe te voegen aan de rapport schermen. Daarnaast kunt u bepalen hoeveel filters u wilt weer geven op het scherm. Deze updates werken allemaal samen om uw rapporten gemakkelijker te kunnen lezen en zo meer te bereiken aan uw behoeften.

Zie [controle logboeken filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) en [aanmeldings activiteiten filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)voor meer informatie over deze updates.

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nieuwe riskDetections-API voor Microsoft Graph (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Identity Protection  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

We zijn blij met het aankondigen van de nieuwe riskDetections-API voor Microsoft Graph nu in open bare preview. U kunt deze nieuwe API gebruiken om een lijst weer te geven met de identiteits beveiliging van de gebruiker en de detectie van de aanmeldings Risico's van uw organisatie. U kunt deze API ook gebruiken om efficiënter een query uit te voeren op uw risico detecties, inclusief details over het detectie type, de status, het niveau en meer.

Zie de [referentie documentatie voor API voor risico detectie](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juni 2019

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** Integratie met toepassing van derden

In juni 2019 hebben we deze 22 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [helper helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager voor Oracle Retail merchandising, Oracle Access Manager voor Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS voor People Soft, Oracle IDCS for JD Edwards

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
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
**Product mogelijkheden:** Beheer van identiteitslevenscycli

We hebben de Azure AD-inrichtings ervaring bijgewerkt zodat er een nieuwe voortgangs balk wordt weer gegeven waarin u kunt zien hoe ver u het proces voor het inrichten van gebruikers hebt. Deze bijgewerkte ervaring bevat ook informatie over het aantal gebruikers dat is ingericht tijdens de huidige cyclus, en hoeveel gebruikers tot nu toe zijn ingericht.

Zie [de status van gebruikers inrichten controleren](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)voor meer informatie.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>De huis stijl van het bedrijf wordt nu weer gegeven bij afmelden en fout schermen

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Verificatie van de gebruiker

Azure AD is bijgewerkt, zodat de huis stijl van uw bedrijf nu wordt weer gegeven op de schermen voor afmelden en fout en op de aanmeldings pagina. U hoeft niets te doen om deze functie in te scha kelen. Azure AD maakt gewoon gebruik van de activa die u al hebt ingesteld in het **huis stijlgebied** van de Azure Portal.

Zie [huisstijl toevoegen aan de Azure Active Directory pagina's van uw organisatie](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)voor meer informatie over het instellen van de huis stijl van uw bedrijf.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>De Azure multi-factor Authentication-Server (MFA) is niet meer beschikbaar voor nieuwe implementaties

**Type:** Afgeschaft  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Met ingang van 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication in hun organisatie willen vereisen, moeten nu Azure Multi-Factor Authentication op basis van de Cloud gebruiken. Klanten die de MFA-server vóór 1 juli hebben geactiveerd, zien geen wijziging. U kunt nog steeds de nieuwste versie downloaden, toekomstige updates ophalen en activerings referenties genereren.

Zie [aan de slag met de Azure multi-factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)voor meer informatie. Zie [een Azure multi-factor Authentication-implementatie plannen in de Cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)voor meer informatie over Azure multi-factor Authentication in de Cloud.

---

## <a name="may-2019"></a>Mei 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Wijziging in service: Toekomstige ondersteuning voor alleen TLS 1,2-protocollen op de Application proxy-service

**Type:** Plan voor wijziging  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Om de beste versleuteling voor onze klanten te bieden, beperken we de toegang tot alleen TLS 1,2-protocollen op de Application proxy-service. Deze wijziging wordt geleidelijk doorgevoerd naar klanten die al alleen gebruikmaken van TLS 1,2-protocollen, zodat u geen wijzigingen ziet.

De afschaffing van TLS 1,0 en TLS 1,1 gebeurt op 31 augustus 2019, maar we bieden nog meer geavanceerde kennisgevingen, dus u hebt de tijd om deze wijziging voor te bereiden. Om deze wijziging voor te bereiden, moet u ervoor zorgen dat de combi Naties van client-server en browser server, inclusief alle clients die uw gebruikers gebruiken om toegang te krijgen tot apps die zijn gepubliceerd via toepassings proxy, worden bijgewerkt met het TLS 1,2-protocol om de verbinding met de toepassing te onderhouden Proxy service. Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)voor meer informatie.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Gebruik het rapport gebruik en inzichten om uw app-gerelateerde aanmeldings gegevens weer te geven

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu het rapport gebruik en inzichten gebruiken, dat zich bevindt in het gebied **bedrijfs toepassingen** van de Azure Portal, om een toepassings gerichte weer gave van uw aanmeldings gegevens te verkrijgen, met inbegrip van informatie over:

- Meestgebruikte apps voor uw organisatie

- Apps met de meeste mislukte aanmeldingen

- Meest voorkomende aanmeld fouten voor elke app

Zie voor meer informatie over deze functie [het rapport gebruik en inzichten in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatiseer uw gebruikers inrichting voor Cloud-apps met behulp van Azure AD

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
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
**Service categorie:** N/A  
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
**Product mogelijkheden:** Ontwikkelaarservaring

De nieuwe [app-registraties](https://aka.ms/appregistrations) -ervaring is nu in algemene Beschik baarheid. Deze nieuwe ervaring omvat alle belang rijke functies van de Azure Portal en de portal voor toepassings registratie en verbetert deze.

- **Betere app-beheer.** In plaats van uw apps te zien op verschillende portals, kunt u nu al uw apps op één locatie bekijken.

- **Vereenvoudigde registratie van de app.** Van de verbeterde navigatie-ervaring tot de uitgebreide mogelijkheden voor het selecteren van machtigingen is het nu eenvoudiger om uw apps te registreren en te beheren.

- **Meer gedetailleerde informatie.** U kunt meer informatie vinden over uw app, inclusief Quick start-gidsen en meer.

Zie voor meer informatie [micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) en de [app-registraties-ervaring is nu algemeen beschikbaar.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) aankondiging van blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nieuwe mogelijkheden die beschikbaar zijn in de Risk ante gebruikers-API voor identiteits beveiliging

**Type:** Nieuwe functie  
**Service categorie:** Identity Protection  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Het is blij dat u de Risk ante gebruikers API kunt gebruiken om de risico geschiedenis van gebruikers op te halen, Risk ante gebruikers te sluiten en gebruikers te bevestigen dat ze zijn aangetast. Met deze wijziging kunt u de risico status van uw gebruikers efficiënter bijwerken en inzicht krijgen in hun risico geschiedenis.

Zie de [naslag documentatie voor Risk ante gebruikers-API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-mei 2019

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** Integratie met toepassing van derden

In mei 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [real links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)unplayr, [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [marketo Sales](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)Outsystems, [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Werk plek](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), webmethodes [API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [eten](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Verbeterde functies voor het maken en beheren van groepen in de Azure AD-Portal

**Type:** Nieuwe functie  
**Service categorie:** Groepsbeheer  
**Product mogelijkheden:** Samenwerking

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
**Service categorie:** Groepsbeheer  
**Product mogelijkheden:** Samenwerking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)voor meer informatie.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Er zijn nu Microsoft Graph API-eind punten beschikbaar voor Azure AD-activiteiten Logboeken (algemene Beschik baarheid)

**Type:** Gewijzigde functie  
**Service categorie:** Rapportage  
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
**Service categorie:** Azure AD-identiteitsbeveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van de bijgewerkte functie Azure AD Identity Protection. Deze nieuwe functionaliteit helpt bij het aanduiden van ongebruikelijke gebruikers activiteit voor een specifieke gebruiker of activiteit die consistent is met bekende aanvals patronen gebaseerd op de interne en externe informatie bronnen van micro soft.

Voor meer informatie over de vernieuwde versie van Azure AD Identity Protection raadpleegt u de [vier belangrijkste uitbrei dingen van de Azure AD Identity Protection nu in de open bare preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) -blog staan en [wat Azure Active Directory Identity Protection (vernieuwd)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) Knowledge. Zie het artikel [Azure Active Directory Identity Protection risico gebeurtenissen](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) voor meer informatie over de detectie van Azure AD Threat Intelligence.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Het beheer van rechten van Azure AD is nu beschikbaar (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Identiteitsbeheer  
**Product mogelijkheden:** Identiteitsbeheer

Het beheer van rechten van Azure AD, nu in open bare preview, helpt klanten bij het delegeren van beheer van toegangs pakketten, waarmee wordt gedefinieerd hoe werk nemers en zakelijke partners toegang kunnen vragen, wie moet goed keuren en hoelang ze toegang hebben. Toegangs pakketten kunnen lidmaatschap beheren in azure AD en Office 365-groepen, roltoewijzingen in zakelijke toepassingen, en roltoewijzingen voor share point online-sites. Meer informatie over rechten beheer vindt u in het [overzicht van het beheer van rechten van Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Zie [Wat is Azure AD Identity governance?](../governance/identity-governance-overview.md)voor meer informatie over de breedte van Azure AD Identity governance functies, waaronder privileged Identity Management, toegangs beoordelingen en gebruiks voorwaarden.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Groepsbeheer  
**Product mogelijkheden:** Samenwerking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)voor meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar in Azure Monitor (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Rapportage  
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
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** Integratie met toepassing van derden

In april 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)NetScaler, [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Bank](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Knot CONNECT](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [ALIBABA Cloud (op rollen gebaseerde SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [Surveymonkey Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nieuwe toegangs beoordelingen frequentie optie en selectie van meerdere rollen

**Type:** Nieuwe functie  
**Service categorie:** Toegangsbeoordelingen  
**Product mogelijkheden:** Identiteitsbeheer

Met nieuwe updates in azure AD-toegangs beoordelingen kunt u het volgende doen:

- Wijzig de frequentie van uw toegangs beoordelingen ineen halfjaarlijkse, naast de eerder bestaande opties wekelijks, maandelijks, elk kwar taal en jaarlijks.

- Selecteer meerdere Azure AD-en Azure-resource rollen bij het maken van één toegangs beoordeling. In dit geval worden alle rollen met dezelfde instellingen ingesteld en worden alle revisoren tegelijk op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)voor meer informatie over het maken van een toegangs beoordeling.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail waarschuwings systeem (en) worden overgezet, zodat er nieuwe e-mail gegevens worden verzonden voor sommige klanten

**Type:** Gewijzigde functie  
**Service categorie:** AD Sync  
**Product mogelijkheden:** Platform

Azure AD Connect is bezig met het overstappen van onze e-mail waarschuwings systeem (s), waardoor sommige klanten een nieuwe e-mail afzender kunnen weer geven. Om dit op te lossen, `azure-noreply@microsoft.com` moet u toevoegen aan de acceptatie lijst van uw organisatie of u kunt geen belang rijke waarschuwingen ontvangen van uw Office 365, Azure of uw synchronisatie Services.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Wijzigingen in UPN-achtervoegsels zijn nu geslaagd tussen federatieve domeinen in Azure AD Connect

**Type:** Vast  
**Service categorie:** AD Sync  
**Product mogelijkheden:** Platform

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

Zie voor meer informatie over het instellen en beheren van uw apparaten en apps met behulp van voorwaardelijke toegang [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) en [goedgekeurde client-apps vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Voor meer informatie over het beheren van toegang met micro soft Edge met Microsoft Intune-beleid, Zie [Internet toegang beheren met een met Microsoft intune-beleid beveiligde browser](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>2019 maart

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
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** Integratie met toepassing van derden

In 2019 maart hebben we deze 14 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [Medius flow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [controle systeem op basis van uitleg](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool prestatie kwesties](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Iris intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [taa](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nieuwe Zscaler-en Atlassian-inrichtings connectors in de Azure AD Gallery-maart 2019

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Integratie met toepassing van derden

Automatisch maken, bijwerken en verwijderen van gebruikers accounts voor de volgende apps:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler twee](https://aka.ms/ZscalerTwoProvisioning), [Zscaler drie](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Uw verwijderde Office 365-groepen herstellen en beheren in de Azure AD-Portal

**Type:** Nieuwe functie  
**Service categorie:** Groepsbeheer  
**Product mogelijkheden:** Samenwerking

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
**Product mogelijkheden:** Verificatie van de gebruiker

Client-apps kunnen honderden dezelfde aanmeldings aanvragen binnen korte tijd onjuist uitgeven. Deze aanvragen, ongeacht of ze wel of niet zijn gelukt, nemen alle bijdragen aan een slechte gebruikers ervaring en verhoogde workloads voor de IDP, verg root de latentie voor alle gebruikers en verminderen de beschik baarheid van de IDP.

Deze update verzendt een `invalid_grant` fout melding `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` : voor client-apps die dubbele aanvragen meerdere keren worden uitgevoerd gedurende een korte periode, buiten het bereik van de normale werking. Client-apps die dit probleem ondervinden, moeten een interactieve prompt weer geven, waarbij de gebruiker zich opnieuw moet aanmelden. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)voor meer informatie over deze wijziging en hoe u uw app kunt herstellen als deze fout optreedt.

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nieuwe audit logboeken gebruikers ervaring nu beschikbaar

**Type:** Gewijzigde functie  
**Service categorie:** Rapportage  
**Product mogelijkheden:** & Rapportage controleren

We hebben een nieuwe Azure AD- **controle logboek** pagina gemaakt waarmee u zowel de Lees baarheid als de manier waarop u naar uw gegevens zoekt, kunt verbeteren. Als u de nieuwe pagina **controle logboeken** wilt weer geven, selecteert u **controle logboeken** in het gedeelte **activiteit** van Azure AD.

![Nieuwe pagina controle logboeken, met voorbeeld gegevens](media/whats-new/audit-logs-page.png)

Zie voor meer informatie over de nieuwe pagina **controle** logboeken [activiteiten controleren in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

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
**Service categorie:** Rapportage  
**Product mogelijkheden:** & Rapportage controleren

U kunt nu grote hoeveel heden activiteiten logboeken rechtstreeks downloaden vanuit het Azure Portal. Met deze update kunt u het volgende doen:

- Down load Maxi maal 250.000 rijen.

- Ontvang een melding wanneer de down load is voltooid.

- Pas de bestands naam aan.

- Bepaal de indeling van de uitvoer, ofwel JSON of CSV.

Voor meer informatie over deze functie raadpleegt [u Quick Start: Een controle rapport downloaden met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Wijziging die fouten veroorzaakt: Updates voor evaluatie van voor waarden door Exchange ActiveSync (EAS)

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Access Control

We zijn bezig met het bijwerken van de manier waarop Exchange ActiveSync (EAS) de volgende voor waarden evalueert:

- Gebruikers locatie, op basis van land, regio of IP-adres

- Aanmeldings risico

- Apparaatplatform

Als u deze voor waarden in uw beleid voor voorwaardelijke toegang eerder hebt gebruikt, is het mogelijk dat het probleem van de voor waarde wordt gewijzigd. Als u bijvoorbeeld eerder de voor waarde voor de gebruikers locatie in een beleid hebt gebruikt, kan het beleid worden overgeslagen op basis van de locatie van uw gebruiker.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configureerbaar Azure AD SAML-token versleuteling (open bare preview) 

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** SSO

U kunt nu elke ondersteunde SAML-app configureren voor het ontvangen van versleutelde SAML-tokens. Wanneer Azure AD is geconfigureerd en gebruikt met een app, worden de verzonden SAML-bevestigingen versleuteld met behulp van een open bare sleutel die is verkregen van een certificaat dat is opgeslagen in azure AD.

Zie [Azure AD SAML-token versleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie over het configureren van uw SAML-token versleuteling.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Een toegangs beoordeling maken voor groepen of apps met behulp van Azure AD-toegangs beoordelingen

**Type:** Nieuwe functie  
**Service categorie:** Toegangsbeoordelingen  
**Product mogelijkheden:** Beheer

U kunt nu meerdere groepen of apps opnemen in één Azure AD-toegangs beoordeling voor groepslid maatschap of app-toewijzing. Toegangs beoordelingen met meerdere groepen of apps worden ingesteld met dezelfde instellingen en alle opgenomen revisoren worden tegelijkertijd op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) voor meer informatie over hoe u een toegangs beoordeling maakt met behulp van Azure AD-toegangs beoordelingen

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-februari 2019

**Type:** Nieuwe functie  
**Service categorie:** Bedrijfsapps  
**Product mogelijkheden:** Integratie met toepassing van derden
 
In februari 2019 zijn deze 27 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Euromonitor Pass Port](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), Port [stack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Permission click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisch](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [delen van een droom](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), webmethodes [integratie Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [kennis Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [ou campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Peri Scope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud door Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Uitgebreide registratie van gecombineerde MFA/SSPR

**Type:** Gewijzigde functie  
**Service categorie:** Selfservice voor wachtwoord opnieuw instellen  
**Product mogelijkheden:** Verificatie van de gebruiker

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
