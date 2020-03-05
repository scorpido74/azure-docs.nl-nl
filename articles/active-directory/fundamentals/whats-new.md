---
title: Wat is nieuw? Releaseopmerkingen - Azure Active Directory | Microsoft Docs
description: Ontdek wat er nieuw in Azure Active Directory, zoals de meest recente release-opmerkingen worden bekende problemen, oplossingen voor problemen, afgeschafte functies en toekomstige wijzigingen.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274200"
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

## <a name="february-2020"></a>Februari 2020
 
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

[IamIP patent platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 SSO voor Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda e-mail beveiligings service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [in het geval van een crisis Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), een [BIC-Cloud ontwerp](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [bijen teelt Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn veer-evaluaties](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada opdracht](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB navigatie](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [nieuwe Relic (beperkte versie)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [thulium](https://admin.thulium.com/login/instance), [ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [sjabloon Kiezer voor teams](https://links.officeatwork.com/templatechooser-download-teams), [bijen](https://www.beesy.me/index.php/site/login), Health- [ondersteunings systeem](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [Mural](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex vdr](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink voor docenten en scholen](https://www.thinglink.com/), [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Bureau-artikel Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [Team Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-februari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud door Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
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
 
De nieuwe gebruikers ervaring voor de eind gebruiker van mijn account werkt de URL bij naar https://myaccount.microsoft.com in de volgende maand. Meer informatie over de ervaring en alle accounts selfservice mogelijkheden voor eind gebruikers vindt u in de [Help van mijn account Portal](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

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
 
Werk ruimten, de filters die beheerders kunnen configureren om hun gebruikers-apps te organiseren, worden nu verzamelingen genoemd. Meer informatie over hoe u deze kunt configureren in [verzamelingen maken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

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
- [Zscaler persoonlijke toegang](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-januari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In januari 2020 hebben we deze 33 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [snelle Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [TERRAFORM Enter prise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Cloud backup voor Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)upsneld, [LEAVEBOT](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [DOTCOM-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [gehoste MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu-eigenschappen beheer platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwerk onderneming](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB voor micro soft teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient-uitvoeren Manager-software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [coreview](https://portal.coreview.com/), [squelch Cloud Office365-connector](https://laxmi.squelch.io/login), PingFlow- [verificatie](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtuele assistent](https://akari.io/akari-virtual-assistant/)

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

Als onderdeel van een beveiligd standaard model voor cookies wordt de Chrome 80-browser gewijzigd hoe cookies worden behandeld zonder het kenmerk `SameSite`. Elke cookie waarbij het kenmerk `SameSite` niet wordt opgegeven, wordt behandeld alsof het is ingesteld op `SameSite=Lax`, wat betekent dat Chrome bepaalde scenario's voor het delen van cookies met meerdere domeinen blokkeert waarvan uw app afhankelijk is. Als u het oudere Chrome-gedrag wilt behouden, kunt u het kenmerk `SameSite=None` gebruiken en een extra `Secure` kenmerk toevoegen, zodat cookies voor meerdere locaties alleen toegankelijk zijn via HTTPS-verbindingen. Chrome is gepland om deze wijziging te volt ooien op 4 februari 2020.

We raden aan dat alle ontwikkel aars hun apps testen met behulp van deze richt lijnen:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- Stel de standaard waarde voor het kenmerk **SameSite** in op **geen**.

- Voeg een extra `SameSite` kenmerk van **veilig**toe.

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

[](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [blauw toegang voor leden (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal single sign on (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [FOKO Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets idee & innovatie beheer](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope gebruikers verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [Jisc student gevoel registratie](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

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

**Type:** Plan voor wijziging  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Als reactie op de feedback van ontwikkel aars kunnen Azure AD Premium P2-abonnees nu complexe query's uitvoeren op Azure AD Identity Protection risico detectie gegevens door gebruik te maken van de nieuwe riskDetection-API voor Microsoft Graph. De bestaande [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API bèta versie retourneert geen gegevens over **10 januari 2020**. Als uw organisatie gebruikmaakt van de identityRiskEvent-API, moet u overstappen naar de nieuwe riskDetection-API.

Zie de [referentie documentatie voor API voor risico detectie](https://aka.ms/RiskDetectionsAPI)voor meer informatie over de nieuwe RISKDETECTION-API.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor toepassings proxy voor het kenmerk SameSite en Chrome 80

**Type:** Plan voor wijziging  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Een paar weken voorafgaand aan de Chrome 80-browser release, willen we bijwerken hoe cookies van toepassings proxy het kenmerk **SameSite** behandelen. Met de release van Chrome 80 wordt elke cookie waarvoor het kenmerk **SameSite** niet wordt opgegeven, behandeld alsof het is ingesteld op `SameSite=Lax`.

Om te voor komen dat mogelijk negatieve gevolgen zijn door deze wijziging, worden de toegangs-en sessie cookies van de toepassings proxy bijgewerkt door:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- De standaard waarde voor het kenmerk **SameSite** in te stellen op **geen**.

    >[!NOTE]
    > Application proxy-toegangs cookies zijn altijd uitsluitend via beveiligde kanalen verzonden. Deze wijzigingen zijn alleen van toepassing op sessie cookies.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)voor meer informatie over de cookie-instellingen van de toepassings proxy.

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>App-registraties (verouderd) en geconvergeerde app-beheer vanuit de portal voor toepassings registratie (apps.dev.microsoft.com) is niet langer beschikbaar

**Type:** Plan voor wijziging  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Ontwikkelaars ervaring

In de nabije toekomst kunnen gebruikers met Azure AD-accounts geen geconvergeerde toepassingen meer registreren en beheren met behulp van de portal voor toepassings registratie (apps.dev.microsoft.com) of de toepassingen registreren en beheren in de App-registraties (verouderd) ervaring in de Azure Portal.

Raadpleeg voor meer informatie over de nieuwe App-registraties-ervaring de [app-registraties in de hand leiding voor Azure Portal training](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers hoeven zich niet langer opnieuw te registreren tijdens de migratie van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

**Type:** Vaste  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Er is een bekend probleem opgelost waarbij gebruikers zich opnieuw moeten registreren als ze zijn uitgeschakeld voor per gebruiker-Multi-Factor Authentication (MFA) en vervolgens via een beleid voor voorwaardelijke toegang zijn ingeschakeld voor MFA.

Als u wilt dat gebruikers zich opnieuw moeten registreren, kunt u de optie voor het **opnieuw registreren van MFA** selecteren uit de verificatie methoden van de gebruiker in de Azure AD-Portal. Voor meer informatie over het migreren van gebruikers van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang, Zie [gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nieuwe mogelijkheden voor het transformeren en verzenden van claims in uw SAML-token

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We hebben extra mogelijkheden toegevoegd waarmee u claims kunt aanpassen en verzenden in uw SAML-token. Deze nieuwe mogelijkheden zijn onder andere:

- Extra functies voor het transformeren van claims, waarmee u de waarde die u in de claim verzendt, kunt wijzigen.

- De mogelijkheid om meerdere trans formaties op één claim toe te passen.

- De mogelijkheid om de claim bron op te geven op basis van het gebruikers type en de groep waartoe de gebruiker behoort.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie over deze nieuwe mogelijkheden, inclusief hoe u deze kunt gebruiken.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Pagina nieuwe mijn aanmeldingen voor eind gebruikers in azure AD

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** & Rapportage controleren

We hebben een nieuwe pagina voor **mijn aanmeldingen** toegevoegd (https://mysignins.microsoft.com) zodat de gebruikers van uw organisatie hun recente aanmeldings geschiedenis kunnen bekijken om te controleren of er ongebruikelijke activiteiten zijn. Met deze nieuwe pagina kunnen uw gebruikers het volgende zien:

- Als iemand probeert hun wacht woord te raden.

- Als een aanvaller zich heeft aangemeld bij hun account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

Zie voor meer informatie de [gebruikers kunnen nu hun aanmeldings geschiedenis controleren op blog van ongebruikelijke activiteiten](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migratie van Azure AD Domain Services (Azure AD DS) van het klassieke naar Azure Resource Manager virtuele netwerken

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services

Voor onze klanten die zijn vastgelopen op klassieke virtuele netwerken, hebben we een geweldig nieuws voor u. U kunt nu een eenmalige migratie uitvoeren van een klassiek virtueel netwerk naar een bestaand virtueel netwerk van Resource Manager. Nadat u bent overgestapt op het virtuele netwerk van Resource Manager, kunt u profiteren van de aanvullende en bijgewerkte functies, zoals een verfijnd wachtwoord beleid, e-mail meldingen en audit Logboeken.

Zie [Preview-Azure AD Domain Services migreren van het klassieke virtuele netwerk naar Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)voor meer informatie.

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates voor de indeling van het Azure AD B2C-pagina contract

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

We hebben een aantal nieuwe wijzigingen geïntroduceerd in versie 1.2.0 van het pagina contract voor Azure AD B2C. In deze bijgewerkte versie kunt u nu de laad volgorde voor uw elementen beheren. deze kan ook helpen bij het stoppen van de Flik kering die optreedt wanneer het opmaak model (CSS) wordt geladen.

Raadpleeg het [logboek voor versie](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)wijzigingen voor een volledige lijst van de wijzigingen die in het pagina-contract zijn aangebracht.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Bijwerken naar de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** Access Control

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de gloed nieuwe mijn apps-ervaring weer geven en openen, met inbegrip van het gebruik van de nieuwe functie werk ruimten, om het voor hen gemakkelijker te maken apps te vinden. De nieuwe functionaliteit voor werk ruimten fungeert als een filter voor de apps waartoe gebruikers van de organisatie al toegang hebben.

Zie [werk ruimten maken in de portal mijn apps (preview-versie)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)voor meer informatie over het implementeren van de nieuwe mijn apps-ervaring en het maken van werk ruimten.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Ondersteuning voor het maandelijks actieve facturerings model op basis van gebruikers (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

Azure AD B2C ondersteunt nu de facturering van maandelijkse actieve gebruikers (MAU). MAU-facturering is gebaseerd op het aantal unieke gebruikers met verificatie activiteiten tijdens een kalender maand. Bestaande klanten kunnen op elk gewenst moment overstappen op deze nieuwe facturerings methode.

Vanaf 1 november 2019 worden alle nieuwe klanten automatisch gefactureerd met behulp van deze methode. Deze facturerings methode biedt klanten de voor delen van kosten en de mogelijkheid om vooruit te plannen.

Zie voor meer informatie [upgraden naar het facturerings model maandelijkse actieve gebruikers](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-oktober 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In oktober 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[In het geval van crisis: Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno traject](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [intact](https://tact.ai/assistant/), [OpusCapita kasbeheer](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contented](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue coördinaat – EU](https://www.hirevue.com/), [HireVue coördinaat-USOnly](https://www.hirevue.com/), [HireVue Coordinate-VS](https://www.hirevue.com/), [WittyParrot Kennis Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mailing!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB navigeert IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking email for intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [gesp roken e-mail voor Office 365 direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care-navigatie systeem](https://ihealthnav.com/account/signin), [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menu-item voor geconsolideerde beveiliging in de Azure AD-Portal

**Type:** Gewijzigde functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

U hebt nu toegang tot alle beschik bare Azure AD-beveiligings functies vanuit het menu met nieuwe **beveiliging** en op de **Zoek** balk in de Azure Portal. Daarnaast biedt de nieuwe **beveiligings** landings pagina, met de naam **beveiliging-aan de slag**, koppelingen naar onze open bare documentatie, beveiligings richtlijnen en implementatie handleidingen.

Het menu nieuwe **beveiliging** bevat:

- Voorwaardelijke toegang
- Identiteitsbeveiliging
- Security Center
- Identiteits veilige Score
- Verificatiemethoden
- MFA
- Risico rapporten-Risk ante gebruikers, Risk ante aanmeldingen, risico detecties
- En nog veel meer...

Zie [beveiliging-aan de slag](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)voor meer informatie.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365-groepen verloop beleid verbeterd met autoverlengen

**Type:** Gewijzigde functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

Het verloop beleid van de Office 365-groepen is uitgebreid met het automatisch vernieuwen van groepen die momenteel door de leden van de groep worden gebruikt. Groepen worden automatisch vernieuwd op basis van gebruikers activiteit in alle Office 365-apps, waaronder Outlook, share point en teams.

Deze verbetering helpt u bij het verminderen van uw groeps meldingen en helpt ervoor te zorgen dat actieve groepen nog steeds beschikbaar zijn. Als u al een actief verloop beleid hebt voor uw Office 365-groepen, hoeft u niets te doen om deze nieuwe functionaliteit in te scha kelen.

Zie [het verloop beleid voor Office 365-groepen configureren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)voor meer informatie.

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Werk ervaring bij het maken van Azure AD Domain Services (Azure AD DS) is bijgewerkt

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services

We hebben Azure AD Domain Services (Azure AD DS) bijgewerkt met een nieuwe en verbeterde ervaring voor het maken van een beheerd domein in slechts drie muis klikken. Daarnaast kunt u nu Azure-AD DS uploaden en implementeren vanuit een sjabloon.

Zie [zelf studie: een Azure Active Directory Domain Services-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)voor meer informatie.

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plannen voor wijziging: afschaffing van de Power BI inhouds pakketten

**Type:** Plan voor wijziging  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & Rapportage controleren

Vanaf 1 oktober 2019 worden alle inhouds pakketten, inclusief het Azure AD Power BI-inhouds pakket, door Power BI af te nemen. Als alternatief voor dit inhouds pakket kunt u Azure AD-werkmappen gebruiken om inzicht te krijgen in uw Azure AD-gerelateerde services. Er zijn extra werkmappen beschikbaar, waaronder werkmappen over beleids regels voor voorwaardelijke toegang in de modus alleen rapport, inzichten op basis van apps en meer.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)voor meer informatie over de werkmappen. Voor meer informatie over de afschaffing van de inhouds pakketten raadpleegt u het blog bericht [Power bi sjabloon apps voor algemene Beschik baarheid aankondigen](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mijn profiel is een nieuwe naam en integratie met de pagina Microsoft Office-account

**Type:** Plan voor wijziging  
**Service categorie:** Mijn profiel/account  
**Product mogelijkheden:** Werking

Vanaf oktober wordt de ervaring mijn profiel van mijn account. Als onderdeel van deze wijziging, wordt **Mijn profiel** gewijzigd in **Mijn account**. Naast de naam wijziging en enkele ontwerp verbeteringen, biedt de bijgewerkte ervaring aanvullende integratie met de pagina Microsoft Office-account. U hebt met name toegang tot Office-installaties en-abonnementen via de pagina **overzichts account** , samen met Office-gerelateerde contact voorkeuren van de pagina **Privacy** .

Zie voor meer informatie over de ervaring mijn profiel (preview) [Mijn profiel (preview-versie) van portal](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Groepen en leden bulksgewijs beheren met CSV-bestanden in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de ervaring voor bulksgewijs groeps beheer in de Azure AD-Portal. U kunt nu een CSV-bestand en de Azure AD-Portal gebruiken om groepen en leden lijsten te beheren, met inbegrip van:

- Leden toevoegen aan of verwijderen uit een groep.

- De lijst met groepen wordt gedownload uit de map.

- De lijst met groeps leden voor een specifieke groep downloaden.

Zie voor meer informatie [bulksgewijs toevoegen leden](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [leden bulksgewijs verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [lijst met leden en items](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)bulksgewijs downloaden en [groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamische toestemming wordt nu ondersteund via een nieuw door de beheerder toestemmings eindpunt

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

We hebben een nieuw beheerders toestemmings eindpunt gemaakt ter ondersteuning van dynamische toestemming. Dit is handig voor apps waarvoor het dynamische toestemming model op het micro soft Identity-platform moet worden gebruikt.

Zie [using the Administrator toestemming endpoint](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)(Engelstalig) voor meer informatie over het gebruik van dit nieuwe eind punt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-september 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In september 2019 hebben we deze 29 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex compliance Office™-eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [concur Traveling en onkosten](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [Arc-faciliteiten](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus team](https://stratus.emea.luware.cloud/login), [brede ideeën](https://wideideas.online/wideideas/), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT client hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath beveiligde browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [Penneo](https://app.penneo.com/), [Hiretual](https://app.testhtm.com/settings/email-integration), [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [gehoste erfgoed online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai-team build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [Discovery voordelen SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-azure-ad-global-reader-role"></a>Nieuwe globale rol van Azure AD-lezer

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control

Vanaf 24 september 2019 beginnen we met het implementeren van een nieuwe Azure Active Directory-rol (AD) met de naam Global Reader. Deze implementatie begint met productie-en mondiale Cloud klanten (GCC), die wereld wijd in oktober wordt voltooid.

De rol van algemene lezer is het alleen-lezen equivalent van de globale beheerder. Gebruikers met deze rol kunnen instellingen en beheer gegevens in Microsoft 365 services lezen, maar kunnen geen beheer acties uitvoeren. We hebben de algemene rol lezer gemaakt om het aantal globale beheerders in uw organisatie te verminderen. Omdat globale beheerders accounts krachtig en kwetsbaar zijn voor aanvallen, raden wij aan dat u minder dan vijf globale beheerders hebt. We raden u aan de rol van globale lezer te gebruiken voor het plannen, controleren of onderzoeken. We raden u ook aan de rol van globale lezer te gebruiken in combi natie met andere beperkte beheerders rollen, zoals Exchange Administrator, om aan de slag te gaan zonder dat hiervoor de rol van globale beheerder is vereist.

De rol van algemene lezer werkt met de nieuwe Microsoft 365 beheer centrum, het Exchange-beheer centrum, teams beheer centrum, Security Center, compliance Center, Azure AD-beheer centrum en het beheer centrum voor Apparaatbeheer.

>[!NOTE]
> Aan het begin van de open bare preview-functie werkt de globale rol van lezer niet met: share point, Privileged Access Management, Klanten-lockbox, gevoeligheids labels, teams levenscyclus, teams rapporteren & aanroep Analytics, teams IP-adres beheer van het apparaat en teams-app Catalogus. Al deze services zijn bedoeld om in de toekomst met de functie te werken.

Zie [machtigingen voor beheerdersrol in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)voor meer informatie.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Toegang tot een on-premises rapport server vanuit uw Power BI-Mobiel-app met behulp van Azure Active Directory-toepassingsproxy

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Dankzij de nieuwe integratie tussen de Power BI mobiele app en Azure AD-toepassingsproxy kunt u zich veilig aanmelden bij de mobiele app van Power BI en de rapporten van uw organisatie weer geven die worden gehost op de on-premises Power BI Report Server.

Zie de [Power bi-site](https://powerbi.microsoft.com/mobile/)voor meer informatie over de Power bi-mobiel-app, inclusief de plek waar u de app kunt downloaden. Zie voor meer informatie over het instellen van de Power BI mobiele app met Azure AD-toepassingsproxy [externe toegang inschakelen voor Power bi-mobiel met azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Er is een nieuwe versie van de Power shell-module AzureADPreview beschikbaar

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Uitvoermap

Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module, waarmee u aangepaste rollen kunt definiëren en toewijzen in azure AD, waaronder:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nieuwe versie van Azure AD Connect

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Uitvoermap

We hebben een bijgewerkte versie van Azure AD Connect uitgebracht voor klanten met automatische upgrades. Deze nieuwe versie bevat verschillende nieuwe functies, verbeteringen en oplossingen voor problemen. Zie [Azure AD Connect: release geschiedenis](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)van de versie voor meer informatie over deze nieuwe versie.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA)-server, versie 8.0.2 is nu beschikbaar

**Type:** Vaste  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Als u een bestaande klant bent die een MFA-server heeft geactiveerd voor 1 juli 2019, kunt u nu de nieuwste versie van de MFA-server (versie 8.0.2) downloaden. In deze nieuwe versie:

- Als er een probleem is opgelost, wordt een e-mail bericht verzonden naar de gebruiker wanneer de Azure AD-synchronisatie een gebruiker van uitgeschakeld wijzigt in ingeschakeld.

- Er is een probleem opgelost waardoor klanten kunnen upgraden, terwijl ze de functionaliteit van Tags blijven gebruiken.

- De land code Kosovo (+ 383) is toegevoegd.

- Eenmalige controle logboek registratie voor bypass is toegevoegd aan MultiFactorAuthSvc. log.

- Verbeterde prestaties voor de Web Service SDK.

- Andere kleine bugs zijn opgelost.

Vanaf 1 juli 2019 heeft micro soft de MFA-server voor nieuwe implementaties gestopt. Nieuwe klanten die multi-factor Authentication vereisen, moeten gebruikmaken van Azure Multi-Factor Authentication op basis van de Cloud. Zie [een Cloud implementatie van Azure multi-factor Authentication plannen](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)voor meer informatie.

---
