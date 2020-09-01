---
title: Wilt u archiveren wat er nieuw is in Azure Active Directory? | Microsoft Docs
description: De opmerkingen bij de release van wat is er nieuw in het gedeelte Overzicht van deze inhoudsset, bevat zes maanden aan activiteit. Na zes maanden worden de items uit het hoofd artikel verwijderd en in dit archief artikel geplaatst.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 887bc083a35a065c4b54590c284bfbc794da18e5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267816"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Wilt u archiveren wat er nieuw is in Azure Active Directory?

De belangrijkste [nieuwe functies in azure Active Directory? release opmerkingen](whats-new.md) artikel bevat updates voor de afgelopen zes maanden, terwijl dit artikel alle oudere informatie bevat.

Wat is er nieuw in Azure Active Directory? release opmerkingen bevatten informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

---
 ## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Aanstaande wijzigingen in aangepaste besturings elementen

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
We zijn van plan de huidige aangepaste controle voorbeelden te vervangen door een benadering waarmee de verificatie mogelijkheden van een partner naadloos kunnen samen werken met de Azure Active Directory beheerder en de ervaring van de eind gebruiker. Tegenwoordig hebben partner MFA-oplossingen de volgende beperkingen: ze werken alleen nadat er een wacht woord is ingevoerd. ze fungeren niet als MFA voor het opschalen van verificatie in andere belang rijke scenario's. en ze zijn niet geïntegreerd met beheer functies voor eind gebruikers of beheer referenties. De nieuwe implementatie zorgt ervoor dat door de partner meegeleverde verificatie factoren naast ingebouwde factoren worden gebruikt voor belang rijke scenario's, waaronder registratie, gebruik, MFA-claims, verificatie, rapportage en logboek registratie. 

