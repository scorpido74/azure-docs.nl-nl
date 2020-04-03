---
title: Wat is nieuw? Releasenotes - Azure Active Directory | Microsoft Documenten
description: Lees wat er nieuw is met Azure Active Directory, zoals de nieuwste releasenotes, bekende problemen, bugfixes, afgeschafte functionaliteit en aankomende wijzigingen.
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
ms.date: 04/01/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b960bf63cae8b55ae2f66b6b809ee0d11229a312
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582784"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>Ontvang een melding over wanneer u deze pagina opnieuw moet `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` bezoeken ![voor updates](./media/whats-new/feed-icon-16x16.png) door deze URL te kopiëren en te plakken: in uw FEED-lezerlezer van rss-feed.

Azure AD ontvangt voortdurend verbeteringen. Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt, dus ga er regelmatig opnieuw naar herin. Als u op zoek bent naar items die ouder zijn dan zes maanden, u deze vinden in het [archief voor nieuwe artikelen in Azure Active Directory.](whats-new-archive.md)

---

## <a name="march-2020"></a>Maart 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Onbeheerde Azure Active Directory-accounts in B2B-update voor maart 2021

**Type:** Plan voor verandering  
**Servicecategorie:** B2b  
**Productmogelijkheden:** B2B/B2C
 
**Vanaf 31 maart 2021**ondersteunt Microsoft niet langer het inwisselen van uitnodigingen door onbeheerde Azure Active Directory-accounts en tenants te maken voor B2B-samenwerkingsscenario's. Ter voorbereiding hiervan raden we u aan u aan om u aan te melden voor [een e-mail verificatie van een malige toegangscode.](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gebruikers met de standaardtoegangsrol hebben het bereik om

**Type:** Plan voor verandering  
**Servicecategorie:** App-inrichting  
**Productmogelijkheden:** Beheer van identiteitslevenscyclus
 
In het verleden zijn gebruikers met de standaardtoegangsrol buiten bereik geweest voor inrichten. We hebben feedback gehoord dat klanten willen dat gebruikers met deze rol in het bereik zijn voor inrichting. We werken aan het implementeren van een wijziging, zodat alle nieuwe inrichtingsconfiguraties gebruikers met de standaardtoegangsrol kunnen inrichten. Geleidelijk aan wijzigen we het gedrag voor bestaande inrichtingsconfiguraties om het inrichten van gebruikers met deze rol te ondersteunen. Er is geen actie van de klant vereist. We plaatsen een update van onze [documentatie](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) zodra deze wijziging van kracht is.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samenwerking is beschikbaar in Microsoft Azure dat wordt beheerd door 21Vianet-tenants (Azure China 21Vianet)

**Type:** Plan voor verandering  
**Servicecategorie:** B2b  
**Productmogelijkheden:** B2B/B2C
 
