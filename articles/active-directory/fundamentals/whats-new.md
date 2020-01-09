---
title: Wat is er nieuw? Releaseopmerkingen - Azure Active Directory | Microsoft Docs
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
ms.date: 12/10/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d55cb4667c846b179caef6c2819a08f4e9fe2ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422743"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` in uw ![pictogram RSS-feed lezer](./media/whats-new/feed-icon-16x16.png) feed-lezer.

Azure AD ontvangt verbeteringen regelmatig. Dit artikel bevat informatie over het volgende om op de hoogte te blijven van de meest recente ontwikkelingen:

- De meest recente versies
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functies
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt, dus regelmatig bezoekt. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in het [Archief voor wat er nieuw is in azure Active Directory](whats-new-archive.md).

---
## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors-inrichting integreren in azure AD en on-premises AD (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** App-inrichting  
**Product-mogelijkheid:** beheer van Identiteitslevenscycli

U kunt SAP SuccessFactors nu integreren als een gezaghebbende identiteits bron in azure AD. Deze integratie helpt u bij het automatiseren van de end-to-end identiteits levenscyclus, met inbegrip van HR-gebeurtenissen, zoals nieuwe mede werkers of beëindigingen, voor het beheren van de inrichting van Azure AD-accounts.

Zie de zelf studie [SAP SuccessFactors Automatic Provisioning configureren](https://aka.ms/SAPSuccessFactorsInboundTutorial) voor meer informatie over het instellen van SAP SuccessFactors inkomend inrichten voor Azure AD.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Ondersteuning voor aangepaste e-mail berichten in Azure AD B2C (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** B2C - identiteitsbeheer van consumenten  
**Product-mogelijkheid:** B2B/B2C

U kunt nu Azure AD B2C gebruiken om aangepaste e-mail berichten te maken wanneer uw gebruikers zich aanmelden om uw apps te gebruiken. Met behulp van DisplayControls (momenteel in Preview) en een e-mail provider van derden (zoals, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)of een aangepaste rest API), kunt u uw eigen e-mail sjabloon gebruiken, **van** adres en onderwerps tekst, maar ook lokalisatie en aangepaste otp-instellingen (eenmalig wacht woord) ondersteunen.

Zie [aangepaste e-mail verificatie in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)voor meer informatie.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Vervanging van basislijn beleid met standaard instellingen voor beveiliging

**Type:** gewijzigde functie  
**Service-categorie:** andere  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

Als onderdeel van een beveiligd model voor verificatie, verwijderen we de bestaande Baseline-beveiligings beleidsregels van alle tenants. Deze verwijdering is bedoeld om aan het einde van februari te worden voltooid. De vervanging voor dit basis beveiligings beleid is de standaard instellingen voor beveiliging. Als u Baseline-beveiligings beleidsregels hebt gebruikt, moet u plannen om over te scha kelen naar het nieuwe beleid voor beveiligings standaarden of naar voorwaardelijke toegang. Als u dit beleid nog niet hebt gebruikt, hoeft u geen actie te ondernemen.

Zie wat zijn de standaard beveiligings [instellingen?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) voor meer informatie over de nieuwe standaard waarden voor de beveiliging. Zie [common Conditional Access policies](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)(Engelstalig) voor meer informatie over beleids regels voor voorwaardelijke toegang.

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor het kenmerk SameSite en Chrome 80

**Type:** plannen voor wijziging  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** gebruikersverificatie

Als onderdeel van een beveiligd standaard model voor cookies wordt de Chrome 80-browser gewijzigd hoe cookies worden behandeld zonder het kenmerk `SameSite`. Elke cookie waarbij het kenmerk `SameSite` niet wordt opgegeven, wordt behandeld alsof het is ingesteld op `SameSite=Lax`, wat betekent dat Chrome bepaalde scenario's voor het delen van cookies met meerdere domeinen blokkeert waarvan uw app afhankelijk is. Als u het oudere Chrome-gedrag wilt behouden, kunt u het kenmerk `SameSite=None` gebruiken en een extra `Secure` kenmerk toevoegen, zodat cookies voor meerdere locaties alleen toegankelijk zijn via HTTPS-verbindingen. Chrome is gepland om deze wijziging te volt ooien op 4 februari 2020.

We raden aan dat alle ontwikkel aars hun apps testen met behulp van deze richt lijnen:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- Stel de standaard waarde voor het kenmerk **SameSite** in op **geen**.

- Voeg een extra `SameSite` kenmerk van **veilig**toe.