Aangepaste besturings elementen worden nog steeds ondersteund in Preview naast het nieuwe ontwerp, totdat de algemene Beschik baarheid wordt bereikt. Op dat moment geven we klanten tijd om te migreren naar het nieuwe ontwerp. Vanwege de beperkingen van de huidige aanpak kunnen we geen nieuwe providers vrijgeven totdat het nieuwe ontwerp beschikbaar is. We werken nauw samen met klanten en providers en zullen de tijd lijn door geven als we dichter bij elkaar komen. [Meer informatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identiteits beveiligde Score

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Voor de nood zaak van bedrijven om te zorgen voor de beste beveiliging bij het Toep assen van beleid dat met hun bedrijf werkt, wordt door micro soft Secure Score drie verbeteringen voor verbetering van de multi-factor Authentication (MFA) verwijderd en worden er twee toegevoegd.

De volgende verbeterings acties worden verwijderd:

- Alle gebruikers voor MFA registreren
- MFA vereisen voor alle gebruikers
- MFA vereisen voor Azure AD-privileged roles

De volgende verbeterings acties worden toegevoegd:

- Zorg ervoor dat alle gebruikers MFA voor beveiligde toegang kunnen volt ooien
- MFA vereisen voor beheerders rollen

Voor deze nieuwe verbeterings acties moet u uw gebruikers of beheerders registreren voor MFA in uw directory en de juiste set beleids regels instellen die voldoen aan de behoeften van uw organisatie. Het belangrijkste doel is om de flexibiliteit te bieden en ervoor te zorgen dat al uw gebruikers en beheerders kunnen verifiëren met meerdere factoren of prompts voor identiteits verificatie op basis van een risico. Dit kan de vorm hebben van de standaard instellingen voor beveiliging waarmee micro soft kan bepalen wanneer gebruikers voor MFA moeten worden aangevraagd of wanneer er meerdere beleids regels gelden voor het Toep assen van beslissingen met een bereik. Als onderdeel van deze verbeteringen van de verbeterings actie, worden beleids regels voor basis beveiliging niet meer opgenomen in Score berekeningen. [Meer informatie over de micro soft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU selecteren

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services
 
We hebben feedback gehoord die Azure AD Domain Services klanten meer flexibiliteit willen bij het selecteren van prestatie niveaus voor hun instanties. Vanaf 1 februari 2020 wordt er overgeschakeld van een dynamisch model (waarbij Azure AD de prestaties en prijs categorie op basis van het aantal objecten bepaalt) in een zelf-selectie model. Klanten kunnen nu een prestatie niveau kiezen dat overeenkomt met hun omgeving. Met deze wijziging kunnen we ook nieuwe scenario's als resource-forests inschakelen, en Premium-functies, zoals dagelijkse back-ups. Het aantal objecten is nu onbeperkt voor alle Sku's, maar we blijven suggesties voor object aantal voor elke laag aanbieden.

**Er is geen onmiddellijke klant actie vereist.** Voor bestaande klanten bepaalt de dynamische laag die werd gebruikt op 1 februari 2020, de nieuwe standaardlaag. Er zijn geen prijzen of gevolgen voor de prestaties als gevolg van deze wijziging. Azure AD DS klanten moeten de prestatie vereisten evalueren als de grootte van de Directory en de kenmerken van de werk belasting worden gewijzigd. Scha kelen tussen service lagen blijft een niet-downtimede bewerking en worden klanten niet meer automatisch verplaatst naar nieuwe lagen op basis van de groei van hun Directory. Bovendien zijn er geen prijs verhogingen en worden nieuwe prijzen afgestemd op het huidige facturerings model. Zie de [documentatie van Azure AD DS sku's](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) en de [Azure AD Domain Services-pagina met prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/)voor meer informatie.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-februari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In februari 2020 zijn deze 31 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[IamIP patent platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 SSO voor Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda e-mail beveiligings service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [in het geval van een crisis-Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC-Cloud ontwerp](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [bijen teelt Azure AD-gegevens connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn-veer](https://www.kornferry.com/solutions/kf-digital/kf-assess)tests, [Verkada-opdracht](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB navigatie](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [nieuwe Relic (beperkte release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [thulium](https://admin.thulium.com/login/instance), [ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [sjabloon kiezer voor teams](https://links.officeatwork.com/templatechooser-download-teams), [bijen](https://www.beesy.me/index.php/site/login), [Health-ondersteunings systeem](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [Mural](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex vdr](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), ThingLink [voor docenten en scholen](https://www.thinglink.com/), [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Bureau-artikel Galaxy en afdruk](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial) [team](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-februari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud van Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-ondersteuning voor FIDO2-beveiligings sleutels in hybride omgevingen

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
We kondigen de open bare preview-versie van Azure AD-ondersteuning voor FIDO2-beveiligings sleutels in hybride omgevingen. Gebruikers kunnen nu FIDO2-beveiligings sleutels gebruiken om zich aan te melden bij hun hybride Azure AD-apparaten die lid zijn van Windows 10 en een naadloze aanmelding voor hun on-premises en Cloud bronnen te krijgen. Ondersteuning voor hybride omgevingen is de meest aangevraagde functie van onze klanten met een wacht woord, omdat we in eerste instantie de open bare Preview voor FIDO2-ondersteuning in azure AD gekoppelde apparaten hebben gestart. Verificatie zonder wacht woord met behulp van geavanceerde technologieën zoals biometrie en crypto grafie met een open bare/persoonlijke sleutel kunt u gemakkelijk en eenvoudig gebruik maken van de beveiliging. Met deze open bare preview kunt u nu moderne verificatie gebruiken, zoals FIDO2-beveiligings sleutels, om toegang te krijgen tot traditionele Active Directory resources. Ga naar [SSO voor on-premises resources](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)voor meer informatie. 

Om aan de slag te gaan, gaat u naar [FIDO2-beveiligings sleutels voor uw Tenant inschakelen](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) voor stapsgewijze instructies. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>De nieuwe ervaring mijn account is nu algemeen verkrijgbaar

**Type:** Gewijzigde functie  
**Service categorie:** Mijn profiel/account  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Mijn account, de ene-stop-shop voor alle beheer behoeften voor eind gebruikers, is nu algemeen beschikbaar. Eind gebruikers hebben toegang tot deze nieuwe site via URL of in de koptekst van de nieuwe mijn apps-ervaring. Meer informatie over de self-service mogelijkheden van de nieuwe ervaring vindt u op [Mijn account Portal overzicht](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Site-URL van mijn account wordt bijgewerkt naar myaccount.microsoft.com

**Type:** Gewijzigde functie  
**Service categorie:** Mijn profiel/account  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
De nieuwe ervaring voor eind gebruikers van mijn account wordt `https://myaccount.microsoft.com` in de volgende maand bijgewerkt naar de URL. Meer informatie over de ervaring en alle accounts selfservice mogelijkheden voor eind gebruikers vindt u in de [Help van mijn account Portal](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---

## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>De nieuwe portal voor mijn apps is nu algemeen verkrijgbaar

**Type:** Plan voor wijziging  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Voer een upgrade uit van uw organisatie naar de nieuwe portals voor mijn apps die nu algemeen beschikbaar zijn. U vindt meer informatie over de nieuwe portal en verzamelingen in [verzamelingen maken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>De naam van werk ruimten in azure AD is gewijzigd in verzamelingen

**Type:** Gewijzigde functie  
**Service categorie:** Mijn apps   
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Werk ruimten, de filters beheerders kunnen configureren om de apps van hun gebruikers te organiseren, worden nu verzamelingen genoemd. Meer informatie over hoe u deze kunt configureren in [verzamelingen maken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefoon registratie en aanmelding met aangepast beleid (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Met het registreren van telefoon nummers en aanmelding kunnen ontwikkel aars en ondernemingen hun klanten toestaan zich aan te melden en zich aan te melden met een eenmalig wacht woord dat via SMS naar het telefoon nummer van de gebruiker wordt verzonden. Met deze functie kan de klant ook hun telefoon nummer wijzigen als de toegang tot hun telefoon verloren is gegaan. Met de kracht van een aangepast beleid kunnen ontwikkel aars en ondernemingen zich aanmelden via de pagina aanpassing van het logo. Meer informatie over het [instellen van aanmelding via de telefoon en het aanmelden met aangepaste beleids regels in azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-januari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-januari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In januari 2020 hebben we deze 33 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [snelle Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [TERRAFORM Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)upsneld, [LEAVEBOT](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [DOTCOM-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [gehoste MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwerk onderneming](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB voor micro soft teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), ContractSafe [Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient uitvoeren Manager-software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [coreview](https://portal.coreview.com/), squelch [Cloud Office365-connector](https://laxmi.squelch.io/login), [PingFlow-verificatie](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), Akari [virtuele assistent](https://akari.io/akari-virtual-assistant/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="two-new-identity-protection-detections"></a>Twee nieuwe detecties van identiteits beveiliging

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Er zijn twee nieuwe gekoppelde aanmeld typen toegevoegd aan identiteits beveiliging: verdacht regels voor het bewerken van het postvak in en mogelijk geen reizen. Deze offline detecties worden gedetecteerd door Microsoft Cloud App Security (MCAS) en beïnvloeden de gebruikers-en aanmeldings Risico's voor identiteits beveiliging. Zie voor meer informatie over deze detecties de [typen aanmeldings Risico's](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Laatste wijziging: URI-fragmenten worden niet door de omleiding van de aanmelding uitgevoerd

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Vanaf 8 februari 2020 wordt een leeg fragment aan de aanvraag toegevoegd wanneer een aanvraag wordt verzonden naar login.microsoftonline.com om zich aan te melden bij een gebruiker.  Dit voor komt een klasse van omleidings aanvallen door ervoor te zorgen dat de browser alle bestaande fragmenten in de aanvraag wist. Een toepassing moet afhankelijk zijn van dit gedrag. Zie belang rijke [wijzigingen](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) in de micro soft Identity platform-documentatie voor meer informatie.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors-inrichting integreren in azure AD en on-premises AD (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

U kunt SAP SuccessFactors nu integreren als een gezaghebbende identiteits bron in azure AD. Deze integratie helpt u bij het automatiseren van de end-to-end identiteits levenscyclus, met inbegrip van HR-gebeurtenissen, zoals nieuwe mede werkers of beëindigingen, voor het beheren van de inrichting van Azure AD-accounts.

Zie de zelf studie [SAP SuccessFactors Automatic Provisioning configureren](https://aka.ms/SAPSuccessFactorsInboundTutorial) voor meer informatie over het instellen van SAP SuccessFactors inkomend inrichten voor Azure AD.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Ondersteuning voor aangepaste e-mail berichten in Azure AD B2C (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

U kunt nu Azure AD B2C gebruiken om aangepaste e-mail berichten te maken wanneer uw gebruikers zich aanmelden om uw apps te gebruiken. Met behulp van DisplayControls (momenteel in Preview) en een e-mail provider van derden (zoals, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)of een aangepaste rest API), kunt u uw eigen e-mail sjabloon gebruiken, **van** adres en onderwerps tekst, maar ook lokalisatie en aangepaste otp-instellingen (eenmalig wacht woord) ondersteunen.

Zie [aangepaste e-mail verificatie in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)voor meer informatie.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Vervanging van basislijn beleid met standaard instellingen voor beveiliging

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

Als onderdeel van een beveiligd model voor verificatie, verwijderen we de bestaande Baseline-beveiligings beleidsregels van alle tenants. Deze verwijdering is bedoeld om aan het einde van februari te worden voltooid. De vervanging voor dit basis beveiligings beleid is de standaard instellingen voor beveiliging. Als u Baseline-beveiligings beleidsregels hebt gebruikt, moet u plannen om over te scha kelen naar het nieuwe beleid voor beveiligings standaarden of naar voorwaardelijke toegang. Als u dit beleid nog niet hebt gebruikt, hoeft u geen actie te ondernemen.

Zie wat zijn de standaard beveiligings [instellingen?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) voor meer informatie over de nieuwe standaard waarden voor de beveiliging. Zie [common Conditional Access policies](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)(Engelstalig) voor meer informatie over beleids regels voor voorwaardelijke toegang.

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor het kenmerk SameSite en Chrome 80

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Als onderdeel van een beveiligd standaard model voor cookies wordt de Chrome 80-browser gewijzigd hoe cookies zonder het kenmerk worden behandeld `SameSite` . Elke cookie waarbij het kenmerk niet `SameSite` wordt opgegeven, wordt behandeld alsof het is ingesteld op `SameSite=Lax` . Hierdoor kunnen Chrome bepaalde scenario's voor het delen van cookies op meerdere domeinen blok keren waarvan uw app afhankelijk is. Als u het oudere Chrome-gedrag wilt behouden, kunt u het `SameSite=None` kenmerk gebruiken en een extra `Secure` kenmerk toevoegen, zodat cookies voor meerdere locaties alleen toegankelijk zijn via HTTPS-verbindingen. Chrome is gepland om deze wijziging te volt ooien op 4 februari 2020.

We raden aan dat alle ontwikkel aars hun apps testen met behulp van deze richt lijnen:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- Stel de standaard waarde voor het kenmerk **SameSite** in op **geen**.

- Voeg een extra `SameSite` kenmerk van **Secure**toe.

Zie voor meer informatie [aanstaande SameSite cookie wijzigingen in ASP.net en ASP.net core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) en [mogelijke onderbreking van klant websites en micro soft-producten en-services in Chrome versie 79 en hoger](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nieuwe hotfix voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type:** Vaste  
**Service categorie:** Microsoft Identity Manager  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

Er is een hotfixcombinatiepakket (build 4.6.34.0) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Met dit pakket Rollup worden problemen opgelost en worden verbeteringen toegevoegd die worden beschreven in de sectie ' problemen opgelost en verbeteringen toegevoegd in deze update '.

Voor meer informatie over het downloaden van het hotfix-pakket raadpleegt u het [Update pakket voor Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) beschikbaar](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nieuw AD FS app-activiteiten rapport voor het migreren van apps naar Azure AD (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

Gebruik het nieuwe Active Directory Federation Services (AD FS) app-activiteiten rapport in de Azure Portal om te bepalen welke van uw apps kunnen worden gemigreerd naar Azure AD. In het rapport worden alle AD FS-apps beoordeeld op compatibiliteit met Azure AD, wordt er gecontroleerd of er problemen zijn en vindt u hulp bij het voorbereiden van afzonderlijke apps voor migratie.

Zie [het rapport AD FS toepassings activiteit gebruiken om toepassingen te migreren naar Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)voor meer informatie.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nieuwe werk stroom voor gebruikers om toestemming van de beheerder aan te vragen (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** Access Control

De nieuwe beheerder van de beheerders toestemming biedt beheerders een manier om toegang te verlenen tot apps waarvoor beheerder goed keuring is vereist. Als een gebruiker toegang probeert te krijgen tot een app, maar geen toestemming kan geven, kan deze nu een aanvraag indienen voor goed keuring door de beheerder. De aanvraag wordt per e-mail verzonden en in een wachtrij geplaatst die toegankelijk is vanaf de Azure Portal, naar alle beheerders die zijn aangewezen als controleurs. Nadat een revisor actie ondervindt voor een aanvraag in behandeling, worden de aanvragen van de gebruiker op de hoogte gesteld van de actie.

Zie [de beheerder toestemming werk stroom configureren (preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)voor meer informatie.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nieuwe Azure AD-app registraties de token configuratie-ervaring voor het beheren van optionele claims (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Ontwikkelaars ervaring

Op de Blade nieuw **Azure AD-App registraties voor token configuratie** op de Azure Portal worden nu app-ontwikkel aars een dynamische lijst met optionele claims voor hun apps weer gegeven. Deze nieuwe ervaring helpt bij het stroom lijnen van Azure AD-App-migraties en voor het minimaliseren van de configuratie van de optionele claims.

Zie [optionele claims voor uw Azure AD-app bieden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)voor meer informatie.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nieuwe goedkeurings werk stroom voor twee fases in azure AD-rechts beheer (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer rechten

We hebben een nieuwe goedkeurings werk stroom in twee fasen geïntroduceerd waarmee u twee goed keurders in staat stelt om de aanvraag van een gebruiker goed te keuren voor een toegangs pakket. U kunt dit bijvoorbeeld instellen zodat de Manager van de gebruiker die de aanvraag heeft ingediend eerst moet goed keuren, en u kunt ook een resource-eigenaar verplichten om goed te keuren. Als een van de goed keurders niet goed keuren, wordt geen toegang verleend.

Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket in azure AD-rechts beheer](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)voor meer informatie.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Updates voor de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product capaciteit:** integratie van derden

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de vernieuwde mijn apps-ervaring weer geven en openen. Deze nieuwe ervaring omvat ook de functie nieuwe werk ruimten, waarmee uw gebruikers gemakkelijker apps kunnen vinden en indelen.

Zie [werk ruimten maken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)voor meer informatie over de nieuwe ervaring voor het gebruik van mijn apps en het maken van werk ruimten.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Ondersteuning voor Google-sociale ID voor Azure AD B2B-samen werking (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** Gebruikers verificatie

Nieuwe ondersteuning voor het gebruik van Google-sociale Id's (Gmail-accounts) in azure AD helpt om samen werking eenvoudiger te maken voor uw gebruikers en partners. Het is niet meer nodig om uw partners om een nieuw micro soft-specifiek account te maken en te beheren. Micro soft teams biedt nu volledige ondersteuning voor Google-gebruikers op alle clients en in de gemeen schappelijke en Tenant-gerelateerde verificatie-eind punten.

Zie [Google als id-provider voor B2B-gast gebruikers toevoegen](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)voor meer informatie.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Mobiele ondersteuning van micro soft Edge voor voorwaardelijke toegang en eenmalige aanmelding (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Azure AD voor micro soft Edge op iOS en Android biedt nu ondersteuning voor eenmalige aanmelding en voorwaardelijke toegang van Azure AD:

- Eenmalige **aanmelding voor micro soft Edge (SSO):** Eenmalige aanmelding is nu beschikbaar via systeem eigen clients (zoals micro soft Outlook en micro soft Edge) voor alle apps die zijn verbonden met Azure AD.

- **Voorwaardelijke toegang voor micro soft Edge:** Met behulp van beleid voor voorwaardelijke toegang op basis van toepassingen moeten uw gebruikers gebruikmaken van Microsoft Intune beveiligde browsers, zoals micro soft Edge.

Voor meer informatie over voorwaardelijke toegang en eenmalige aanmelding met micro soft Edge raadpleegt u het blog bericht [mobiele ondersteuning voor voorwaardelijke toegang en eenmalige aanmelding die nu algemeen beschikbaar is](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Voor meer informatie over het instellen van uw client-apps met behulp [van voorwaardelijke toegang op basis van apps](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) of [voorwaardelijke toegang op basis](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)van een apparaat, raadpleegt [u Web access beheren met een met Microsoft intune-beleid beveiligde browser](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Beheer van rechten van Azure AD (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer rechten

Het beheer van rechten van Azure AD is een nieuwe functie voor Identity governance, waarmee organisaties de levens cyclus van identiteiten en toegang op schaal kunnen beheren. Deze nieuwe functie helpt u bij het automatiseren van werk stromen voor toegangs aanvragen, toegangs toewijzingen, beoordelingen en verloop tijd voor groepen, apps en share point online-sites.

Met het beheer van rechten van Azure AD kunt u de toegang efficiënter beheren voor werk nemers en voor gebruikers buiten uw organisatie die toegang nodig hebben tot deze resources.

Zie [Wat is Azure AD-rechts beheer?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) voor meer informatie.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden  

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

[SAP Cloud platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-november 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In november 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial) [, de](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [blauwe toegang voor leden (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal single sign on (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [FOKO Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets idee & innovatie beheer](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope gebruikers verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [jisc student gevoel registratie](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nieuwe en verbeterde Azure AD-toepassings galerie

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We hebben de Azure AD-toepassings galerie bijgewerkt zodat u gemakkelijk vooraf geïntegreerde apps kunt vinden die ondersteuning bieden voor inrichting, OpenID Connect Connect en SAML op uw Azure Active Directory-Tenant.

Zie [een toepassing toevoegen aan uw Azure Active Directory-Tenant](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)voor meer informatie.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>De lengte limiet van de functie definitie van de app-rol is verhoogd van 120 tot 240 tekens

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We hebben van klanten gehoord dat de lengte limiet voor de definitie waarde van de app-functie in sommige apps en services te kort is om 120 tekens. Als antwoord hebben we de maximale lengte van de roldefinitie-waarde verhoogd tot 240 tekens.

Zie [app-functies toevoegen in uw toepassing en ontvangen in het token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)voor meer informatie over het gebruik van toepassingsspecifieke roldefinities.

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Afschaffing van de identityRiskEvent-API voor detectie van Azure AD Identity Protection-Risico's

**Type:** Planning voor wijziging **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Als reactie op de feedback van ontwikkel aars kunnen Azure AD Premium P2-abonnees nu complexe query's uitvoeren op Azure AD Identity Protection risico detectie gegevens door gebruik te maken van de nieuwe riskDetection-API voor Microsoft Graph. De bestaande [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API bèta versie retourneert geen gegevens over **10 januari 2020**. Als uw organisatie gebruikmaakt van de identityRiskEvent-API, moet u overstappen naar de nieuwe riskDetection-API.

Zie de [referentie documentatie voor API voor risico detectie](https://aka.ms/RiskDetectionsAPI)voor meer informatie over de nieuwe RISKDETECTION-API.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor toepassings proxy voor het kenmerk SameSite en Chrome 80

**Type:** Planning voor wijziging **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Een paar weken voorafgaand aan de Chrome 80-browser release, willen we bijwerken hoe cookies van toepassings proxy het kenmerk **SameSite** behandelen. Met de release van Chrome 80 wordt elke cookie waarbij het kenmerk **SameSite** niet wordt opgegeven, behandeld alsof het is ingesteld op `SameSite=Lax` .

Om te voor komen dat mogelijk negatieve gevolgen zijn door deze wijziging, worden de toegangs-en sessie cookies van de toepassings proxy bijgewerkt door:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- De standaard waarde voor het kenmerk **SameSite** in te stellen op **geen**.

    >[!NOTE]
    > Application proxy-toegangs cookies zijn altijd uitsluitend via beveiligde kanalen verzonden. Deze wijzigingen zijn alleen van toepassing op sessie cookies.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)voor meer informatie over de cookie-instellingen van de toepassings proxy.

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>App-registraties (verouderd) en app-beheer in het portal voor toepassings registratie (apps.dev.microsoft.com) is niet meer beschikbaar

**Type:** Planning voor **service categorie wijzigen:** n.v.t. **product mogelijkheid:** ontwikkelaars ervaring

Gebruikers met Azure AD-accounts kunnen geen toepassingen meer registreren of beheren via de portal voor toepassings registratie (apps.dev.microsoft.com) of toepassingen registreren en beheren in de App-registraties (verouderde) ervaring in de Azure Portal.

Raadpleeg voor meer informatie over de nieuwe App-registraties-ervaring de [app-registraties in de hand leiding voor Azure Portal training](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers hoeven zich niet langer opnieuw te registreren tijdens de migratie van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

**Type:** Vaste **service categorie:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Er is een bekend probleem opgelost waarbij gebruikers zich opnieuw moeten registreren als ze zijn uitgeschakeld voor per gebruiker-Multi-Factor Authentication (MFA) en vervolgens via een beleid voor voorwaardelijke toegang zijn ingeschakeld voor MFA.

Als u wilt dat gebruikers zich opnieuw moeten registreren, kunt u de optie voor het **opnieuw registreren van MFA** selecteren uit de verificatie methoden van de gebruiker in de Azure AD-Portal. Voor meer informatie over het migreren van gebruikers van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang, Zie [gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nieuwe mogelijkheden voor het transformeren en verzenden van claims in uw SAML-token

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

We hebben extra mogelijkheden toegevoegd waarmee u claims kunt aanpassen en verzenden in uw SAML-token. Deze nieuwe mogelijkheden zijn onder andere:

- Extra functies voor het transformeren van claims, waarmee u de waarde die u in de claim verzendt, kunt wijzigen.

- De mogelijkheid om meerdere trans formaties op één claim toe te passen.

- De mogelijkheid om de claim bron op te geven op basis van het gebruikers type en de groep waartoe de gebruiker behoort.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie over deze nieuwe mogelijkheden, inclusief hoe u deze kunt gebruiken.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Pagina nieuwe mijn aanmeldingen voor eind gebruikers in azure AD

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** bewaking & rapportage

We hebben een nieuwe pagina **mijn aanmeldingen** toegevoegd ( https://mysignins.microsoft.com) zodat de gebruikers van uw organisatie hun recente aanmeldings geschiedenis kunnen bekijken om te controleren of er ongebruikelijke activiteiten zijn. Met deze nieuwe pagina kunnen uw gebruikers het volgende zien:

- Als iemand probeert hun wacht woord te raden.

- Als een aanvaller zich heeft aangemeld bij hun account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

Zie voor meer informatie de [gebruikers kunnen nu hun aanmeldings geschiedenis controleren op blog van ongebruikelijke activiteiten](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migratie van Azure AD Domain Services (Azure AD DS) van het klassieke naar Azure Resource Manager virtuele netwerken

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Voor onze klanten die zijn vastgelopen op klassieke virtuele netwerken, hebben we een geweldig nieuws voor u. U kunt nu een eenmalige migratie uitvoeren van een klassiek virtueel netwerk naar een bestaand virtueel netwerk van Resource Manager. Nadat u bent overgestapt op het virtuele netwerk van Resource Manager, kunt u profiteren van de aanvullende en bijgewerkte functies, zoals een verfijnd wachtwoord beleid, e-mail meldingen en audit Logboeken.

Zie [Preview-Azure AD Domain Services migreren van het klassieke virtuele netwerk naar Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)voor meer informatie.

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates voor de indeling van het Azure AD B2C-pagina contract

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

We hebben een aantal nieuwe wijzigingen geïntroduceerd in versie 1.2.0 van het pagina contract voor Azure AD B2C. In deze bijgewerkte versie kunt u nu de laad volgorde voor uw elementen beheren. deze kan ook helpen bij het stoppen van de Flik kering die optreedt wanneer het opmaak model (CSS) wordt geladen.

Raadpleeg het [logboek voor versie](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)wijzigingen voor een volledige lijst van de wijzigingen die in het pagina-contract zijn aangebracht.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Bijwerken naar de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** Nieuwe functie **service categorie:** mijn apps **Product mogelijkheid:** Access Control

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de gloed nieuwe mijn apps-ervaring weer geven en openen, met inbegrip van het gebruik van de nieuwe functie werk ruimten, om het voor hen gemakkelijker te maken apps te vinden. De nieuwe functionaliteit voor werk ruimten fungeert als een filter voor de apps waartoe gebruikers van de organisatie al toegang hebben.

Zie [werk ruimten maken in de portal mijn apps (preview-versie)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)voor meer informatie over het implementeren van de nieuwe mijn apps-ervaring en het maken van werk ruimten.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Ondersteuning voor het maandelijks actieve facturerings model op basis van gebruikers (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

Azure AD B2C ondersteunt nu de facturering van maandelijkse actieve gebruikers (MAU). MAU-facturering is gebaseerd op het aantal unieke gebruikers met verificatie activiteiten tijdens een kalender maand. Bestaande klanten kunnen op elk gewenst moment overstappen op deze nieuwe facturerings methode.

Vanaf 1 november 2019 worden alle nieuwe klanten automatisch gefactureerd met behulp van deze methode. Deze facturerings methode biedt klanten de voor delen van kosten en de mogelijkheid om vooruit te plannen.

Zie voor meer informatie [upgraden naar het facturerings model maandelijkse actieve gebruikers](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-oktober 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In oktober 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[In het geval van crisis: Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno traject](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [intact](https://tact.ai/assistant/), [OpusCapita Liquid Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contented](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europa](https://www.hirevue.com/), [HireVue-coördinaat-USOnly](https://www.hirevue.com/), [HIREVUE-coördinaten-US](https://www.hirevue.com/), [WittyParrot kennis Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mail slag!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [snelheid voor teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigeer IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [spreek mail voor intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [gesp roken e-mail voor Office 365 direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care-navigatie systeem](https://ihealthnav.com/account/signin)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menu-item voor geconsolideerde beveiliging in de Azure AD-Portal

**Type:** Gewijzigde functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

U hebt nu toegang tot alle beschik bare Azure AD-beveiligings functies vanuit het menu met nieuwe **beveiliging** en op de **Zoek** balk in de Azure Portal. Daarnaast biedt de nieuwe **beveiligings** landings pagina, met de naam **beveiliging-aan de slag**, koppelingen naar onze open bare documentatie, beveiligings richtlijnen en implementatie handleidingen.

Het menu nieuwe **beveiliging** bevat:

- Voorwaardelijke toegang
- Identiteitsbeveiliging
- Beveiligingscentrum
- Identiteits veilige Score
- Verificatiemethoden
- MFA
- Risico rapporten-Risk ante gebruikers, Risk ante aanmeldingen, risico detecties
- En nog veel meer...

Zie [beveiliging-aan de slag](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)voor meer informatie.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365-groepen verloop beleid verbeterd met autoverlengen

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product mogelijkheden:** Identity Lifecycle Management

Het verloop beleid van de Office 365-groepen is uitgebreid met het automatisch vernieuwen van groepen die momenteel door de leden van de groep worden gebruikt. Groepen worden automatisch vernieuwd op basis van gebruikers activiteit in alle Office 365-apps, waaronder Outlook, share point en teams.

Deze verbetering helpt u bij het verminderen van uw groeps meldingen en helpt ervoor te zorgen dat actieve groepen nog steeds beschikbaar zijn. Als u al een actief verloop beleid hebt voor uw Office 365-groepen, hoeft u niets te doen om deze nieuwe functionaliteit in te scha kelen.

Zie [het verloop beleid voor Office 365-groepen configureren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)voor meer informatie.

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Werk ervaring bij het maken van Azure AD Domain Services (Azure AD DS) is bijgewerkt

**Type:** Gewijzigde functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

We hebben Azure AD Domain Services (Azure AD DS) bijgewerkt met een nieuwe en verbeterde ervaring voor het maken van een beheerd domein in slechts drie muis klikken. Daarnaast kunt u nu Azure-AD DS uploaden en implementeren vanuit een sjabloon.

Zie [zelf studie: een Azure Active Directory Domain Services-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)voor meer informatie.

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plannen voor wijziging: afschaffing van de Power BI inhouds pakketten

**Type:** Planning voor **service categorie wijzigen:** rapportage van **product mogelijkheden:** bewaking & rapportage

Vanaf 1 oktober 2019 worden alle inhouds pakketten, inclusief het Azure AD Power BI-inhouds pakket, door Power BI af te nemen. Als alternatief voor dit inhouds pakket kunt u Azure AD-werkmappen gebruiken om inzicht te krijgen in uw Azure AD-gerelateerde services. Er zijn extra werkmappen beschikbaar, waaronder werkmappen over beleids regels voor voorwaardelijke toegang in de modus alleen rapport, inzichten op basis van apps en meer.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)voor meer informatie over de werkmappen. Voor meer informatie over de afschaffing van de inhouds pakketten raadpleegt u het blog bericht [Power bi sjabloon apps voor algemene Beschik baarheid aankondigen](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mijn profiel is een nieuwe naam en integratie met de pagina Microsoft Office-account

**Type:** Planning voor wijziging **service categorie:** mijn profiel/account **product mogelijkheid:** samen werking

Vanaf oktober wordt de ervaring mijn profiel van mijn account. Als onderdeel van deze wijziging, wordt **Mijn profiel** gewijzigd in **Mijn account**. Naast de naam wijziging en enkele ontwerp verbeteringen, biedt de bijgewerkte ervaring aanvullende integratie met de pagina Microsoft Office-account. U hebt met name toegang tot Office-installaties en-abonnementen via de pagina **overzichts account** , samen met Office-gerelateerde contact voorkeuren van de pagina **Privacy** .

Zie voor meer informatie over de ervaring mijn profiel (preview) [Mijn profiel (preview-versie) van portal](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Groepen en leden bulksgewijs beheren met CSV-bestanden in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de ervaring voor bulksgewijs groeps beheer in de Azure AD-Portal. U kunt nu een CSV-bestand en de Azure AD-Portal gebruiken om groepen en leden lijsten te beheren, met inbegrip van:

- Leden toevoegen aan of verwijderen uit een groep.

- De lijst met groepen wordt gedownload uit de map.

- De lijst met groeps leden voor een specifieke groep downloaden.

Zie voor meer informatie [bulksgewijs toevoegen leden](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [leden bulksgewijs verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [lijst met leden en items](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)bulksgewijs downloaden en [groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamische toestemming wordt nu ondersteund via een nieuw door de beheerder toestemmings eindpunt

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

We hebben een nieuw beheerders toestemmings eindpunt gemaakt ter ondersteuning van dynamische toestemming. Dit is handig voor apps waarvoor het dynamische toestemming model op het micro soft Identity-platform moet worden gebruikt.

Zie [using the Administrator toestemming endpoint](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)(Engelstalig) voor meer informatie over het gebruik van dit nieuwe eind punt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-september 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In september 2019 hebben we deze 29 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access voor Ethidex-naleving &trade; Office-eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [concur Traveling and lasten](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/` , [Arc facilities](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus team](https://stratus.emea.luware.cloud/login), [Wide ideeën](https://wideideas.online/wideideas/), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT client hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/` , `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [gehoste erfgoed online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai-team build](https://ms-contacts.coo-kai.jp/) [, Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [Discovery bene fits](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-azure-ad-global-reader-role"></a>Nieuwe globale rol van Azure AD-lezer

**Type:** Nieuwe functie **service categorie:** Azure AD roles- **Product mogelijkheden:** Access Control

Vanaf 24 september 2019 beginnen we met het implementeren van een nieuwe Azure Active Directory-rol (AD) met de naam Global Reader. Deze implementatie begint met productie-en mondiale Cloud klanten (GCC), die wereld wijd in oktober wordt voltooid.

De rol van algemene lezer is het alleen-lezen equivalent van de globale beheerder. Gebruikers met deze rol kunnen instellingen en beheer gegevens in Microsoft 365 services lezen, maar kunnen geen beheer acties uitvoeren. We hebben de algemene rol lezer gemaakt om het aantal globale beheerders in uw organisatie te verminderen. Omdat globale beheerders accounts krachtig en kwetsbaar zijn voor aanvallen, raden wij aan dat u minder dan vijf globale beheerders hebt. We raden u aan de rol van globale lezer te gebruiken voor het plannen, controleren of onderzoeken. We raden u ook aan de rol van globale lezer te gebruiken in combi natie met andere beperkte beheerders rollen, zoals Exchange Administrator, om aan de slag te gaan zonder dat hiervoor de rol van globale beheerder is vereist.

De rol van algemene lezer werkt met de nieuwe Microsoft 365 beheer centrum, het Exchange-beheer centrum, teams beheer centrum, Security Center, compliance Center, Azure AD-beheer centrum en het beheer centrum voor Apparaatbeheer.

>[!NOTE]
> Aan het begin van de open bare preview-versie werkt de rol van globale lezer niet met: share point, Privileged Access Management, Klanten-lockbox, gevoeligheids labels, teams levenscyclus, teams rapporteren & oproep analyse, teams IP-telefoon Apparaatbeheer en teams-app-catalogus.

Zie [machtigingen voor beheerdersrol in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)voor meer informatie.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Toegang tot een on-premises rapport server vanuit uw Power BI-Mobiel-app met behulp van Azure Active Directory-toepassingsproxy

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Dankzij de nieuwe integratie tussen de Power BI mobiele app en Azure AD-toepassingsproxy kunt u zich veilig aanmelden bij de mobiele app van Power BI en de rapporten van uw organisatie weer geven die worden gehost op de on-premises Power BI Report Server.

Zie de [Power bi-site](https://powerbi.microsoft.com/mobile/)voor meer informatie over de Power bi-mobiel-app, inclusief de plek waar u de app kunt downloaden. Zie voor meer informatie over het instellen van de Power BI mobiele app met Azure AD-toepassingsproxy [externe toegang inschakelen voor Power bi-mobiel met azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Er is een nieuwe versie van de Power shell-module AzureADPreview beschikbaar

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module, waarmee u aangepaste rollen kunt definiëren en toewijzen in azure AD, waaronder:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nieuwe versie van Azure AD Connect

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

We hebben een bijgewerkte versie van Azure AD Connect uitgebracht voor klanten met automatische upgrades. Deze nieuwe versie bevat verschillende nieuwe functies, verbeteringen en oplossingen voor problemen. Zie [Azure AD Connect: release geschiedenis](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)van de versie voor meer informatie over deze nieuwe versie.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA)-server, versie 8.0.2 is nu beschikbaar

**Type:** Vaste **service categorie:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

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

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de uitgebreide ervaringen met groepen in de Azure AD-Portal. Deze verbeteringen helpen u bij het beheren van groepen en leden lijsten door het volgende te bieden:

- Geavanceerde zoek functies, zoals subtekenreeksen, zoeken in groepen-lijsten.
- Geavanceerde filter-en sorteer opties voor de lijsten leden en eigen aren.
- Nieuwe zoek mogelijkheden voor lijsten van leden en eigen aren.
- Nauw keurigere groeps aantallen voor grote groepen.

Zie [groepen beheren in de Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)voor meer informatie.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Er zijn nieuwe aangepaste rollen beschikbaar voor app-registratie beheer (open bare preview)

**Type:** Nieuwe functie **service categorie:** Azure AD roles- **Product mogelijkheden:** Access Control

Aangepaste rollen (beschikbaar met een Azure AD P1-of P2-abonnement) kunnen u nu voorzien van gedetailleerde toegang, door roldefinities te maken met specifieke machtigingen en deze rollen vervolgens toe te wijzen aan specifieke resources. Op dit moment kunt u aangepaste rollen maken met behulp van machtigingen voor het beheren van app-registraties en vervolgens de rol toewijzen aan een specifieke app. Zie [aangepaste beheerders rollen in azure Active Directory (preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)voor meer informatie over aangepaste rollen.

Als u aanvullende machtigingen of bronnen nodig hebt die momenteel niet worden weer gegeven, kunt u feedback verzenden naar onze [Azure-feedback site](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) . we voegen uw aanvraag toe aan onze update-weg kaart.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Met nieuwe inrichtings Logboeken kunt u de implementatie van de app-inrichting controleren en problemen oplossen (open bare preview)

**Type:** Nieuwe functie **service categorie:** product inrichting van de app **:** Identity Lifecycle Management

Er zijn nieuwe inrichtings logboeken beschikbaar om u te helpen bij het controleren en oplossen van problemen met de implementatie van gebruikers en groepen. Deze nieuwe logboek bestanden bevatten informatie over:

- Welke groepen zijn gemaakt in [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Welke rollen zijn geïmporteerd uit [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- De werk nemers die niet zijn geïmporteerd uit [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Zie [inrichtings rapporten in de Azure Active Directory Portal (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)voor meer informatie.

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nieuwe beveiligings rapporten voor alle Azure AD-beheerders (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

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

**Type:** Nieuwe functie **service categorie:** beheerde identiteiten voor Azure-resources **product mogelijkheden:** ontwikkelaars ervaring

Door de gebruiker toegewezen beheerde identiteiten zijn nu algemeen beschikbaar voor Virtual Machines en Virtual Machine Scale Sets. Als onderdeel hiervan kan Azure een identiteit maken in de Azure AD-Tenant die wordt vertrouwd door het abonnement en kan worden toegewezen aan een of meer Azure-service-exemplaren. Zie [Wat is beheerde identiteiten voor Azure-resources?](https://aka.ms/azuremanagedidentity)voor meer informatie over door de gebruiker toegewezen beheerde identiteiten.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gebruikers kunnen hun wacht woord opnieuw instellen met behulp van een mobiele app of een hardware-token (algemene Beschik baarheid)

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

Gebruikers die een mobiele app hebben geregistreerd bij uw organisatie kunnen nu hun eigen wacht woord opnieuw instellen door een melding van de Microsoft Authenticator-app goed te keuren of door een code uit hun mobiele app of hardware-token in te voeren.

Zie [hoe het werkt: Azure AD self-service password reset](https://aka.ms/authappsspr)(Engelstalig) voor meer informatie. Zie [uw eigen werk-of school wachtwoord opnieuw instellen-overzicht](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)voor meer informatie over de gebruikers ervaring.

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET negeert de gedeelde cache van MSAL.NET voor namens een scenario

**Type:** Vaste **service categorie:** authenticaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Vanaf Azure AD Authentication Library (ADAL.NET) versie 5.0.0-Preview moeten app-ontwikkel aars [één cache per account serialiseren voor web-apps en Web-api's](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Anders is het mogelijk dat sommige scenario's die gebruikmaken van de [namen van de stroom](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), samen met een aantal specifieke gebruiks cases `UserAssertion` , een verhoogde bevoegdheid hebben. Om dit beveiligings probleem te voor komen, negeert ADAL.NET nu de micro soft-verificatie bibliotheek voor dotnet (MSAL.NET) gedeelde cache voor namens een scenario.

Zie [Azure Active Directory beveiligingslek met betrekking tot misbruik van bevoegdheden voor verificatie bibliotheek](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)voor meer informatie over dit probleem.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-augustus 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2019 augustus hebben we deze 26 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Civic-platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos OPS Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy-aanwezigheid](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync door CinCom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliverable. media &trade; Portal](https://portal.deliver.media), [Frontline onderwijs](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [bekijken op kleuren](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [harnas](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate strategisch Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Er zijn nieuwe versies van de Power shell-en AzureADPreview-AzureAD

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

Er zijn nieuwe updates beschikbaar voor de AzureAD-en AzureAD preview Power shell-modules:

- Er is een nieuwe `-Filter` para meter toegevoegd aan de `Get-AzureADDirectoryRole` para meter in de AzureAD-module. Met deze para meter kunt u filteren op de Directory functies die door de cmdlet worden geretourneerd.
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

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product capaciteit:** samen werking

Er zijn enkele verbeteringen in de gebruikers interface aangebracht in de opbouw functie voor dynamische groeps regels, die beschikbaar is in de Azure Portal, zodat u gemakkelijk een nieuwe regel kunt instellen of bestaande regels kunt wijzigen. Met deze ontwerp verbetering kunt u regels maken met Maxi maal vijf expressies, in plaats van slechts één. Daarnaast hebben we de lijst met eigenschappen van het apparaat bijgewerkt om afgeschafte apparaateigenschappen te verwijderen.

Zie [regels voor dynamische lidmaatschap beheren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)voor meer informatie.

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Er is een nieuwe Microsoft Graph app-machtiging beschikbaar voor gebruik met toegangs beoordelingen

**Type:** Gewijzigde functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** Identity governance

We hebben een nieuwe Microsoft Graph app-machtiging geïntroduceerd, `AccessReview.ReadWrite.Membership` waarmee apps automatisch toegangs Beoordelingen voor groepslid maatschappen en app-toewijzingen kunnen maken en ophalen. Deze machtiging kan worden gebruikt door uw geplande taken of als onderdeel van uw automatisering zonder dat hiervoor een aangemelde gebruikers context nodig is.

Zie voor meer informatie het [maken van Azure AD-toegangs beoordelingen met Microsoft Graph app-machtigingen met Power shell-blog](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar voor overheids Cloud instanties in Azure Monitor

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

We zijn enthousiast dat Azure AD-activiteiten logboeken nu beschikbaar zijn voor instanties van overheids-Clouds in Azure Monitor. U kunt nu Azure AD-logboeken naar uw opslag account of een Event Hub verzenden om te integreren met uw SIEM-hulpprogram ma's, zoals [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)en [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight).

Voor meer informatie over het instellen van Azure Monitor raadpleegt u [Azure AD-activiteiten Logboeken in azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Werk uw gebruikers bij naar de nieuwe, verbeterde ervaring voor beveiligings informatie

**Type:** Gewijzigde functie **service categorie:**  verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Op 25 september 2019 worden de oude, niet-verbeterde beveiligings gegevens voor het registreren en beheren van gebruikers beveiligings gegevens uitgeschakeld en wordt alleen de nieuwe, [verbeterde versie](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)ingeschakeld. Dit betekent dat uw gebruikers de oude ervaring niet langer kunnen gebruiken.

Zie onze [beheerders documentatie](https://aka.ms/securityinfodocs) en onze [gebruikers documentatie](https://aka.ms/securityinfoguide)voor meer informatie over de verbeterde ervaring met beveiligings informatie.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Als u deze nieuwe ervaring wilt inschakelen, moet u het volgende doen:

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder.

2. Ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster beheren**.

3. Selecteer in de sectie **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiligings gegevens-uitgebreid** gebied **geselecteerd**, en kies vervolgens een groep gebruikers of kies **alle** om deze functie in te scha kelen voor alle gebruikers in de Tenant.

4. In de * * gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiliging * * info * * * * *. Selecteer **geen**.

5. Sla uw instellingen op.

    Nadat u de instellingen hebt opgeslagen, hebt u geen toegang meer tot de oude beveiligings informatie.

>[!Important]
>Als u deze stappen vóór 25 september 2019 niet voltooit, wordt uw Azure Active Directory-Tenant automatisch ingeschakeld voor de verbeterde ervaring. Als u vragen hebt, kunt u contact met ons opnemen via registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Verificatie aanvragen via POST-aanmeldingen worden strikter gevalideerd

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** standaarden

Vanaf 2 september 2019 worden verificatie aanvragen via de POST-methode strikt gevalideerd op basis van de HTTP-standaarden. In het bijzonder worden spaties en dubbele aanhalings tekens (") niet meer verwijderd uit de waarden van het aanvraag formulier. Deze wijzigingen worden niet verwacht om bestaande clients te verstoren en ervoor te zorgen dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden afgehandeld.

Zie voor meer informatie de [meldingen over Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)-onderbrekingen.

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plannen voor wijziging: update van Application proxy-service om alleen TLS 1,2 te ondersteunen

**Type:** Planning voor wijziging **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Om u te helpen met onze krach tigste versleuteling, gaan we de toegang tot de toepassings proxy service beperken tot alleen TLS 1,2-protocollen. Deze beperking wordt eerst doorgevoerd naar klanten die al gebruikmaken van TLS 1,2-protocollen, zodat u de impact niet kunt zien. De afronding van de protocollen TLS 1,0 en TLS 1,1 is voltooid op 31 augustus 2019. Klanten die nog TLS 1,0 en TLS 1,1 gebruiken, ontvangen een geavanceerde kennisgeving om deze wijziging voor te bereiden.

Als u de verbinding met de service toepassings proxy tijdens deze wijziging wilt behouden, kunt u het beste controleren of uw combi Naties van client-server en browser-server zijn bijgewerkt voor het gebruik van TLS 1,2. We raden u ook aan om ervoor te zorgen dat alle client systemen die door uw werk nemers worden gebruikt, toegang hebben tot apps die zijn gepubliceerd via de Application proxy-service.

Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)voor meer informatie.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plannen voor wijziging: ontwerp updates zijn beschikbaar voor de toepassings galerie

**Type:** Planning voor **service categorie wijzigen:** Enter prise apps **product Capability:** SSO

Nieuwe wijzigingen in de gebruikers interface zijn afkomstig van het ontwerp van de **toevoegen vanuit het gebied Galerie** van de Blade **een toepassing toevoegen** . Met deze wijzigingen kunt u eenvoudig uw apps vinden die ondersteuning bieden voor automatische inrichting, OpenID Connect Connect, Security Assertion Markup Language (SAML) en een wacht woord voor eenmalige aanmelding (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plannen voor wijziging: het IP-adres van de MFA-server verwijderen uit het Office 365 IP-adres

**Type:** Planning voor **service categorie wijzigen:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Het IP-adres van de MFA-server wordt verwijderd uit het [Office 365 IP-adres en de URL-webservice](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Als u momenteel afhankelijk bent van deze pagina's om uw firewall instellingen bij te werken, moet u ervoor zorgen dat u ook de lijst met IP-adressen die worden beschreven in de sectie **azure multi-factor Authentication-Server-firewall vereisten** van het artikel aan de slag [met Azure multi-factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Voor alleen app-tokens moet de client-app bestaan in de resource Tenant

**Type:** Vaste **service categorie:** authenticaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Op 26 juli 2019 hebben we gewijzigd hoe we alleen app-tokens bieden via de [client referenties verlenen](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Voorheen konden apps tokens krijgen om andere apps aan te roepen, ongeacht of de client-app zich in de Tenant bevindt. We hebben dit gedrag bijgewerkt, waardoor enkele Tenant bronnen, ook wel web-Api's genoemd, alleen kunnen worden aangeroepen door client-apps die voor komen in de resource-Tenant.

Als uw app zich niet in de resource Tenant bevindt, wordt er een fout bericht weer gegeven waarin wordt vermeld, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` om dit probleem op te lossen, moet u de client app Service-Principal in de Tenant maken met behulp van het [beheerders toestemmings eindpunt](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) of [via Power shell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), zodat uw Tenant de app toestemming heeft gegeven om binnen de Tenant te werken.

Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)voor meer informatie.

> [!NOTE]
> Bestaande toestemming tussen de client en de API blijft niet vereist. Apps moeten nog steeds hun eigen autorisatie controles uitvoeren.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nieuwe aanmelding zonder wacht woord voor Azure AD met behulp van FIDO2-beveiligings sleutels

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD-klanten kunnen nu beleid instellen voor het beheren van FIDO2-beveiligings sleutels voor de gebruikers en groepen van hun organisatie. Eind gebruikers kunnen hun beveiligings sleutels ook zelf registreren, de sleutels gebruiken om zich aan te melden bij hun micro soft-accounts op websites, terwijl ze op FIDO apparaten werken, en aanmelden bij hun Azure AD-Windows 10-apparaten.

Zie voor meer informatie [aanmelden zonder wacht woord inschakelen voor Azure AD (preview)](/azure/active-directory/authentication/concept-authentication-passwordless) voor informatie die betrekking heeft op de beheerder en [Stel beveiligings informatie in voor het gebruik van een beveiligings sleutel (preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) voor informatie die aan de eind gebruiker is gerelateerd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-juli 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juli 2019 zijn deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [helder patroon Omnichannel contact centrum](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [slimme Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex compliance &trade; Office](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hele hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [schuinis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nieuwe Azure AD Domain Services servicetag voor de netwerk beveiligings groep

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Als u veel lijsten met IP-adressen en-bereiken niet meer wilt beheren, kunt u de nieuwe **AzureActiveDirectoryDomainServices** -netwerk servicetag gebruiken in uw Azure-netwerk beveiligings groep om inkomend verkeer naar uw Azure AD Domain Services subnet van het virtuele netwerk te beveiligen.

Zie [netwerk beveiligings groepen voor Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)voor meer informatie over deze nieuwe servicetag.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services als kritisch inzicht verschaffen door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD Domain Service-Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)voor meer informatie.

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nieuwe verificatie methoden Usage & Insights (open bare preview)

**Type:** Nieuwe functie **service categorie:** self-service voor het opnieuw instellen van wacht woorden **:** bewaking & rapportage

Met de nieuwe verificatie methoden & Insights-rapporten kunt u begrijpen hoe functies zoals Azure Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden worden geregistreerd en gebruikt in uw organisatie, met inbegrip van het aantal geregistreerde gebruikers voor elke functie, hoe vaak de selfservice voor wachtwoord herstel wordt gebruikt om wacht woorden opnieuw in te stellen en op basis van welke methode het opnieuw moet worden ingesteld.

Zie het gebruik van de [verificatie methoden & Insights (preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)voor meer informatie.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nieuwe beveiligings rapporten zijn beschikbaar voor alle Azure AD-beheerders (open bare preview)

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Alle Azure AD-beheerders kunnen nu het vaandel selecteren boven aan bestaande beveiligings rapporten, zoals de gebruikers die zijn **gemarkeerd voor risico** rapport, om de nieuwe beveiligings ervaring te gebruiken, zoals wordt weer gegeven in de rapporten **Risk ante gebruikers** en **Risk ante aanmeldingen** . Na verloop van tijd worden alle beveiligings rapporten van oudere versies naar de nieuwe versies verplaatst, met de nieuwe rapporten die u de volgende aanvullende mogelijkheden bieden:

- Geavanceerd filteren en sorteren

- Bulk acties, zoals het negeren van gebruikers Risico's

- Bevestiging van aangetast of veilige entiteiten

- Risico status, waaronder: risico, genegeerd, hersteld en bevestigde schadelijke

Zie rapport met [Risk ante gebruikers](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) en [rapporten met Risk ante aanmeldingen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)voor meer informatie.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services als kritisch inzicht verschaffen door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD Domain Service-Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)voor meer informatie.

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nieuwe B2B direct Federation met behulp van SAML/WS-voeder (open bare preview)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

Directe Federatie maakt het gemakkelijker voor u om te werken met partners waarvan de IT-beheerde identiteits oplossing niet Azure AD is, door te werken met identiteits systemen die ondersteuning bieden voor de SAML-of WS-voeder-standaarden. Nadat u een directe Federatie relatie met een partner hebt ingesteld, kan elke nieuwe gast gebruiker die u uit dat domein uitnodigt met behulp van hun bestaande organisatie account samen werken, waardoor de gebruikers ervaring voor uw gasten naadloos wordt.

Zie voor meer informatie [directe Federatie met AD FS en providers van derden voor gast gebruikers (preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nieuwe controle op dubbele groeps namen in de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

Wanneer u nu een groeps naam vanuit de Azure AD-Portal maakt of bijwerkt, wordt er een controle uitgevoerd om te zien of u een bestaande groeps naam in uw resource dupliceert. Als we bepalen dat de naam al wordt gebruikt door een andere groep, wordt u gevraagd om uw naam te wijzigen.

Zie [groepen beheren in de Azure AD-Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)voor meer informatie.

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD biedt nu ondersteuning voor statische query parameters in antwoord-Uri's (redirect)

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD-apps kunnen nu Uri's voor beantwoorden (omleiden) registreren en gebruiken met statische query parameters (bijvoorbeeld `https://contoso.com/oauth2?idp=microsoft` ) voor OAuth 2,0-aanvragen. De statische query parameter is onderhevig aan de teken reeks die overeenkomt met antwoord-Uri's, net als elk ander deel van de antwoord-URI. Als er geen geregistreerde teken reeks is die overeenkomt met de door de URL gedecodeerde omleidings-URI, wordt de aanvraag geweigerd. Als de antwoord-URI wordt gevonden, wordt de gehele teken reeks gebruikt om de gebruiker te omleiden, inclusief de para meter static query.

Dynamische antwoord-Uri's zijn nog niet toegestaan omdat ze een beveiligings risico vertegenwoordigen en niet kunnen worden gebruikt om status informatie over een verificatie aanvraag te houden. Voor dit doel gebruikt u de `state` para meter.

Momenteel zijn de app-registratie schermen van de Azure Portal nog steeds blok keren query parameters. U kunt het app-manifest echter hand matig bewerken om query parameters toe te voegen en te testen in uw app. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)voor meer informatie.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Activiteiten Logboeken (MS Graph-Api's) voor Azure AD zijn nu beschikbaar via Power shell-cmdlets

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Met trots kondigen we aan dat Azure AD-activiteiten Logboeken (rapporten over controles en aanmeldingen) nu beschikbaar zijn via de Azure AD Power shell-module. U kunt voorheen uw eigen scripts maken met behulp van MS Graph API-eind punten en nu hebben we die mogelijkheid tot Power shell-cmdlets uitgebreid.

Zie [Azure AD Power shell-cmdlets voor rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)voor meer informatie over het gebruik van deze cmdlets.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Bijgewerkte filter besturings elementen voor controle-en aanmeld Logboeken in azure AD

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

De controle-en aanmeldings logboek rapporten zijn bijgewerkt, zodat u nu verschillende filters kunt Toep assen zonder ze als kolommen toe te voegen aan de rapport schermen. Daarnaast kunt u bepalen hoeveel filters u wilt weer geven op het scherm. Deze updates werken allemaal samen om uw rapporten gemakkelijker te kunnen lezen en zo meer te bereiken aan uw behoeften.

Zie [controle logboeken filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) en [aanmeldings activiteiten filteren](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)voor meer informatie over deze updates.

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nieuwe riskDetections-API voor Microsoft Graph (open bare preview)

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

We zijn blij met het aankondigen van de nieuwe riskDetections-API voor Microsoft Graph nu in open bare preview. U kunt deze nieuwe API gebruiken om een lijst weer te geven met de identiteits beveiliging van de gebruiker en de detectie van de aanmeldings Risico's van uw organisatie. U kunt deze API ook gebruiken om efficiënter een query uit te voeren op uw risico detecties, inclusief details over het detectie type, de status, het niveau en meer.

Zie de [referentie documentatie voor API voor risico detectie](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juni 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juni 2019 hebben we deze 22 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [helper helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager voor Oracle retail merchandising, Oracle Access Manager voor Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS voor People Soft, Oracle IDCS voor JD Edwards

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [In- en uitzoomen](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>De real-time voortgang van de Azure AD-inrichtings service weer geven

**Type:** Gewijzigde functie **service categorie:** app Provisioning **product Capability:** Identity Lifecycle Management

We hebben de Azure AD-inrichtings ervaring bijgewerkt zodat er een nieuwe voortgangs balk wordt weer gegeven waarin u kunt zien hoe ver u het proces voor het inrichten van gebruikers hebt. Deze bijgewerkte ervaring bevat ook informatie over het aantal gebruikers dat is ingericht tijdens de huidige cyclus, en hoeveel gebruikers tot nu toe zijn ingericht.

Zie [de status van gebruikers inrichten controleren](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)voor meer informatie.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>De huis stijl van het bedrijf wordt nu weer gegeven bij afmelden en fout schermen

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD is bijgewerkt, zodat de huis stijl van uw bedrijf nu wordt weer gegeven op de schermen voor afmelden en fout en op de aanmeldings pagina. U hoeft niets te doen om deze functie in te scha kelen. Azure AD maakt gewoon gebruik van de activa die u al hebt ingesteld in het **huis stijlgebied** van de Azure Portal.

Zie [huisstijl toevoegen aan de Azure Active Directory pagina's van uw organisatie](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)voor meer informatie over het instellen van de huis stijl van uw bedrijf.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>De Azure Multi-Factor Authentication-Server (MFA) is niet meer beschikbaar voor nieuwe implementaties

**Type:** Afgeschafte **service categorie:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Met ingang van 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication in hun organisatie willen vereisen, moeten nu Azure Multi-Factor Authentication op basis van de Cloud gebruiken. Klanten die de MFA-server vóór 1 juli hebben geactiveerd, zien geen wijziging. U kunt nog steeds de nieuwste versie downloaden, toekomstige updates ophalen en activerings referenties genereren.

Zie [aan de slag met de Azure-multi-factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)voor meer informatie. Zie [een Cloud implementatie op basis van multi-factor Authentication Azure plannen](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)voor meer informatie over Azure multi-factor Authentication op basis van de Cloud.

---

## <a name="may-2019"></a>Mei 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Service wijziging: toekomstige ondersteuning voor alleen TLS 1,2-protocollen op de Application proxy-service

**Type:** Planning voor wijziging **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Om de beste versleuteling voor onze klanten te bieden, beperken we de toegang tot alleen TLS 1,2-protocollen op de Application proxy-service. Deze wijziging wordt geleidelijk doorgevoerd naar klanten die al alleen gebruikmaken van TLS 1,2-protocollen, zodat u geen wijzigingen ziet.

De afschaffing van TLS 1,0 en TLS 1,1 gebeurt op 31 augustus 2019, maar we bieden nog meer geavanceerde kennisgevingen, dus u hebt de tijd om deze wijziging voor te bereiden. Om deze wijziging voor te bereiden, moet u ervoor zorgen dat de combi Naties van client-server en browser server, inclusief alle clients die uw gebruikers gebruiken om toegang te krijgen tot apps die zijn gepubliceerd via toepassings proxy, worden bijgewerkt met het TLS 1,2-protocol om de verbinding met de Application proxy-service te onderhouden. Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)voor meer informatie.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Gebruik het rapport gebruik en inzichten om uw app-gerelateerde aanmeldings gegevens weer te geven

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het rapport gebruik en inzichten gebruiken, dat zich bevindt in het gebied **bedrijfs toepassingen** van de Azure Portal, om een toepassings gerichte weer gave van uw aanmeldings gegevens te verkrijgen, met inbegrip van informatie over:

- Meestgebruikte apps voor uw organisatie

- Apps met de meeste mislukte aanmeldingen

- Meest voorkomende aanmeld fouten voor elke app

Zie voor meer informatie over deze functie [het rapport gebruik en inzichten in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatiseer uw gebruikers inrichting voor Cloud-apps met behulp van Azure AD

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

Volg deze nieuwe zelf studies om de Azure AD-inrichtings service te gebruiken voor het automatiseren van het maken, verwijderen en bijwerken van gebruikers accounts voor de volgende Cloud-apps:

- [Voldoen aan](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

U kunt deze nieuwe Dropbox- [zelf studie](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)ook volgen, die informatie bevat over het inrichten van groeps objecten.

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Beveiligde score voor identiteit is nu beschikbaar in azure AD (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** n.v.t. **product mogelijkheid:** identiteits beveiliging & beveiliging

U kunt nu uw identiteits beveiligings postuur bewaken en verbeteren met behulp van de functie voor beveiligde scores voor identiteiten in azure AD. De functie voor het beveiligen van identiteiten maakt gebruik van één dash board waarmee u het volgende kunt doen:

- Objectief meet uw identiteits beveiligings postuur op basis van een score tussen 1 en 223.

- Uw verbeteringen voor identiteits beveiliging plannen

- Bekijk het succes van uw beveiligings verbeteringen

Zie [Wat is de identiteit beveiligde Score in azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)voor meer informatie over de functie voor identiteits beveiliging.

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nieuwe App-registraties-ervaring is nu beschikbaar (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** authenticaties (aanmeldingen) **product mogelijkheid:** ontwikkelaars ervaring

De nieuwe [app-registraties](https://aka.ms/appregistrations) -ervaring is nu in algemene Beschik baarheid. Deze nieuwe ervaring omvat alle belang rijke functies van de Azure Portal en de portal voor toepassings registratie en verbetert deze.

- **Betere app-beheer.** In plaats van uw apps te zien op verschillende portals, kunt u nu al uw apps op één locatie bekijken.

- **Vereenvoudigde registratie van de app.** Van de verbeterde navigatie-ervaring tot de uitgebreide mogelijkheden voor het selecteren van machtigingen is het nu eenvoudiger om uw apps te registreren en te beheren.

- **Meer gedetailleerde informatie.** U kunt meer informatie vinden over uw app, inclusief Quick start-gidsen en meer.

Zie voor meer informatie [micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) en de [app-registraties-ervaring is nu algemeen beschikbaar.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) aankondiging van blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nieuwe mogelijkheden die beschikbaar zijn in de Risk ante gebruikers-API voor identiteits beveiliging

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Het is blij dat u de Risk ante gebruikers API kunt gebruiken om de risico geschiedenis van gebruikers op te halen, Risk ante gebruikers te sluiten en gebruikers te bevestigen dat ze zijn aangetast. Met deze wijziging kunt u de risico status van uw gebruikers efficiënter bijwerken en inzicht krijgen in hun risico geschiedenis.

Zie de [naslag documentatie voor Risk ante gebruikers-API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-mei 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In mei 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [real links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [deplayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [marketo Sales](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [Outsystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum werkplek](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webmethodes API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [levens](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)werk, [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Verbeterde functies voor het maken en beheren van groepen in de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

We hebben verbeteringen aangebracht in de groeps-gerelateerde ervaringen in de Azure AD-Portal. Met deze verbeteringen kunnen beheerders de lijsten met groepen, leden lijsten en aanvullende opties voor het maken beter beheren.

De verbeteringen zijn onder andere:

- Basis filtering op lidmaatschaps type en groeps type.

- Toevoeging van nieuwe kolommen, zoals bron-en e-mail adres.

- De mogelijkheid om meerdere groepen, leden en eigen aars lijsten te selecteren voor eenvoudige verwijdering.

- Mogelijkheid om een e-mail adres te kiezen en eigen aren toe te voegen tijdens het maken van de groep.

Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) voor meer informatie.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (algemene Beschik baarheid)

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product capaciteit:** samen werking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)voor meer informatie.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Er zijn nu Microsoft Graph API-eind punten beschikbaar voor Azure AD-activiteiten Logboeken (algemene Beschik baarheid)

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

We zijn blij met het aankondigen van de algemene Beschik baarheid van Microsoft Graph API-eind punten ondersteuning voor Azure AD-activiteiten Logboeken. Met deze versie kunt u nu versie 1,0 van zowel de Azure AD-audit logboeken en de Api's voor aanmeld Logboeken gebruiken.

Zie overzicht van de [API voor Azure AD-controle logboeken](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)voor meer informatie.

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Beheerders kunnen nu voorwaardelijke toegang gebruiken voor het gecombineerde registratie proces (open bare preview)

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Beheerders kunnen nu beleid voor voorwaardelijke toegang maken voor gebruik door de gecombineerde registratie pagina. Dit geldt ook voor het Toep assen van beleid om registratie in te stellen als:

- Gebruikers bevinden zich in een vertrouwd netwerk.

- Gebruikers zijn een laag aanmeldings risico.

- Gebruikers bevinden zich op een beheerd apparaat.

- Gebruikers komen overeen met de gebruiks voorwaarden van de organisatie.

Zie voor meer informatie over voorwaardelijke toegang en het opnieuw instellen van wacht woorden de [voorwaardelijke toegang voor het blog bericht over gecombineerde MFA-en wachtwoord herstel voor Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Zie [beleid voor voorwaardelijke toegang voor gecombineerde registratie](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)voor meer informatie over beleids regels voor voorwaardelijke toegang voor het gecombineerde registratie proces. Zie [Azure Active Directory functie gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie over de functie gebruiks voorwaarden van Azure AD.

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nieuwe Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van Azure AD Identity Protection

**Type:** Nieuwe functie **service categorie:** Azure AD Identity Protection **product capaciteit:** identiteits beveiliging & beveiliging

Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van de bijgewerkte functie Azure AD Identity Protection. Deze nieuwe functionaliteit helpt bij het aanduiden van ongebruikelijke gebruikers activiteit voor een specifieke gebruiker of activiteit die consistent is met bekende aanvals patronen gebaseerd op de interne en externe informatie bronnen van micro soft.

Voor meer informatie over de vernieuwde versie van Azure AD Identity Protection raadpleegt u de [vier belangrijkste uitbrei dingen van de Azure AD Identity Protection nu in de open bare preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) -blog staan en [wat Azure Active Directory Identity Protection (vernieuwd)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) . Voor meer informatie over de detectie van Azure AD Threat Intelligence raadpleegt u het artikel [Azure Active Directory Identity Protection risico detectie](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Het beheer van rechten van Azure AD is nu beschikbaar (open bare preview)

**Type:** Nieuwe functie **service categorie:** Identity governance **product Capability:** Identity governance

Het beheer van rechten van Azure AD, nu in open bare preview, helpt klanten bij het delegeren van beheer van toegangs pakketten, waarmee wordt gedefinieerd hoe werk nemers en zakelijke partners toegang kunnen vragen, wie moet goed keuren en hoelang ze toegang hebben. Toegangs pakketten kunnen lidmaatschap beheren in azure AD en Office 365-groepen, roltoewijzingen in zakelijke toepassingen, en roltoewijzingen voor share point online-sites. Meer informatie over rechten beheer vindt u in het [overzicht van het beheer van rechten van Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Zie [Wat is Azure AD Identity governance?](../governance/identity-governance-overview.md)voor meer informatie over de breedte van Azure AD Identity governance functies, waaronder privileged Identity Management, toegangs beoordelingen en gebruiks voorwaarden.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)voor meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar in Azure Monitor (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Om uw feedback over visualisaties met de activiteiten logboeken van Azure AD te helpen aanpakken, introduceren we een nieuwe inzichten-functie in Log Analytics. Deze functie helpt u bij het verkrijgen van inzichten over uw Azure AD-resources door gebruik te maken van onze interactieve sjablonen, werkmappen genoemd. Deze vooraf gemaakte werkmappen kunnen Details bieden voor apps of gebruikers, en omvatten:

- **Aanmeldingen.** Biedt Details voor apps en gebruikers, waaronder aanmeldings locatie, het besturings systeem in gebruik of de browser-client en-versie, en het aantal geslaagde of mislukte aanmeldingen.

- **Verouderde verificatie en voorwaardelijke toegang.** Biedt Details voor apps en gebruikers met behulp van verouderde verificatie, met inbegrip van Multi-Factor Authentication gebruik dat wordt geactiveerd door het beleid voor voorwaardelijke toegang, apps die gebruikmaken van beleid voor voorwaardelijke toegang, enzovoort.

- **Analyse van mislukte aanmeldingen.** Helpt u te bepalen of uw aanmeldings fouten optreden als gevolg van een gebruikers actie, beleids problemen of uw infra structuur.

- **Aangepaste rapporten.** U kunt nieuwe werkmappen maken of bewerken om de inzichten-functie voor uw organisatie aan te passen.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-april 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In april 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Bank](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [afknot Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (op rollen gebaseerde SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [surveymonkey Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nieuwe toegangs beoordelingen frequentie optie en selectie van meerdere rollen

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** Identity governance

Met nieuwe updates in azure AD-toegangs beoordelingen kunt u het volgende doen:

- Wijzig de frequentie van uw toegangs beoordelingen in een **halfjaarlijkse**, naast de eerder bestaande opties wekelijks, maandelijks, elk kwar taal en jaarlijks.

- Selecteer meerdere Azure AD-en Azure-resource rollen bij het maken van één toegangs beoordeling. In dit geval worden alle rollen met dezelfde instellingen ingesteld en worden alle revisoren tegelijk op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)voor meer informatie over het maken van een toegangs beoordeling.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail waarschuwings systeem (en) worden overgezet, zodat er nieuwe e-mail gegevens worden verzonden voor sommige klanten

**Type:** Gewijzigde functie **service categorie:** AD Sync **product capaciteit:** platform

Azure AD Connect is bezig met het overstappen van onze e-mail waarschuwings systeem (s), waardoor sommige klanten een nieuwe e-mail afzender kunnen weer geven. Om dit op te lossen, moet u toevoegen `azure-noreply@microsoft.com` aan de acceptatie lijst van uw organisatie of u kunt geen belang rijke waarschuwingen ontvangen van uw Office 365, Azure of uw synchronisatie Services.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Wijzigingen in UPN-achtervoegsels zijn nu geslaagd tussen federatieve domeinen in Azure AD Connect

**Type:** **Categorie vaste service:** AD Sync **product capaciteit:** platform

U kunt nu het UPN-achtervoegsel van een gebruiker van een federatief domein wijzigen in een ander federatief domein in Azure AD Connect. Met deze oplossing wordt het FederatedDomainChangeError-fout bericht tijdens de synchronisatie cyclus niet meer ervaren of wordt er een e-mail bericht met de melding ' kan dit object niet bijwerken in Azure Active Directory, omdat het kenmerk [FederatedUser. UserPrincipalName] ongeldig is. Werk de waarde in uw lokale adreslijst Services bij.

Zie [probleemoplossings fouten tijdens de synchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)voor meer informatie.

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Verbeterde beveiliging met behulp van het beleid voor voorwaardelijke toegang op basis van app-beveiliging in azure AD (open bare preview)

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Voorwaardelijke toegang op basis van app-beveiliging is nu beschikbaar via het beveiligings beleid voor het **vereisen van apps** . Dit nieuwe beleid helpt de beveiliging van uw organisatie te verbeteren door te voor komen dat:

- Gebruikers die toegang krijgen tot apps zonder een Microsoft Intune-licentie.

- Gebruikers kunnen geen Microsoft Intune beleid voor app-beveiliging ophalen.

- Gebruikers die toegang krijgen tot apps zonder een geconfigureerd Microsoft Intune beveiligings beleid voor apps.

Zie [app-beveiligings beleid vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)voor meer informatie.

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nieuwe ondersteuning voor eenmalige aanmelding voor Azure AD en voorwaardelijke toegang in micro soft Edge (open bare preview)

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

We hebben onze Azure AD-ondersteuning voor micro soft Edge uitgebreid, met inbegrip van nieuwe ondersteuning voor eenmalige aanmelding voor Azure AD en voorwaardelijke toegang. Als u Microsoft Intune Managed Browser eerder hebt gebruikt, kunt u nu gebruikmaken van micro soft Edge.

Zie voor meer informatie over het instellen en beheren van uw apparaten en apps met behulp van voorwaardelijke toegang [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) en [goedgekeurde client-apps vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Voor meer informatie over het beheren van toegang met micro soft Edge met Microsoft Intune-beleid, Zie [Internet toegang beheren met een met Microsoft intune-beleid beveiligde browser](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Maart 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Het Framework voor identiteits ervaring en aangepaste beleids ondersteuning in Azure Active Directory B2C is nu beschikbaar (GA)

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

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

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2019 maart hebben we deze 14 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [Medius flow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [controle systeem op basis van uitleg](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool prestatie zaken](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizonten](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [taa](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nieuwe Zscaler-en Atlassian-inrichtings connectors in de Azure AD Gallery-maart 2019

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

Automatisch maken, bijwerken en verwijderen van gebruikers accounts voor de volgende apps:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler twee](https://aka.ms/ZscalerTwoProvisioning), [Zscaler drie](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Uw verwijderde Office 365-groepen herstellen en beheren in de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

U kunt nu uw verwijderde Office 365-groepen bekijken en beheren via de Azure AD-Portal. Met deze wijziging kunt u zien welke groepen beschikbaar zijn om te herstellen, en kunt u alle groepen die niet nodig zijn voor uw organisatie permanent verwijderen.

Zie [verlopen of verwijderde groepen herstellen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)voor meer informatie.

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Eenmalige aanmelding is nu beschikbaar voor Azure AD SAML-beveiligde on-premises apps via toepassings proxy (open bare preview)

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

U kunt nu een SSO-ervaring (eenmalige aanmelding) bieden voor on-premises, met SAML geverifieerde apps en externe toegang tot deze apps via toepassings proxy. Zie voor meer informatie over het instellen van SAML SSO met uw on-premises apps [SAML eenmalige aanmelding voor on-premises toepassingen met toepassings proxy (preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Client-apps in de aanvraag lussen worden onderbroken om de betrouw baarheid en gebruikers ervaring te verbeteren

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Client-apps kunnen honderden dezelfde aanmeldings aanvragen binnen korte tijd onjuist uitgeven. Deze aanvragen, ongeacht of ze wel of niet zijn gelukt, nemen alle bijdragen aan een slechte gebruikers ervaring en verhoogde workloads voor de IDP, verg root de latentie voor alle gebruikers en verminderen de beschik baarheid van de IDP.

Deze update verzendt een `invalid_grant` fout melding: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` voor client-apps die dubbele aanvragen meerdere keren worden uitgevoerd gedurende een korte periode, buiten het bereik van de normale werking. Client-apps die dit probleem ondervinden, moeten een interactieve prompt weer geven, waarbij de gebruiker zich opnieuw moet aanmelden. Zie [Wat is er nieuw voor verificatie?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)voor meer informatie over deze wijziging en hoe u uw app kunt herstellen als deze fout optreedt.

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nieuwe audit logboeken gebruikers ervaring nu beschikbaar

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

We hebben een nieuwe Azure AD- **controle logboek** pagina gemaakt waarmee u zowel de Lees baarheid als de manier waarop u naar uw gegevens zoekt, kunt verbeteren. Als u de nieuwe pagina **controle logboeken** wilt weer geven, selecteert u **controle logboeken** in het gedeelte **activiteit** van Azure AD.

![Nieuwe pagina controle logboeken, met voorbeeld gegevens](media/whats-new/audit-logs-page.png)

Zie voor meer informatie over de nieuwe pagina **controle logboeken** [activiteiten controleren in de Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nieuwe waarschuwingen en richt lijnen om te voor komen dat onbedoelde beheerders niet-geconfigureerde beleids regels voor voorwaardelijke toegang kunnen worden vergrendeld

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Om te voor komen dat beheerders zichzelf via onjuist geconfigureerde beleids regels voor voorwaardelijke toegang per ongeluk kunnen vergren delen, hebben we nieuwe waarschuwingen en bijgewerkte richt lijnen gemaakt in de Azure Portal. Zie [Wat zijn service afhankelijkheden in azure Active Directory voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)voor meer informatie over de nieuwe richt lijnen.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Betere gebruiks voorwaarden voor eind gebruikers op mobiele apparaten

**Type:** Gewijzigde functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

We hebben onze bestaande gebruiks voorwaarden bijgewerkt om te helpen bij het verbeteren van de manier waarop u de gebruiks voorwaarden op een mobiel apparaat bekijkt en ermee akkoord gaat. U kunt nu in-en uitzoomen, terugkeren, de informatie downloaden en hyper links selecteren. Zie [Azure Active Directory functie gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)voor meer informatie over de bijgewerkte gebruiks voorwaarden.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nieuwe ervaring voor het downloaden van Azure AD-activiteiten logboeken beschikbaar

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

U kunt nu grote hoeveel heden activiteiten logboeken rechtstreeks downloaden vanuit het Azure Portal. Met deze update kunt u het volgende doen:

- Down load Maxi maal 250.000 rijen.

- Ontvang een melding wanneer de down load is voltooid.

- Pas de bestands naam aan.

- Bepaal de indeling van de uitvoer, ofwel JSON of CSV.

Voor meer informatie over deze functie raadpleegt [u Quick Start: een controle rapport downloaden met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Belang rijke wijziging: updates voor evaluatie van voor waarden door Exchange ActiveSync (EAS)

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: Access Control

We zijn bezig met het bijwerken van de manier waarop Exchange ActiveSync (EAS) de volgende voor waarden evalueert:

- Gebruikers locatie, op basis van land, regio of IP-adres

- Aanmeldingsrisico

- Apparaatplatform

Als u deze voor waarden in uw beleid voor voorwaardelijke toegang eerder hebt gebruikt, is het mogelijk dat het probleem van de voor waarde wordt gewijzigd. Als u bijvoorbeeld eerder de voor waarde voor de gebruikers locatie in een beleid hebt gebruikt, kan het beleid worden overgeslagen op basis van de locatie van uw gebruiker.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configureerbaar Azure AD SAML-token versleuteling (open bare preview)

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

U kunt nu elke ondersteunde SAML-app configureren voor het ontvangen van versleutelde SAML-tokens. Wanneer Azure AD is geconfigureerd en gebruikt met een app, worden de verzonden SAML-bevestigingen versleuteld met behulp van een open bare sleutel die is verkregen van een certificaat dat is opgeslagen in azure AD.

Zie [Azure AD SAML-token versleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie over het configureren van uw SAML-token versleuteling.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Een toegangs beoordeling maken voor groepen of apps met behulp van Azure AD-toegangs beoordelingen

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** governance

U kunt nu meerdere groepen of apps opnemen in één Azure AD-toegangs beoordeling voor groepslid maatschap of app-toewijzing. Toegangs beoordelingen met meerdere groepen of apps worden ingesteld met dezelfde instellingen en alle opgenomen revisoren worden tegelijkertijd op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) voor meer informatie over hoe u een toegangs beoordeling maakt met behulp van Azure AD-toegangs beoordelingen

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-februari 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In februari 2019 zijn deze 27 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Euromonitor Pass Port](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), Port [stack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), permission click, Brandfolder [,](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial)StoregateSmartFile [, Pexip,](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial) [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisch](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [delen van een droom](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webmethodes integratie Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [kennis Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OE campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Peri Scope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud door Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), ClickUp [productiviteits platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial) [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Uitgebreide registratie van gecombineerde MFA/SSPR

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

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

**Type:** Gewijzigde functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

We hebben het proces voor het maken en beheren van beleid voor gebruikers stromen bijgewerkt (voorheen bekend als ingebouwde beleids regels). Deze nieuwe ervaring is nu de standaard instelling voor al uw Azure AD-tenants.

U kunt aanvullende feedback en suggesties bieden met behulp van de pictogrammen glim lach of frons in het gebied **Feedback verzenden** boven aan het portal scherm.

Voor meer informatie over de nieuwe beleids beheer-ervaring raadpleegt u de [Azure AD B2C nu java script customization en veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Specifieke versie van pagina-elementen kiezen die wordt opgegeven door Azure AD B2C

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu een specifieke versie van de pagina-elementen kiezen die door Azure AD B2C worden verschaft. Door een specifieke versie te selecteren, kunt u de updates testen voordat ze op een pagina worden weer gegeven en kunt u voorspel bare gedrag ophalen. Daarnaast kunt u ervoor kiezen om specifieke pagina versies af te dwingen om Java script-aanpassingen toe te staan. Als u deze functie wilt inschakelen, gaat u naar de pagina **Eigenschappen** in uw gebruikers stromen.

Voor meer informatie over het kiezen van specifieke versies van pagina-elementen raadpleegt u de [Azure AD B2C nu java script customization en veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Configureer bare vereisten voor het wacht woord voor de eind gebruiker voor B2C (GA)

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu de wachtwoord complexiteit van uw organisatie instellen voor uw eind gebruikers, in plaats van uw systeem eigen Azure AD-wachtwoord beleid te gebruiken. Op de Blade **Eigenschappen** van uw gebruikers stromen (voorheen bekend als uw ingebouwde beleids regels) kunt u een wachtwoord complexiteit van **eenvoudig** of **sterk**kiezen, of u kunt een **aangepaste** set vereisten maken.

Zie [complexiteits vereisten configureren voor wacht woorden in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)voor meer informatie over de configuratie van de vereisten voor wachtwoord complexiteit.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nieuwe standaard sjablonen voor aangepaste merk authenticatie-ervaringen

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt onze nieuwe standaard sjablonen op de Blade **pagina-indelingen** van uw gebruikers stromen (voorheen bekend als ingebouwde beleids regels) gebruiken om een aangepaste merk bare verificatie-ervaring voor uw gebruikers te maken.

Voor meer informatie over het gebruik van de sjablonen raadpleegt u [Azure AD B2C nu java script-aanpassing en nog veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samen werking met authenticatie met eenmalige verificatie (open bare preview)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

We hebben eenmalige wachtwoord verificatie (OTP) geïntroduceerd voor B2B-gast gebruikers die niet kunnen worden geverifieerd via andere manieren, zoals Azure AD, een Microsoft-account (MSA) of Google Federatie. Deze nieuwe verificatie methode betekent dat gast gebruikers geen nieuwe Microsoft-account hoeven te maken. Een gast gebruiker kan in plaats daarvan een uitnodiging inwisselen of toegang tot een gedeelde bron aanvragen om een tijdelijke code aan een e-mail adres te verzenden. Met deze tijdelijke code kan de gast gebruiker zich blijven aanmelden.

Zie voor meer informatie [e-mail One-time wachtwoord code verificatie (preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) en de blog. [Azure AD maakt delen en samen werking naadloos mogelijk voor elke gebruiker met een account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nieuwe instellingen voor Azure AD-toepassingsproxy cookie

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

We hebben drie nieuwe cookie-instellingen geïntroduceerd, die beschikbaar zijn voor uw apps die zijn gepubliceerd via toepassings proxy:

- **Cookie met alleen HTTP gebruiken.** Hiermee stelt u de **HTTPOnly** -vlag in voor de toegangs-en sessie cookies van de toepassings proxy. Het inschakelen van deze instelling biedt extra veiligheids voordelen, zoals het voor komen van het kopiëren of wijzigen van cookies via scripting aan de client zijde. U wordt aangeraden deze vlag in te scha kelen ( **Ja**) om de extra voor delen te bepalen.

- **Gebruik beveiligde cookie.** Hiermee stelt u de **beveiligde** vlag in voor de toegangs-en sessie cookies van de toepassings proxy. Het inschakelen van deze instelling biedt extra veiligheids voordelen door ervoor te zorgen dat cookies alleen worden verzonden via TLS-beveiligde kanalen, zoals HTTPS. U wordt aangeraden deze vlag in te scha kelen ( **Ja**) om de extra voor delen te bepalen.

- **Permanente cookie gebruiken.** Hiermee voor komt u dat de toegang tot cookies verloopt wanneer de webbrowser is gesloten. Deze cookies duren voor de levens duur van het toegangs token. De cookies worden echter opnieuw ingesteld als de verloop tijd wordt bereikt of als de gebruiker de cookie hand matig verwijdert. We raden u aan om de standaard instelling **Nee**te laten, alleen de instelling in te scha kelen voor oudere apps die geen cookies delen tussen processen.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)voor meer informatie over de nieuwe cookies.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-januari 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In januari 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talen palet](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial) [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco paraplu](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [verloop herinnering](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), InstaVR- [Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [werk woord](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), belopend, [CallPlease](https://webapp.callplease.com/create-account/create-account.html), GTNEXUS- [SSO-systeem](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial) [, Signagelive,](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial) [aren voor Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 voor Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [bezoek](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn veer Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial) [Adoddle cSaas](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nieuwe Azure AD Identity Protection verbeteringen (open bare preview)

**Type:** Gewijzigde functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

We zijn enthousiast dat we de volgende uitbrei dingen hebben toegevoegd aan de aanbieding van Azure AD Identity Protection open bare preview, waaronder:

- Een bijgewerkte en meer geïntegreerde gebruikers interface

- Aanvullende Api's

- Verbeterde risico analyse via machine learning

- Uitlijning op het hele product voor Risk ante gebruikers en Risk ante aanmeldingen

Zie [Wat is Azure Active Directory Identity Protection (vernieuwd)?](https://aka.ms/IdentityProtectionDocs) voor meer informatie over de uitbrei dingen. voor meer informatie en om je mening te delen via de prompts in het product.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nieuwe app-vergrendelings functie voor de app Microsoft Authenticator op iOS-en Android-apparaten

**Type:** Nieuwe functie **service categorie:** Microsoft Authenticator app- **product mogelijkheid:** identiteits beveiliging & beveiliging

Als u uw eenmalige wachtwoord code, app-informatie en app-instellingen veiliger wilt laten, kunt u de functie voor het vergren delen van apps inschakelen in de app Microsoft Authenticator. Wanneer u app Lock inschakelt, wordt u gevraagd om te verifiëren met uw pincode of biometrisch telkens wanneer u de Microsoft Authenticator-app opent.

Zie de [Veelgestelde vragen over de Microsoft Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)voor meer informatie.

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Export mogelijkheden voor verbeterde Azure AD Privileged Identity Management (PIM)

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Privileged Identity Management (PIM) beheerders kunnen nu alle actieve en in aanmerking komende roltoewijzingen voor een specifieke resource exporteren, die roltoewijzingen voor alle onderliggende resources bevat. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en ze moest roltoewijzingen voor elke specifieke resource exporteren.

Zie [activiteit en controle geschiedenis voor Azure-resource rollen weer geven in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)voor meer informatie.

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gebruikers die zijn verwijderd uit het synchronisatie bereik, worden niet meer overgeschakeld naar alleen-Cloud accounts

**Type:** Vaste **service categorie:** gebruikers beheer **product mogelijkheid:** map

>[!Important]
>We hebben uw frustratie gehoord en begrepen door deze oplossing. Daarom hebben we deze wijziging tot nu toe gewijzigd, zodat u de oplossing gemakkelijker kunt implementeren in uw organisatie.

Er is een fout opgelost waarbij de vlag DirSyncEnabled van een gebruiker onjuist wordt overgeschakeld naar **False** wanneer het object Active Directory Domain Services (AD DS) is uitgesloten van het synchronisatie bereik en vervolgens is verplaatst naar de Prullenbak in azure ad tijdens de volgende synchronisatie cyclus. Als gevolg van deze oplossing geldt dat als de gebruiker is uitgesloten van het synchronisatie bereik en daarna wordt hersteld vanuit de Prullenbak van Azure AD, het gebruikers account blijft gesynchroniseerd van on-premises AD, zoals verwacht, en niet kan worden beheerd in de Cloud omdat de bron van de autoriteit (SoA) blijft als on-premises AD.

Voorafgaand aan deze oplossing is er een probleem opgetreden toen de vlag DirSyncEnabled is overgeschakeld naar onwaar. Er is een onjuiste indruk gegeven dat deze accounts zijn geconverteerd naar objecten in de Cloud en dat de accounts in de Cloud kunnen worden beheerd. De accounts behouden echter nog steeds hun SoA als on-premises en alle gesynchroniseerde eigenschappen (schaduw kenmerken) die afkomstig zijn van on-premises AD. Deze voor waarde heeft meerdere problemen ondervonden in azure AD en andere Cloud werkbelastingen (zoals Exchange Online) die verwacht worden deze accounts te behandelen als gesynchroniseerd vanuit AD, maar nu fungeren als alleen Cloud accounts.

Op dit moment is de enige manier om een gesynchroniseerd-van-AD-account echt te converteren naar een alleen-Cloud account door DirSync op Tenant niveau uit te scha kelen, waarmee een back-end-bewerking wordt geactiveerd om de SoA over te dragen. Dit type SoA-wijziging vereist (maar is niet beperkt tot) het schoonmaken van alle on-premises verwante kenmerken (zoals LastDirSyncTime en schaduw kenmerken) en het verzenden van een signaal naar andere Cloud werkbelastingen zodat het desbetreffende object ook naar een alleen-Cloud account wordt geconverteerd.

Deze oplossing verhindert daarom directe updates van het kenmerk ImmutableID van een gebruiker die is gesynchroniseerd vanuit AD, wat in sommige scenario's in het verleden was vereist. Het ImmutableID van een object in azure AD, zoals de naam al aangeeft, is bedoeld om onveranderbaar te zijn. Nieuwe functies die in Azure AD Connect Health zijn geïmplementeerd en Azure AD Connect-synchronisatie-client, zijn beschikbaar om deze scenario's te verhelpen:

- **Grootschalige ImmutableID-update voor veel gebruikers in een gefaseerde benadering**

  U moet bijvoorbeeld een langdurige AD DS tussen forest-migratie uitvoeren. Oplossing: gebruik Azure AD Connect om het **bron anker te configureren** en kopieer, wanneer de gebruiker migreert, de bestaande ImmutableID-waarden van Azure AD naar het kenmerk MS-DS-Consistency-GUID van de lokale AD DS gebruiker van het nieuwe forest. Zie [using MS-DS-ConsistencyGuid als source Anchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)voor meer informatie.

- **Grootschalige ImmutableID-updates voor veel gebruikers per foto**

  Tijdens de implementatie Azure AD Connect u bijvoorbeeld een fout gemaakt en nu moet u het kenmerk source Anchor wijzigen. Oplossing: Schakel DirSync uit op Tenant niveau en wis alle ongeldige ImmutableID-waarden. Zie Directory-synchronisatie uitschakelen [voor Office 365](/office365/enterprise/turn-off-directory-synchronization)voor meer informatie.

- **Een on-premises gebruiker opnieuw koppelen aan een bestaande gebruiker in azure AD** Een gebruiker die opnieuw is gemaakt in AD DS genereert bijvoorbeeld een duplicaat in het Azure AD-account in plaats van het opnieuw te koppelen aan een bestaand Azure AD-account (zwevend object). Oplossing: gebruik Azure AD Connect Health in de Azure Portal om de bron-anker-ImmutableID opnieuw toe te wijzen. Zie voor meer informatie [zwevend object scenario](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Belang rijke wijziging: updates voor het schema voor controle en aanmeldings logboeken via Azure Monitor

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Momenteel publiceren we de audit-en aanmeldings logboek stromen via Azure Monitor, zodat u de logboek bestanden naadloos kunt integreren met uw SIEM-hulpprogram ma's of met Log Analytics. Op basis van uw feedback en in de voor bereiding van de algemene Beschik baarheid van deze functie, worden de volgende wijzigingen aangebracht in het schema. Deze schema wijzigingen en de bijbehorende documentatie-updates worden uitgevoerd door de eerste week van januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nieuwe velden in het audit schema
Er wordt een nieuw **bewerkings type** veld toegevoegd om het type bewerking te bieden dat wordt uitgevoerd op de resource. Bijvoorbeeld: **toevoegen**, **bijwerken**of **verwijderen**.

#### <a name="changed-fields-in-the-audit-schema"></a>Gewijzigde velden in het audit schema
De volgende velden worden gewijzigd in het controle schema:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|Categorie|Dit is het veld **service naam** . Nu is het veld **controle categorieën** . De naam van de **service naam** is gewijzigd in het veld **loggedByService** .|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Self-service voor wacht woord opnieuw instellen</li></ul>|<ul><li>Gebruikersbeheer</li><li>Groepsbeheer</li><li>App-beheer</li></ul>|
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

**Type:** Gewijzigde functie **service categorie:** identiteits beschermings **product:** risico scores

Verbeteringen aan de gebruikers-en aanmeldings risico beoordelings engine voor identiteits beveiliging kunnen helpen de nauw keurigheid en dekking van de gebruikers Risico's te verbeteren. Beheerders kunnen merken dat het risico niveau van de gebruiker niet langer rechtstreeks is gekoppeld aan het risico niveau van specifieke detecties en dat er een toename is in het aantal en het niveau van Risk ante aanmeldings gebeurtenissen.

Risico detecties worden nu geëvalueerd door het machine learning model met toezicht, waarmee gebruikers Risico's worden berekend met behulp van aanvullende functies van de aanmeldingen van de gebruiker en een patroon van detecties. Op basis van dit model kan de beheerder gebruikers met hoge risico scores vinden, zelfs als detecties die aan die gebruiker zijn gekoppeld, een laag of gemiddeld risico hebben.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Beheerders kunnen hun eigen wacht woord opnieuw instellen met behulp van de Microsoft Authenticator-app (open bare preview)

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

Azure AD-beheerders kunnen nu hun eigen wacht woord opnieuw instellen met behulp van de Microsoft Authenticator app-meldingen of een code van een mobiele verificator-app of-hardware-token. Beheerders kunnen nu twee van de volgende methoden gebruiken om hun eigen wacht woord opnieuw in te stellen:

- App-melding Microsoft Authenticator

- Andere Mobile Authenticator-app/hardware-token code

- E-mail

- Telefoongesprek

- Sms-bericht

Voor meer informatie over het gebruik van de Microsoft Authenticator-app om wacht woorden opnieuw in te stellen, Zie [selfservice voor wachtwoord herstel van Azure AD-mobiele app en SSPR (preview-versie)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nieuwe rol van Azure AD-Cloud apparaat-beheerder (open bare preview)

**Type:** Nieuwe functie **service categorie:** de mogelijkheid om het apparaat te registreren en te beheren **:** toegangs beheer

Beheerders kunnen gebruikers toewijzen aan de nieuwe rol van de beheerder van het Cloud apparaat voor het uitvoeren van beheer taken voor de Cloud. Gebruikers met de rol Administrators van Cloud apparaten kunnen apparaten in azure AD inschakelen, uitschakelen en verwijderen, en kunnen Windows 10 BitLocker-sleutels (indien aanwezig) in de Azure Portal lezen.

Zie [beheerders rollen toewijzen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor meer informatie over rollen en machtigingen.

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Uw apparaten beheren met de nieuwe tijds tempel van de activiteit in azure AD (open bare preview)

**Type:** Nieuwe functie **service categorie:** apparaat registratie en beheer **product mogelijkheid:** levenscyclus beheer van apparaten

Na verloop van tijd moet u de apparaten van uw organisatie in azure AD vernieuwen en buiten gebruik stellen om te voor komen dat er verouderde apparaten in uw omgeving worden uitgevoerd. Om u te helpen bij dit proces, worden uw apparaten nu door Azure AD bijgewerkt met een nieuwe tijds tempel van de activiteit, zodat u de levens cyclus van uw apparaat kunt beheren.

Zie [How to: de verouderde apparaten beheren in azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices) voor meer informatie over het ophalen en gebruiken van deze tijds tempel.

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Beheerders kunnen vereisen dat gebruikers de gebruiks voorwaarden voor elk apparaat accepteren

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Beheerders kunnen nu de optie **gebruikers moeten toestemming geven op elk apparaat** , zodat uw gebruikers de gebruiks voorwaarden kunnen accepteren op elk apparaat dat ze gebruiken in uw Tenant.

Zie de [sectie met gebruiks voorwaarden per apparaat van de functie Azure Active Directory gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)voor meer informatie.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Beheerders kunnen een gebruiks voorwaarden configureren om te verlopen op basis van een terugkerend schema

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance


Beheerders kunnen de optie voor het **verstrijken van verlopen** nu inschakelen om een gebruiks voorwaarden te laten verlopen voor al uw gebruikers op basis van uw opgegeven terugkerende planning. De planning kan jaarlijks, tweejaarlijkse, elk kwar taal of maandelijks zijn. Nadat de gebruiks voorwaarden verlopen zijn, moeten gebruikers deze opnieuw accepteren.

Zie de [sectie gebruiks voorwaarden toevoegen van de functie Azure Active Directory gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)voor meer informatie.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Beheerders kunnen een gebruiks voorwaarden configureren om te verlopen op basis van de planning van elke gebruiker

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Beheerders kunnen nu een duur opgeven die de gebruiker nodig heeft om een gebruiks voorwaarden opnieuw te accepteren. Beheerders kunnen bijvoorbeeld opgeven dat gebruikers elke 90 dagen een gebruiks voorwaarden opnieuw moeten accepteren.

Zie de [sectie gebruiks voorwaarden toevoegen van de functie Azure Active Directory gebruiksrecht overeenkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)voor meer informatie.

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nieuwe Azure AD Privileged Identity Management (PIM) e-mails voor Azure Active Directory rollen

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Klanten die Azure AD Privileged Identity Management (PIM) gebruiken, kunnen nu een wekelijks overzicht van e-mail ontvangen, met inbegrip van de volgende informatie over de afgelopen zeven dagen:

- Overzicht van de meest geschikte en permanente roltoewijzingen

- Aantal gebruikers die rollen activeren

- Aantal gebruikers dat is toegewezen aan rollen in PIM

- Aantal gebruikers dat is toegewezen aan rollen buiten PIM

- Het aantal gebruikers dat permanent is gemaakt in PIM

Zie [e-mail meldingen in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)voor meer informatie over PIM en de beschik bare e-mail meldingen.

---

### <a name="group-based-licensing-is-now-generally-available"></a>Op groep gebaseerde licentie verlening is nu algemeen beschikbaar

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

De open bare preview-versie van een groeps licentie is niet beschikbaar en is nu algemeen verkrijgbaar. Als onderdeel van deze algemene release hebben we deze functie uitgebreidere schaalbaar en hebben ze de mogelijkheid voor het opnieuw verwerken van op groepen gebaseerde licentie toewijzingen voor één gebruiker en de mogelijkheid om op groep gebaseerde licentie verlening te gebruiken met Office 365 E3/a3-licenties.

Zie [Wat is op groep gebaseerde licentie verlening in azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) voor meer informatie over groeps licenties?

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-november 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In november 2018 hebben deze 26 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy voor Business Central 365](https://accounting.zenegy.com/), [Everbridge member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex apps-klassieke test](https://test.plexonline.com/signon), [Plex-apps-klassiek](https://www.plexonline.com/signon), Plex [-apps-UX testen](https://test.cloud.plex.com/sso), [Plex apps – UX](https://cloud.plex.com/sso), [Plex apps – iam](https://accounts.plex.com/), [Craft-Childcare records, aanwezigheid, & systeem voor financiële registratie](https://getcrafts.ca/craftsregistration)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-logboeken werken nu met Azure Log Analytics (open bare preview)

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Het is enthousiast om te melden dat u uw Azure AD-logboeken nu kunt door sturen naar Azure Log Analytics! Deze belangrijkste functie biedt u nog betere toegang tot analyses voor uw bedrijf, bedrijfs activiteiten en beveiliging, en een manier om uw infra structuur te bewaken. Zie voor meer informatie de [Azure Active Directory activiteiten Logboeken in Azure log Analytics nu beschikbaar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-oktober 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In oktober 2018 hebben we deze 14 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Mijn toekennings punten](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler drie](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [beoordeeld](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mail meldingen Azure AD Domain Services

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Azure AD Domain Services biedt waarschuwingen over de Azure Portal over onjuiste configuratie of problemen met uw beheerde domein. Deze waarschuwingen bevatten stapsgewijze hand leidingen, zodat u kunt proberen de problemen op te lossen zonder dat u contact hoeft op te nemen met de ondersteuning.

Vanaf oktober kunt u de instellingen voor meldingen voor uw beheerde domein aanpassen zodat er nieuwe waarschuwingen optreden, wordt een e-mail bericht verzonden naar een aangewezen groep personen, waardoor de portal voortdurend niet meer hoeft te worden gecontroleerd op updates.

Zie [meldings instellingen in azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)voor meer informatie.

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD Portal biedt ondersteuning voor het gebruik van de ForceDelete-domein-API voor het verwijderen van aangepaste domeinen

**Type:** Gewijzigde functie **service categorie:** Directory Management **product Capability:** Directory

Het is blij dat u de ForceDelete-domein-API kunt gebruiken om uw aangepaste domein namen te verwijderen door de naam van verwijzingen, zoals gebruikers, groepen en apps van uw aangepaste domein naam (contoso.com), weer te wijzigen in de oorspronkelijke standaard domeinnaam (contoso.onmicrosoft.com).

Met deze wijziging kunt u uw aangepaste domein namen sneller verwijderen als uw organisatie de naam niet meer gebruikt of als u de domein naam moet gebruiken met een andere Azure AD.

Zie [een aangepaste domein naam verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)voor meer informatie.

---

## <a name="september-2018"></a>September 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>De machtigingen voor de beheerdersrol voor dynamische groepen zijn bijgewerkt

**Type:** Vaste **service categorie:** functionaliteit voor groeps beheer **product:** samen werking

Er is een probleem opgelost waardoor specifieke beheerders rollen nu dynamische lidmaatschaps regels kunnen maken en bijwerken, zonder dat de eigenaar van de groep hoeft te zijn.

De rollen zijn:

- Globale beheerder

- Intune-beheerder

- Gebruikersbeheerder

Zie [een dynamische groep maken en de status controleren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) voor meer informatie

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Vereenvoudigde configuratie-instellingen voor eenmalige aanmelding (SSO) voor sommige apps van derden

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

We beseffen dat het instellen van eenmalige aanmelding (SSO) voor software as a Service (SaaS)-apps lastig is vanwege de unieke aard van elke configuratie van de apps. We hebben een vereenvoudigde configuratie-ervaring ontwikkeld voor het automatisch invullen van de SSO-configuratie-instellingen voor de volgende SaaS-apps van derden:

- Zendesk

- ArcGis online

- Jamf Pro

Ga naar de **Azure portal**  >  **configuratie** pagina van Azure Portal SSO voor de app om te beginnen met het gebruik van deze sessie met één klik. Zie voor meer informatie [SaaS-toepassings integratie met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory-waar bevinden zich uw gegevens? Faxvoorblad

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** GoLocal

Selecteer de regio van uw bedrijf in de **Azure Active Directory-waar** bevindt zich uw gegevens pagina om te zien welk Azure-Data Center uw Azure AD-gegevens in rust heeft voor alle Azure AD-Services. U kunt de informatie filteren op specifieke Azure AD-Services voor de regio van uw bedrijf.

Als u toegang wilt krijgen tot deze functie en voor meer informatie, Zie [Azure Active Directory-waar bevindt zich uw gegevens](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nieuw implementatie plan beschikbaar voor het toegangs venster mijn apps

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

Bekijk het nieuwe implementatie plan dat beschikbaar is voor het toegangs venster mijn apps ( https://aka.ms/deploymentplans) .
Het toegangs venster voor mijn apps biedt gebruikers één plek om hun apps te vinden en toegang te krijgen. Deze portal biedt gebruikers ook selfservice mogelijkheden, zoals het aanvragen van toegang tot apps en groepen, of het beheren van toegang tot deze bronnen namens anderen.

Zie [Wat is de portal mijn apps?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) voor meer informatie.

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nieuwe tabblad probleem oplossing en ondersteuning op de pagina aanmeld logboeken van de Azure Portal

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Het nieuwe tabblad **probleem oplossing en ondersteuning** op de pagina **aanmeldingen** van het Azure Portal, is bedoeld om beheerders te helpen bij het oplossen van problemen met betrekking tot Azure AD-aanmeldingen. Dit nieuwe tabblad bevat de fout code, het fout bericht en aanbevelingen voor herstel (indien van toepassing) om het probleem op te lossen. Als u het probleem niet kunt oplossen, geeft u ook een nieuwe manier om een ondersteunings ticket te maken met behulp van de functie **kopiëren naar klem bord** , waarmee de velden **aanvraag-id** en **datum (UTC)** voor het logboek bestand in uw ondersteunings ticket worden ingevuld.

![Aanmeld logboeken met het nieuwe tabblad](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Verbeterde ondersteuning voor aangepaste extensie-eigenschappen die worden gebruikt voor het maken van dynamische lidmaatschaps regels

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product capaciteit:** samen werking

Met deze update kunt u nu klikken op de koppeling **aangepaste extensie eigenschappen ophalen** van de opbouw functie voor de groep voor dynamische gebruikers, uw unieke App-ID invoeren en de volledige lijst met aangepaste extensie-eigenschappen ontvangen die moeten worden gebruikt bij het maken van een dynamische lidmaatschaps regel voor gebruikers. Deze lijst kan ook worden vernieuwd om nieuwe aangepaste extensie-eigenschappen voor die app te krijgen.

Voor meer informatie over het gebruik van aangepaste uitbreidings eigenschappen voor dynamische lidmaatschaps regels raadpleegt u de eigenschappen van de [extensie en aangepaste extensie](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

De volgende apps zijn te vinden op de lijst met [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Zie voor meer informatie:

- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nieuwe ondersteuning voor selfservice voor wachtwoord herstel via het vergrendelings scherm van Windows 7/8/8.1

**Type:** Nieuwe functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Nadat u deze nieuwe functie hebt ingesteld, krijgen gebruikers een koppeling te zien om hun wacht woord opnieuw in te stellen vanaf het **vergrendelings** scherm van een apparaat met Windows 7, Windows 8 of Windows 8,1. Als u op deze koppeling klikt, wordt de gebruiker via de webbrowser begeleid via dezelfde wachtwoord herstel stroom.

Zie [How to enable password reset from Windows 7, 8 en 8,1 (](https://aka.ms/ssprforwindows78) Engelstalig) voor meer informatie.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Wijzigings bericht: autorisatie codes kunnen niet langer opnieuw worden gebruikt

**Type:** Planning voor **service categorie wijzigen:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Vanaf 15 november 2018 zal Azure AD stoppen met het accepteren van eerder gebruikte verificatie codes voor apps. Deze beveiligings wijziging helpt Azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1-als v2-eind punten.

Als uw app autorisatie codes opnieuw gebruikt om tokens voor meerdere resources op te halen, raden we u aan om de code te gebruiken om een vernieuwings token op te halen en vervolgens dat vernieuwings token te gebruiken voor het verkrijgen van aanvullende tokens voor andere resources. Autorisatie codes kunnen slechts één keer worden gebruikt, maar vernieuwings tokens kunnen meerdere keren worden gebruikt in meerdere resources. Een app die probeert een verificatie code opnieuw te gebruiken tijdens de OAuth-code stroom, krijgt een invalid_grant fout.

Zie voor deze en andere protocollen gerelateerde wijzigingen [de volledige lijst met nieuwe functies voor verificatie](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-september 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In september 2018 hebben we deze 16 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [tegemoetkomen aan wervings software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [sneeuw](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [racks Pace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO voor Azure, surveymonkey, [bijeenroeping](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="support-for-additional-claims-transformations-methods"></a>Ondersteuning voor aanvullende methoden voor claim transformaties

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

We hebben nieuwe claim transformatie methoden, ToLower () en ToUpper () geïntroduceerd, die kunnen worden toegepast op SAML-tokens van de configuratie pagina voor **eenmalige aanmelding** op basis van SAML.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) voor meer informatie.

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Bijgewerkte gebruikers interface voor app-configuratie op basis van SAML (preview-versie)

**Type:** Gewijzigde functie **service categorie:** Enter prise apps **product Capability:** SSO

Als onderdeel van onze bijgewerkte app configuratie-UI op basis van SAML krijgt u het volgende:

- Een bijgewerkte walkthrough-ervaring voor het configureren van uw op SAML gebaseerde apps.

- Meer inzicht in wat er ontbreekt of onjuist is in uw configuratie.

- De mogelijkheid om meerdere e-mail adressen toe te voegen voor de melding van een verlopen certificaat.

- Nieuwe claim transformatie methoden, ToLower () en ToUpper (), en meer.

- Een manier om uw eigen token handtekening certificaat voor uw zakelijke apps te uploaden.

- Een manier om de NameID-indeling voor SAML-apps in te stellen en een manier om de NameID-waarde als Directory-extensies in te stellen.

Als u deze bijgewerkte weer gave wilt inschakelen, klikt u op de koppeling **onze nieuwe ervaring proberen** vanaf de bovenkant van de pagina voor **eenmalige aanmelding** . Zie [zelf studie: eenmalige aanmelding op basis van SAML configureren voor een toepassing met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)voor meer informatie.

---

## <a name="august-2018"></a>Augustus 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Wijzigingen in Azure Active Directory IP-adresbereiken

**Type:** Planning voor **service categorie wijzigen:** andere **product capaciteit:** platform

We introduceren een groter IP-bereik voor Azure AD. Dit betekent dat als u Azure AD IP-adresbereiken voor uw firewalls, routers of netwerk beveiligings groepen hebt geconfigureerd, deze moeten worden bijgewerkt. We maken deze update, zodat u de IP-adresbereiken van uw firewall, router of netwerk beveiligings groepen niet opnieuw hoeft te wijzigen wanneer Azure AD nieuwe eind punten toevoegt.

Netwerk verkeer wordt in de volgende twee maanden verplaatst naar deze nieuwe bereiken. Als u wilt door gaan met een ononderbroken service, moet u deze bijgewerkte waarden vóór 10 september 2018 toevoegen aan uw IP-adressen:

- 20.190.128.0/18

- 40.126.0.0/18

Het is raadzaam om de oude IP-adresbereiken pas te verwijderen als al uw netwerk verkeer is verplaatst naar de nieuwe bereiken. Zie [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken voor updates over de verplaatsing en om te leren wanneer u de oude bereiken kunt verwijderen.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Wijzigings bericht: autorisatie codes kunnen niet langer opnieuw worden gebruikt

**Type:** Planning voor **service categorie wijzigen:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Vanaf 15 november 2018 zal Azure AD stoppen met het accepteren van eerder gebruikte verificatie codes voor apps. Deze beveiligings wijziging helpt Azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1-als v2-eind punten.

Als uw app autorisatie codes opnieuw gebruikt om tokens voor meerdere resources op te halen, raden we u aan om de code te gebruiken om een vernieuwings token op te halen en vervolgens dat vernieuwings token te gebruiken voor het verkrijgen van aanvullende tokens voor andere resources. Autorisatie codes kunnen slechts één keer worden gebruikt, maar vernieuwings tokens kunnen meerdere keren worden gebruikt in meerdere resources. Een app die probeert een verificatie code opnieuw te gebruiken tijdens de OAuth-code stroom, krijgt een invalid_grant fout.

Zie voor deze en andere protocollen gerelateerde wijzigingen [de volledige lijst met nieuwe functies voor verificatie](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Geconvergeerde beveiligings gegevens beheren voor SSPR (self-service password) en Multi-Factor Authentication (MFA)

**Type:** Nieuwe functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Deze nieuwe functie helpt gebruikers bij het beheren van hun beveiligings gegevens (zoals, telefoon nummer, mobiele app, enzovoort) voor SSPR en MFA op één locatie en ervaring; in vergelijking met eerder, waar de oplossing is uitgevoerd op twee verschillende locaties.

Deze geconvergeerde ervaring werkt ook voor mensen die gebruikmaken van SSPR of MFA. Als uw organisatie geen MFA-of SSPR-registratie afdwingt, kunnen gebruikers nog steeds de door uw organisatie toegestane MFA-of SSPR Security-gegevens methoden registreren vanuit de portal mijn apps.

Dit is een open bare preview-versie. Beheerders kunnen de nieuwe ervaring (indien gewenst) voor een geselecteerde groep of voor alle gebruikers in een Tenant inschakelen. Voor meer informatie over de geconvergeerde ervaring raadpleegt u de [geconvergeerde ervaring blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nieuwe instelling voor alleen HTTP-cookies in azure AD-toepassings proxy-apps

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Er is een nieuwe instelling met de naam **http-only cookies** in uw toepassings proxy-apps. Deze instelling helpt extra beveiliging te bieden door de HTTPOnly-vlag op te nemen in de HTTP-reactie header voor toegang tot de toepassings proxy en sessie cookies, de toegang tot de cookie vanuit een script aan de client zijde te stoppen en acties te voor komen zoals het kopiëren of aanpassen van de cookie. Hoewel deze vlag niet eerder is gebruikt, zijn uw cookies altijd versleuteld en verzonden met behulp van een TLS-verbinding om te helpen beschermen tegen onjuiste wijzigingen.

Deze instelling is niet compatibel met apps die gebruikmaken van ActiveX-besturings elementen, zoals Extern bureaublad. Als u in deze situatie bent, raden we u aan deze instelling uit te scha kelen.

Zie [toepassingen publiceren met Azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)voor meer informatie over de instelling voor alleen HTTP-cookies.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) voor Azure-resources ondersteunt resource typen van beheer groepen

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Just-in-time-activering en toewijzings instellingen kunnen nu worden toegepast op resource typen van de beheer groep, net zoals u al hebt gedaan voor abonnementen, resource groepen en resources (zoals Vm's, App Services en meer). Daarnaast kan iedereen met een rol die beheerders toegang biedt voor een beheer groep die resource in PIM detecteren en beheren.

Zie [Azure-resources detecteren en beheren met privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources) voor meer informatie over PIM-en Azure-resources.

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Toegang tot toepassingen (preview) biedt snellere toegang tot de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Wanneer een rol wordt geactiveerd met behulp van PIM, kan het langer dan tien minuten duren voordat de machtigingen van kracht worden. Als u ervoor kiest om toegang tot de toepassing te gebruiken, die momenteel beschikbaar is als open bare preview, hebben beheerders toegang tot de Azure AD-Portal zodra de activerings aanvraag is voltooid.

Momenteel ondersteunt toepassings toegang alleen de Azure AD Portal-ervaring en Azure-resources. Zie [Wat is Azure AD privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) voor meer informatie over PIM en toegang tot toepassingen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-augustus 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2018 augustus hebben we deze 16 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [BridgeLine](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial)niet-gebonden [, saus Labs-mobiele en webtests](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [META netwerken connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [manier van werken](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (per Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-onkosten rapporten](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 live chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Ondersteuning voor systeem eigen tableau is nu beschikbaar in azure AD-toepassingsproxy

**Type:** Gewijzigde functie **service categorie:** app-proxy **Product mogelijkheid:** Access Control

Met onze update van de OpenID connect verbinding maken met het OAuth 2,0 code Grant-protocol voor ons pre-verificatie protocol, hoeft u geen aanvullende configuratie meer te doen voor het gebruik van tableau met toepassings proxy. Deze protocol wijziging helpt ook toepassings proxy om meer moderne apps te ondersteunen door alleen HTTP-omleidingen te gebruiken, die algemeen worden ondersteund in Java script en HTML-tags.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nieuwe ondersteuning voor het toevoegen van Google als een id-provider voor B2B-gast gebruikers in Azure Active Directory (preview-versie)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

Door Federatie in te stellen met Google in uw organisatie, kunt u aan gebruikers met een eigen Google-account aanmelden bij uw gedeelde apps en resources, zonder dat hiervoor een persoonlijk micro soft-account (Msa's) of een Azure AD-account hoeft te worden gemaakt.

Dit is een open bare preview-versie. Zie [Google als een id-provider voor B2B-gast gebruikers toevoegen](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)voor meer informatie over Google Federation.

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Verbeteringen in Azure Active Directory e-mail meldingen

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Identity Lifecycle Management

E-mail berichten van Azure Active Directory (Azure AD) beschikken nu over een bijgewerkt ontwerp en wijzigingen in het e-mail adres van de afzender en de weergave naam van de afzender, wanneer deze worden verzonden vanuit de volgende services:

- Azure AD-toegangs beoordelingen
- Azure AD Connect Health (Engelstalig)
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Certificaat meldingen voor de Enter prise-app verlopen
- Service meldingen voor de Enter prise-app inrichten

De e-mail meldingen worden verzonden vanaf het volgende e-mail adres en de weergave naam:

- E-mail adres: azure-noreply@microsoft.com
- Weergave naam: Microsoft Azure

Zie [e-mail meldingen in azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832)voor een voor beeld van een aantal nieuwe e-mail ontwerpen en meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar via Azure Monitor

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

De activiteiten logboeken van Azure AD zijn nu beschikbaar in de open bare Preview voor de Azure Monitor (Azure-bewakings service voor het hele platform). Azure Monitor biedt u een lange termijn retentie en naadloze integratie, naast de volgende verbeteringen:

- Lange termijn retentie door uw logboek bestanden te routeren naar uw eigen Azure-opslag account.

- Naadloze SIEM-integratie, zonder dat u aangepaste scripts hoeft te schrijven of te onderhouden.

- Naadloze integratie met uw eigen aangepaste oplossingen, analyse hulpprogramma's of oplossingen voor incident beheer.

Voor meer informatie over deze nieuwe mogelijkheden raadpleegt u onze blog [Azure AD-activiteiten Logboeken in azure monitor Diagnostics is nu beschikbaar in de open bare preview-versie](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) en in onze documentatie [Azure Active Directory activiteiten logboeken in azure monitor (preview)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informatie over voorwaardelijke toegang toegevoegd aan het Azure AD-aanmeld rapport

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** identiteits beveiliging & beveiliging

Met deze update kunt u zien welke beleids regels worden geëvalueerd wanneer een gebruiker zich aanmeldt samen met het resultaat van het beleid. Daarnaast bevat het rapport nu het type client-app dat door de gebruiker wordt gebruikt, zodat u verouderd protocol verkeer kunt identificeren. Rapport vermeldingen kunnen nu ook worden doorzocht op een correlatie-ID, die kan worden gevonden in het fout bericht aan de gebruiker en kan worden gebruikt om de overeenkomende aanmeldings aanvraag te identificeren en op te lossen.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Verouderde verificaties weer geven via activiteiten logboeken voor aanmeldingen

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Met de introductie van het veld **client-app** in de logboeken voor aanmeldings activiteiten kunnen klanten nu zien welke gebruikers oudere verificaties gebruiken. Klanten hebben toegang tot deze informatie met behulp van de aanmeldingen Microsoft Graph API of via de aanmeld activiteiten Logboeken in de Azure AD-Portal, waar u het besturings element **client-app** kunt gebruiken om te filteren op verouderde verificaties. Raadpleeg de documentatie voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juli 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juli 2018 zijn deze 16 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Innovatie hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bepaalde beheer-SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC staging [, IPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screen cast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified leslokaal, [Eli onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar externe ondersteuning](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [denkers webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow-connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [vaardig heden basis](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nieuwe gebruikers die SaaS-app-integraties inrichten-juli 2018

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers identiteiten automatiseren in SaaS-toepassingen, zoals Dropbox, Sales Force, ServiceNow en meer. Voor 2018 juli hebben we ondersteuning voor gebruikers inrichting toegevoegd voor de volgende toepassingen in de app-galerie van Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Zie [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial)voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure AD-galerie.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health voor synchronisatie: een eenvoudigere manier om zwevende en dubbele kenmerk synchronisatie fouten op te lossen

**Type:** Nieuwe functie **service categorie:** AD Connect **product Capability:** monitoring & Reporting

Azure AD Connect Health introduceert Self-service herstel om u te helpen bij het markeren en oplossen van synchronisatie fouten. Deze functie lost dubbele kenmerken synchronisatie fouten op en verhelpt objecten die zijn verwijderd uit Azure AD. Deze diagnose biedt de volgende voor delen:

- Hiermee worden gedupliceerde kenmerk synchronisatie fouten beperkt, waardoor specifieke oplossingen worden geboden

- Past een oplossing toe voor specifieke Azure AD-scenario's, waarbij fouten in één stap worden opgelost

- Er is geen upgrade of configuratie vereist om deze functie in te scha kelen en te gebruiken

Zie voor meer informatie [problemen met dubbele kenmerken vaststellen en oplossen](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuele updates voor de Azure AD-en MSA-aanmeld ervaringen

**Type:** Gewijzigde functie **service categorie:** Azure AD- **product functionaliteit:** gebruikers verificatie

De gebruikers interface voor de onlineservices aanmeld procedure van micro soft is bijgewerkt, bijvoorbeeld voor Office 365 en Azure. Met deze wijziging worden de schermen minder overzichtelijk en eenvoudiger. Zie voor meer informatie over deze wijziging de [aanstaande verbeteringen in de blog van Azure AD-aanmeld ervaring](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nieuwe versie van Azure AD Connect-juli 2018

**Type:** Gewijzigde functie **service categorie:** app Provisioning **product Capability:** Identity Lifecycle Management

De nieuwste versie van Azure AD Connect omvat:

- Problemen met oplossingen en ondersteunings updates

- Algemene Beschik baarheid van de integratie met ping-webrelatie

- Updates voor de nieuwste SQL 2012-client

Zie [Azure AD Connect: release geschiedenis](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) van de versie voor meer informatie over deze update.

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Updates voor de gebruiks voorwaarden van de gebruikers interface van de eind gebruiker

**Type:** Gewijzigde functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

De acceptatie teken reeks wordt bijgewerkt in de gebruikers interface van de gebruiksrecht overeenkomst.

**Huidige tekst.** U moet de gebruiks voorwaarden accepteren om toegang te krijgen tot resources van [Tenant].<br>**Nieuwe tekst.** Als u toegang wilt krijgen tot de resource [tenantnaam], moet u de gebruiksrecht overeenkomst lezen.

**Huidige tekst:** Als u ervoor kiest om te accepteren, moet u akkoord gaan met alle bovenstaande gebruiks voorwaarden.<br>**Nieuwe tekst:** Klik op accepteren om te bevestigen dat u de gebruiks voorwaarden hebt gelezen en begrepen.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Pass Through-verificatie ondersteunt verouderde protocollen en toepassingen

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Pass-Through-verificatie ondersteunt nu oudere protocollen en apps. De volgende beperkingen worden nu volledig ondersteund:

- Gebruikers aanmeldingen voor oudere Office-client toepassingen, Office 2010 en Office 2013, zonder moderne verificatie.

- Toegang tot agenda delen en beschikbaarheids info in hybride omgevingen van Exchange alleen in Office 2010.

- Aanmelden bij gebruikers van Skype voor bedrijven-client toepassingen zonder moderne verificatie.

- Aanmeldingen van gebruikers naar Power shell-versie 1,0.

- De Apple-Device Enrollment Program (Apple DEP) met behulp van de iOS-Configuratieassistent.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Geconvergeerde beveiligings gegevens beheren voor selfservice voor wachtwoord herstel en Multi-Factor Authentication

**Type:** Nieuwe functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Met deze nieuwe functie kunnen gebruikers hun beveiligings gegevens beheren (bijvoorbeeld telefoon nummer, e-mail adres, mobiele app enzovoort) voor selfservice voor wachtwoord herstel (SSPR) en Multi-Factor Authentication (MFA) in één ervaring. Gebruikers hoeven niet langer dezelfde beveiligings gegevens voor SSPR en MFA te registreren in twee verschillende ervaringen. Deze nieuwe ervaring is ook van toepassing op gebruikers die een SSPR of MFA hebben.

Als een organisatie geen MFA-of SSPR-registratie afdwingt, kunnen gebruikers hun beveiligings gegevens registreren via de portal **mijn apps** . Van daaruit kunnen gebruikers methoden registreren die zijn ingeschakeld voor MFA of SSPR.

Dit is een open bare preview-versie. Beheerders kunnen de nieuwe ervaring (indien gewenst) inschakelen voor een geselecteerde groep gebruikers of voor alle gebruikers in een Tenant.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>De Microsoft Authenticator-app gebruiken om uw identiteit te verifiëren wanneer u uw wacht woord opnieuw instelt

**Type:** Gewijzigde functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Met deze functie kunnen niet-beheerders hun identiteit verifiëren tijdens het opnieuw instellen van een wacht woord met behulp van een melding of code van Microsoft Authenticator (of een andere verificator-app). Nadat beheerders deze self-service voor het opnieuw instellen van wacht woorden hebben ingeschakeld, kunnen gebruikers die een mobiele app hebben geregistreerd via aka.ms/mfasetup of aka.ms/setupsecurityinfo hun mobiele app gebruiken als een verificatie methode tijdens het opnieuw instellen van hun wacht woord.

De melding voor mobiele apps kan alleen worden ingeschakeld als onderdeel van een beleid dat twee methoden vereist om uw wacht woord opnieuw in te stellen.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Wijzigings bericht: beveiligings oplossing voor de gedelegeerde autorisatie stroom voor apps met behulp van de Azure AD-activiteiten logboeken-API

**Type:** Planning voor **service categorie wijzigen:** rapportage van **product mogelijkheden:** bewaking & rapportage

Vanwege onze sterkere beveiligings handhaving hebben we een wijziging aangebracht in de machtigingen voor apps die gebruikmaken van een gedelegeerde autorisatie stroom om toegang te krijgen tot de [api's van Azure AD-activiteiten logboeken](https://aka.ms/aadreportsapi). Deze wijziging gaat in op **26 juni 2018**.

Als uw apps Azure AD-activiteiten logboek-Api's gebruiken, voert u de volgende stappen uit om ervoor te zorgen dat de app niet wordt onderbroken nadat de wijziging is opgegaan.

**Uw app-machtigingen bijwerken**

1. Meld u aan bij de Azure Portal, selecteer **Azure Active Directory**en selecteer vervolgens **app-registraties**.
2. Selecteer uw app die gebruikmaakt van de API voor Azure AD-activiteiten logboeken, selecteer **instellingen**, selecteer **vereiste machtigingen**en selecteer vervolgens de **Windows Azure Active Directory** -API.
3. Schakel in het gebied **gedelegeerde machtigingen** van de Blade **toegang inschakelen** het selectie vakje in naast **Directory gegevens lezen** en selecteer vervolgens **Opslaan**.
4. Selecteer **machtigingen verlenen**en selecteer vervolgens **Ja**.

    >[!Note]
    >U moet een globale beheerder zijn om machtigingen voor de app te kunnen verlenen.

Zie het gedeelte [machtigingen verlenen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) van de vereisten voor toegang tot het Azure AD Reporting API-artikel voor meer informatie.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>TLS-instellingen configureren om verbinding te maken met Azure AD-Services voor PCI DSS compatibiliteit

**Type:** Nieuwe functie **service categorie:** n.v.t. **product capaciteit:** platform

Transport Layer Security (TLS) is een protocol dat privacy-en gegevens integriteit mogelijk maakt tussen twee communicatie toepassingen en het meest gebruikte beveiligings protocol dat momenteel wordt gebruikt.

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft vastgesteld dat vroege versies van TLS en Secure Sockets Layer (SSL) moeten worden uitgeschakeld in het voor deel van het inschakelen van nieuwe en veiligere app-protocollen, met naleving vanaf **30 juni 2018**. Deze wijziging betekent dat als u verbinding maakt met Azure AD-Services en PCI DSS-naleving vereist, u TLS 1,0 moet uitschakelen. Er zijn meerdere versies van TLS beschikbaar, maar TLS 1,2 is de meest recente versie die beschikbaar is voor Azure Active Directory Services. We raden u ten zeerste aan om rechtstreeks naar TLS 1,2 te gaan voor client-en server-en browser-en server combinaties.

Verouderde browsers ondersteunen mogelijk geen nieuwere TLS-versies, zoals TLS 1,2. Als u wilt zien welke versies van TLS door uw browser worden ondersteund, gaat u naar de [QUALYS SSL Labs](https://www.ssllabs.com/) -site en klikt u op **uw browser testen**. U wordt aangeraden om een upgrade uit te voeren naar de nieuwste versie van uw webbrowser en bij voor keur alleen TLS 1,2 in te scha kelen.

**TLS 1,2 inschakelen, per browser**

- **Micro soft Edge en Internet Explorer (beide worden ingesteld met Internet Explorer)**

    1. Open Internet Explorer, selecteer **extra**  >  **geavanceerde Internet opties**  >  **Advanced**.
    2. Selecteer in het gebied **beveiliging** de optie **TLS 1,2 gebruiken**en selecteer vervolgens **OK**.
    3. Sluit alle browser vensters en start Internet Explorer opnieuw.

- **Google Chrome**

    1. Open Google Chrome, typ *Chrome://Settings/* in de adres balk en druk op **Enter**.
    2. Vouw de **Geavanceerde** opties uit, ga naar het gebied **systeem** en selecteer **proxy-instellingen openen**.
    3. Selecteer in het vak **Internet eigenschappen** het tabblad **Geavanceerd** , ga naar het gebied **beveiliging** , selecteer **TLS 1,2 gebruiken**en selecteer vervolgens **OK**.
    4. Sluit alle browser vensters en start Google Chrome opnieuw op.

- **Mozilla Firefox**

    1. Open Firefox, typ *about: config* in de adres balk en druk op **Enter**.
    2. Zoek naar de term *TLS*en selecteer vervolgens de vermelding **Security. TLS. version. Max** .
    3. Stel de waarde in op **3** om te zorgen dat de browser Maxi maal versie TLS 1,2 gebruikt en selecteer **OK**.

        >[!NOTE]
        >Versie 60,0 van Firefox ondersteunt TLS 1,3, dus u kunt ook de waarde Security. TLS. version. Max instellen op **4**.

    4. Sluit alle browser vensters en Start Mozilla Firefox opnieuw.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juni 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juni 2018 hebben we deze 15 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), het [afwikkelen van muziek](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), het [SAML 1,1-token ingeschakelde LOB-app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [supersfeer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [endpoint backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TONICDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [share point on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [topcx Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [chronischex](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-wachtwoord beveiliging is beschikbaar als open bare preview

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** gebruikers verificatie

Gebruik Azure AD-wachtwoord beveiliging om eenvoudig te raden wacht woorden uit uw omgeving te verwijderen. Door deze wacht woorden te elimineren, wordt het risico van inbreuk op een type aanval op wacht woord verminderd.

Met name Azure AD-wachtwoord beveiliging helpt u bij het volgende:

- Beveilig de accounts van uw organisatie in zowel Azure AD als Windows Server Active Directory (AD).
- Hiermee wordt voor komen dat gebruikers wacht woorden gebruiken voor een lijst met meer dan 500 van de meest gebruikte wacht woorden en meer dan 1.000.000 teken vervanging van deze wacht woorden.
- Beheer Azure AD-wachtwoord beveiliging vanaf één locatie in de Azure AD-portal voor zowel Azure AD als on-premises Windows Server AD.

Zie voor meer informatie over Azure AD-wachtwoord beveiliging [het elimineren van ongeldige wacht woorden in uw organisatie](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe sjabloon voor beleid voor voorwaardelijke toegang van alle gasten gemaakt tijdens het maken van de gebruiksrecht overeenkomst

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Tijdens het maken van de gebruiks voorwaarden wordt ook een nieuwe beleids sjabloon voor voorwaardelijke toegang gemaakt voor alle gasten en alle apps. Met deze nieuwe beleids sjabloon past u de nieuwe gebruiks voorwaarden toe, waarmee u het proces voor het maken en afdwingen voor gasten kunt stroom lijnen.

Zie [Azure Active Directory gebruiksvoorwaarden functie](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe aangepaste beleids sjabloon voor voorwaardelijke toegang die is gemaakt tijdens het maken van de gebruiksrecht overeenkomst

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Tijdens het maken van de gebruiks voorwaarden wordt ook een nieuwe ' aangepaste beleids sjabloon voor voorwaardelijke toegang gemaakt. Met deze nieuwe beleids sjabloon kunt u de gebruiks voorwaarden maken en vervolgens direct naar de Blade voor het maken van beleid voor voorwaardelijke toegang gaan, zonder dat u hand matig door de portal hoeft te navigeren.

Zie [Azure Active Directory gebruiksvoorwaarden functie](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nieuwe en uitgebreide richt lijnen voor het implementeren van Azure Multi-Factor Authentication

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** identiteits beveiliging & beveiliging

We hebben nieuwe stapsgewijze richt lijnen uitgebracht voor het implementeren van Azure Multi-Factor Authentication (MFA) in uw organisatie.

Als u de MFA-implementatie handleiding wilt weer geven, gaat u naar de hand leiding voor [identiteits implementatie](https://aka.ms/DeploymentPlans) opslag plaats op github. Als u feedback wilt geven over de implementatie handleidingen, gebruikt u het [feedback formulier voor het implementatie plan](https://aka.ms/deploymentplanfeedback). Als u vragen hebt over de implementatie handleidingen, kunt u contact met ons opnemen via [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD gedelegeerde app management-rollen bevinden zich in de open bare preview

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product capability:** Access Control

Beheerders kunnen nu app-beheer taken delegeren zonder de rol van globale beheerder toe te wijzen. De nieuwe functies en mogelijkheden zijn:

- **Nieuwe standaard Azure AD-beheerders rollen:**

    - **Toepassings beheerder.** Biedt de mogelijkheid om alle aspecten van alle apps te beheren, waaronder registratie, SSO-instellingen, app-toewijzingen en licenties, instellingen voor app-proxy en toestemming (met uitzonde ring van Azure AD-resources).

    - **Cloud toepassings beheerder.** Verleent alle toepassings beheerder-vaardig heden, met uitzonde ring van app proxy, omdat deze geen on-premises toegang biedt.

    - **Application Developer.** Biedt de mogelijkheid om app-registraties te maken, zelfs als de optie **gebruikers toestaan apps te registreren** is uitgeschakeld.

- **Eigendom (per app-registratie en per bedrijfs-app instellen, vergelijkbaar met het groeps eigenaars proces:**

    - **Eigenaar van app-registratie.** Biedt de mogelijkheid om alle aspecten van app-registratie te beheren, met inbegrip van het app-manifest en toevoegen van extra eigen aren.

    - **Eigenaar van de Enter prise-app.** Biedt de mogelijkheid om veel aspecten van bedrijfs-apps te beheren, waaronder SSO-instellingen, app-toewijzingen en toestemming (met uitzonde ring van Azure AD-resources).

Voor meer informatie over open bare preview-versie, zie de [open bare preview-functie voor Azure AD-gedelegeerde toepassingen.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blogs. Zie [beheerders rollen toewijzen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)voor meer informatie over rollen en machtigingen.

---

## <a name="may-2018"></a>Mei 2018

### <a name="expressroute-support-changes"></a>ExpressRoute ondersteunen wijzigingen

**Type:** Planning voor wijziging **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** platform

Software als een service aanbieding, zoals Azure Active Directory (Azure AD), is ontworpen om het beste te kunnen werken door rechtstreeks via internet te gaan, zonder dat hiervoor ExpressRoute of andere particuliere VPN-tunnels zijn vereist. Daarom zullen we op **1 augustus 2018**geen ondersteuning meer bieden voor ExpressRoute voor Azure AD-Services met behulp van open bare Azure-peering en Azure-Community's in micro soft-peering. Services die van invloed zijn op deze wijziging, merken mogelijk Azure AD-verkeer geleidelijk af van ExpressRoute naar Internet.

Hoewel we onze ondersteuning wijzigen, weten we ook dat er nog steeds situaties zijn waarin u mogelijk een speciale set circuits voor uw verificatie verkeer moet gebruiken. Hierdoor blijft Azure AD ondersteuning bieden voor de IP-bereik beperkingen per Tenant met behulp van ExpressRoute en services die al op micro soft-peering zijn aangesloten met de community ' other Office 365 Online Services '. Als uw services worden beïnvloed, maar u ExpressRoute nodig hebt, moet u het volgende doen:

- **Als u gebruikmaakt van open bare Azure-peering.** Ga naar micro soft-peering en meld u aan voor de **andere Office 365 Online Services (12076:5100)-** community. Zie het artikel [een openbaar peering naar micro soft-peering verplaatsen](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) voor meer informatie over het verplaatsen van open bare Azure-peering naar micro soft-peering.

- **Als u gebruikmaakt van micro soft-peering.** Meld u aan voor de **andere Office 365 online service-Community (12076:5100)** . Zie de [sectie ondersteuning voor BGP-community's](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) van het artikel ExpressRoute Routing Requirements (Engelstalig) voor meer informatie over routerings vereisten.

Als u speciale circuits moet blijven gebruiken, moet u contact opnemen met uw micro soft-account team over het verkrijgen van toestemming voor het gebruik van de **andere Office 365 online service-Community (12076:5100)** . Op het MS Office-Managed Review Board wordt gecontroleerd of u deze circuits nodig hebt en weet u zeker dat u de technische implicaties begrijpt van het bijhouden van de stroom. Bij niet-geautoriseerde abonnementen voor het maken van route filters voor Office 365 wordt een fout bericht weer gegeven.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph Api's voor beheer scenario's voor gebruiks voorwaarden

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product mogelijkheid:** ontwikkelaars ervaring

We hebben Microsoft Graph-Api's toegevoegd voor de beheer bewerking van Azure AD-gebruiks voorwaarden. U kunt het object voor waarden van het gebruik maken, bijwerken en verwijderen.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Een Azure AD-eind punt voor meerdere tenants toevoegen als een id-provider in Azure AD B2C

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

Met aangepaste beleids regels kunt u nu het gemeen schappelijke eind punt van Azure AD toevoegen als een id-provider in Azure AD B2C. Op deze manier kunt u één toegangs punt hebben voor alle Azure AD-gebruikers die zich aanmelden bij uw toepassingen. Zie [Azure Active Directory B2C: gebruikers toestaan zich aan te melden bij een multi tenant Azure ad-id-provider met behulp van aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)voor meer informatie.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Gebruik interne Url's voor toegang tot apps vanaf elke locatie met onze aanmeld extensie voor mijn apps en Azure AD-toepassingsproxy

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

Gebruikers hebben nu toegang tot toepassingen via interne Url's, zelfs wanneer ze zich buiten het bedrijfs netwerk bevinden met behulp van de beveiligde aanmeldings extensie voor mijn apps voor Azure AD. Dit werkt met alle toepassingen die u hebt gepubliceerd met behulp van Azure AD-toepassingsproxy, op elke browser waarop ook de browser uitbreiding van het toegangs venster is geïnstalleerd. De functie voor URL-omleiding wordt automatisch ingeschakeld wanneer een gebruiker zich aanmeldt bij de extensie. De uitbrei ding is beschikbaar voor down loads van [micro soft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)en [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory-gegevens in Europa voor Europa-klanten

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** GoLocal

Klanten in Europa moeten hun gegevens in Europa blijven en niet worden gerepliceerd buiten de Europese data centers voor de privacy-en Europese wetgeving van de vergadering. Dit [artikel](https://go.microsoft.com/fwlink/?linkid=872328) bevat de specifieke details over welke identiteits gegevens worden opgeslagen in Europa en geeft ook informatie over informatie die buiten Europese data centers zal worden opgeslagen.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nieuwe gebruikers die SaaS-app-integraties inrichten-mei 2018

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers identiteiten automatiseren in SaaS-toepassingen, zoals Dropbox, Sales Force, ServiceNow en meer. Voor mei 2018 hebben we ondersteuning voor het inrichten van gebruikers toegevoegd voor de volgende toepassingen in de app-galerie van Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Zie voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure AD-galerie [https://aka.ms/appstutorial](https://aka.ms/appstutorial) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-toegangs beoordelingen van groepen en app-toegang biedt nu terugkerende Recensies

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** governance

Toegangs beoordeling van groepen en apps is nu algemeen beschikbaar als onderdeel van Azure AD Premium P2.  Beheerders kunnen toegangs Beoordelingen voor groepslid maatschappen en toepassings toewijzingen configureren om automatisch te laten terugkeren met regel matige tussen pozen, zoals maandelijks of per kwar taal.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-activiteiten Logboeken (aanmeldingen en controleren) zijn nu beschikbaar via MS Graph

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Azure AD-activiteiten logboeken, die, inclusief aanmeldingen en audit logboeken, zijn nu beschikbaar via de Microsoft Graph-API. Er zijn twee eind punten beschikbaar via de Microsoft Graph-API voor toegang tot deze logboeken. Bekijk onze [documenten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) voor programmatische toegang tot Azure AD Reporting api's om aan de slag te gaan.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Verbeteringen in het B2B-inwisselings proces en een organisatie verlaten

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

**Just-in-time-inwisseling:** Nadat u een resource met een gast-API hebt gedeeld, hoeft u geen speciale e-mail met een uitnodiging te verzenden. In de meeste gevallen heeft de gast gebruiker toegang tot de bron en wordt de inwisselings ervaring gewoon in tijd genomen. Er is geen gevolgen meer als gevolg van gemist e-mail berichten. Niet meer vragen om uw gast gebruikers "had u geklikt op die opname koppeling het systeem heeft u gestuurd?". Dit betekent dat wanneer SPO de uitnodigings Manager gebruikt: Cloud bijlagen kunnen dezelfde canonieke URL hebben voor alle gebruikers – intern en extern – in elke staat van inwisseling.

**Moderne aflossings ervaring:** Geen pagina meer gesplitste scherm opname. Gebruikers krijgen een moderne toestemming te zien met de privacyverklaring van de uitgenodigde organisatie, net als bij apps van derden.

**Gast gebruikers kunnen de organisatie verlaten:** Zodra de relatie van een gebruiker met een organisatie is afgelopen, kunnen ze zelf de organisatie verlaten. U hoeft de beheerder van de uitnodigende organisatie niet meer aan te roepen om te worden verwijderd. er worden geen ondersteunings tickets meer verhoogd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-mei 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In mei 2018 hebben we deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), openspoel, [Arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [koppel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nieuwe stapsgewijze implementatie handleidingen voor Azure Active Directory

**Type:** Nieuwe functie **service categorie:** andere **product capaciteit:** Directory

Nieuwe stapsgewijze richt lijnen voor het implementeren van Azure Active Directory (Azure AD), waaronder selfservice voor wachtwoord herstel (SSPR), eenmalige aanmelding (SSO), voorwaardelijke toegang (CA), app proxy, User provisioning, Active Directory Federation Services (ADFS) voor Pass-Through-verificatie (PTA) en ADFS to Password Hash Sync (PHS).

Als u de implementatie handleidingen wilt weer geven, gaat u naar de [hand leidingen voor identiteits implementatie](https://aka.ms/DeploymentPlans) opslag plaats op github. Als u feedback wilt geven over de implementatie handleidingen, gebruikt u het [feedback formulier voor het implementatie plan](https://aka.ms/deploymentplanfeedback). Als u vragen hebt over de implementatie handleidingen, kunt u contact met ons opnemen via [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Bedrijfs toepassingen zoeken-meer apps laden

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

Hebt u problemen met het vinden van uw toepassingen/service-principals? We hebben de mogelijkheid toegevoegd om meer toepassingen te laden in de lijst met alle toepassingen van uw bedrijfs toepassingen. Standaard worden er 20 toepassingen weer gegeven. U kunt nu klikken, **meer laden** om extra toepassingen weer te geven.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>De mei-release van AADConnect bevat een open bare preview van de integratie met PingFederate, belang rijke beveiligings updates, veel oplossingen voor fouten en nieuwe fantastische nieuwe hulpprogram ma's voor probleem oplossing.

**Type:** Gewijzigde functie **service categorie:** AD Connect **product Capability:** Identity Lifecycle Management

De mei-release van AADConnect bevat een open bare preview van de integratie met PingFederate, belang rijke beveiligings updates, veel oplossingen voor fouten en nieuwe fantastische nieuwe hulpprogram ma's voor probleem oplossing. U vindt [hier](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)de release opmerkingen.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-toegangs beoordelingen: automatisch Toep assen

**Type:** Gewijzigde functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** governance

Toegangs beoordelingen van groepen en apps zijn nu algemeen beschikbaar als onderdeel van Azure AD Premium P2. Een beheerder kan configureren om de wijzigingen van de revisor voor die groep of app automatisch toe te passen wanneer de toegangs beoordeling is voltooid. De beheerder kan ook opgeven wat er gebeurt met de voortdurende toegang van de gebruiker als revisoren niet hebben gereageerd, toegang verwijderen, toegang houden of systeem aanbevelingen nemen.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-tokens kunnen niet meer worden geretourneerd met de query response_mode voor nieuwe apps.

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Apps die zijn gemaakt op of na 25 april 2018 kunnen geen **id_token** meer aanvragen met behulp van de **query** response_mode.  Dit brengt Azure AD inline met de OIDC-specificaties en vermindert de kwets baarheid voor uw apps.  Apps die zijn gemaakt vóór 25 april 2018, worden niet geblokkeerd voor het gebruik van de **query** response_mode met een response_type van **id_token**.  De geretourneerde fout, bij het aanvragen van een id_token van AAD, is **AADSTS70007: ' query ' is geen ondersteunde waarde van ' response_mode ' bij het aanvragen van een token**.

Het **fragment** en **form_post** response_modes blijven werken: bij het maken van nieuwe toepassings objecten (bijvoorbeeld voor het gebruik van de app-proxy), moet u ervoor zorgen dat een van deze response_modes wordt gebruikt voordat een nieuwe toepassing wordt gemaakt.

---

## <a name="april-2018"></a>April 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C toegangs token is GA

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U hebt nu toegang tot Web-Api's die worden beveiligd door Azure AD B2C met behulp van toegangs tokens. De functie wordt verplaatst van de open bare preview naar GA. De gebruikers interface-ervaring voor het configureren van Azure AD B2C toepassingen en Web-Api's is verbeterd en er zijn andere kleine verbeteringen aangebracht.

Zie [Azure AD B2C: toegangs tokens aanvragen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)voor meer informatie.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Configuratie van eenmalige aanmelding voor op SAML gebaseerde toepassingen testen

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

Bij het configureren van op SAML gebaseerde SSO-toepassingen kunt u de integratie op de pagina configuratie testen. Als er een fout optreedt tijdens het aanmelden, kunt u de fout in de test ervaring opgeven en Azure AD voorziet in oplossings stappen om het specifieke probleem op te lossen.

Zie voor meer informatie:

- [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps) (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)
- [Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD-gebruiks voorwaarden hebben nu per gebruiker rapporteert

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Beheerders kunnen nu een bepaalde gebruiks voorwaarden selecteren en alle gebruikers weer geven die hebben ingestemd op die gebruiks voorwaarden en de datum/tijd waarop ze hebben plaatsgevonden.

Zie de [functie gebruiks voorwaarden van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: riskant IP-adres voor AD FS beveiliging van vergren deling

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** bewaking & rapportage

Connect Health ondersteunt nu de mogelijkheid om IP-adressen te detecteren die de drempel van mislukte U/P-aanmeldingen per uur of per dag overschrijden. De mogelijkheden van deze functie zijn:

- Uitgebreid rapport met het IP-adres en het aantal mislukte aanmeldingen dat per uur per dag wordt gegenereerd met aanpas bare drempel waarde.
- Op e-mail gebaseerde waarschuwingen die aangeven wanneer een specifiek IP-adres de drempel van mislukte U/P-aanmeldingen per uur/dagelijks heeft overschreden.
- Een download optie om een gedetailleerde analyse van de gegevens uit te voeren

Zie [riskive IP Report](https://aka.ms/aadchriskyip)(Engelstalig) voor meer informatie.

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Eenvoudige app-configuratie met bestand of URL van meta gegevens

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

Op de pagina bedrijfs toepassingen kunnen beheerders een SAML-meta gegevensbestand uploaden om op SAML gebaseerde aanmelding te configureren voor AAD-galerie en niet-galerie toepassingen.

Daarnaast kunt u de URL van de Azure AD-toepassing voor federatieve meta gegevens gebruiken voor het configureren van eenmalige aanmelding met de doel toepassing.

Zie [eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassings galerie bevinden](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)voor meer informatie.

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Gebruiksvoorwaarden nu algemeen verkrijgbaar

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving


Azure AD-gebruiks voorwaarden zijn verplaatst van de open bare preview naar algemeen beschikbaar.

Zie de [functie gebruiks voorwaarden van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Uitnodigingen aan B2B-gebruikers van specifieke organisaties toestaan of blokkeren

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C


U kunt nu opgeven welke partner organisaties u wilt delen en samen werken met in azure AD B2B-samen werking. Als u dit wilt doen, kunt u een lijst maken met specifieke domeinen voor toestaan of weigeren. Wanneer een domein wordt geblokkeerd met behulp van deze mogelijkheden, kunnen werk nemers geen uitnodigingen meer verzenden naar personen in dat domein.

Zo kunt u de toegang tot uw resources beheren, terwijl u een soepele ervaring voor goedgekeurde gebruikers inschakelt.

Deze functie voor B2B-samen werking is beschikbaar voor alle Azure Active Directory klanten en kan worden gebruikt in combi natie met Azure AD Premium functies zoals voorwaardelijke toegang en identiteits beveiliging voor een gedetailleerdere controle van wanneer en hoe externe zakelijke gebruikers zich aanmelden en toegang krijgen.

Zie [uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In april 2018 hebben we deze 13 nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

Criterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [organigram Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta performance monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [stroomx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), schap, [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-gebruikers in azure AD toegang verlenen tot uw on-premises toepassingen (open bare preview)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

Als organisatie die gebruikmaakt van Azure Active Directory (Azure AD) B2B-samenwerkings mogelijkheden om gast gebruikers van partner organisaties te uitnodigen voor uw Azure AD, kunt u deze B2B-gebruikers nu toegang bieden tot on-premises apps. Deze on-premises apps kunnen gebruikmaken van op SAML gebaseerde verificatie of geïntegreerde Windows-verificatie (IWA) met Kerberos-beperkte delegering (KCD).

Zie [B2B-gebruikers in azure AD-toegang verlenen aan uw on-premises toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)voor meer informatie.

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Zelf studies voor SSO-integratie ophalen via Azure Marketplace

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** externe integratie

Als een toepassing die in de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) wordt vermeld, ondersteuning biedt voor eenmalige aanmelding op basis van SAML, kunt u op **Get it nu** de zelf studie voor integratie koppelen die aan die toepassing is gekoppeld.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snellere prestaties van automatische gebruikers inrichting voor SaaS-toepassingen in azure AD

**Type:** Gewijzigde functie **service categorie:** app Provisioning **product mogelijkheid:** externe integratie

Voorheen kunnen klanten die de Azure Active Directory User Provisioning connectors voor SaaS-toepassingen (bijvoorbeeld Sales Force, ServiceNow en box) gebruiken, trage prestaties ondervinden als hun Azure AD-tenants zich in meer dan 100.000 gecombineerde gebruikers en groepen bevinden, en ze gebruikers-en groeps toewijzingen gebruiken om te bepalen welke gebruikers moeten worden ingericht.

Op 2 april 2018 zijn er belang rijke verbeteringen voor prestaties geïmplementeerd in de Azure AD-inrichtings service die de benodigde tijd voor het uitvoeren van initiële synchronisaties tussen Azure Active Directory en de doel-SaaS-toepassingen aanzienlijk reduceert.

Als gevolg hiervan worden veel klanten die aanvankelijk hebben gesynchroniseerd naar apps die veel dagen of nog nooit hebben voltooid, binnen een paar minuten of uren voltooid.

Zie [Wat gebeurt er tijdens het inrichten?](/azure//active-directory/app-provisioning/how-provisioning-works) voor meer informatie.

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Selfservice voor wachtwoord herstel van Windows 10-vergrendelings scherm voor hybride Azure AD gekoppelde computers

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

We hebben de SSPR-functie van Windows 10 bijgewerkt zodat deze ondersteuning biedt voor machines die zijn toegevoegd aan hybride Azure AD. Deze functie is beschikbaar in Windows 10 RS4. Hiermee kunnen gebruikers hun wacht woord opnieuw instellen vanaf het vergrendelings scherm van een Windows 10-computer. Gebruikers die zijn ingeschakeld en geregistreerd voor selfservice voor wachtwoord herstel, kunnen gebruikmaken van deze functie.

Zie [Azure AD-wacht woord opnieuw instellen in het aanmeldings scherm](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)voor meer informatie.

---

## <a name="march-2018"></a>Maart 2018

### <a name="certificate-expire-notification"></a>Melding dat het certificaat verloopt

**Type:** Vaste **service categorie:** Enter prise apps **product Capability:** SSO

Azure AD verzendt een melding wanneer een certificaat voor een galerie of niet-galerie toepassing bijna is verlopen.

Sommige gebruikers hebben geen meldingen ontvangen voor bedrijfs toepassingen die zijn geconfigureerd voor eenmalige aanmelding op basis van SAML. Dit probleem is opgelost. Azure AD verzendt een melding voor certificaten die verlopen zijn in 7, 30 en 60 dagen. U kunt deze gebeurtenis zien in de audit Logboeken.

Zie voor meer informatie:

- [Certificaten beheren voor federatieve eenmalige aanmelding in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Controleactiviteitenrapporten in Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter-en GitHub-id-providers in Azure AD B2C

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu Twitter of GitHub toevoegen als een id-provider in Azure AD B2C. Twitter wordt verplaatst van open bare preview naar GA. GitHub wordt uitgebracht in een open bare preview.

Zie [Wat is Azure AD B2B-samen werking?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)voor meer informatie.

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Browser toegang beperken met Intune Managed Browser met voorwaardelijke toegang op basis van een toepassing voor Azure AD voor iOS en Android

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

**Nu beschikbaar in de open bare preview.**

**INTUNE Managed browser SSO:** Uw werk nemers kunnen eenmalige aanmelding gebruiken voor systeem eigen clients (zoals micro soft Outlook) en de Intune Managed Browser voor alle apps die zijn verbonden met Azure AD.

**Ondersteuning voor voorwaardelijke toegang intune Managed browser:** U kunt werk nemers nu de intune Managed browser gebruiken met behulp van beleid voor voorwaardelijke toegang op basis van toepassingen.

Meer informatie hierover vindt u in onze [blog post](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Zie voor meer informatie:

- [Voorwaardelijke toegang op basis van toepassingen instellen](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed browser-beleid configureren](https://aka.ms/managedbrowser)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App-proxy-cmdlets in Power shell GA-module

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Ondersteuning voor toepassings proxy-cmdlets is nu beschikbaar in de Power shell-module. Hiervoor moet u de Power shell-modules bijgewerkt blijven: als u meer dan een jaar achter raakt, werken sommige cmdlets niet meer.

Zie [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)voor meer informatie.

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 native-clients worden ondersteund door naadloze SSO met behulp van een niet-interactief Protocol

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Gebruiker die gebruikmaakt van Office 365 native clients (versie 16.0.8730. xxxx en hoger), krijgt de mogelijkheid om zich aan te melden met naadloze SSO. Deze ondersteuning wordt geboden door de toevoeging van een niet-interactief Protocol (WS-Trust) aan Azure AD.

Zie [Hoe werkt aanmelden op een systeem eigen client met naadloze SSO?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work) voor meer informatie.

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Gebruikers krijgen een stille aanmelding, met naadloze SSO, als een toepassing aanmeldings aanvragen verzendt naar de Tenant-eind punten van Azure AD

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Gebruikers krijgen een stille aanmelding, met naadloze SSO, als een toepassing (bijvoorbeeld `https://contoso.sharepoint.com` ) aanmeldings aanvragen verzendt naar de Tenant eindpunten van Azure AD, dat wil zeggen, `https://login.microsoftonline.com/contoso.com/<..>` of `https://login.microsoftonline.com/<tenant_ID>/<..>` -in plaats van het gemeen schappelijke eind punt () van Azure AD `https://login.microsoftonline.com/common/<...>` .

Zie [Azure Active Directory naadloze eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)voor meer informatie.

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>U hoeft slechts één Azure AD-URL, in plaats van twee Url's, toe te voegen aan de intranet zone-instellingen van gebruikers om naadloze SSO uit te vouwen

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Als u naadloze SSO wilt implementeren voor uw gebruikers, moet u slechts één Azure AD-URL toevoegen aan de intranet zone-instellingen van de gebruikers met behulp van groeps beleid in Active Directory: `https://autologon.microsoftazuread-sso.com` . Voorheen moesten we twee Url's toevoegen.

Zie [Azure Active Directory naadloze eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2018 maart hebben we deze 15 nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), assistent van FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enter prise server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM voor Azure-resources is algemeen beschikbaar

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Als u Azure AD Privileged Identity Management gebruikt voor Directory-rollen, kunt u nu gebruikmaken van tijd afhankelijke toegang en toewijzings mogelijkheden van PIM voor Azure-resource rollen, zoals abonnementen, resource groepen, Virtual Machines en andere bronnen die door Azure Resource Manager worden ondersteund. Multi-Factor Authentication afdwingen bij het activeren van functies just-in-time en het plannen van activeringen in combi natie met goedgekeurde wijzigings Vensters. Daarnaast voegt deze release verbeteringen toe die niet beschikbaar zijn tijdens de open bare preview, inclusief een bijgewerkte gebruikers interface, werk stromen voor goed keuring en de mogelijkheid om rollen uit te breiden die binnenkort verlopen en verlopen rollen vernieuwen.

Zie [PIM voor Azure-resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) voor meer informatie

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Optionele claims toevoegen aan uw app-tokens (open bare preview)

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Uw Azure AD-app kan nu aangepaste of optionele claims aanvragen in JWTs-of SAML-tokens.  Dit zijn claims over de gebruiker of Tenant die niet standaard zijn opgenomen in het token, vanwege grootte-of toepas baarheids beperkingen.  Dit is momenteel beschikbaar als open bare Preview voor Azure AD-apps op de eind punten v 1.0 en v 2.0.  Raadpleeg de documentatie voor informatie over welke claims kunnen worden toegevoegd en hoe u het toepassings manifest kunt bewerken om ze aan te vragen.

Zie [optionele claims in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)voor meer informatie.

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD ondersteunt PKCE voor meer beveiligde OAuth-stromen

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD-documenten zijn bijgewerkt met ondersteuning voor PKCE, waarmee veiliger communicatie mogelijk is tijdens de OAuth 2,0-autorisatie code subsidie stroom.  Zowel S256 als code_challenges met lees bare tekst worden ondersteund op de eind punten v 1.0 en v 2.0.

Zie [een autorisatie code aanvragen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)voor meer informatie.

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Ondersteuning voor het inrichten van alle gebruikers kenmerk waarden die beschikbaar zijn in de workday Get_Workers-API

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

De open bare preview van de inkomende inrichting van de werkdag naar Active Directory en Azure AD ondersteunt nu de mogelijkheid om alle kenmerk waarden die beschikbaar zijn in de werk dagen Get_Workers API te extra heren en in te richten. Dit voegt ondersteuning toe voor honderden extra standaard-en aangepaste kenmerken dan die die worden geleverd met de eerste versie van de inkomende inrichtings connector van de werkdag.

Zie [de lijst met gebruikers kenmerken van workday aanpassen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes) voor meer informatie.

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Groepslid maatschap wijzigen van dynamisch in statisch en omgekeerd

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

Het is mogelijk om te wijzigen hoe het lidmaatschap wordt beheerd in een groep. Dit is handig als u dezelfde groeps naam en ID in het systeem wilt behouden, zodat eventuele bestaande verwijzingen naar de groep nog geldig zijn. Als u een nieuwe groep maakt, moeten deze verwijzingen worden bijgewerkt.
Het Azure AD-beheer centrum is bijgewerkt voor ondersteuning van deze functionaliteit. Klanten kunnen nu bestaande groepen van een dynamisch lidmaatschap omzetten in een toegewezen lidmaatschap en vice versa. De bestaande Power shell-cmdlets zijn ook nog steeds beschikbaar.

Zie voor meer informatie [dynamische lidmaatschaps regels voor groepen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Verbeterd gedrag bij afmelden met naadloze SSO

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Voorheen, zelfs als gebruikers zich expliciet afmelden bij een toepassing die is beveiligd door Azure AD, worden ze automatisch opnieuw aangemeld met naadloze SSO als ze opnieuw proberen een Azure AD-toepassing te openen binnen hun Corpnet vanaf hun apparaten die lid zijn van hun domein. Bij deze wijziging wordt afmelden ondersteund.  Hiermee kunnen gebruikers hetzelfde of een ander Azure AD-account kiezen om zich opnieuw aan te melden met, in plaats van dat ze automatisch worden aangemeld met naadloze SSO.

Zie [Azure Active Directory naadloze eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) voor meer informatie

---

### <a name="application-proxy-connector-version-154020-released"></a>Versie van connector voor toepassings proxy 1.5.402.0 uitgebracht

**Type:** Gewijzigde functie **service categorie:** app-proxy **product mogelijkheid:** identiteits beveiliging & beveiliging

Deze connector versie wordt geleidelijk geïmplementeerd tot en met november. Deze nieuwe connector versie bevat de volgende wijzigingen:

- De connector stelt nu cookies op domein niveau in in plaats van subdomeinniveau. Dit zorgt voor een soepelere SSO-ervaring en vermijdt dubbele verificatie prompts.
- Ondersteuning voor gesegmenteerde coderings aanvragen
- Verbeterde status controle van connectors
- Verschillende oplossingen voor oplossingen en stabiliteits verbeteringen

Zie [Wat is Azure AD-toepassingsproxy-connectors](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)? voor meer informatie.

---

## <a name="february-2018"></a>Februari 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Verbeterde navigatie voor het beheren van gebruikers en groepen

**Type:** Planning voor **service categorie wijzigen:** Directory Management **product Capability:** Directory

De navigatie-ervaring voor het beheren van gebruikers en groepen is gestroomlijnd. U kunt nu rechtstreeks navigeren vanuit het Directory-overzicht, direct naar de lijst met alle gebruikers, met eenvoudige toegang tot de lijst met verwijderde gebruikers. U kunt ook vanuit het Directory-overzicht rechtstreeks naar de lijst met alle groepen navigeren, met een eenvoudigere toegang tot de instellingen voor groeps beheer. Daarnaast kunt u op de pagina overzicht van mappen zoeken naar een gebruiker, groep, bedrijfs toepassing of app-registratie.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Beschik baarheid van aanmeldingen en controle rapporten in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet)

**Type:** Nieuwe functie **service categorie:** Azure stack **product capaciteit:** bewaking & rapportage

Azure AD-activiteiten logboek rapporten zijn nu beschikbaar in Microsoft Azure beheerd door 21Vianet-exemplaren (Azure China 21Vianet). De volgende logboeken zijn opgenomen:

- **Activiteiten logboeken voor aanmeldingen**  : bevat alle aanmeld logboeken die zijn gekoppeld aan uw Tenant.

- **Audit logboeken voor selfservice wacht woorden** : bevat alle SSPR-controle Logboeken.

- **Controle logboeken voor Directory beheer** : bevat alle controle logboeken met betrekking tot Directory beheer, zoals gebruikers beheer, app-beheer en anderen.

Met deze logboeken kunt u inzicht krijgen in de manier waarop uw omgeving bezig is. Met de gegevens kunt u het volgende doen:

- Bepaal hoe uw apps en services door uw gebruikers worden gebruikt.

- Los problemen op om te voor komen dat uw gebruikers hun werk doen.

Zie [Azure Active Directory Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)(Engelstalig) voor meer informatie over het gebruik van deze rapporten.

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>De rol Report Reader (niet-beheerdersrol) gebruiken om Azure AD-activiteiten rapporten weer te geven

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Als onderdeel van klanten om niet-beheerders rollen in te scha kelen om toegang te krijgen tot Azure AD-activiteiten logboeken, hebben we de mogelijkheid ingeschakeld voor gebruikers die de rol Report Reader hebben om toegang te krijgen tot aanmeldingen en controle activiteiten binnen de Azure Portal, evenals het gebruik van de Microsoft Graph-API.

Zie [Azure Active Directory Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)(Engelstalig) voor meer informatie over het gebruik van deze rapporten.

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Aanvraag voor werk nemers beschikbaar als gebruikers kenmerk en gebruikers-id

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

U kunt werk **nemers** configureren als de gebruikers-id en het gebruikers kenmerk voor leden gebruikers en B2B-gasten in op SAML gebaseerde aanmeldings toepassingen vanuit de gebruikers interface van de bedrijfs toepassing.

Zie voor meer informatie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Vereenvoudigd toepassings beheer met behulp van joker tekens in azure AD-toepassingsproxy

**Type:** Nieuwe functie **service categorie:** app proxy **product mogelijkheid:** gebruikers verificatie

We bieden nu ondersteuning voor het publiceren van toepassingen met behulp van joker tekens om de toepassings implementatie eenvoudiger te maken en uw administratieve overhead te verlagen. Als u een Joker toepassing wilt publiceren, kunt u de standaard publicatie stroom van de toepassing volgen, maar een Joker teken gebruiken in de interne en externe Url's.

Zie voor meer informatie [joker tekens toepassingen in de Azure Active Directory toepassings proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nieuwe cmdlets voor de ondersteuning van de configuratie van toepassings proxy

**Type:** Nieuwe functie **service categorie:** app proxy **product mogelijkheid:** platform

De nieuwste versie van de AzureAD Power shell preview-module bevat nieuwe cmdlets waarmee klanten toepassings proxy toepassingen kunnen configureren met behulp van Power shell.

De nieuwe cmdlets zijn:

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
- Get-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nieuwe cmdlets om de configuratie van groepen te ondersteunen

**Type:** Nieuwe functie **service categorie:** app proxy **product mogelijkheid:** platform

De meest recente versie van de AzureAD Power shell-module bevat cmdlets voor het beheren van groepen in azure AD. Deze cmdlets zijn eerder beschikbaar in de AzureADPreview-module en zijn nu toegevoegd aan de AzureAD-module

De groeps-cmdlets die nu worden uitgebracht voor algemene Beschik baarheid zijn:

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

**Type:** Nieuwe functie **service categorie:** AD Sync **product capaciteit:** platform

Azure AD Connect is het voorkeurs programma voor het synchroniseren van gegevens tussen Azure AD en on-premises gegevens bronnen, waaronder Windows Server Active Directory en LDAP.

>[!Important]
>Deze build introduceert wijzigingen in schema's en synchronisatie regels. De Azure AD Connect-synchronisatie service activeert een volledige import-en volledige synchronisatie stappen na een upgrade. Zie voor meer informatie over hoe u dit gedrag kunt wijzigen de [volledige synchronisatie uitstellen na de upgrade](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Deze release bevat de volgende updates en wijzigingen:

**Opgeloste problemen**

- Corrigeer het tijd venster op de achtergrond taken voor de pagina partitie filtering wanneer u overschakelt naar de volgende pagina.

- Er is een fout opgelost die de toegangs fout veroorzaakte tijdens de aangepaste ConfigDB-actie.

- Er is een fout opgelost bij het herstellen van de time-out van de SQL-verbinding.

- Er is een fout opgelost waarbij certificaten met SAN-joker tekens niet voldoen aan de vereisten controle.

- Er is een fout opgelost die ervoor zorgt dat miiserver.exe vastloopt tijdens het exporteren van de AAD-connector.

- Er is een fout opgelost waarbij een onjuist wacht woord wordt geregistreerd op de domein controller tijdens het uitvoeren van de AAD Connect-wizard om de configuratie te wijzigen

**Nieuwe functies en verbeteringen**

- Applicatie-telemetrie: beheerders kunnen deze klasse van gegevens in-of uitschakelen.

- Status gegevens van Azure AD-beheerders moeten de status Portal bezoeken om hun status instellingen te beheren. Zodra het service beleid is gewijzigd, worden deze door de agents gelezen en afgedwongen.

- De configuratie acties voor het terugschrijven van apparaten en een voortgangs balk voor het initialiseren van pagina's zijn toegevoegd.

- Verbeterde algemene diagnostische gegevens met HTML-rapport en volledige gegevensverzameling in een ZIP-text-of HTML-rapport.

- Verbeterde betrouw baarheid van automatische upgrade en extra telemetrie toegevoegd om te zorgen dat de status van de server kan worden bepaald.

- Beperk machtigingen die beschikbaar zijn voor bevoegde accounts op het AD Connector-account. Voor nieuwe installaties beperkt de wizard de machtigingen die privileged accounts hebben op het MSOL-account nadat het MSOL-account is gemaakt. De wijzigingen zijn van invloed op snelle installaties en aangepaste installaties met het automatisch maken van een account.

- Het installatie programma is gewijzigd zodat er geen SA-bevoegdheid vereist is bij een schone installatie van AADConnect.

- Nieuw hulp programma voor het oplossen van synchronisatie problemen voor een specifiek object. Op dit moment controleert het hulp programma op de volgende zaken:

    - De UserPrincipalName van het gesynchroniseerde gebruikers object en het gebruikers account in de Azure AD-Tenant komen niet overeen.

    - Als het object wordt gefilterd op basis van synchronisatie vanwege domein filtering

    - Als het object wordt gefilterd op basis van synchronisatie vanwege het filteren van organisatie-eenheid (OE)

- Nieuw hulp programma voor het synchroniseren van de huidige wacht woord-hash die is opgeslagen in het on-premises Active Directory voor een specifiek gebruikers account. Voor het hulp programma is geen wachtwoord wijziging vereist.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Toepassingen die Intune-app-beveiliging beleid ondersteunen dat is toegevoegd voor gebruik met voorwaardelijke toegang op basis van Azure AD-toepassing

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Er zijn meer toepassingen toegevoegd die ondersteuning bieden voor voorwaardelijke toegang op basis van toepassingen. Nu kunt u toegang krijgen tot Office 365 en andere met Azure AD verbonden Cloud-apps met behulp van deze goedgekeurde client-apps.

De volgende toepassingen worden toegevoegd aan het einde van februari:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Zie voor meer informatie:

- [Vereiste voor client-app vereist](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Gebruiksvoorwaarden update voor de mobiele ervaring

**Type:** Gewijzigde functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Wanneer de gebruiks voorwaarden worden weer gegeven, kunt u nu klikken op **problemen met weer gave? Klik hier**. Als u op deze koppeling klikt, wordt de gebruiksrecht overeenkomst voor uw apparaat geopend. Ongeacht de teken grootte in het document of de scherm grootte van het apparaat, kunt u inzoomen en het document naar behoefte lezen.

---

## <a name="january-2018"></a>Januari 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In januari 2018 zijn de volgende nieuwe apps met ondersteuning voor Federatie toegevoegd in de app-galerie:

[IBM-Openpaginas](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust privacy management-software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory en [betrouw baarheid netvoor delen](https://go.microsoft.com/fwlink/?linkid=864701).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="sign-in-with-additional-risk-detected"></a>Meld u aan met een extra risico gedetecteerd

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Het inzicht dat u krijgt bij een gedetecteerde risico detectie is gekoppeld aan uw Azure AD-abonnement. Met de Azure AD Premium P2-editie krijgt u de meest gedetailleerde informatie over alle onderliggende detecties.

Met de Azure AD Premium P1 Edition worden detecties die niet onder uw licentie vallen, weer gegeven als het aanmelden van de risico detectie met een extra risico gedetecteerd.

Zie [Azure Active Directory-risico detectie](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)voor meer informatie.

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-toepassingen verbergen via de toegangs Vensters van de eind gebruiker

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

U kunt nu beter beheren hoe Office 365-toepassingen worden weer gegeven op de toegangs Vensters van uw gebruikers via een nieuwe gebruikers instelling. Deze optie is handig voor het verminderen van het aantal apps op de toegangs Vensters van een gebruiker als u alleen Office-apps in de Office-Portal wilt weer geven. De instelling bevindt zich in de **gebruikers instellingen** en is voorzien van een label, **gebruikers kunnen alleen Office 365-apps zien in de Office 365-Portal**.

Zie [een toepassing verbergen van gebruikers ervaring in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)voor meer informatie.

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Naadloos aanmelden bij apps die rechtstreeks zijn ingeschakeld voor wacht woord-SSO vanuit de URL van de app

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

De browser extensie van mijn apps is nu beschikbaar via een handig hulp programma waarmee u de mogelijkheid voor eenmalige aanmelding van mijn apps kunt gebruiken als snelkoppeling in uw browser. Na de installatie ziet de gebruiker in hun browser een wafel-pictogram dat snelle toegang biedt tot apps. Gebruikers kunnen nu profiteren van:

- De mogelijkheid om zich rechtstreeks aan te melden bij op wacht woord gebaseerde apps vanaf de aanmeldings pagina van de app
- Een app starten met de functie voor snel zoeken
- Snelkoppelingen naar recent gebruikte apps uit de uitbrei ding
- De uitbrei ding is beschikbaar voor micro soft Edge, Chrome en Firefox.

Zie voor meer informatie [mijn apps beveiligde aanmeldings extensie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>De Azure AD-beheer ervaring in Klassieke Azure-portal is buiten gebruik gesteld

**Type:** Afgeschafte **service categorie:** Azure AD- **product mogelijkheid:** map

Vanaf 8 januari 2018 is de Azure AD-beheer ervaring in de klassieke Azure-Portal buiten gebruik gesteld. Dit vond plaats in combi natie met de buiten gebruiks telling van de klassieke Azure-portal zelf. In de toekomst moet u het [Azure AD-beheer centrum](https://aad.portal.azure.com) gebruiken voor al uw op portals gebaseerd beheer van Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>De Phone factor-webportal is buiten gebruik gesteld

**Type:** Afgeschafte **service categorie:** Azure AD- **product mogelijkheid:** map

Vanaf 8 januari 2018 is de Phone factor-webportal buiten gebruik gesteld. Deze portal is gebruikt voor het beheer van MFA-server, maar deze functies zijn verplaatst naar de Azure Portal op portal.azure.com.

De MFA-configuratie bevindt zich op: **Azure Active Directory \> MFA-server**

---

### <a name="deprecate-azure-ad-reports"></a>Azure AD-rapporten afschaffen

**Type:** Afgeschafte **service categorie:** rapportage van **product mogelijkheden:** Identity Lifecycle Management


Met de algemene Beschik baarheid van de nieuwe Azure Active Directory-beheer console en nieuwe Api's die nu beschikbaar zijn voor activiteiten-en beveiligings rapporten, zijn de rapport-Api's onder "/Reports" eind punt buiten 31 december 2017.

**Wat is er beschikbaar?**

Als onderdeel van de overgang naar de nieuwe beheer console hebben we twee nieuwe Api's beschikbaar gesteld voor het ophalen van Azure AD-activiteiten Logboeken. De nieuwe set Api's biedt uitgebreide filter-en sorteer functies, naast het bieden van uitgebreide controle-en aanmeldings activiteiten. De gegevens die eerder via de beveiligings rapporten beschikbaar zijn, kunnen nu worden geopend via de API voor risico detectie van identiteits beveiliging in Microsoft Graph.

Zie voor meer informatie:

- [Aan de slag met de API voor Azure Active Directory rapportage](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Gebruiksvoorwaarden in het toegangs venster

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

U kunt nu naar het toegangs venster gaan en de gebruiks voorwaarden bekijken die u eerder hebt geaccepteerd.

Volg deze stappen:

1. Ga naar de [MyApps-Portal](https://myapps.microsoft.com)en meld u aan.

2. Selecteer uw naam in de rechter bovenhoek en selecteer vervolgens **profiel** in de lijst.

3. Selecteer in uw **profiel** **gebruiks voorwaarden bekijken**.

4. Nu kunt u de gebruiks voorwaarden bekijken die u hebt geaccepteerd.

Zie de [functie Azure AD-gebruiks voorwaarden (preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="new-azure-ad-sign-in-experience"></a>Nieuwe Azure AD-aanmeldings ervaring

**Type:** Nieuwe functie **service categorie:** Azure AD- **product mogelijkheid:** gebruikers verificatie

De UIs van Azure AD en het Microsoft-account-identiteits systeem zijn zodanig ontworpen dat ze een consistent uiterlijk hebben. Daarnaast verzamelt de aanmeldings pagina van Azure AD de gebruikers naam eerst, gevolgd door de referentie op een tweede scherm.

Zie voor meer informatie [de nieuwe Azure AD-aanmeldings ervaring is nu beschikbaar als open bare preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Minder aanmeldings prompts: een nieuwe ervaring ' aangemeld blijven ' voor aanmelding bij Azure AD

**Type:** Nieuwe functie **service categorie:** Azure AD- **product mogelijkheid:** gebruikers verificatie

Het selectie vakje **aangemeld blijven** op de aanmeldings pagina van Azure AD is vervangen door een nieuwe prompt die wordt weer gegeven nadat u zich hebt geverifieerd.

Als u **Ja** op deze vraag reageert, geeft de service u een permanent vernieuwings token. Dit gedrag is hetzelfde als wanneer u het selectie vakje **aangemeld blijven** in de oude ervaring hebt ingeschakeld. Voor federatieve tenants wordt deze prompt weer gegeven nadat u zich met de federatieve service hebt geverifieerd.

Zie voor meer informatie [minder aanmeldings prompts: de nieuwe ervaring ' aangemeld blijven ' voor Azure AD is in Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Configuratie toevoegen om te vereisen dat de gebruiks voorwaarden worden uitgevouwen voordat ze worden geaccepteerd

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Gebruikers moeten de gebruiks voorwaarden uitvouwen voordat ze de voor waarden accepteren.

Selecteer aan **of** **uit** om gebruikers te verplichten de gebruiks voorwaarden uit te breiden. Met de instelling **on** moeten gebruikers de gebruiks voorwaarden weer geven voordat ze deze kunnen accepteren.

Zie de [functie Azure AD-gebruiks voorwaarden (preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Scoped activering voor in aanmerking komende roltoewijzingen

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

U kunt scoped activering gebruiken om in aanmerking komende Azure-resource roltoewijzingen met minder autonomie te activeren dan de oorspronkelijke standaard waarden voor de toewijzing. Een voor beeld is als u wordt toegewezen als de eigenaar van een abonnement in uw Tenant. Als u het bereik hebt geactiveerd, kunt u de rol eigenaar activeren voor Maxi maal vijf resources die zich in het abonnement bevinden (zoals resource groepen en virtuele machines). Het bereik van uw activering vermindert mogelijk de mogelijkheid om ongewenste wijzigingen in essentiële Azure-resources uit te voeren.

Zie [Wat is Azure AD privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)voor meer informatie.

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nieuwe federatieve apps in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In december 2017 hebben we deze nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital Store](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CYBSAFE, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image Works](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [Mobile Iron Azure AD Integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO voor bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO voor bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Goedkeurings werk stromen voor Azure AD-adreslijst rollen

**Type:** Gewijzigde functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

De goedkeurings werk stroom voor Azure AD Directory-functies is algemeen beschikbaar.

Met een goedkeurings werk stroom kunnen beheerders met verhoogde bevoegdheid rollen die in aanmerking komen, vereisen om de activering van rollen aan te vragen voordat ze de geprivilegieerde rol kunnen gebruiken. Meerdere gebruikers en groepen kunnen gedelegeerde goedkeurings verantwoordelijkheden zijn. In aanmerking komende leden ontvangen meldingen wanneer goed keuring is voltooid en de bijbehorende rol actief is.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Pass-Through-verificatie: ondersteuning voor Skype voor bedrijven

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Pass-Through-verificatie ondersteunt nu gebruikers aanmeldingen bij Skype voor bedrijven-client toepassingen die ondersteuning bieden voor moderne verificatie, waaronder online-en hybride topologieën.

Zie voor meer informatie [Skype voor bedrijven-topologieën die worden ondersteund door moderne verificatie](https://technet.microsoft.com/library/mt803262.aspx).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Updates voor Azure AD Privileged Identity Management voor Azure RBAC (preview-versie)

**Type:** Gewijzigde functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Met de open bare preview-vernieuwing van Azure AD Privileged Identity Management (PIM) voor op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u nu het volgende doen:

* Gebruik gewoon voldoende beheer.
* Goed keuring vereisen om resource rollen te activeren.
* Plan een toekomstige activering van een rol die goed keuring vereist voor zowel Azure AD-als Azure-rollen.

Zie [privileged Identity Management voor Azure-resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)voor meer informatie.

---

## <a name="november-2017"></a>November 2017

### <a name="access-control-service-retirement"></a>Buiten gebruik stellen Access Control

**Type:** Planning voor wijzigings **service categorie:** Access Control-service **Product mogelijkheid:** Access Control service

Azure Active Directory Access Control (ook wel bekend als de Access Control-service), wordt na een eind 2018 afgetrokken. Meer informatie over een gedetailleerd schema en migratie richtlijnen op hoog niveau wordt in de komende weken weer gegeven. U kunt opmerkingen op deze pagina achterlaten met vragen over de Access Control-service en een teamlid beantwoordt deze.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Browser toegang tot de Intune Managed Browser beperken

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

U kunt browser toegang tot Office 365 en andere met Azure AD verbonden Cloud-apps beperken door de Intune Managed Browser als goedgekeurde app te gebruiken.

U kunt nu de volgende voor waarde configureren voor voorwaardelijke toegang op basis van een toepassing:

**Client-apps:** Browser

**Wat is het effect van de wijziging?**

De toegang is momenteel geblokkeerd wanneer u deze voor waarde gebruikt. Wanneer de preview beschikbaar is, is het gebruik van de beheerde browser toepassing vereist voor alle toegang.

Zoek deze mogelijkheid en meer informatie in aanstaande blogs en release opmerkingen.

Zie [voorwaardelijke toegang in azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)voor meer informatie.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

De volgende apps zijn te vinden op de lijst met [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Zie voor meer informatie:

- [Vereiste voor client-app vereist](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Ondersteuning voor voor waarden voor meerdere talen

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Beheerders kunnen nu nieuwe gebruiks voorwaarden maken die meerdere PDF-documenten bevatten. U kunt deze PDF-documenten labelen met een bijbehorende taal. Gebruikers worden de PDF weer gegeven met de overeenkomende taal op basis van hun voor keuren. Als er geen overeenkomst is, wordt de standaard taal weer gegeven.

---

### <a name="real-time-password-writeback-client-status"></a>Status van real-time-client voor terugschrijven van wacht woorden

**Type:** Nieuwe functie **service categorie:** de mogelijkheid van selfservice voor het opnieuw instellen van wacht woorden **:** gebruikers verificatie

U kunt nu de status controleren van uw on-premises client voor het terugschrijven van wacht woorden. Deze optie is beschikbaar in de sectie **on-premises integratie** van de pagina [wacht woord opnieuw instellen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) .

Als er problemen zijn met uw verbinding met uw on-premises terugschrijf client, wordt een fout bericht weer gegeven waarin u het volgende kunt doen:

- Informatie over waarom u geen verbinding kunt maken met uw on-premises terugschrijf client.
- Een koppeling naar documentatie die u helpt bij het oplossen van het probleem.

Zie [on-premises integratie](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)voor meer informatie.

---

### <a name="azure-ad-app-based-conditional-access"></a>Voorwaardelijke toegang op basis van Azure AD-app

**Type:** Nieuwe functie **service categorie:** Azure AD- **product functionaliteit:** identiteits beveiliging en-beveiliging

U kunt nu de toegang tot Office 365 en andere met Azure AD verbonden Cloud-apps beperken tot [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) die ondersteuning bieden voor het intune-beveiligings beleid voor apps met behulp van [voorwaardelijke toegang op basis van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Het beveiligings beleid voor apps in intune wordt gebruikt voor het configureren en beveiligen van Bedrijfs gegevens op deze client toepassingen.

Door [app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) te combi neren met op [apparaten gebaseerd](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) beleid voor voorwaardelijke toegang, hebt u de flexibiliteit om gegevens te beveiligen voor persoonlijke en Bedrijfs apparaten.

De volgende voor waarden en besturings elementen zijn nu beschikbaar voor gebruik met voorwaardelijke toegang op basis van apps:

**Ondersteunde platform voorwaarde**

- iOS
- Android

**Voor waarde voor client-apps**

- Mobiele apps en desktop-clients

**Toegangsbeheer**

- Goedgekeurde client-apps vereisen

Zie [voorwaardelijke toegang op basis van apps voor Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)voor meer informatie.

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-apparaten beheren in de Azure Portal

**Type:** Nieuwe functie **service categorie:** apparaat registratie en beheer **product mogelijkheid:** identiteits beveiliging en-beveiliging

U kunt nu al uw apparaten vinden die zijn verbonden met Azure AD en de apparaat-gerelateerde activiteiten op één plek. Er is een nieuwe beheer ervaring voor het beheren van al uw apparaat-id's en instellingen in de Azure Portal. In deze release kunt u het volgende doen:

- Bekijk alle apparaten die beschikbaar zijn voor voorwaardelijke toegang in azure AD.
- Eigenschappen weer geven, waaronder uw hybride apparaten die deel uitmaken van Azure AD.
- Zoek BitLocker-sleutels voor uw apparaten die deel uitmaken van Azure AD, beheer uw apparaat met intune en meer.
- Instellingen voor Azure AD-apparaten beheren.

Zie [apparaten beheren met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)voor meer informatie.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Ondersteuning voor macOS als een platform voor voorwaardelijke toegang van Azure AD

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

U kunt macOS in uw beleid voor voorwaardelijke toegang van Azure AD nu opnemen (of uitsluiten). Met het toevoegen van macOS aan de ondersteunde platformen kunt u het volgende doen:

- **Registreer en beheer macOS-apparaten met behulp van intune.** Net als bij andere platformen, zoals iOS en Android, is een bedrijfs portal-toepassing beschikbaar voor macOS om Unified-inschrijvingen uit te voeren. U kunt de nieuwe bedrijfs portal-app voor macOS gebruiken om een apparaat in te schrijven bij intune en dit te registreren bij Azure AD.
- **Zorg ervoor dat macOS-apparaten voldoen aan het nalevings beleid van uw organisatie dat is gedefinieerd in intune.** U kunt in intune op het Azure Portal nu nalevings beleid instellen voor macOS-apparaten.
- **Beperk de toegang tot toepassingen in azure AD tot alleen compatibele macOS-apparaten.** Het ontwerp van het beleid voor voorwaardelijke toegang heeft macOS als een afzonderlijke platform optie. Nu kunt u macOS-specifiek beleid voor voorwaardelijke toegang ontwerpen voor de doel toepassing die in Azure is ingesteld.

Zie voor meer informatie:

- [Een apparaatnalevingsbeleid maken voor macOS-apparaten in Intune](https://aka.ms/macoscompliancepolicy)
- [Voorwaardelijke toegang in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Network Policy Server extensie voor Azure Multi-Factor Authentication

**Type:** Nieuwe functie **service categorie:**  multi-factor Authentication- **product mogelijkheid:** gebruikers verificatie

Met de Network Policy Server extensie voor Azure Multi-Factor Authentication worden Cloud Multi-Factor Authentication mogelijkheden toegevoegd aan uw verificatie-infra structuur met behulp van uw bestaande servers. Met de uitbrei ding Network Policy Server kunt u een telefoon gesprek, tekst bericht of verificatie via de telefoon toevoegen aan uw bestaande verificatie stroom. U hoeft geen nieuwe servers te installeren, te configureren en te onderhouden.

Deze extensie is gemaakt voor organisaties die virtuele particuliere netwerk verbindingen willen beveiligen zonder de Azure-Multi-Factor Authentication-server te implementeren. De uitbrei ding Network Policy Server fungeert als een adapter tussen RADIUS-en cloud-gebaseerde Azure-Multi-Factor Authentication om een tweede factor van verificatie te bieden voor federatieve of gesynchroniseerde gebruikers.

Zie [uw bestaande Network Policy Server-infra structuur integreren met Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)voor meer informatie.

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Verwijderde gebruikers herstellen of permanent verwijderen

**Type:** Nieuwe functie **service categorie:** gebruikers beheer **product mogelijkheid:** map

In het Azure AD-beheer centrum kunt u nu het volgende doen:

- Een verwijderde gebruiker herstellen.
- Een gebruiker definitief verwijderen.

**Om het uit te proberen:**

1. Selecteer in het Azure AD-beheer centrum [alle gebruikers](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) in de sectie **beheren** .

2. Selecteer **onlangs verwijderde gebruikers**in de lijst **weer geven** .

3. Selecteer een of meer recent verwijderde gebruikers en herstel deze vervolgens of verwijder ze definitief.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

De volgende apps zijn toegevoegd aan de lijst met [goedgekeurde client-apps](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft Planner
- Azure Information Protection

Zie voor meer informatie:

- [Vereiste voor client-app vereist](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Voorwaardelijke toegang op basis van Azure AD-app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>De besturings elementen ' OR ' gebruiken in een beleid voor voorwaardelijke toegang

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

U kunt nu ' of ' (een van de geselecteerde besturings elementen vereisen) voor besturings elementen voor voorwaardelijke toegang gebruiken. U kunt deze functie gebruiken voor het maken van beleids regels met ' OR ' tussen besturings elementen voor toegang. U kunt deze functie bijvoorbeeld gebruiken om een beleid te maken waarbij een gebruiker zich moet aanmelden met behulp van Multi-Factor Authentication ' of ' moet worden gebruikt voor een compatibel apparaat.

Zie [besturings elementen in voorwaardelijke toegang tot Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)voor meer informatie.

---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregatie van real-time risico detecties

**Type:** Gewijzigde functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging en-beveiliging

In Azure AD Identity Protection worden alle real-time-risico detecties die afkomstig zijn van hetzelfde IP-adres op een bepaalde dag, nu geaggregeerd voor elk type risico detectie. Met deze wijziging wordt het volume aan risico detecties beperkt zonder dat er wijzigingen in de gebruikers beveiliging worden weer gegeven.

De onderliggende realtime detectie werkt telkens wanneer de gebruiker zich aanmeldt. Als u een beveiligings beleid voor aanmeldings Risico's hebt ingesteld op Multi-Factor Authentication of toegang blokkeert, wordt het nog steeds geactiveerd tijdens elke Risk ante aanmelding.

---

## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Azure AD-rapporten afschaffen

**Type:** Planning voor **service categorie wijzigen:** rapportage van **product mogelijkheden:** Identity Lifecycle Management

De Azure Portal biedt u het volgende:

- Een nieuwe Azure AD-beheer console.
- Nieuwe Api's voor activiteiten-en beveiligings rapporten.

Als gevolg van deze nieuwe mogelijkheden werden de rapport-Api's onder het punt/Reports buiten gebruik gesteld op 10 december 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Detectie van automatisch aanmeldings veld

**Type:** Vaste **service categorie:** mijn apps **product mogelijkheid:** eenmalige aanmelding

Azure AD biedt ondersteuning voor het automatisch detecteren van aanmeldings velden voor toepassingen die een HTML-gebruikers naam en-wachtwoord veld weer geven. Deze stappen worden beschreven in [het automatisch vastleggen van aanmeldings velden voor een toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). U kunt deze mogelijkheid vinden door een *niet-galerie* toepassing toe te voegen op de pagina **bedrijfs toepassingen** in de [Azure Portal](https://aad.portal.azure.com). Daarnaast kunt u de modus voor **eenmalige aanmelding** op deze nieuwe toepassing configureren voor **eenmalige aanmelding op basis van wacht woorden**, een web-URL opgeven en de pagina vervolgens opslaan.

Deze functionaliteit is tijdelijk uitgeschakeld vanwege een service probleem. Het probleem is opgelost en de detectie van het automatische aanmeldings veld is weer beschikbaar.

---

### <a name="new-multi-factor-authentication-features"></a>Nieuwe Multi-Factor Authentication functies

**Type:** Nieuwe functie **service categorie:** multi-factor Authentication- **product mogelijkheid:** identiteits beveiliging en-beveiliging

Multi-factor Authentication (MFA) is een essentieel onderdeel van de beveiliging van uw organisatie. De volgende functies zijn toegevoegd om referenties meer adaptief te maken en de ervaring te versoepelen:

- Multi-factor Challenge-resultaten worden rechtstreeks geïntegreerd in het aanmeldings rapport van Azure AD, inclusief programmatische toegang tot MFA-resultaten.
- De configuratie van MFA is dieper geïntegreerd in de Azure AD-configuratie in de Azure Portal.

Met deze open bare preview is MFA-beheer en-rapportage een geïntegreerd onderdeel van de basis configuratie-ervaring van Azure AD. U kunt nu de functionaliteit van de MFA-beheer portal beheren binnen de Azure AD-ervaring.

Zie [referentie voor MFA-rapportage in de Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)voor meer informatie.

---

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

U kunt Azure AD-gebruiks voorwaarden gebruiken om informatie te presen teren, zoals relevante disclaimers voor juridische of nalevings vereisten voor gebruikers.

U kunt de gebruiksrecht overeenkomst van Azure AD gebruiken in de volgende scenario's:

- Algemene Gebruiks voorwaarden voor alle gebruikers in uw organisatie
- Specifieke gebruiks voorwaarden op basis van de kenmerken van een gebruiker (bijvoorbeeld artsen vs. verpleegt of binnenland versus internationale werk nemers, uitgevoerd door dynamische groepen)
- Specifieke gebruiks voorwaarden voor toegang tot zakelijke apps met hoge impact, zoals Sales Force

Zie [gebruiks voorwaarden voor Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)voor meer informatie.

---

### <a name="enhancements-to-privileged-identity-management"></a>Verbeteringen in Privileged Identity Management

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Met Azure AD Privileged Identity Management kunt u de toegang tot Azure-resources (preview) binnen uw organisatie beheren, controleren en bewaken tot:

- Abonnementen
- Resourcegroepen
- Virtuele machines

Alle resources in de Azure Portal die gebruikmaken van de functionaliteit van Azure RBAC, kunnen profiteren van alle functies voor beveiliging en levenscyclus beheer die Azure AD Privileged Identity Management te bieden hebben.

Zie [privileged Identity Management voor Azure-resources](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)voor meer informatie.

---

### <a name="access-reviews"></a>Toegangsbeoordelingen

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** naleving

Organisaties kunnen toegangs beoordelingen (preview) gebruiken om groepslid maatschappen en toegang tot bedrijfs toepassingen efficiënt te beheren:

- U kunt toegang voor gastgebruikers opnieuw certificeren door gebruik te maken van toegangsbeoordelingen van hun toegang tot toepassingen en lidmaatschappen van groepen. Revisoren kunnen efficiënt bepalen of gast toegang is toegestaan op basis van de inzichten van de toegangs Beoordelingen.
- Met toegangsbeoordelingen kunt u de toegang van werknemers voor toepassingen en groepslidmaatschappen opnieuw certificeren.

U kunt de controles van toegangsbeoordelingen verzamelen in programma's die relevant zijn voor uw organisatie, om beoordelingen bij te houden voor naleving of risicogevoelige toepassingen.

Zie [Azure AD Access revisies](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)(Engelstalig) voor meer informatie.

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Toepassingen van derden verbergen vanuit mijn apps en het start programma voor apps van Office 365

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** eenmalige aanmelding

U kunt nu beter apps beheren die worden weer gegeven op de portals van uw gebruikers via een nieuwe eigenschap voor het verbergen van een **app** . U kunt apps verbergen om te helpen in gevallen waarin app-tegels worden weer gegeven voor back-end-services of dubbele tegels en de app-starters van onbelangrijke gebruikers. De wissel knop bevindt zich in de sectie **Eigenschappen** van de app van derden en is **zichtbaar voor de gebruiker?** U kunt een app ook via Power shell verbergen.

Zie [een toepassing van een derde partij verbergen in de gebruikers ervaring van Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)voor meer informatie.


**Wat is er beschikbaar?**

 Als onderdeel van de overgang naar de nieuwe beheer console zijn er twee nieuwe Api's voor het ophalen van Azure AD-activiteiten logboeken beschikbaar. De nieuwe set Api's biedt uitgebreide filter-en sorteer functies, naast het bieden van uitgebreide controle-en aanmeldings activiteiten. De gegevens die eerder via de beveiligings rapporten beschikbaar zijn, kunnen worden geopend via de API voor risico detectie van identiteits beveiliging in Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix voor Identity Manager

**Type:** Gewijzigde functie **service categorie:** Identity Manager- **product mogelijkheid:** Identity Lifecycle Management

Een hotfix-samengevouwen pakket (build 4.4.1642.0) is beschikbaar vanaf 25 september 2017 voor Identity Manager 2016 Service Pack 1. Dit samengevouwen pakket:

- Lost problemen op en voegt verbeteringen toe.
- Is een cumulatieve update waarbij alle Identity Manager 2016 Service Pack 1-updates worden vervangen om 4.4.1459.0 te bouwen voor Identity Manager 2016.
- Hiervoor moet u Identity Manager 2016 build 4.4.1302.0 hebben.

Zie hotfixcombinatiepakket [(build 4.4.1642.0) is beschikbaar voor Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562)voor meer informatie.

---