De Azure AD B2B-samenwerkingsmogelijkheden worden beschikbaar gesteld in Microsoft Azure dat wordt beheerd door 21Vianet-tenants (Azure China 21Vianet), waardoor gebruikers in een Azure China 21Vianet-tenant naadloos kunnen samenwerken met gebruikers in andere Azure China 21Vianet-tenants. [Meer informatie over Azure AD B2B-samenwerking](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration uitnodiging e-mail herontwerp

**Type:** Plan voor verandering  
**Servicecategorie:** B2b  
**Productmogelijkheden:** B2B/B2C
 
De [e-mails](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) die worden verzonden door de Azure AD B2B-uitnodigingsservice voor samenwerking om gebruikers uit te nodigen voor de directory, worden opnieuw ontworpen om de uitnodigingsinformatie en de volgende stappen van de gebruiker duidelijker te maken.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Wijzigingen in het HomeRealmDiscovery-beleid worden weergegeven in de controlelogboeken

**Type:** Vaste  
**Servicecategorie:** Audit  
**Productmogelijkheden:** Monitoring & Rapportage
 
We hebben een bug opgelost waarbij wijzigingen in het [HomeRealmDiscovery-beleid](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) niet zijn opgenomen in de controlelogboeken. U nu zien wanneer en hoe het beleid is gewijzigd en door wie. 

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-samenwerking beschikbaar in azure-overheidstenants

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheden:** B2B/B2C
 
De Azure AD B2B-samenwerkingsfuncties zijn nu beschikbaar tussen sommige azure-overheidstenants.  Als u wilt weten of uw tenant deze mogelijkheden kan gebruiken, volgt u de instructies op [Hoe kan ik zien of B2B-samenwerking beschikbaar is in mijn Azure US Government-tenant?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor-integratie voor Azure-logboeken is nu beschikbaar in Azure Government

**Type:** Nieuwe functie  
**Servicecategorie:** Rapportage  
**Productmogelijkheden:** Monitoring & Rapportage
 
Azure Monitor-integratie met Azure AD-logboeken is nu beschikbaar in Azure Government. U Azure AD-logboeken (Controle- en aanmeldingslogboeken) doorsturen naar een opslagaccount, gebeurtenishub en logboekanalyse. Bekijk de [gedetailleerde documentatie](https://aka.ms/aadlogsinamd) en [implementatieplannen voor rapportage en monitoring](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) voor Azure AD-scenario's.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Vernieuwing van identiteitsbescherming in Azure-beheer

**Type:** Nieuwe functie  
**Servicecategorie:** Identiteitsbescherming  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &

We zijn verheugd om te delen dat we nu de vernieuwde [Azure AD-identiteitsbeveiliging-ervaring](https://aka.ms/IdentityProtectionDocs) hebben uitgerold in de [Microsoft Azure Government-portal.](https://portal.azure.us/) Voor meer informatie, zie onze [aankondiging blog post](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Herstel na noodgevallen: uw inrichtingsconfiguratie downloaden en opslaan

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheden:** Beheer van identiteitslevenscyclus
 
De Azure AD-inrichtingsservice biedt een uitgebreide set configuratiemogelijkheden. Klanten moeten hun configuratie kunnen opslaan, zodat ze er later naar kunnen verwijzen of terug kunnen keren naar een bekende goede versie. We hebben de mogelijkheid toegevoegd om uw inrichtingsconfiguratie als JSON-bestand te downloaden en te uploaden wanneer u het nodig hebt. [Meer informatie](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (self-service password reset) vereist nu twee gates voor beheerders in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet) 

**Type:** Gewijzigde functie  
**Servicecategorie:** Selfservice wachtwoord opnieuw instellen  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &
 
Voorheen in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet), beheerders met behulp van self-service password reset (SSPR) om hun eigen wachtwoorden te resetten had slechts een "gate" (uitdaging) om hun identiteit te bewijzen. In openbare en andere nationale wolken, moeten de admins over het algemeen twee poorten gebruiken om hun identiteit te bewijzen wanneer het gebruiken van SSPR. Maar omdat we geen sms- of telefoongesprekken in Azure China 21Vianet ondersteunden, hebben we een wachtwoord met één poort laten resetten door beheerders.

We maken SSPR-functiepariteit tussen Azure China 21Vianet en de public cloud. In de toekomst moeten beheerders twee poorten gebruiken bij het gebruik van SSPR. Sms,, telefoongesprekken en authenticator-appmeldingen en -codes worden ondersteund. [Meer informatie](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>De wachtwoordlengte is beperkt tot 256 tekens

**Type:** Gewijzigde functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheden:** Gebruikersverificatie
 
Om de betrouwbaarheid van de Azure AD-service te garanderen, zijn gebruikerswachtwoorden nu beperkt in lengte tot 256 tekens. Gebruikers met wachtwoorden langer dan dit zal worden gevraagd om hun wachtwoord te wijzigen bij de volgende login, hetzij door contact op te nemen met hun admin of door gebruik te maken van de self-service wachtwoord reset functie.

Deze wijziging is ingeschakeld op 13 maart 2020, om 10:00 PST (18:00 GMT) en de fout is AADSTS 50052, InvalidPasswordExceedsMaxLength. Zie de [aankondiging van de wijziging](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) voor meer details.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD-aanmeldingslogboeken zijn nu beschikbaar voor alle gratis tenants via de Azure-portal

**Type:** Gewijzigde functie  
**Servicecategorie:** Rapportage  
**Productmogelijkheden:** Monitoring & Rapportage
 
Vanaf nu hebben klanten die gratis huurders hebben toegang tot de [Azure AD-aanmeldingslogboeken van de Azure-portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) gedurende maximaal 7 dagen. Voorheen waren aanmeldingslogboeken alleen beschikbaar voor klanten met Azure Active Directory Premium-licenties. Met deze wijziging hebben alle tenants toegang tot deze logboeken via de portal.

> [!NOTE]
> Klanten hebben nog steeds een premiumlicentie (Azure Active Directory Premium P1 of P2) nodig om toegang te krijgen tot de aanmeldingslogboeken via Microsoft Graph API en Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>De optie Voorverzameling van directorybrede groepen uit algemene instellingen groepen op Azure-portal

**Type:** Afgekeurd  
**Servicecategorie:** Groepsbeheer  
**Productmogelijkheden:** Samenwerking

Om klanten een flexibelere manier te bieden om directorybrede groepen te maken die het beste aan hun behoeften voldoen, hebben we de optie **Groepen voor** elke map vervangen door de**algemene** instellingen **groepen** > in de Azure-portal met een koppeling naar [dynamische groepsdocumentatie.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) We hebben onze documentatie verbeterd met meer instructies, zodat beheerders groepen voor alle gebruikers kunnen maken die gastgebruikers opnemen of uitsluiten.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Aankomende wijzigingen in aangepaste besturingselementen

**Type:** Plan voor verandering  
**Servicecategorie:** Mfa  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &
 
We zijn van plan om de huidige aangepaste besturingselementen voorbeeld te vervangen door een aanpak die partner-meegeleverde authenticatie mogelijkheden om naadloos te werken met de Azure Active Directory beheerder en eindgebruikers ervaringen. Vandaag de dag worden partner MFA-oplossingen geconfronteerd met de volgende beperkingen: ze werken pas nadat een wachtwoord is ingevoerd; ze dienen niet als MFA voor step-up authenticatie in andere belangrijke scenario's; en ze integreren niet met functies voor beheer van eindgebruikers of beheerdersreferenties. Met de nieuwe implementatie kunnen door partners verstrekte verificatiefactoren samenwerken met ingebouwde factoren voor belangrijke scenario's, waaronder registratie, gebruik, MFA-claims, verhoogverificatie, rapportage en logboekregistratie. 

Aangepaste besturingselementen blijven worden ondersteund in preview naast het nieuwe ontwerp totdat het de algemene beschikbaarheid bereikt. Op dat moment geven we klanten de tijd om te migreren naar het nieuwe ontwerp. Vanwege de beperkingen van de huidige aanpak, zullen we niet aan boord van nieuwe aanbieders totdat het nieuwe ontwerp beschikbaar is. We werken nauw samen met klanten en providers en communiceren de tijdlijn naarmate we dichterbij komen. [Meer informatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - MFA-verbeteringsactie-updates

**Type:** Plan voor verandering  
**Servicecategorie:** Mfa  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &
 
Om rekening te houden met de noodzaak voor bedrijven om de grootst mogelijke beveiliging te garanderen en tegelijkertijd beleid toe te passen dat met hun bedrijf werkt, verwijdert Microsoft Secure Score drie verbeteringsacties rond multi-factor authenticatie (MFA) en voegt er twee toe.

De volgende verbeteringsacties worden verwijderd:

- Alle gebruikers registreren voor MFA
- MFA vereisen voor alle gebruikers
- MFA vereisen voor ad-bevoegdheden van Azure

De volgende verbeteringsacties worden toegevoegd:

- Zorg ervoor dat alle gebruikers MFA kunnen voltooien voor beveiligde toegang
- MFA vereisen voor administratieve rollen

Deze nieuwe verbeteringsacties vereisen dat uw gebruikers of beheerders voor MFA in uw directory worden geregistreerd en dat de juiste set beleidsregels wordt vastgesteld die passen bij uw organisatiebehoeften. Het belangrijkste doel is om flexibiliteit te hebben en ervoor te zorgen dat al uw gebruikers en beheerders kunnen verifiëren met meerdere factoren of op risico's gebaseerde identiteitsverificatieprompts. Dit kan de vorm aannemen van het instellen van beveiligingsstandaards waarmee Microsoft kan beslissen wanneer gebruikers voor MFA worden uitdaagt, of meerdere beleidsregels hebben die scoped-beslissingen toepassen. Als onderdeel van deze verbeteringensacties wordt het beleid voor basislijnbeveiliging niet langer opgenomen in scoreberekeningen. [Lees meer over wat er komt in Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>SKU-selectie azure AD Domain Services

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD-domeinservices  
**Productmogelijkheden:** Azure AD-domeinservices
 
We hebben feedback gehoord dat klanten van Azure AD Domain Services meer flexibiliteit willen bij het selecteren van prestatieniveaus voor hun instanties. Vanaf 1 februari 2020 zijn we overgestapt van een dynamisch model (waarbij Azure AD de prestatie- en prijscategorie bepaalt op basis van objecttelling) naar een zelfselectiemodel. Klanten kunnen nu een prestatielaag kiezen die past bij hun omgeving. Deze wijziging stelt ons ook in staat om nieuwe scenario's zoals Resource Forests en Premium-functies zoals dagelijkse back-ups mogelijk te maken. Het aantal objecten is nu onbeperkt voor alle SKU's, maar we blijven suggesties voor het aantal objecten voor elke laag aanbieden.

**Er is geen onmiddellijke actie van de klant vereist.** Voor bestaande klanten bepaalt de dynamische laag die op 1 februari 2020 in gebruik was, de nieuwe standaardlaag. Er is geen prijs- of prestatie-impact als gevolg van deze wijziging. Azure AD DS-klanten moeten in de toekomst de prestatievereisten evalueren als hun adreslijstgrootte en werkbelastingkenmerken veranderen. Schakelen tussen servicelagen blijft een no-downtime-bewerking en we verplaatsen klanten niet langer automatisch naar nieuwe lagen op basis van de groei van hun directory. Bovendien zullen er geen prijsverhogingen zijn en zullen nieuwe prijzen overeenkomen met ons huidige factureringsmodel. Zie de Azure [AD DS SKU's-documentatie](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) en de [prijspagina azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/)voor meer informatie.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nieuwe Federated Apps beschikbaar in Azure AD App-galerie - februari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden
 
In februari 2020 hebben we deze 31 nieuwe apps met Federatie-ondersteuning toegevoegd aan de app-galerij: 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO Voor Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), Barracuda Email Security [Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), In case of crisis - Online [Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), BIC [Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), Beekeeper Azure AD Data [Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), Korn [Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), New Relic [(Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), Template [Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [LavaDo](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Herprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial) [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)

 
Zie [SaaS-toepassingsintegratie met Azure Active Directory](https://aka.ms/appstutorial)voor meer informatie over de apps. Zie [Uw toepassing aanbieden in de azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest)voor meer informatie over het aanbieden van uw toepassing in de azure AD-appgalerij .

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD Application Gallery - februari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden
 
U nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuw geïntegreerde apps automatiseren:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud van Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Zie [Gebruikersinrichting automatiseren voor SaaS-toepassingen met Azure AD voor](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)meer informatie over het beter beveiligen van uw organisatie door gebruik te maken van geautomatiseerde gebruikersaccountinrichting.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-ondersteuning voor FIDO2-beveiligingssleutels in hybride omgevingen

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheden:** Gebruikersverificatie
 
We kondigen de openbare preview van Azure AD-ondersteuning voor FIDO2-beveiligingssleutels in hybride omgevingen aan. Gebruikers kunnen nu FIDO2-beveiligingssleutels gebruiken om zich aan te melden bij hun Hybride Azure AD-apparaten met Windows 10 en naadloos aanmelden bij hun on-premises en cloudbronnen. Ondersteuning voor hybride omgevingen is de meest gevraagde functie van onze wachtwoordloze klanten sinds we in eerste instantie de openbare preview voor FIDO2-ondersteuning in Azure AD-apparaten lanceerden. Wachtwoordloze verificatie met behulp van geavanceerde technologieën zoals biometrie en public/private key cryptografie bieden gemak en gebruiksgemak terwijl het veilig is. Met deze openbare preview u nu moderne verificatie zoals FIDO2-beveiligingssleutels gebruiken om toegang te krijgen tot traditionele Active Directory-bronnen. Ga voor meer informatie naar [SSO naar on-premises resources.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Ga op [bezoek bij FIDO2-beveiligingssleutels voor uw tenant](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) voor stapsgewijze instructies. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>De nieuwe Ervaring met Mijn account is nu algemeen beschikbaar

**Type:** Gewijzigde functie  
**Servicecategorie:** Mijn profiel/account  
**Productmogelijkheden:** Ervaringen van eindgebruikers
 
Mijn account, de one stop shop voor alle behoeften van het beheer van eindgebruikersaccounts, is nu algemeen beschikbaar! Eindgebruikers hebben toegang tot deze nieuwe site via de URL of in de kop van de nieuwe Ervaring Mijn apps. Meer informatie over alle selfservicemogelijkheden die de nieuwe ervaring biedt, vindt u in [het Overzicht van mijn accountportal.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL van mijn account bijwerken naar myaccount.microsoft.com

**Type:** Gewijzigde functie  
**Servicecategorie:** Mijn profiel/account  
**Productmogelijkheden:** Ervaringen van eindgebruikers
 
De nieuwe gebruikerservaring van mijn account `https://myaccount.microsoft.com` wordt de URL in de komende maand bijgewerkt. Meer informatie over de ervaring en alle zelfbedieningsmogelijkheden voor het account die het biedt aan eindgebruikers bij [De help voor mijn accountportal.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>De nieuwe Mijn Apps-portal is nu algemeen beschikbaar

**Type:** Plan voor verandering  
**Servicecategorie:** Mijn apps  
**Productmogelijkheden:** Ervaringen van eindgebruikers
 
Upgrade uw organisatie naar de nieuwe Mijn Apps-portal die nu algemeen beschikbaar is! Meer informatie over de nieuwe portal en collecties vindt [u op De mijn apps-portal](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)maken.

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Werkruimten in Azure AD zijn omgedoopt tot verzamelingen

**Type:** Gewijzigde functie  
**Servicecategorie:** Mijn apps   
**Productmogelijkheden:** Ervaringen van eindgebruikers
 
Werkruimten, de filters beheerders kunnen configureren om hun gebruikers apps te organiseren, zal nu worden aangeduid als collecties. Meer informatie over het configureren ervan vindt u [bij Collecties maken op de mijn apps-portal.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Aanmelden en aanmelden voor Azure AD B2C-telefoon met aangepast beleid (Openbare voorbeeld)

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Consumer Identity Management  
**Productmogelijkheden:** B2B/B2C
 
Met het aanmelden van telefoonnummers en aanmelding kunnen ontwikkelaars en bedrijven hun klanten toestaan zich aan te melden en zich aan te melden met een eenmalig wachtwoord dat via sms naar het telefoonnummer van de gebruiker wordt verzonden. Met deze functie kan de klant ook zijn telefoonnummer wijzigen als hij of zij de toegang tot zijn telefoon verliest. Met de kracht van aangepast beleid, telefoon aanmelding en aanmelding stelt ontwikkelaars en ondernemingen in staat om hun merk te communiceren door middel van pagina aanpassing. Ontdek hoe u zich aanmeldt [voor telefoon aanmeldingen en aanmelding met aangepast beleid in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD Application Gallery - januari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden
 
U nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuw geïntegreerde apps automatiseren:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Zie [Gebruikersinrichting automatiseren voor SaaS-toepassingen met Azure AD voor](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)meer informatie over het beter beveiligen van uw organisatie door gebruik te maken van geautomatiseerde gebruikersaccountinrichting.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nieuwe federated-apps beschikbaar in azure AD-app-galerie - januari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden
 
In januari 2020 hebben we deze 33 nieuwe apps met Federatie-ondersteuning toegevoegd aan de app-galerie: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), `https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access` [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, en JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), Hosted [MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), `https://sites.lumapps.com/login` [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB voor Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), Printer [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taak](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) `https://app.sandwai.com/`

Zie [SaaS-toepassingsintegratie met Azure Active Directory](https://aka.ms/appstutorial)voor meer informatie over de apps. Zie [Uw toepassing aanbieden in de azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest)voor meer informatie over het aanbieden van uw toepassing in de azure AD-appgalerij .

---

### <a name="two-new-identity-protection-detections"></a>Twee nieuwe detecties voor identiteitsbescherming

**Type:** Nieuwe functie  
**Servicecategorie:** Identiteitsbescherming  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &
 
We hebben twee nieuwe aanmeldingsgekoppelde detectietypen toegevoegd aan Identiteitsbeveiliging: verdachte regels voor manipulatie van inboxen en Onmogelijke reizen. Deze offline detecties worden ontdekt door Microsoft Cloud App Security (MCAS) en beïnvloeden de gebruiker en aanmeldingsrisico's in Identiteitsbeveiliging. Zie voor meer informatie over deze detecties onze [aanmeldingsrisicotypen.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: URI Fragments will not be carried through the login redirect Breaking Change: URI Fragments will not be carried through the login redirect Breaking Change: URI Fragments will not be carried through the login redirect Breaking Change

**Type:** Gewijzigde functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheden:** Gebruikersverificatie
 
Vanaf 8 februari 2020, wanneer een verzoek wordt verzonden naar login.microsoftonline.com om een gebruiker aan te melden, zal de service een leeg fragment toevoegen aan de aanvraag.  Dit voorkomt een klasse van redirect-aanvallen door ervoor te zorgen dat de browser elk bestaand fragment in het verzoek wegveegt. Geen enkele toepassing mag afhankelijk zijn van dit gedrag. Zie Wijzigingen in de documentatie van het Microsoft-identiteitsplatform [verbreken](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) voor meer informatie.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP-succesfactoren inrichten integreren in Azure AD en on-premises AD (Public Preview)

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheden:** Beheer van identiteitslevenscyclus

U SAP SuccessFactors nu integreren als een gezaghebbende identiteitsbron in Azure AD. Met deze integratie u de end-to-end identiteitslevenscyclus automatiseren, inclusief het gebruik van HR-gebaseerde gebeurtenissen, zoals nieuwe aanwervingen of beëindigingen, om de inrichting van Azure AD-accounts te beheren.

Zie de zelfstudie Voor het automatisch inrichten van [SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) voor meer informatie over het instellen van SAP SuccessFactors in komende inrichting voor Azure AD.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Ondersteuning voor aangepaste e-mails in Azure AD B2C (Public Preview)

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Consumer Identity Management  
**Productmogelijkheden:** B2B/B2C

U azure AD B2C nu gebruiken om aangepaste e-mails te maken wanneer uw gebruikers zich aanmelden om uw apps te gebruiken. Door DisplayControls (momenteel in preview) en een externe e-mailprovider (zoals [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)of een aangepaste REST-API) te gebruiken, u uw eigen e-mailsjabloon, **Van** adres en onderwerptekst gebruiken, evenals ondersteuningslokalisatie en aangepaste OTP-instellingen (one-time password).

Zie [Aangepaste e-mailverificatie in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)voor meer informatie.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Vervanging van basislijnbeleid door beveiligingsstandaards

**Type:** Gewijzigde functie  
**Servicecategorie:** Andere  
**Productmogelijkheden:** Identiteitsbeveiliging en -bescherming

Als onderdeel van een secure-by-default-model voor verificatie verwijderen we het bestaande beleid voor basislijnbeveiliging van alle tenants. Deze verwijdering is bedoeld voor voltooiing aan het einde van februari. De vervanging voor dit beleid voor basislijnbeveiliging is beveiligingsstandaard. Als u beleid voor basislijnbeveiliging hebt gebruikt, moet u van plan zijn over te stappen naar het nieuwe beleid voor beveiligingsstandaarden of naar Voorwaardelijke toegang. Als u dit beleid niet hebt gebruikt, u geen actie ondernemen.

Zie [Wat zijn beveiligingsstandaards voor](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) meer informatie over de nieuwe beveiligingsstandaardinstellingen? Zie [Beleid voor algemene voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)voor meer informatie over beleid voor voorwaardelijke toegang .

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor het kenmerk SameSite en Chrome 80

**Type:** Plan voor verandering  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheden:** Gebruikersverificatie

Als onderdeel van een secure-by-default-model voor cookies verandert de Chrome 80-browser de manier waarop cookies worden behandeld zonder het `SameSite` kenmerk. Elke cookie die het `SameSite` kenmerk niet opgeeft, wordt `SameSite=Lax`behandeld alsof deze is ingesteld, wat ertoe zal leiden dat Chrome bepaalde scenario's voor het delen van cross-domain-cookies blokkeert waarvan uw app mogelijk afhankelijk is. Als u het oudere Chrome-gedrag `SameSite=None` wilt behouden, `Secure` u het kenmerk gebruiken en een extra attribuut toevoegen, zodat cross-site cookies alleen toegankelijk zijn via HTTPS-verbindingen. Chrome is gepland om deze wijziging te voltooien door 4 februari 2020.

We raden al onze ontwikkelaars aan hun apps te testen met behulp van deze richtlijnen:

- Stel de standaardwaarde in voor de instelling **Beveiligde cookie gebruiken** op **Ja**.

- Stel de standaardwaarde voor het kenmerk **SameSite** in op **Geen**.

- Voeg een `SameSite` extra kenmerk van **Secure toe.**

Zie [Komende SameSite-cookiewijzigingen in ASP.NET en ASP.NET Kern-](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) en [potentiële onderbreking van websites van klanten en Microsoft-producten en -services in Chrome-versie 79 en hoger](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)voor meer informatie.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nieuwe hotfix voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type:** Vaste  
**Servicecategorie:** Microsoft Identity Manager  
**Productmogelijkheden:** Beheer van identiteitslevenscyclus

Er is een hotfix rollup-pakket (build 4.6.34.0) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Dit rolluppakket lost problemen op en voegt verbeteringen toe die worden beschreven in de sectie 'Opgeloste problemen en verbeteringen die in deze update zijn toegevoegd'.

Zie [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Update Rollup is beschikbaar](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)voor meer informatie en om het hotfix-pakket te downloaden.

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nieuw activiteitsrapport voor AD FS-apps om apps te migreren naar Azure AD (Public Preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** Sso

Gebruik het nieuwe ad fs-app-activiteitenrapport (Active Directory Federation Services) in de Azure-portal om te bepalen welke van uw apps kunnen worden gemigreerd naar Azure AD. In het rapport worden alle AD FS-apps beoordeeld op compatibiliteit met Azure AD, wordt gecontroleerd op eventuele problemen en wordt richtlijnen gegeven voor het voorbereiden van afzonderlijke apps op migratie.

Zie [Het rapport AD FS-toepassingsactiviteit gebruiken om toepassingen te migreren naar Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)voor meer informatie.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nieuwe werkstroom voor gebruikers om toestemming van de beheerder aan te vragen (Public Preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** Toegangsbeheer

De nieuwe werkstroom voor beheerderstoestemming biedt beheerders een manier om toegang te verlenen tot apps waarvoor beheerdersgoedkeuring nodig zijn. Als een gebruiker toegang probeert te krijgen tot een app, maar geen toestemming kan geven, kan hij nu een verzoek om goedkeuring van de beheerder verzenden. Het verzoek wordt per e-mail verzonden en in een wachtrij geplaatst die toegankelijk is vanaf de Azure-portal, naar alle beheerders die zijn aangewezen als revisoren. Nadat een revisor actie heeft ondernomen op een in behandeling zijnde aanvraag, worden de verzoekende gebruikers op de hoogte gebracht van de actie.

Zie [De werkstroom voor beheerderstoestemming configureren (voorbeeld) voor](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)meer informatie.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nieuwe Configuratie-ervaring voor Azure AD-app-registraties token voor het beheren van optionele claims (Openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheden:** Ervaring met ontwikkelaars

Het nieuwe **Azure AD App Registrations Token-configuratieblad** op de Azure-portal toont app-ontwikkelaars nu een dynamische lijst met optionele claims voor hun apps. Deze nieuwe ervaring helpt bij het stroomlijnen van azure AD-app-migraties en het minimaliseren van optionele foutconfiguraties van claims.

Zie [Optionele claims voor uw Azure AD-app verstrekken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)voor meer informatie.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nieuwe goedkeuringswerkstroom in twee fasen in Azure AD-rechtenbeheer (Openbare voorbeeld)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheden:** Beheer van rechten

We hebben een nieuwe goedkeuringsworkflow in twee fasen geïntroduceerd waarmee u twee fiatteurs nodig hebt om het verzoek van een gebruiker voor een toegangspakket goed te keuren. U het bijvoorbeeld zo instellen dat de manager van de aanvragende gebruiker het eerst moet goedkeuren en vervolgens u ook een broneigenaar verplichten om het goed te keuren. Als een van de fiatteurs het niet goedkeurt, wordt de toegang niet verleend.

Zie [Aanvraag- en goedkeuringsinstellingen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer voor](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)meer informatie.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Updates voor de pagina Mijn apps en nieuwe werkruimten (Openbare voorbeeld)

**Type:** Nieuwe functie  
**Servicecategorie:** Mijn apps  
**Productmogelijkheden:** integratie van derden

U nu de manier waarop de gebruikers van uw organisatie de vernieuwde Mijn Apps-ervaring bekijken en openen. Deze nieuwe ervaring bevat ook de nieuwe werkruimtefunctie, waardoor het voor uw gebruikers gemakkelijker wordt om apps te vinden en te organiseren.

Zie [Werkruimten maken op de mijn apps-portal](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)voor meer informatie over de nieuwe ervaring met Mijn apps en het maken van werkruimten.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google social ID-ondersteuning voor Azure AD B2B-samenwerking (Algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheden:** Gebruikersverificatie

Nieuwe ondersteuning voor het gebruik van sociale id's van Google (Gmail-accounts) in Azure AD helpt samenwerking eenvoudiger te maken voor uw gebruikers en partners. Uw partners hoeven geen nieuw Microsoft-specifiek account meer aan te maken en te beheren. Microsoft Teams ondersteunt Google-gebruikers nu volledig op alle clients en op de algemene en tenantgerelateerde verificatieeindpunten.

Zie [Google toevoegen als identiteitsprovider voor B2B-gastgebruikers voor](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)meer informatie.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile Support voor voorwaardelijke toegang en één aanmelding (algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &

Azure AD voor Microsoft Edge op iOS en Android ondersteunt nu Azure AD Single Sign-On en Voorwaardelijke toegang:

- **Microsoft Edge single sign-on (SSO):** Eenmalige aanmelding is nu beschikbaar voor native clients (zoals Microsoft Outlook en Microsoft Edge) voor alle Azure AD-apps.

- **Voorwaardelijke toegang tot Microsoft Edge:** Via toepassingsgebaseerdbeleid voor voorwaardelijke toegang moeten uw gebruikers door Microsoft Intune beveiligde browsers, zoals Microsoft Edge, gebruiken.

Zie het [Microsoft Edge Mobile Support for Conditional Access en Single Sign-on Now GeneralLy Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogpost voor meer informatie over voorwaardelijke toegang en SSO met Microsoft Edge. Zie Webtoegang beheren [met een browser met beleid met Microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)voor meer informatie over het instellen van uw [client-apps](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) met behulp van voorwaardelijke toegang op basis van apps op basis van apps of [voorwaardelijke toegang op basis van apparaten.](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD-rechtenbeheer (algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheden:** Beheer van rechten

Azure AD-rechtenbeheer is een nieuwe functie voor identiteitsbeheer, waarmee organisaties de levenscyclus van identiteit en toegang op schaal kunnen beheren. Deze nieuwe functie helpt bij het automatiseren van toegangsaanvraagwerkstromen, toegangstoewijzingen, recensies en vervaldatum in groepen, apps en SharePoint Online-sites.

Met Azure AD-rechtenbeheer u de toegang efficiënter beheren, zowel voor werknemers als voor gebruikers buiten uw organisatie die toegang tot deze bronnen nodig hebben.

Zie [Wat is Azure AD-rechtenbeheer voor](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) meer informatie?

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Gebruikersaccountvoorziening automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden  

U nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuw geïntegreerde apps automatiseren:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Zie [Gebruikersinrichting automatiseren voor SaaS-toepassingen met Azure AD voor](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)meer informatie over het beter beveiligen van uw organisatie door gebruik te maken van geautomatiseerde gebruikersaccountinrichting.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nieuwe Federated Apps beschikbaar in Azure AD App-galerie - november 2019

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden

In november 2019 hebben we deze 21 nieuwe apps met Federatie-ondersteuning toegevoegd aan de app-galerie:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM),](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial) [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=) `https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279`, [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Zie [SaaS-toepassingsintegratie met Azure Active Directory](https://aka.ms/appstutorial)voor meer informatie over de apps. Zie [Uw toepassing aanbieden in de azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest)voor meer informatie over het aanbieden van uw toepassing in de azure AD-appgalerij .

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nieuwe en verbeterde Azure AD-toepassingsgalerie

**Type:** Gewijzigde functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** Sso

We hebben de Azure AD-toepassingsgalerie bijgewerkt om het voor u gemakkelijker te maken om vooraf geïntegreerde apps te vinden die provisioning, OpenID Connect en SAML ondersteunen op uw Azure Active Directory-tenant.

Zie [Een toepassing toevoegen aan uw Azure Active Directory-tenant](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)voor meer informatie.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Limiet voor langere limiet voor de definitie van app-rollen van 120 tot 240 tekens

**Type:** Gewijzigde functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** Sso

We hebben van klanten gehoord dat de lengtelimiet voor de waarde van de app-roldefinitie in sommige apps en services te kort is met 120 tekens. Als reactie hierop hebben we de maximale lengte van de definitie van rolwaarde verhoogd tot 240 tekens.

Zie [App-rollen toevoegen in uw toepassing en deze ontvangen in het token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)voor meer informatie over het gebruik van toepassingsspecifieke roldefinities.

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Afschrijving van de identityRiskEvent API voor Azure AD Identity Protection risicodetecties  

**Type:** Plan voor verandering  
**Servicecategorie:** Identiteitsbescherming  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &

In reactie op feedback van ontwikkelaars kunnen Azure AD Premium P2-abonnees nu complexe query's uitvoeren op de risicodetectiegegevens van Azure AD Identity Protection met behulp van de nieuwe riskDetection API voor Microsoft Graph. De bestaande [bètaversie van identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API stopt rond **10 januari 2020**met het retourneren van gegevens. Als uw organisatie de identityRiskEvent API gebruikt, moet u overschakelen naar de nieuwe riskDetection API.

Zie de [referentiedocumentatie voor API voor risicodetectie](https://aka.ms/RiskDetectionsAPI)voor meer informatie over de nieuwe API voor risicodetectie.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor toepassingsproxy voor het samesitekenmerk en Chrome 80

**Type:** Plan voor verandering  
**Servicecategorie:** App-proxy  
**Productmogelijkheden:** Toegangsbeheer

Een paar weken voor de release van de Chrome 80-browser zijn we van plan om bij te werken hoe Application Proxy-cookies het **kenmerk SameSite** behandelen. Met de release van Chrome 80 wordt elke cookie die het **kenmerk SameSite** `SameSite=Lax`niet opgeeft, behandeld alsof het is ingesteld op .

Om mogelijke negatieve gevolgen als gevolg van deze wijziging te voorkomen, werken we de toegangs- en sessiecookies van Application Proxy bij door:

- De standaardwaarde instellen voor de instelling **Beveiligde cookie gebruiken** op **Ja**.

- De standaardwaarde voor het kenmerk **SameSite** instellen op **Geen**.

    >[!NOTE]
    > Application Proxy access cookies zijn altijd uitsluitend via beveiligde kanalen verzonden. Deze wijzigingen zijn alleen van toepassing op sessiecookies.

Zie [Cookie-instellingen voor toegang tot on-premises toepassingen in Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)meer informatie over de cookie-instellingen voor toepassingsproxy.

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>App-registraties (legacy) en geconvergeerd appbeheer vanuit de Application Registration Portal (apps.dev.microsoft.com) zijn niet langer beschikbaar

**Type:** Plan voor verandering  
**Servicecategorie:** N/a  
**Productmogelijkheden:** Ervaring met ontwikkelaars

In de nabije toekomst kunnen gebruikers met Azure AD-accounts geconvergeerde toepassingen niet meer registreren en beheren met behulp van de Application Registration Portal (apps.dev.microsoft.com), of toepassingen registreren en beheren in de app-registratie (legacy)-ervaring in de Azure-portal.

Zie de [app-registraties in de trainingshandleiding voor Azure-portalen](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)voor meer informatie over de nieuwe ervaring met app-registraties.

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers hoeven zich niet langer opnieuw te registreren tijdens de migratie van MFA per gebruiker naar mfa op basis van voorwaardelijke toegang

**Type:** Vaste  
**Servicecategorie:** Mfa  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &

We hebben een bekend probleem opgelost waarbij gebruikers zich opnieuw moesten registreren als ze waren uitgeschakeld voor multi-factorauthenticatie per gebruiker (MFA) en vervolgens zijn ingeschakeld voor MFA via een beleid voor voorwaardelijke toegang.

Als u wilt dat gebruikers zich opnieuw registreren, u de optie Vereiste MFA opnieuw registreren in de verificatiemethoden van de gebruiker in de Azure **AD-portal.** Zie Gebruikers converteren van MFA per gebruiker naar op voorwaardelijke [toegang.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nieuwe mogelijkheden om claims te transformeren en te verzenden in uw SAML-token

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** Sso

We hebben extra mogelijkheden toegevoegd om u te helpen claims aan te passen en te verzenden in uw SAML-token. Deze nieuwe mogelijkheden omvatten:

- Aanvullende functies voor het transformeren van claims, zodat u de waarde wijzigen die u in de claim verzendt.

- Mogelijkheid om meerdere transformaties toe te passen op één claim.

- Mogelijkheid om de claimbron op te geven op basis van het gebruikerstype en de groep waartoe de gebruiker behoort.

Zie [Claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)gedetailleerde informatie over deze nieuwe mogelijkheden, inclusief hoe deze te gebruiken.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nieuwe pagina Mijn aanmeldingen voor eindgebruikers in Azure AD

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheden:** Monitoring & Rapportage

We hebben een nieuwe pagina **Mijn aanmeldingstoegevoegd** (omhttps://mysignins.microsoft.com) de gebruikers van uw organisatie hun recente aanmeldingsgeschiedenis te laten bekijken om te controleren op ongebruikelijke activiteiten. Op deze nieuwe pagina kunnen uw gebruikers het volgende zien:

- Als iemand probeert om hun wachtwoord te raden.

- Als een aanvaller zich met succes heeft aangemeld bij zijn account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

Zie de gebruikers [kunnen nu hun aanmeldingsgeschiedenis controleren op een blog met ongebruikelijke activiteiten](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) voor meer informatie.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migratie van Azure AD Domain Services (Azure AD DS) van klassieke naar Azure Resource Manager virtuele netwerken

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD-domeinservices  
**Productmogelijkheden:** Azure AD-domeinservices

Aan onze klanten die zijn geplakt op klassieke virtuele netwerken - we hebben goed nieuws voor u! U nu een eenmalige migratie uitvoeren van een klassiek virtueel netwerk naar een bestaand virtueel netwerk van Resource Manager. Nadat u bent verhuisd naar het virtuele netwerk van Resource Manager, u profiteren van de extra en verbeterde functies, zoals fijnmazig wachtwoordbeleid, e-mailmeldingen en controlelogboeken.

Zie [Voorbeeld - Azure AD Domain Services migreren van het virtuele netwerkmodel Classic naar Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)voor meer informatie.

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates voor de contractindeling van Azure AD B2C-pagina's

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Consumer Identity Management  
**Productmogelijkheden:** B2B/B2C

We hebben een aantal nieuwe wijzigingen aangebracht in versie 1.2.0 van het paginacontract voor Azure AD B2C. In deze bijgewerkte versie u nu de belastingsvolgorde voor uw elementen beheren, wat ook kan helpen om de flikkering te stoppen die optreedt wanneer de stijlpagina (CSS) wordt geladen.

Zie het [logboek Versiewijziging](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)voor een volledige lijst met de wijzigingen in het paginacontract.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Update naar de pagina Mijn apps samen met nieuwe werkruimten (openbare voorbeeld)

**Type:** Nieuwe functie  
**Servicecategorie:** Mijn apps  
**Productmogelijkheden:** Toegangsbeheer

U nu de manier aanpassen waarop de gebruikers van uw organisatie de gloednieuwe Mijn apps-ervaring bekijken en openen, inclusief het gebruik van de nieuwe werkruimtenfunctie om het voor hen gemakkelijker te maken om apps te vinden. De nieuwe werkruimtenfunctionaliteit fungeert als een filter voor de apps waartoe de gebruikers van uw organisatie al toegang hebben.

Zie [Werkruimten maken op de portal Mijn apps (voorbeeld) voor](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)meer informatie over het uitrollen van de nieuwe Ervaring met Mijn apps en het maken van werkruimten.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Ondersteuning voor het maandelijksactieve factureringsmodel voor gebruikers (algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Consumer Identity Management  
**Productmogelijkheden:** B2B/B2C

Azure AD B2C ondersteunt nu maandelijksactieve gebruikers (MAU) facturering. MAU-facturering is gebaseerd op het aantal unieke gebruikers met verificatieactiviteit gedurende een kalendermaand. Bestaande klanten kunnen op elk gewenst moment overschakelen naar deze nieuwe factureringsmethode.

Vanaf 1 november 2019 worden alle nieuwe klanten automatisch gefactureerd via deze methode. Deze factureringsmethode komt klanten ten goede door kostenvoordelen en de mogelijkheid om vooruit te plannen.

Zie [Upgraden naar het factureringsmodel voor maandelijksactieve gebruikers voor](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nieuwe federated-apps beschikbaar in azure AD-app-galerie - oktober 2019

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheden:** integratie van derden

In oktober 2019 hebben we deze 35 nieuwe apps met Federatie-ondersteuning toegevoegd aan de app-galerie:

[In geval van crisis – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), Juno [Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [Freshworks](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coördinaat – Europa](https://www.hirevue.com/), [HireVue Coördinaat - USOnly](https://www.hirevue.com/), [HireVue Coördinaat - US](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass ,](https://login.xirrus.com/azure-signup) [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), Speaking Email for [Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Zie [SaaS-toepassingsintegratie met Azure Active Directory](https://aka.ms/appstutorial)voor meer informatie over de apps. Zie [Uw toepassing aanbieden in de azure Active Directory-toepassingsgalerie](https://aka.ms/azureadapprequest)voor meer informatie over het aanbieden van uw toepassing in de azure AD-appgalerij .

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menu-item Voor geconsolideerde beveiliging in de Azure AD-portal

**Type:** Gewijzigde functie  
**Servicecategorie:** Identiteitsbescherming  
**Productmogelijkheden:** Bescherming van identiteitsbeveiliging &

U hebt nu toegang tot alle beschikbare Azure AD-beveiligingsfuncties in het nieuwe **menu-item Beveiliging** en via de **zoekbalk** in de Azure-portal. Daarnaast biedt de nieuwe **bestemmingspagina Beveiliging,** beveiliging genaamd **Beveiliging - Aan de slag,** links naar onze openbare documentatie, beveiligingsrichtlijnen en implementatiehandleidingen.

Het nieuwe **menu Beveiliging** bevat:

- Voorwaardelijke toegang
- Identiteitsbeveiliging
- Security Center
- Identiteitsbeveiligde score
- Verificatiemethoden
- Mfa
- Risicorapporten - Riskante gebruikers, risicovolle aanmeldingen, risicodetecties
- En meer...

Zie [Beveiliging - Aan de slag voor](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)meer informatie.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Verlopensbeleid voor Office 365-groepen verbeterd met automatische verlenging

**Type:** Gewijzigde functie  
**Servicecategorie:** Groepsbeheer  
**Productmogelijkheden:** Beheer van identiteitslevenscyclus

Het expiratiebeleid voor Office 365-groepen is uitgebreid om groepen die actief worden gebruikt door de leden automatisch te vernieuwen. Groepen worden automatisch verlengd op basis van gebruikersactiviteit in alle Office 365-apps, waaronder Outlook, SharePoint en Teams.

Deze verbetering helpt om meldingen voor het verlopen van uw groep te verminderen en helpt ervoor te zorgen dat actieve groepen beschikbaar blijven. Als u al een actief verloopbeleid hebt voor uw Office 365-groepen, hoeft u niets te doen om deze nieuwe functionaliteit in te schakelen.

Zie Het [verloopbeleid voor Office 365-groepen configureren voor](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)meer informatie .

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Ervaring met het maken van Azure AD Domain Services (Azure AD DS)

**Type:** Gewijzigde functie  
**Servicecategorie:** Azure AD-domeinservices  
**Productmogelijkheden:** Azure AD-domeinservices

We hebben Azure AD Domain Services (Azure AD DS) bijgewerkt met een nieuwe en verbeterde creatie-ervaring, zodat u in slechts drie klikken een beheerd domein maken! Bovendien u Azure AD DS nu uploaden en implementeren vanuit een sjabloon.

Zie [Zelfstudie: Een azure Active Directory Domain Services-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)voor meer informatie.

---