Zie voor meer informatie [aanstaande SameSite cookie wijzigingen in ASP.net en ASP.net core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) en [mogelijke onderbreking van klant websites en micro soft-producten en-services in Chrome versie 79 en hoger](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nieuwe hotfix voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type:** vast  
**Service categorie:** Microsoft Identity Manager  
**Product-mogelijkheid:** beheer van Identiteitslevenscycli

Er is een hotfixcombinatiepakket (build 4.6.34.0) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Met dit pakket Rollup worden problemen opgelost en worden verbeteringen toegevoegd die worden beschreven in de sectie ' problemen opgelost en verbeteringen toegevoegd in deze update '.

Voor meer informatie over het downloaden van het hotfix-pakket raadpleegt u het [Update pakket voor Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) beschikbaar](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nieuw AD FS app-activiteiten rapport voor het migreren van apps naar Azure AD (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** eenmalige aanmelding

Gebruik het nieuwe Active Directory Federation Services (AD FS) app-activiteiten rapport in de Azure Portal om te bepalen welke van uw apps kunnen worden gemigreerd naar Azure AD. In het rapport worden alle AD FS-apps beoordeeld op compatibiliteit met Azure AD, wordt er gecontroleerd of er problemen zijn en vindt u hulp bij het voorbereiden van afzonderlijke apps voor migratie.

Zie [het rapport AD FS toepassings activiteit gebruiken om toepassingen te migreren naar Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)voor meer informatie.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nieuwe werk stroom voor gebruikers om toestemming van de beheerder aan te vragen (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** Access Control

De nieuwe beheerder van de beheerders toestemming biedt beheerders een manier om toegang te verlenen tot apps waarvoor beheerder goed keuring is vereist. Als een gebruiker toegang probeert te krijgen tot een app, maar geen toestemming kan geven, kan deze nu een aanvraag indienen voor goed keuring door de beheerder. De aanvraag wordt per e-mail verzonden en in een wachtrij geplaatst die toegankelijk is vanaf de Azure Portal, naar alle beheerders die zijn aangewezen als controleurs. Nadat een revisor actie ondervindt voor een aanvraag in behandeling, worden de aanvragen van de gebruiker op de hoogte gesteld van de actie.

Zie [de beheerder toestemming werk stroom configureren (preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)voor meer informatie.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nieuwe Azure AD-app registraties de token configuratie-ervaring voor het beheren van optionele claims (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** andere  
**Product-mogelijkheid:** ervaring voor ontwikkelaars

Op de Blade nieuw **Azure AD-App registraties voor token configuratie** op de Azure Portal worden nu app-ontwikkel aars een dynamische lijst met optionele claims voor hun apps weer gegeven. Deze nieuwe ervaring helpt bij het stroom lijnen van Azure AD-App-migraties en voor het minimaliseren van de configuratie van de optionele claims.

Zie [optionele claims voor uw Azure AD-app bieden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)voor meer informatie.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nieuwe goedkeurings werk stroom voor twee fases in azure AD-rechts beheer (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** andere  
**Product mogelijkheden:** Beheer rechten

We hebben een nieuwe goedkeurings werk stroom in twee fasen geïntroduceerd waarmee u twee goed keurders in staat stelt om de aanvraag van een gebruiker goed te keuren voor een toegangs pakket. U kunt dit bijvoorbeeld instellen zodat de Manager van de gebruiker die de aanvraag heeft ingediend eerst moet goed keuren, en u kunt ook een resource-eigenaar verplichten om goed te keuren. Als een van de goed keurders niet goed keuren, wordt geen toegang verleend.

Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket in azure AD-rechts beheer](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)voor meer informatie.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Updates voor de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** mijn Apps  
**Product-mogelijkheid:** 3e partij-integratie

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de vernieuwde mijn apps-ervaring weer geven en openen. Deze nieuwe ervaring omvat ook de functie nieuwe werk ruimten, waarmee uw gebruikers gemakkelijker apps kunnen vinden en indelen.

Zie [werk ruimten maken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)voor meer informatie over de nieuwe ervaring voor het gebruik van mijn apps en het maken van werk ruimten.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Ondersteuning voor Google-sociale ID voor Azure AD B2B-samen werking (algemene Beschik baarheid)

**Type:** nieuwe functie  
**Service-categorie:** B2B  
**Product-mogelijkheid:** gebruikersverificatie

Nieuwe ondersteuning voor het gebruik van Google-sociale Id's (Gmail-accounts) in azure AD helpt om samen werking eenvoudiger te maken voor uw gebruikers en partners. Het is niet meer nodig om uw partners om een nieuw micro soft-specifiek account te maken en te beheren. Micro soft teams biedt nu volledige ondersteuning voor Google-gebruikers op alle clients en in de gemeen schappelijke en Tenant-gerelateerde verificatie-eind punten.

Zie [Google als id-provider voor B2B-gast gebruikers toevoegen](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)voor meer informatie.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Mobiele ondersteuning van micro soft Edge voor voorwaardelijke toegang en eenmalige aanmelding (algemene Beschik baarheid)

**Type:** nieuwe functie  
**Service-categorie:** voorwaardelijke toegang  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Azure AD voor micro soft Edge op iOS en Android biedt nu ondersteuning voor eenmalige aanmelding en voorwaardelijke toegang van Azure AD:

- Eenmalige **aanmelding voor micro soft Edge (SSO):** Eenmalige aanmelding is nu beschikbaar via systeem eigen clients (zoals micro soft Outlook en micro soft Edge) voor alle apps die zijn verbonden met Azure AD.

- **Voorwaardelijke toegang voor micro soft Edge:** Met behulp van beleid voor voorwaardelijke toegang op basis van toepassingen moeten uw gebruikers gebruikmaken van Microsoft Intune beveiligde browsers, zoals micro soft Edge.

Voor meer informatie over voorwaardelijke toegang en eenmalige aanmelding met micro soft Edge raadpleegt u het blog bericht [mobiele ondersteuning voor voorwaardelijke toegang en eenmalige aanmelding die nu algemeen beschikbaar is](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Voor meer informatie over het instellen van uw client-apps met behulp [van voorwaardelijke toegang op basis van apps](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) of [voorwaardelijke toegang op basis](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)van een apparaat, raadpleegt [u Web access beheren met een met Microsoft intune-beleid beveiligde browser](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Beheer van rechten van Azure AD (algemene Beschik baarheid)

**Type:** nieuwe functie  
**Service-categorie:** andere  
**Product mogelijkheden:** Beheer rechten

Het beheer van rechten van Azure AD is een nieuwe functie voor Identity governance, waarmee organisaties de levens cyclus van identiteiten en toegang op schaal kunnen beheren. Deze nieuwe functie helpt u bij het automatiseren van werk stromen voor toegangs aanvragen, toegangs toewijzingen, beoordelingen en verloop tijd voor groepen, apps en share point online-sites.

Met het beheer van rechten van Azure AD kunt u de toegang efficiënter beheren voor werk nemers en voor gebruikers buiten uw organisatie die toegang nodig hebben tot deze resources.

Zie [Wat is Azure AD-rechts beheer?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) voor meer informatie.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** 3e partij-integratie  

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

[SAP Cloud platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-november 2019

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** 3e partij-integratie

In november 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [blauw toegang voor leden (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal single sign on (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [FOKO Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets idee & innovatie beheer](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope gebruikers verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [Jisc student gevoel registratie](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nieuwe en verbeterde Azure AD-toepassings galerie

**Type:** gewijzigde functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** eenmalige aanmelding

We hebben de Azure AD-toepassings galerie bijgewerkt zodat u gemakkelijk vooraf geïntegreerde apps kunt vinden die ondersteuning bieden voor inrichting, OpenID Connect Connect en SAML op uw Azure Active Directory-Tenant.

Zie [een toepassing toevoegen aan uw Azure Active Directory-Tenant](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)voor meer informatie.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>De lengte limiet van de functie definitie van de app-rol is verhoogd van 120 tot 240 tekens

**Type:** gewijzigde functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** eenmalige aanmelding

We hebben van klanten gehoord dat de lengte limiet voor de definitie waarde van de app-functie in sommige apps en services te kort is om 120 tekens. Als antwoord hebben we de maximale lengte van de roldefinitie-waarde verhoogd tot 240 tekens.

Zie [app-functies toevoegen in uw toepassing en ontvangen in het token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)voor meer informatie over het gebruik van toepassingsspecifieke roldefinities.

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Afschaffing van de identityRiskEvent-API voor detectie van Azure AD Identity Protection-Risico's  

**Type:** plannen voor wijziging  
**Service-categorie:** Identity Protection  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Als reactie op de feedback van ontwikkel aars kunnen Azure AD Premium P2-abonnees nu complexe query's uitvoeren op Azure AD Identity Protection risico detectie gegevens door gebruik te maken van de nieuwe riskDetection-API voor Microsoft Graph. De bestaande [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API bèta versie retourneert geen gegevens over **10 januari 2020**. Als uw organisatie gebruikmaakt van de identityRiskEvent-API, moet u overstappen naar de nieuwe riskDetection-API.

Zie de [referentie documentatie voor API voor risico detectie](https://aka.ms/RiskDetectionsAPI)voor meer informatie over de nieuwe RISKDETECTION-API.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor toepassings proxy voor het kenmerk SameSite en Chrome 80

**Type:** plannen voor wijziging  
**Service-categorie:** App Proxy  
**Product-mogelijkheid:** Access Control

Een paar weken voorafgaand aan de Chrome 80-browser release, willen we bijwerken hoe cookies van toepassings proxy het kenmerk **SameSite** behandelen. Met de release van Chrome 80 wordt elke cookie waarvoor het kenmerk **SameSite** niet wordt opgegeven, behandeld alsof het is ingesteld op `SameSite=Lax`.

Om te voor komen dat mogelijk negatieve gevolgen zijn door deze wijziging, worden de toegangs-en sessie cookies van de toepassings proxy bijgewerkt door:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- De standaard waarde voor het kenmerk **SameSite** in te stellen op **geen**.

    >[!NOTE]
    > Application proxy-toegangs cookies zijn altijd uitsluitend via beveiligde kanalen verzonden. Deze wijzigingen zijn alleen van toepassing op sessie cookies.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)voor meer informatie over de cookie-instellingen van de toepassings proxy.

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>App-registraties (verouderd) en geconvergeerde app-beheer vanuit de portal voor toepassings registratie (apps.dev.microsoft.com) is niet langer beschikbaar

**Type:** plannen voor wijziging  
**Service-categorie:** N.V.T.  
**Product-mogelijkheid:** ervaring voor ontwikkelaars

In de nabije toekomst kunnen gebruikers met Azure AD-accounts geen geconvergeerde toepassingen meer registreren en beheren met behulp van de portal voor toepassings registratie (apps.dev.microsoft.com) of de toepassingen registreren en beheren in de App-registraties (verouderd) ervaring in de Azure Portal.

Raadpleeg voor meer informatie over de nieuwe App-registraties-ervaring de [app-registraties in de hand leiding voor Azure Portal training](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers hoeven zich niet langer opnieuw te registreren tijdens de migratie van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

**Type:** vast  
**Service categorie:** MFA  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Er is een bekend probleem opgelost waarbij gebruikers zich opnieuw moeten registreren als ze zijn uitgeschakeld voor per gebruiker-Multi-Factor Authentication (MFA) en vervolgens via een beleid voor voorwaardelijke toegang zijn ingeschakeld voor MFA.

Als u wilt dat gebruikers zich opnieuw moeten registreren, kunt u de optie voor het **opnieuw registreren van MFA** selecteren uit de verificatie methoden van de gebruiker in de Azure AD-Portal. Voor meer informatie over het migreren van gebruikers van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang, Zie [gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nieuwe mogelijkheden voor het transformeren en verzenden van claims in uw SAML-token

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** eenmalige aanmelding

We hebben extra mogelijkheden toegevoegd waarmee u claims kunt aanpassen en verzenden in uw SAML-token. Deze nieuwe mogelijkheden zijn onder andere:

- Extra functies voor het transformeren van claims, waarmee u de waarde die u in de claim verzendt, kunt wijzigen.

- De mogelijkheid om meerdere trans formaties op één claim toe te passen.

- De mogelijkheid om de claim bron op te geven op basis van het gebruikers type en de groep waartoe de gebruiker behoort.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie over deze nieuwe mogelijkheden, inclusief hoe u deze kunt gebruiken.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Pagina nieuwe mijn aanmeldingen voor eind gebruikers in azure AD

**Type:** nieuwe functie  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** controleren en rapporteren

We hebben een nieuwe pagina voor **mijn aanmeldingen** toegevoegd (https://mysignins.microsoft.com) zodat de gebruikers van uw organisatie hun recente aanmeldings geschiedenis kunnen bekijken om te controleren of er ongebruikelijke activiteiten zijn. Met deze nieuwe pagina kunnen uw gebruikers het volgende zien:

- Als iemand probeert hun wacht woord te raden.

- Als een aanvaller zich heeft aangemeld bij hun account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

Zie voor meer informatie de [gebruikers kunnen nu hun aanmeldings geschiedenis controleren op blog van ongebruikelijke activiteiten](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migratie van Azure AD Domain Services (Azure AD DS) van het klassieke naar Azure Resource Manager virtuele netwerken

**Type:** nieuwe functie  
**Service-categorie:** Azure AD Domain Services  
**Product-mogelijkheid:** Azure AD Domain Services

Voor onze klanten die zijn vastgelopen op klassieke virtuele netwerken, hebben we een geweldig nieuws voor u. U kunt nu een eenmalige migratie uitvoeren van een klassiek virtueel netwerk naar een bestaand virtueel netwerk van Resource Manager. Nadat u bent overgestapt op het virtuele netwerk van Resource Manager, kunt u profiteren van de aanvullende en bijgewerkte functies, zoals een verfijnd wachtwoord beleid, e-mail meldingen en audit Logboeken.

Zie [Preview-Azure AD Domain Services migreren van het klassieke virtuele netwerk naar Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)voor meer informatie.

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates voor de indeling van het Azure AD B2C-pagina contract

**Type:** nieuwe functie  
**Service-categorie:** B2C - identiteitsbeheer van consumenten  
**Product-mogelijkheid:** B2B/B2C

We hebben een aantal nieuwe wijzigingen geïntroduceerd in versie 1.2.0 van het pagina contract voor Azure AD B2C. In deze bijgewerkte versie kunt u nu de laad volgorde voor uw elementen beheren. deze kan ook helpen bij het stoppen van de Flik kering die optreedt wanneer het opmaak model (CSS) wordt geladen.

Raadpleeg het [logboek voor versie](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)wijzigingen voor een volledige lijst van de wijzigingen die in het pagina-contract zijn aangebracht.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Bijwerken naar de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** mijn Apps  
**Product-mogelijkheid:** Access Control

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de gloed nieuwe mijn apps-ervaring weer geven en openen, met inbegrip van het gebruik van de nieuwe functie werk ruimten, om het voor hen gemakkelijker te maken apps te vinden. De nieuwe functionaliteit voor werk ruimten fungeert als een filter voor de apps waartoe gebruikers van de organisatie al toegang hebben.

Zie [werk ruimten maken in de portal mijn apps (preview-versie)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)voor meer informatie over het implementeren van de nieuwe mijn apps-ervaring en het maken van werk ruimten.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Ondersteuning voor het maandelijks actieve facturerings model op basis van gebruikers (algemene Beschik baarheid)

**Type:** nieuwe functie  
**Service-categorie:** B2C - identiteitsbeheer van consumenten  
**Product-mogelijkheid:** B2B/B2C

Azure AD B2C ondersteunt nu de facturering van maandelijkse actieve gebruikers (MAU). MAU-facturering is gebaseerd op het aantal unieke gebruikers met verificatie activiteiten tijdens een kalender maand. Bestaande klanten kunnen op elk gewenst moment overstappen op deze nieuwe facturerings methode.

Vanaf 1 november 2019 worden alle nieuwe klanten automatisch gefactureerd met behulp van deze methode. Deze facturerings methode biedt klanten de voor delen van kosten en de mogelijkheid om vooruit te plannen.

Zie voor meer informatie [upgraden naar het facturerings model maandelijkse actieve gebruikers](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-oktober 2019

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** 3e partij-integratie

In oktober 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[In het geval van crisis: Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno traject](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [intact](https://tact.ai/assistant/), [OpusCapita kasbeheer](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contented](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue coördinaat – EU](https://www.hirevue.com/), [HireVue coördinaat-USOnly](https://www.hirevue.com/), [HireVue Coordinate-VS](https://www.hirevue.com/), [WittyParrot Kennis Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mailing!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB navigeert IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking email for intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [gesp roken e-mail voor Office 365 direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care-navigatie systeem](https://ihealthnav.com/account/signin), [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menu-item voor geconsolideerde beveiliging in de Azure AD-Portal

**Type:** gewijzigde functie  
**Service-categorie:** Identity Protection  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

U hebt nu toegang tot alle beschik bare Azure AD-beveiligings functies vanuit het menu met nieuwe **beveiliging** en op de **Zoek** balk in de Azure Portal. Daarnaast biedt de nieuwe **beveiligings** landings pagina, met de naam **beveiliging-aan de slag**, koppelingen naar onze open bare documentatie, beveiligings richtlijnen en implementatie handleidingen.

Het menu nieuwe **beveiliging** bevat:

- Voorwaardelijke toegang
- Identiteitsbeveiliging
- Beveiligingscentrum
- Identiteits veilige Score
- Authenticatiemethoden
- MFA
- Risico rapporten-Risk ante gebruikers, Risk ante aanmeldingen, risico detecties
- En meer...

Zie [beveiliging-aan de slag](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)voor meer informatie.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365-groepen verloop beleid verbeterd met autoverlengen

**Type:** gewijzigde functie  
**Service-categorie:** groepsbeheer  
**Product-mogelijkheid:** beheer van Identiteitslevenscycli

Het verloop beleid van de Office 365-groepen is uitgebreid met het automatisch vernieuwen van groepen die momenteel door de leden van de groep worden gebruikt. Groepen worden automatisch vernieuwd op basis van gebruikers activiteit in alle Office 365-apps, waaronder Outlook, share point en teams.

Deze verbetering helpt u bij het verminderen van uw groeps meldingen en helpt ervoor te zorgen dat actieve groepen nog steeds beschikbaar zijn. Als u al een actief verloop beleid hebt voor uw Office 365-groepen, hoeft u niets te doen om deze nieuwe functionaliteit in te scha kelen.

Zie [het verloop beleid voor Office 365-groepen configureren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)voor meer informatie.

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Werk ervaring bij het maken van Azure AD Domain Services (Azure AD DS) is bijgewerkt

**Type:** gewijzigde functie  
**Service-categorie:** Azure AD Domain Services  
**Product-mogelijkheid:** Azure AD Domain Services

We hebben Azure AD Domain Services (Azure AD DS) bijgewerkt met een nieuwe en verbeterde ervaring voor het maken van een beheerd domein in slechts drie muis klikken. Daarnaast kunt u nu Azure-AD DS uploaden en implementeren vanuit een sjabloon.

Zie [zelf studie: een Azure Active Directory Domain Services-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)voor meer informatie.

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plannen voor wijziging: afschaffing van de Power BI inhouds pakketten

**Type:** plannen voor wijziging  
**Service-categorie:** rapportage  
**Product-mogelijkheid:** controleren en rapporteren

Vanaf 1 oktober 2019 worden alle inhouds pakketten, inclusief het Azure AD Power BI-inhouds pakket, door Power BI af te nemen. Als alternatief voor dit inhouds pakket kunt u Azure AD-werkmappen gebruiken om inzicht te krijgen in uw Azure AD-gerelateerde services. Er zijn extra werkmappen beschikbaar, waaronder werkmappen over beleids regels voor voorwaardelijke toegang in de modus alleen rapport, inzichten op basis van apps en meer.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)voor meer informatie over de werkmappen. Voor meer informatie over de afschaffing van de inhouds pakketten raadpleegt u het blog bericht [Power bi sjabloon apps voor algemene Beschik baarheid aankondigen](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mijn profiel is een nieuwe naam en integratie met de pagina Microsoft Office-account

**Type:** plannen voor wijziging  
**Service categorie:** Mijn profiel/account  
**Product-mogelijkheid:** samenwerking

Vanaf oktober wordt de ervaring mijn profiel van mijn account. Als onderdeel van deze wijziging, wordt **Mijn profiel** gewijzigd in **Mijn account**. Naast de naam wijziging en enkele ontwerp verbeteringen, biedt de bijgewerkte ervaring aanvullende integratie met de pagina Microsoft Office-account. U hebt met name toegang tot Office-installaties en-abonnementen via de pagina **overzichts account** , samen met Office-gerelateerde contact voorkeuren van de pagina **Privacy** .

Zie voor meer informatie over de ervaring mijn profiel (preview) [Mijn profiel (preview-versie) van portal](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Groepen en leden bulksgewijs beheren met CSV-bestanden in de Azure AD-Portal (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** groepsbeheer  
**Product-mogelijkheid:** samenwerking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de ervaring voor bulksgewijs groeps beheer in de Azure AD-Portal. U kunt nu een CSV-bestand en de Azure AD-Portal gebruiken om groepen en leden lijsten te beheren, met inbegrip van:

- Leden toevoegen aan of verwijderen uit een groep.

- De lijst met groepen wordt gedownload uit de map.

- De lijst met groeps leden voor een specifieke groep downloaden.

Zie voor meer informatie [bulksgewijs toevoegen leden](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [leden bulksgewijs verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [lijst met leden en items](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)bulksgewijs downloaden en [groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamische toestemming wordt nu ondersteund via een nieuw door de beheerder toestemmings eindpunt

**Type:** nieuwe functie  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** gebruikersverificatie

We hebben een nieuw beheerders toestemmings eindpunt gemaakt ter ondersteuning van dynamische toestemming. Dit is handig voor apps waarvoor het dynamische toestemming model op het micro soft Identity-platform moet worden gebruikt.

Zie [using the Administrator toestemming endpoint](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)(Engelstalig) voor meer informatie over het gebruik van dit nieuwe eind punt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-september 2019

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** 3e partij-integratie

In september 2019 hebben we deze 29 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex compliance Office™-eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [concur Traveling en onkosten](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [Arc-faciliteiten](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus team](https://stratus.emea.luware.cloud/login), [brede ideeën](https://wideideas.online/wideideas/), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT client hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath beveiligde browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [Penneo](https://app.penneo.com/), [Hiretual](https://app.testhtm.com/settings/email-integration), [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [gehoste erfgoed online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai-team build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [Discovery voordelen SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nieuwe globale rol van Azure AD-lezer

**Type:** nieuwe functie  
**Service categorie:** RBAC  
**Product-mogelijkheid:** Access Control

Vanaf 24 september 2019 beginnen we met het implementeren van een nieuwe Azure Active Directory-rol (AD) met de naam Global Reader. Deze implementatie begint met productie-en mondiale Cloud klanten (GCC), die wereld wijd in oktober wordt voltooid.

De rol van algemene lezer is het alleen-lezen equivalent van de globale beheerder. Gebruikers met deze rol kunnen instellingen en beheer gegevens in Microsoft 365 services lezen, maar kunnen geen beheer acties uitvoeren. We hebben de algemene rol lezer gemaakt om het aantal globale beheerders in uw organisatie te verminderen. Omdat globale beheerders accounts krachtig en kwetsbaar zijn voor aanvallen, raden wij aan dat u minder dan vijf globale beheerders hebt. We raden u aan de rol van globale lezer te gebruiken voor het plannen, controleren of onderzoeken. We raden u ook aan de rol van globale lezer te gebruiken in combi natie met andere beperkte beheerders rollen, zoals Exchange Administrator, om aan de slag te gaan zonder dat hiervoor de rol van globale beheerder is vereist.

De rol van algemene lezer werkt met de nieuwe Microsoft 365 beheer centrum, het Exchange-beheer centrum, teams beheer centrum, Security Center, compliance Center, Azure AD-beheer centrum en het beheer centrum voor Apparaatbeheer.

>[!NOTE]
> Aan het begin van de open bare preview-functie werkt de globale rol van lezer niet met: share point, Privileged Access Management, Klanten-lockbox, gevoeligheids labels, teams levenscyclus, teams rapporteren & aanroep Analytics, teams IP-adres beheer van het apparaat en teams-app Catalogus. Al deze services zijn bedoeld om in de toekomst met de functie te werken.

Zie [machtigingen voor beheerdersrol in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)voor meer informatie.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Toegang tot een on-premises rapport server vanuit uw Power BI-Mobiel-app met behulp van Azure Active Directory-toepassingsproxy

**Type:** nieuwe functie  
**Service-categorie:** App Proxy  
**Product-mogelijkheid:** Access Control

Dankzij de nieuwe integratie tussen de Power BI mobiele app en Azure AD-toepassingsproxy kunt u zich veilig aanmelden bij de mobiele app van Power BI en de rapporten van uw organisatie weer geven die worden gehost op de on-premises Power BI Report Server.

Zie de [Power bi-site](https://powerbi.microsoft.com/mobile/)voor meer informatie over de Power bi-mobiel-app, inclusief de plek waar u de app kunt downloaden. Zie voor meer informatie over het instellen van de Power BI mobiele app met Azure AD-toepassingsproxy [externe toegang inschakelen voor Power bi-mobiel met azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Er is een nieuwe versie van de Power shell-module AzureADPreview beschikbaar

**Type:** gewijzigde functie  
**Service-categorie:** andere  
**Product-mogelijkheid:** Directory

Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module, waarmee u aangepaste rollen kunt definiëren en toewijzen in azure AD, waaronder:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nieuwe versie van Azure AD Connect

**Type:** gewijzigde functie  
**Service-categorie:** andere  
**Product-mogelijkheid:** Directory

We hebben een bijgewerkte versie van Azure AD Connect uitgebracht voor klanten met automatische upgrades. Deze nieuwe versie bevat verschillende nieuwe functies, verbeteringen en oplossingen voor problemen. Zie [Azure AD Connect: release geschiedenis](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)van de versie voor meer informatie over deze nieuwe versie.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA)-server, versie 8.0.2 is nu beschikbaar

**Type:** vast  
**Service categorie:** MFA  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Als u een bestaande klant bent die een MFA-server heeft geactiveerd voor 1 juli 2019, kunt u nu de nieuwste versie van de MFA-server (versie 8.0.2) downloaden. In deze nieuwe versie:

- Als er een probleem is opgelost, wordt een e-mail bericht verzonden naar de gebruiker wanneer de Azure AD-synchronisatie een gebruiker van uitgeschakeld wijzigt in ingeschakeld.

- Er is een probleem opgelost waardoor klanten kunnen upgraden, terwijl ze de functionaliteit van Tags blijven gebruiken.

- De land code Kosovo (+ 383) is toegevoegd.

- Eenmalige controle logboek registratie voor bypass is toegevoegd aan MultiFactorAuthSvc. log.

- Verbeterde prestaties voor de Web Service SDK.

- Andere kleine bugs zijn opgelost.

Vanaf 1 juli 2019 heeft micro soft de MFA-server voor nieuwe implementaties gestopt. Nieuwe klanten die multi-factor Authentication vereisen, moeten gebruikmaken van Azure Multi-Factor Authentication op basis van de Cloud. Zie [een Cloud implementatie van Azure multi-factor Authentication plannen](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)voor meer informatie.

---

## <a name="august-2019"></a>Augustus 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Uitgebreide zoek-, filter-en sorteer bewerkingen voor groepen zijn beschikbaar in de Azure AD-Portal (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** groepsbeheer  
**Product-mogelijkheid:** samenwerking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de uitgebreide ervaringen met groepen in de Azure AD-Portal. Deze verbeteringen helpen u bij het beheren van groepen en leden lijsten door het volgende te bieden:

- Geavanceerde zoek functies, zoals subtekenreeksen, zoeken in groepen-lijsten.
- Geavanceerde filter-en sorteer opties voor de lijsten leden en eigen aren.
- Nieuwe zoek mogelijkheden voor lijsten van leden en eigen aren.
- Nauw keurigere groeps aantallen voor grote groepen.

Zie [groepen beheren in de Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)voor meer informatie.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Er zijn nieuwe aangepaste rollen beschikbaar voor app-registratie beheer (open bare preview)

**Type:** nieuwe functie  
**Service categorie:** RBAC  
**Product-mogelijkheid:** Access Control

Aangepaste rollen (beschikbaar met een Azure AD P1-of P2-abonnement) kunnen u nu voorzien van gedetailleerde toegang, door roldefinities te maken met specifieke machtigingen en deze rollen vervolgens toe te wijzen aan specifieke resources. Op dit moment kunt u aangepaste rollen maken met behulp van machtigingen voor het beheren van app-registraties en vervolgens de rol toewijzen aan een specifieke app. Zie [aangepaste beheerders rollen in azure Active Directory (preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)voor meer informatie over aangepaste rollen.

Als u aanvullende machtigingen of bronnen nodig hebt die momenteel niet worden weer gegeven, kunt u feedback verzenden naar onze [Azure-feedback site](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) . we voegen uw aanvraag toe aan onze update-weg kaart.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Met nieuwe inrichtings Logboeken kunt u de implementatie van de app-inrichting controleren en problemen oplossen (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** App-inrichting  
**Product-mogelijkheid:** beheer van Identiteitslevenscycli

Er zijn nieuwe inrichtings logboeken beschikbaar om u te helpen bij het controleren en oplossen van problemen met de implementatie van gebruikers en groepen. Deze nieuwe logboek bestanden bevatten informatie over:

- Welke groepen zijn gemaakt in [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Welke rollen zijn geïmporteerd uit [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- De werk nemers die niet zijn geïmporteerd uit [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Zie [inrichtings rapporten in de Azure Active Directory Portal (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)voor meer informatie.

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nieuwe beveiligings rapporten voor alle Azure AD-beheerders (algemene Beschik baarheid)

**Type:** nieuwe functie  
**Service-categorie:** Identity Protection  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Standaard hebben alle Azure AD-beheerders binnenkort toegang tot moderne beveiligings rapporten in azure AD. Tot het einde van september kunt u de banner aan de bovenkant van de moderne beveiligings rapporten gebruiken om terug te keren naar de oude rapporten.

De moderne beveiligings rapporten bieden extra mogelijkheden van de oudere versies, waaronder:

- Geavanceerd filteren en sorteren
- Bulk acties, zoals het negeren van gebruikers Risico's
- Bevestiging van aangetast of veilige entiteiten
- Risico status, waaronder: risico, genegeerd, hersteld en bevestigde schadelijke
- Nieuwe detecties met een risico (beschikbaar voor Azure AD Premium abonnees)

Zie [Risk ante gebruikers](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Risk ante aanmeldingen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)en [risico detectie](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)voor meer informatie.

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Door de gebruiker toegewezen beheerde identiteit is beschikbaar voor Virtual Machines en Virtual Machine Scale Sets (algemene Beschik baarheid)

**Type:** nieuwe functie  
**Service categorie:** Beheerde identiteiten voor Azure-resources  
**Product-mogelijkheid:** ervaring voor ontwikkelaars

Door de gebruiker toegewezen beheerde identiteiten zijn nu algemeen beschikbaar voor Virtual Machines en Virtual Machine Scale Sets. Als onderdeel hiervan kan Azure een identiteit maken in de Azure AD-Tenant die wordt vertrouwd door het abonnement en kan worden toegewezen aan een of meer Azure-service-exemplaren. Zie [Wat is beheerde identiteiten voor Azure-resources?](https://aka.ms/azuremanagedidentity)voor meer informatie over door de gebruiker toegewezen beheerde identiteiten.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gebruikers kunnen hun wacht woord opnieuw instellen met behulp van een mobiele app of een hardware-token (algemene Beschik baarheid)

**Type:** gewijzigde functie  
**Service-categorie:** selfservice voor wachtwoordherstel  
**Product-mogelijkheid:** gebruikersverificatie

Gebruikers die een mobiele app hebben geregistreerd bij uw organisatie kunnen nu hun eigen wacht woord opnieuw instellen door een melding van de Microsoft Authenticator-app goed te keuren of door een code uit hun mobiele app of hardware-token in te voeren.

Zie [hoe het werkt: Azure AD self-service password reset](https://aka.ms/authappsspr)(Engelstalig) voor meer informatie. Zie [uw eigen werk-of school wachtwoord opnieuw instellen-overzicht](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)voor meer informatie over de gebruikers ervaring.

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET negeert de gedeelde cache van MSAL.NET voor namens een scenario

**Type:** vast  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** gebruikersverificatie

Vanaf Azure AD Authentication Library (ADAL.NET) versie 5.0.0-Preview moeten app-ontwikkel aars [één cache per account serialiseren voor web-apps en Web-api's](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Anders is het mogelijk dat sommige scenario's die gebruikmaken van de namen van de [stroom](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), samen met een aantal specifieke gebruiks voorbeelden van `UserAssertion`, een uitbrei ding van bevoegdheden kunnen opleveren. Om dit beveiligings probleem te voor komen, negeert ADAL.NET nu de micro soft-verificatie bibliotheek voor dotnet (MSAL.NET) gedeelde cache voor namens een scenario.

Zie [Azure Active Directory beveiligingslek met betrekking tot misbruik van bevoegdheden voor verificatie bibliotheek](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)voor meer informatie over dit probleem.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-augustus 2019

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** 3e partij-integratie

In 2019 augustus hebben we deze 26 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Civic platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos OPS Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy-aanwezigheid](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [CARBONITE endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync door CinCom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee,](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial) [Delivery. Media™ Portal](https://portal.deliver.media), [Frontline onderwijs](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [kijken op kleuren](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [harnas](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate strategisch Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Er zijn nieuwe versies van de Power shell-en AzureADPreview-AzureAD

**Type:** gewijzigde functie  
**Service-categorie:** andere  
**Product-mogelijkheid:** Directory

Er zijn nieuwe updates beschikbaar voor de AzureAD-en AzureAD preview Power shell-modules:

- Er is een nieuwe `-Filter`-para meter toegevoegd aan de para meter `Get-AzureADDirectoryRole` in de module AzureAD. Met deze para meter kunt u filteren op de Directory functies die door de cmdlet worden geretourneerd.
- Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module, waarmee u aangepaste rollen kunt definiëren en toewijzen in azure AD, waaronder:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Verbeteringen in de gebruikers interface van de opbouw functie voor dynamische groeps regels in de Azure Portal

**Type:** gewijzigde functie  
**Service-categorie:** groepsbeheer  
**Product-mogelijkheid:** samenwerking

Er zijn enkele verbeteringen in de gebruikers interface aangebracht in de opbouw functie voor dynamische groeps regels, die beschikbaar is in de Azure Portal, zodat u gemakkelijk een nieuwe regel kunt instellen of bestaande regels kunt wijzigen. Met deze ontwerp verbetering kunt u regels maken met Maxi maal vijf expressies, in plaats van slechts één. Daarnaast hebben we de lijst met eigenschappen van het apparaat bijgewerkt om afgeschafte apparaateigenschappen te verwijderen.

Zie [regels voor dynamische lidmaatschap beheren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule)voor meer informatie.

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Er is een nieuwe Microsoft Graph app-machtiging beschikbaar voor gebruik met toegangs beoordelingen

**Type:** gewijzigde functie  
**Service-categorie:** Toegangsbeoordelingen  
**Product mogelijkheden:** Identity governance

We hebben een nieuwe Microsoft Graph app-machtiging geïntroduceerd, `AccessReview.ReadWrite.Membership`, waarmee apps automatisch toegangs Beoordelingen voor groepslid maatschappen en app-toewijzingen kunnen maken en ophalen. Deze machtiging kan worden gebruikt door uw geplande taken of als onderdeel van uw automatisering zonder dat hiervoor een aangemelde gebruikers context nodig is.

Zie voor meer informatie het [maken van Azure AD-toegangs beoordelingen met Microsoft Graph app-machtigingen met Power shell-blog](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar voor overheids Cloud instanties in Azure Monitor

**Type:** gewijzigde functie  
**Service-categorie:** rapportage  
**Product-mogelijkheid:** controleren en rapporteren

We zijn enthousiast dat Azure AD-activiteiten logboeken nu beschikbaar zijn voor instanties van overheids-Clouds in Azure Monitor. U kunt nu Azure AD-logboeken naar uw opslag account of een Event Hub verzenden om te integreren met uw SIEM-hulpprogram ma's, zoals [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)en [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Voor meer informatie over het instellen van Azure Monitor raadpleegt u [Azure AD-activiteiten Logboeken in azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Werk uw gebruikers bij naar de nieuwe, verbeterde ervaring voor beveiligings informatie

**Type:** gewijzigde functie  
**Service categorie:**  Authenticaties (aanmeldingen)   
**Product-mogelijkheid:** gebruikersverificatie

Op 25 september 2019 worden de oude, niet-verbeterde beveiligings gegevens voor het registreren en beheren van gebruikers beveiligings gegevens uitgeschakeld en wordt alleen de nieuwe, [verbeterde versie](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)ingeschakeld. Dit betekent dat uw gebruikers de oude ervaring niet langer kunnen gebruiken.

Zie onze [beheerders documentatie](https://aka.ms/securityinfodocs) en onze [gebruikers documentatie](https://aka.ms/securityinfoguide)voor meer informatie over de verbeterde ervaring met beveiligings informatie.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Als u deze nieuwe ervaring wilt inschakelen, moet u het volgende doen:

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder.

2. Ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster beheren**.

3. Selecteer in de sectie **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiligings gegevens-uitgebreid** gebied **geselecteerd**, en kies vervolgens een groep gebruikers of kies **alle** om deze functie in te scha kelen voor alle gebruikers in de Tenant.

4. In de **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiliging** **info**   Selecteer **geen**.

5. Sla uw wijzigingen op.

    Nadat u de instellingen hebt opgeslagen, hebt u geen toegang meer tot de oude beveiligings informatie.

>[!Important]
>Als u deze stappen vóór 25 september 2019 niet voltooit, wordt uw Azure Active Directory-Tenant automatisch ingeschakeld voor de verbeterde ervaring. Als u vragen hebt, kunt u contact met ons opnemen op registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Verificatie aanvragen via POST-aanmeldingen worden strikter gevalideerd

**Type:** gewijzigde functie  
**Service-categorie:** verificaties (aanmeldingen)  
**Product mogelijkheden:** Normalisatie

Vanaf 2 september 2019 worden verificatie aanvragen via de POST-methode strikt gevalideerd op basis van de HTTP-standaarden. In het bijzonder worden spaties en dubbele aanhalings tekens (") niet meer verwijderd uit de waarden van het aanvraag formulier. Deze wijzigingen worden niet verwacht om bestaande clients te verstoren en ervoor te zorgen dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden afgehandeld.

Zie voor meer informatie de [meldingen over Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)-onderbrekingen.

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plannen voor wijziging: update van Application proxy-service om alleen TLS 1,2 te ondersteunen

**Type:** plannen voor wijziging  
**Service-categorie:** App Proxy  
**Product-mogelijkheid:** Access Control

Om u te helpen met onze krach tigste versleuteling, gaan we de toegang tot de toepassings proxy service beperken tot alleen TLS 1,2-protocollen. Deze beperking wordt eerst doorgevoerd naar klanten die al gebruikmaken van TLS 1,2-protocollen, zodat u de impact niet kunt zien. De afronding van de protocollen TLS 1,0 en TLS 1,1 is voltooid op 31 augustus 2019. Klanten die nog TLS 1,0 en TLS 1,1 gebruiken, ontvangen een geavanceerde kennisgeving om deze wijziging voor te bereiden.

Als u de verbinding met de service toepassings proxy tijdens deze wijziging wilt behouden, kunt u het beste controleren of uw combi Naties van client-server en browser-server zijn bijgewerkt voor het gebruik van TLS 1,2. We raden u ook aan om ervoor te zorgen dat alle client systemen die door uw werk nemers worden gebruikt, toegang hebben tot apps die zijn gepubliceerd via de Application proxy-service.

Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)voor meer informatie.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plannen voor wijziging: ontwerp updates zijn beschikbaar voor de toepassings galerie

**Type:** plannen voor wijziging  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** eenmalige aanmelding

Nieuwe wijzigingen in de gebruikers interface zijn afkomstig van het ontwerp van de **toevoegen vanuit het gebied Galerie** van de Blade **een toepassing toevoegen** . Met deze wijzigingen kunt u eenvoudig uw apps vinden die ondersteuning bieden voor automatische inrichting, OpenID Connect Connect, Security Assertion Markup Language (SAML) en een wacht woord voor eenmalige aanmelding (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plannen voor wijziging: het IP-adres van de MFA-server verwijderen uit het Office 365 IP-adres

**Type:** plannen voor wijziging  
**Service categorie:** MFA  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Het IP-adres van de MFA-server wordt verwijderd uit het [Office 365 IP-adres en de URL-webservice](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Als u momenteel afhankelijk bent van deze pagina's om uw firewall instellingen bij te werken, moet u ervoor zorgen dat u ook de lijst met IP-adressen die worden beschreven in de sectie **azure multi-factor Authentication-Server-firewall vereisten** van het artikel aan de slag [met Azure multi-factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Voor alleen app-tokens moet de client-app bestaan in de resource Tenant

**Type:** vast  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** gebruikersverificatie

Op 26 juli 2019 hebben we gewijzigd hoe we alleen app-tokens bieden via de [client referenties verlenen](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Voorheen konden apps tokens krijgen om andere apps aan te roepen, ongeacht of de client-app zich in de Tenant bevindt. We hebben dit gedrag bijgewerkt, waardoor enkele Tenant bronnen, ook wel web-Api's genoemd, alleen kunnen worden aangeroepen door client-apps die voor komen in de resource-Tenant.

Als uw app zich niet in de resource Tenant bevindt, wordt er een fout bericht weer gegeven waarin staat dat `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` om dit probleem op te lossen, moet u de client app Service-Principal in de Tenant maken met behulp van het [beheerders toestemmings eindpunt](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) of [via Power shell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), zodat uw Tenant de app toestemming heeft gegeven om binnen de Tenant te werken.

Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)voor meer informatie.

> [!NOTE]
> Bestaande toestemming tussen de client en de API blijft niet vereist. Apps moeten nog steeds hun eigen autorisatie controles uitvoeren.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nieuwe aanmelding zonder wacht woord voor Azure AD met behulp van FIDO2-beveiligings sleutels

**Type:** nieuwe functie  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** gebruikersverificatie

Azure AD-klanten kunnen nu beleid instellen voor het beheren van FIDO2-beveiligings sleutels voor de gebruikers en groepen van hun organisatie. Eind gebruikers kunnen hun beveiligings sleutels ook zelf registreren, de sleutels gebruiken om zich aan te melden bij hun micro soft-accounts op websites, terwijl ze op FIDO apparaten werken, en aanmelden bij hun Azure AD-Windows 10-apparaten.

Zie voor meer informatie [aanmelden zonder wacht woord inschakelen voor Azure AD (preview)](/azure/active-directory/authentication/concept-authentication-passwordless) voor informatie die betrekking heeft op de beheerder en [Stel beveiligings informatie in voor het gebruik van een beveiligings sleutel (preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) voor informatie die aan de eind gebruiker is gerelateerd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-juli 2019

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** 3e partij-integratie

In juli 2019 zijn deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [helder patroon Omnichannel contact centrum](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [slimme Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access voor Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hele hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [schuine](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassing-integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie voor meer informatie over het aanbieden van uw toepassing in de Azure AD-app-galerie [uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** controleren en rapporteren

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

**Type:** nieuwe functie  
**Service-categorie:** Azure AD Domain Services  
**Product-mogelijkheid:** Azure AD Domain Services

Als u veel lijsten met IP-adressen en-bereiken niet meer wilt beheren, kunt u de nieuwe **AzureActiveDirectoryDomainServices** -netwerk servicetag gebruiken in uw Azure-netwerk beveiligings groep om inkomend verkeer naar uw Azure AD Domain Services subnet van het virtuele netwerk te beveiligen.

Zie [netwerk beveiligings groepen voor Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)voor meer informatie over deze nieuwe servicetag.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** Azure AD Domain Services  
**Product-mogelijkheid:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services van cruciaal belang maken door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD-domein service Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)voor meer informatie.

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nieuwe verificatie methoden Usage & Insights (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** selfservice voor wachtwoordherstel  
**Product-mogelijkheid:** controleren en rapporteren

Met de nieuwe verificatie methoden & Insights-rapporten kunt u beter begrijpen hoe functies zoals Azure Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden worden geregistreerd en gebruikt in uw organisatie, inclusief het aantal geregistreerde gebruikers voor elke functie, hoe vaak de selfservice voor wachtwoord herstel wordt gebruikt om wacht woorden opnieuw in te stellen, en op basis van welke methode het opnieuw instellen plaatsvindt.

Zie het gebruik van de [verificatie methoden & Insights (preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)voor meer informatie.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nieuwe beveiligings rapporten zijn beschikbaar voor alle Azure AD-beheerders (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** Identity Protection  
**Product-mogelijkheid:** Identiteitbeveiliging en -beveiliging

Alle Azure AD-beheerders kunnen nu het vaandel selecteren boven aan bestaande beveiligings rapporten, zoals de gebruikers die zijn **gemarkeerd voor risico** rapport, om de nieuwe beveiligings ervaring te gebruiken, zoals wordt weer gegeven in de rapporten **Risk ante gebruikers** en **Risk ante aanmeldingen** . Na verloop van tijd worden alle beveiligings rapporten van oudere versies naar de nieuwe versies verplaatst, met de nieuwe rapporten die u de volgende aanvullende mogelijkheden bieden:

- Geavanceerd filteren en sorteren

- Bulk acties, zoals het negeren van gebruikers Risico's

- Bevestiging van aangetast of veilige entiteiten

- Risico status, waaronder: risico, genegeerd, hersteld en bevestigde schadelijke

Zie rapport met [Risk ante gebruikers](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) en [rapporten met Risk ante aanmeldingen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)voor meer informatie.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** Azure AD Domain Services  
**Product-mogelijkheid:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services van cruciaal belang maken door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD-domein service Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)voor meer informatie.

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nieuwe B2B direct Federation met behulp van SAML/WS-voeder (open bare preview)

**Type:** nieuwe functie  
**Service-categorie:** B2B  
**Product-mogelijkheid:** B2B/B2C

Directe Federatie maakt het gemakkelijker voor u om te werken met partners waarvan de IT-beheerde identiteits oplossing niet Azure AD is, door te werken met identiteits systemen die ondersteuning bieden voor de SAML-of WS-voeder-standaarden. Nadat u een directe Federatie relatie met een partner hebt ingesteld, kan elke nieuwe gast gebruiker die u uit dat domein uitnodigt met behulp van hun bestaande organisatie account samen werken, waardoor de gebruikers ervaring voor uw gasten naadloos wordt.

Zie voor meer informatie [directe Federatie met AD FS en providers van derden voor gast gebruikers (preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** nieuwe functie  
**Service-categorie:** zakelijke Apps  
**Product-mogelijkheid:** controleren en rapporteren

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

**Type:** nieuwe functie  
**Service-categorie:** groepsbeheer  
**Product-mogelijkheid:** samenwerking

Wanneer u nu een groeps naam vanuit de Azure AD-Portal maakt of bijwerkt, wordt er een controle uitgevoerd om te zien of u een bestaande groeps naam in uw resource dupliceert. Als we bepalen dat de naam al wordt gebruikt door een andere groep, wordt u gevraagd om uw naam te wijzigen.

Zie [groepen beheren in de Azure AD-Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)voor meer informatie.

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD biedt nu ondersteuning voor statische query parameters in antwoord-Uri's (redirect)

**Type:** nieuwe functie  
**Service-categorie:** verificaties (aanmeldingen)  
**Product-mogelijkheid:** gebruikersverificatie

Azure AD-apps kunnen nu Uri's van antwoorden (omleiding) met statische query parameters registreren en gebruiken (bijvoorbeeld `https://contoso.com/oauth2?idp=microsoft`) voor OAuth 2,0-aanvragen. De statische query parameter is onderhevig aan de teken reeks die overeenkomt met antwoord-Uri's, net als elk ander deel van de antwoord-URI. Als er geen geregistreerde teken reeks is die overeenkomt met de door de URL gedecodeerde omleidings-URI, wordt de aanvraag geweigerd. Als de antwoord-URI wordt gevonden, wordt de gehele teken reeks gebruikt om de gebruiker te omleiden, inclusief de para meter static query.

Dynamische antwoord-Uri's zijn nog niet toegestaan omdat ze een beveiligings risico vertegenwoordigen en niet kunnen worden gebruikt om status informatie over een verificatie aanvraag te houden. Gebruik voor dit doel de para meter `state`.

Momenteel zijn de app-registratie schermen van de Azure Portal nog steeds blok keren query parameters. U kunt het app-manifest echter hand matig bewerken om query parameters toe te voegen en te testen in uw app. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)voor meer informatie.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Activiteiten Logboeken (MS Graph-Api's) voor Azure AD zijn nu beschikbaar via Power shell-cmdlets

**Type:** nieuwe functie  
**Service-categorie:** rapportage  
**Product-mogelijkheid:** controleren en rapporteren

Met trots kondigen we aan dat Azure AD-activiteiten Logboeken (rapporten over controles en aanmeldingen) nu beschikbaar zijn via de Azure AD Power shell-module. U kunt voorheen uw eigen scripts maken met behulp van MS Graph API-eind punten en nu hebben we die mogelijkheid tot Power shell-cmdlets uitgebreid.

Zie [Azure AD Power shell-cmdlets voor rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)voor meer informatie over het gebruik van deze cmdlets.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Bijgewerkte filter besturings elementen voor controle-en aanmeld Logboeken in azure AD

**Type:** gewijzigde functie  
**Service-categorie:** rapportage  
**Product-mogelijkheid:** controleren en rapporteren

De controle-en aanmeldings logboek rapporten zijn bijgewerkt, zodat u nu verschillende filters kunt Toep assen zonder ze als kolommen toe te voegen aan de rapport schermen. Daarnaast kunt u bepalen hoeveel filters u wilt weer geven op het scherm. Deze updates werken allemaal samen om uw rapporten gemakkelijker te kunnen lezen en zo meer te bereiken aan uw behoeften.

Zie [controle logboeken filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) en [aanmeldings activiteiten filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)voor meer informatie over deze updates.

---
