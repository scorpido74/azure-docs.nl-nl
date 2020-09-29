---
title: Wat is nieuw? Release opmerkingen-Azure Active Directory | Microsoft Docs
description: Meer informatie over wat er nieuw is in Azure Active Directory; zoals de nieuwste opmerkingen bij de release, bekende problemen, fout oplossingen, afgeschafte functionaliteit en aanstaande wijzigingen.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9bcc356835fcfc080bd381043552d6e8868cc7f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446628"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>U kunt een melding krijgen wanneer deze pagina is bijgewerkt door de URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` te kopiëren en plakken in uw ![pictogram van RSS-feedlezer](./media/whats-new/feed-icon-16x16.png) feedlezer.

Azure AD ontvangt voortdurend verbeteringen. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt. Ga daarom regel matig opnieuw te werk. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in [Archief voor wat er nieuw is in azure Active Directory](whats-new-archive.md).

---

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-september 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot-beveiligings bewustmaking](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Gecontroleerd BitLocker-herstel in azure AD-open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beheer voor apparaten  
**Product mogelijkheden:** Levenscyclus beheer van apparaten
 
Wanneer IT-beheerders of eind gebruikers BitLocker-herstel sleutel (s) hebben gelezen waartoe ze toegang hebben, wordt door Azure Active Directory nu een audit logboek gegenereerd waarin wordt vastgelegd wie de herstel sleutel heeft geopend. Dezelfde controle bevat details van het apparaat waaraan de BitLocker-sleutel is gekoppeld.

Eind gebruikers [hebben via mijn account toegang tot hun herstel sleutels](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). IT-beheerders hebben toegang tot herstel sleutels via de [API van de BitLocker-herstel sleutel in bèta](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta,) of via de Azure AD-Portal. Zie [BitLocker-sleutels weer geven of kopiëren in de Azure AD-Portal](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)voor meer informatie.

---

### <a name="teams-devices-administrator-built-in-role"></a>Teams ingebouwde rol van de beheerder van de apparaten

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Gebruikers met de rol [teams van apparaten](../users-groups-roles/directory-assign-admin-roles.md#teams-devices-administrator) kunnen [teams, gecertificeerde apparaten](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) beheren vanuit het beheer centrum teams. 

Met deze rol kan de gebruiker alle apparaten in één oogopslag weer geven, met de mogelijkheid om apparaten te zoeken en te filteren. De gebruiker kan ook de details van elk apparaat controleren, inclusief het aangemelde account en het merk en model van het apparaat. De gebruiker kan de instellingen op het apparaat wijzigen en de software versies bijwerken. Deze rol verleent geen machtigingen om de activiteit teams te controleren en de kwaliteit van het apparaat aan te roepen.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Geavanceerde query mogelijkheden voor Directory-objecten

**Type:** Nieuwe functie  
**Service categorie:** MS Graph  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
Alle nieuwe query mogelijkheden die zijn geïntroduceerd voor Directory objecten in azure AD Api's zijn nu beschikbaar in het eind punt v 1.0 en productie gereed. Ontwikkel aars kunnen Directory-objecten en gerelateerde koppelingen tellen, zoeken, filteren en sorteren met behulp van de standaard OData-Opera tors.

Raadpleeg de documentatie [hier](https://aka.ms/BlogPostMezzoGA)voor meer informatie. u kunt ook feedback verzenden met deze [korte enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Open bare Preview: voortdurende toegangs beoordeling voor tenants die beleids regels voor voorwaardelijke toegang hebben geconfigureerd

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De evaluatie van voortdurende toegang (CAE) is nu beschikbaar in de open bare Preview voor Azure AD-tenants met beleid voor voorwaardelijke toegang. Met CAE worden essentiële beveiligings gebeurtenissen en-beleids regels in realtime geëvalueerd. Dit omvat het uitschakelen van accounts, het opnieuw instellen van wacht woorden en het wijzigen van de locatie. Zie [evaluatie van continue toegang](../conditional-access/concept-continuous-access-evaluation.md)voor meer informatie.

---

### <a name="ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Gebruikers vragen om een toegangs pakket aanvullende vragen om goedkeurings beslissingen te verbeteren

**Type:** Nieuwe functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
Beheerders kunnen er nu voor zorgen dat gebruikers die een toegangs pakket aanvragen, meer vragen beantwoorden dan alleen de zakelijke reden in de mijn Access-portal van Azure AD-rechten beheer. De antwoorden van de gebruikers worden vervolgens weer gegeven aan de goed keurders, zodat ze een nauw keurigere goed keuring voor toegang kunnen krijgen. Zie [aanvullende gegevens van de aanvrager voor goed keuring verzamelen (preview)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)voor meer informatie.
 
---

### <a name="public-preview-enhanced-user-management"></a>Open bare Preview: verbeterd gebruikers beheer

**Type:** Nieuwe functie  
**Service categorie:** Gebruikers beheer  
**Product mogelijkheden:** Gebruikers beheer
 

De Azure AD-Portal is bijgewerkt, zodat u gebruikers gemakkelijker kunt vinden op de pagina's alle gebruikers en verwijderde gebruikers. Wijzigingen in de preview zijn onder andere: 
- Meer zicht bare gebruikers eigenschappen, waaronder de object-ID, de synchronisatie status van de Directory, het aanmaak type en de identiteits verlener.
- Met zoeken kunt u nu gecombineerde Zoek opdrachten van namen, e-mails en object-Id's.
- Verbeterd filteren op gebruikers type (lid, gast en geen), adreslijst synchronisatie status, aanmaak type, bedrijfs naam en domein naam.
- Nieuwe sorteer mogelijkheden voor eigenschappen zoals naam, user principal name en verwijderings datum.
- Een nieuw totaal aantal gebruikers dat wordt bijgewerkt met Zoek opdrachten of filters.

Zie [verbeteringen voor gebruikers beheer (preview) in azure Active Directory](../users-groups-roles/users-search-enhanced.md)voor meer informatie.

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nieuw notitie veld voor zakelijke toepassingen

**Type:** Nieuwe functie  
**Service categorie:** **Product mogelijkheden** van ENTER prise apps: SSO

U kunt gratis tekst notities toevoegen aan zakelijke toepassingen. U kunt alle relevante informatie toevoegen die u helpt bij het beheer van toepassingen onder bedrijfs toepassingen. Zie [Quick Start: eigenschappen configureren voor een toepassing in uw Azure Active Directory-Tenant (Azure AD)](../manage-apps/add-application-portal-configure.md)voor meer informatie. 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-september 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In september 2020 zijn de volgende 34 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[VMware horizon-Unified Access Gateway](), [Pulse Secure pc's](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [gender geschiktheid](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [grammaticaal](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [Raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [mobiele kluis](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [soonlijk Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [beveiligde ondertekening Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [beveiligde ondertekening Enterprise Portal Aad-installatie](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec online](https://wisteconline.com/auth/oidc), [Oracle people-protected by F5 BIG-IP apm](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden: https://aka.ms/AppsTutorial .

Lees de volgende informatie voor het weer geven van uw toepassing in de app-galerie van Azure AD: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nieuwe delegering functie in azure AD-rechts beheer: Access package Assignment Manager

**Type:** Nieuwe functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
Er is een nieuwe rol voor toegangs pakket toewijzings beheer toegevoegd aan Azure AD-rechts beheer om gedetailleerde machtigingen te bieden voor het beheren van toewijzingen. U kunt nu taken overdragen aan een gebruiker met deze rol, die het beheer van toewijzingen van een toegangs pakket kan delegeren aan een zakelijke eigenaar. Een toegangs pakket toewijzings beheer kan echter het toegangs pakket beleid of andere eigenschappen die door de beheerders zijn ingesteld, niet wijzigen. 

Met deze nieuwe rol profiteert u van de mini maal benodigde bevoegdheden voor het delegeren van beheer van toewijzingen en het onderhouden van beheer beheer voor alle andere configuraties van het toegangs pakket. Zie [rechten beheer rollen](../governance/entitlement-management-delegate.md#entitlement-management-roles)voor meer informatie.
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Wijzigingen in de onboarding-stroom van Privileged Identity Management

**Type:** Gewijzigde functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Voorheen heeft het onboarding van Privileged Identity Management (PIM) vereiste gebruikers toestemming en een onboarding-stroom in de Blade PIM opgenomen die de inschrijving in azure MFA bijwerkt. Met de recente integratie van PIM-ervaring op de Blade rollen en beheerders van Azure AD, wordt deze ervaring verwijderd. Elke Tenant met een geldige P2-licentie wordt automatisch onboarded naar PIM.

Onboarding naar PIM heeft geen direct schadelijk effect op uw Tenant. U kunt de volgende wijzigingen verwachten:
- Aanvullende toewijzings opties zoals actief versus. in aanmerking komen als begin-en eind tijd wanneer u een toewijzing maakt in de Blade PIM of Azure AD-rollen en Administrators. 
- Extra bereik mechanismen, zoals administratieve eenheden en aangepaste rollen, worden direct in de toewijzings ervaring geïntroduceerd. 
- Als u een globale beheerder of beheerder van een bevoorrechte rol bent, kunt u beginnen met het ophalen van enkele extra e-mail berichten zoals het wekelijks samen vatting van PIM. 
- U ziet mogelijk ook MS-PIM-Service-Principal in het controle logboek dat betrekking heeft op de roltoewijzing. Deze verwachte wijziging is niet van invloed op uw normale werk stroom.

 Zie [beginnen met privileged Identity Management](../privileged-identity-management/pim-getting-started.md)voor meer informatie.

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Beheer van rechten van Azure AD: in het deel venster met toegangs pakket resources worden nu standaard de resources weer gegeven die momenteel in de geselecteerde catalogus staan

**Type:** Gewijzigde functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 

In de stroom voor het maken van het toegangs pakket, onder het tabblad Resource rollen, wordt het gedrag van het deel venster selecteren gewijzigd. Op dit moment is het standaard gedrag om alle resources weer te geven die eigendom zijn van de gebruiker en resources die zijn toegevoegd aan de geselecteerde catalogus. 

Deze ervaring wordt zodanig gewijzigd dat er standaard alleen de resources worden weer gegeven die momenteel aan de catalogus zijn toegevoegd, zodat gebruikers eenvoudig resources uit de catalogus kunnen kiezen. De update helpt u bij het detecteren van de resources om deze toe te voegen aan toegangs pakketten, en vermindert het risico van het per ongeluk toevoegen van resources die eigendom zijn van de gebruiker die geen deel uitmaakt van de catalogus. Zie [een nieuw toegangs pakket maken in azure AD-rechts beheer](../governance/entitlement-management-access-package-create.md#resource-roles)voor meer informatie.
 
---

## <a name="august-2020"></a>Augustus 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Updates voor Azure Multi-Factor Authentication-server-firewall vereisten

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Vanaf 1 oktober 2020 zijn voor de Azure MFA Server-firewall vereisten extra IP-adresbereiken vereist.

Als u uitgaande firewall regels in uw organisatie hebt, werkt u de regels bij zodat uw MFA-servers kunnen communiceren met alle benodigde IP-bereiken. De IP-bereiken zijn gedocumenteerd in [vereisten voor Azure multi-factor Authentication-Server-firewall](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Aanstaande wijzigingen in de gebruikers ervaring van de identiteit beveiligde Score

**Type:** Plan voor wijziging  
**Service categorie:** **Product mogelijkheid** voor identiteits beveiliging: identiteits beveiliging & beveiliging

De portal voor identiteits beveiliging wordt bijgewerkt met de wijzigingen die zijn aangebracht in de [nieuwe release](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)van de micro soft Secure Score. 

De preview-versie met de wijzigingen is aan het begin van september beschikbaar. De wijzigingen in de preview-versie zijn onder andere:
- De naam van de beveiligde score voor identiteits beveiliging is gewijzigd in ' beveiligde score voor identiteit ' voor een merk uitlijning met een beveiligde Score van micro soft
- Punten genormaliseerd naar standaard schaal en gerapporteerd in procenten in plaats van punten

In deze preview kunnen klanten scha kelen tussen de bestaande ervaring en de nieuwe ervaring. Deze preview gaat door tot eind november 2020. Na de preview worden de klanten automatisch omgeleid naar de nieuwe UX-ervaring.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nieuwe beperkte machtigingen voor gast toegang in azure AD-open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Access Control   
**Product mogelijkheden:** Gebruikers beheer

Er zijn machtigingen op mapniveau bijgewerkt voor gast gebruikers. Met deze machtigingen kunnen beheerders aanvullende beperkingen en besturings elementen vereisen voor toegang tot externe gast gebruikers. Beheerders kunnen nu aanvullende beperkingen voor de toegang van externe gasten toevoegen aan het profiel en de lidmaatschaps gegevens van gebruikers en groepen. Met deze open bare preview-functie kunnen klanten externe gebruikers toegang op schaal beheren door groepslid maatschappen te maken, met inbegrip van het beperken van gast gebruikers om lidmaatschappen te zien van de groep (en) waarin ze zich bevinden.

Zie [beperkte gast toegangs machtigingen](../users-groups-roles/users-restrict-guest-permissions.md) en [gebruikers standaard machtigingen](./users-default-permissions.md)voor meer informatie.
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Algemene Beschik baarheid van Delta query's voor service-principals

**Type:** Nieuwe functie  
**Service categorie:** MS Graph  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
Microsoft Graph Delta query ondersteunt nu het resource type in v 1.0:
- Service-principal

Clients kunnen nu op efficiënte wijze wijzigingen in deze bronnen bijhouden en de beste oplossing bieden voor het synchroniseren van wijzigingen aan deze resources met een lokale gegevens opslag. Zie [Delta query gebruiken om wijzigingen in Microsoft Graph gegevens](/graph/delta-query-overview)bij te houden voor informatie over het configureren van deze resources in een query.
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Algemene Beschik baarheid van Delta query's voor oAuth2PermissionGrant

**Type:** Nieuwe functie  
**Service categorie:** MS Graph  
**Product mogelijkheden:** Ontwikkelaars ervaring

Microsoft Graph Delta query ondersteunt nu het resource type in v 1.0:
- OAuth2PermissionGrant

Clients kunnen nu wijzigingen in deze bronnen bijhouden en de beste oplossing bieden voor het synchroniseren van wijzigingen aan deze resources met een lokale gegevens opslag. Zie [Delta query gebruiken om wijzigingen in Microsoft Graph gegevens](/graph/delta-query-overview)bij te houden voor informatie over het configureren van deze resources in een query.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-augustus 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In augustus 2020 zijn de volgende 25 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365-connector](http://enlyft.com/), [Serraview Space-gebruiks software oplossingen](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [zichtbaar](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum-Courseware-analyses: exact pad](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix naleving en vastleg ging](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GreenLight Enter prise Control platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec](https://www.clearance.network/), beschik [Wandera End User](https://www.wandera.com/) bare [ISAM](../saas-apps/isams-tutorial.md) [, VeraSMART,](../saas-apps/verasmart-tutorial.md) [Amiko,](https://amiko.web.rivero.app/) [twingate,](https://auth.twingate.com/signup) [trechter leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect) [Scalefusion](https://scalefusion.com/users/sign_in/) [Bpanda](https://goto.bpanda.com/login) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Voor het weer geven van uw toepassing in de app-galerie van Azure AD raadpleegt u de details hier https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Resource forests nu beschikbaar voor Azure AD DS 

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services   
**Product mogelijkheden:** Azure AD Domain Services
 
De mogelijkheid van bron-forests in Azure AD Domain Services is nu algemeen beschikbaar. U kunt nu autorisatie inschakelen zonder wachtwoord hash-synchronisatie om Azure AD Domain Services te gebruiken, inclusief smartcard autorisatie. Zie voor meer informatie [replica sets-concepten en-functies voor Azure Active Directory Domain Services (preview)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regionale replica-ondersteuning voor Azure AD DS beheerde domeinen nu beschikbaar

**Type:** Nieuwe functie   
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services
 
U kunt een beheerd domein uitbreiden als u meer dan één replicaset per Azure AD-tenant wilt hebben. Replica sets kunnen worden toegevoegd aan elk gekoppeld virtueel netwerk in een Azure-regio die Azure AD Domain Services ondersteunt. Extra replica sets in verschillende Azure-regio's bieden geografisch herstel na noodgeval voor oudere toepassingen als een Azure-regio offline gaat. Zie voor meer informatie [replica sets-concepten en-functies voor Azure Active Directory Domain Services (preview)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Algemene Beschik baarheid van Azure AD-aanmeldingen

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Azure AD mijn aanmeldingen is een nieuwe functie waarmee zakelijke gebruikers hun aanmeldings geschiedenis kunnen bekijken om te controleren of er ongebruikelijke activiteiten zijn. Daarnaast kunnen eind gebruikers met deze functie "This was ik" of "Ik ben ik" niet op verdachte activiteiten rapporteren. Zie voor meer informatie over het gebruik van deze functie [uw recente aanmeldings activiteit bekijken en doorzoeken vanaf de pagina Mijn aanmeldingen](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Door SAP SuccessFactors HR gestuurde gebruikers inrichten voor Azure AD is nu algemeen verkrijgbaar

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
U kunt SAP SuccessFactors nu integreren als de gezaghebbende identiteits bron met Azure AD en de end-to-end identiteits levenscyclus automatiseren met HR-gebeurtenissen zoals nieuwe huur-en beëindigings functies voor het inrichten en het ongedaan maken van de inrichting van accounts in azure AD. 

Raadpleeg de zelf studie [Configure SAP SuccessFactors to Active Directory User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)voor meer informatie over het configureren van SAP SuccessFactors inkomend inrichten voor Azure AD.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Aangepaste open-ID Connect MS Graph API-ondersteuning voor Azure AD B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Voorheen kunnen aangepaste open ID Connect-providers alleen worden toegevoegd of beheerd via de Azure Portal. De Azure AD B2C klanten kunnen nu ook de bèta versie van Microsoft Graph Api's toevoegen en beheren. Zie [Identity provider resource type](/graph/api/resources/identityprovider?view=graph-rest-beta)voor meer informatie over het configureren van deze bron met api's.
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Ingebouwde Azure AD-rollen toewijzen aan Cloud groepen

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control

U kunt nu Azure AD ingebouwde rollen toewijzen aan Cloud groepen met deze nieuwe functie. U kunt bijvoorbeeld de share point-beheerdersrol toewijzen aan Contoso_SharePoint_Admins groep. U kunt ook PIM gebruiken om de groep een in aanmerking komend lid van de rol te maken, in plaats van permanente toegang toe te kennen. Zie [Cloud groepen gebruiken om roltoewijzingen te beheren in azure Active Directory (preview)](../users-groups-roles/roles-groups-concept.md)voor meer informatie over het configureren van deze functie.
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>De ingebouwde rol voor Insights Business Leader is nu beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
Gebruikers in de rol inzichten bedrijfs leider hebben toegang tot een set Dash boards en inzichten via de [M365 Insights-toepassing](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Dit omvat volledige toegang tot alle Dash boards en weer gegeven inzicht in de functionaliteit voor het verkennen van gegevens. Gebruikers met deze rol hebben echter geen toegang tot de configuratie-instellingen van het product. Dit is de verantwoordelijkheid van de rol Insights-beheerder. Zie [machtigingen voor beheerdersrol in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-business-leader) voor meer informatie over deze rol.
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Ingebouwde rol Insights-beheerder is nu beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
Gebruikers met de rol inzichten beheerder hebben toegang tot de volledige set beheer mogelijkheden in de [M365 Insights-toepassing](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Een gebruiker met deze rol kan Directory gegevens lezen, service status controleren, bestands ondersteuning en toegang tot de aspecten van de Insights-beheerders instellingen. Zie [machtigingen voor beheerdersrol in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-administrator) voor meer informatie over deze rol.
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Toepassings beheerder en Cloud toepassings beheerder kunnen uitbreidings eigenschappen van toepassingen beheren

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
Voorheen kon alleen de globale beheerder de extensie- [eigenschap](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)beheren. Deze mogelijkheid wordt nu ook ingeschakeld voor de toepassings beheerder en de beheerder van de Cloud toepassing.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 hotfix 4.6.263.0 en connectors 1.1.1301.0

**Type:** Gewijzigde functie  
**Service categorie:** Microsoft Identity Manager  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

Er is een hotfixcombinatiepakket [(build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Dit pakket bevat updates voor de MIM CM, MIM Synchronization Manager en PAM-onderdelen. Daarnaast bevat de 1.1.1301.0 van MIM generic-connectors updates voor de Graph connector.

---
 
## <a name="july-2020"></a>Juli 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Als IT-beheerder wil ik client-apps richten met behulp van voorwaardelijke toegang

**Type:** Plan voor wijziging   
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Met de GA-versie van de voor waarde client-apps in voorwaardelijke toegang wordt nieuw beleid nu standaard toegepast op alle client toepassingen. Dit omvat verouderde verificatie-clients. Bestaande beleids regels blijven ongewijzigd, maar de wissel knop *Ja/Nee* wordt verwijderd uit bestaande beleids regels om eenvoudig te zien welke client-apps door het beleid worden toegepast. 

Wanneer u een nieuw beleid maakt, moet u ervoor zorgen dat u gebruikers en service accounts uitsluit die nog steeds gebruikmaken van verouderde verificatie; Als u dit niet doet, worden ze geblokkeerd. [Meer informatie](https://aka.ms/caclientapps).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Aanstaande SCIM-nalevings correcties

**Type:** Plan voor wijziging  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De Azure AD-inrichtings service maakt gebruik van de SCIM-standaard voor de integratie met toepassingen. Onze implementatie van de SCIM-standaard is in ontwikkeling en we verwachten dat we wijzigingen aanbrengen in het gedrag van het uitvoeren van PATCH bewerkingen en de eigenschap "actief" voor een resource instellen. [Meer informatie](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>De instelling groeps eigenaar op de Azure-beheer portal wordt gewijzigd

**Type:** Plan voor wijziging  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

De pagina instellingen van eigenaar van groepen kan worden geconfigureerd om machtigingen voor de toewijzing van de eigenaar te beperken tot een beperkte groep gebruikers in de Azure-beheer Portal en het toegangs venster. Binnenkort kunnen we de bevoegdheid groeps eigenaars toewijzen niet alleen op deze twee UX-portals, maar ook het beleid op de back-end afdwingen om consistent gedrag te bieden over eind punten, zoals Power shell en Microsoft Graph. 

We gaan de huidige instelling uitschakelen voor de klanten die deze niet gebruiken en bieden een optie om gebruikers in de komende maanden de bevoegdheid van de groeps eigenaars te bereiken. Zie voor hulp bij het bijwerken van groeps instellingen de groeps gegevens bewerken met behulp van [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>De ondersteuning voor Azure Active Directory registratie service wordt beëindigd voor TLS 1,0 en 1,1

**Type:** Plan voor wijziging  
**Service categorie:** Apparaatregistratie en-beheer  
**Product mogelijkheden:** Onafhankelijk

Trans port Layer Security (TLS) 1,2 en update servers en clients zullen binnenkort communiceren met Azure Active Directory Device Registration service. Ondersteuning voor TLS 1,0 en 1,1 voor communicatie met de Azure AD Device Registration-service wordt buiten gebruik stellen:
- Op 31 augustus 2020 in alle soevereine Clouds (GCC High, DoD, enz.)
- Op 30 oktober 2020 in alle commerciële Clouds

Meer [informatie](../devices/reference-device-registration-tls-1-2.md) over TLS 1,2 voor de Azure AD-registratie service.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows hello voor bedrijven-aanmeldingen worden weer gegeven in Logboeken van Azure AD-aanmelding

**Type:** Vaste  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Met Windows hello voor bedrijven kunnen eind gebruikers zich aanmelden bij Windows-computers met een penbeweging (zoals een pincode of biometrische). Azure AD-beheerders kunnen Windows hello voor bedrijven-aanmeldingen onderscheiden van andere Windows-aanmeldingen als onderdeel van de reis naar wacht woordloze verificatie. 

Beheerders kunnen nu zien of een Windows-verificatie gebruikt Windows hello voor bedrijven door het tabblad verificatie gegevens voor een Windows-aanmeld gebeurtenis op de Blade Azure AD-aanmeldingen in de Azure Portal te controleren. Windows hello voor bedrijven-authenticaties bevatten ' WindowsHelloForBusiness ' in het veld verificatie methode. Raadpleeg de [documentatie bij aanmeld logboeken](../reports-monitoring/concept-sign-ins.md)voor meer informatie over het interpreteren van aanmeldings Logboeken.
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Oplossingen voor het verwijderen van groepen en prestatie verbeteringen

**Type:** Vaste  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Voorheen werd het groeps object niet verwijderd wanneer een groep is gewijzigd van ' in-Scope ' naar ' out-of-scope ' en een beheerder op opnieuw starten heeft geklikt voordat de wijziging is voltooid. Het groeps object wordt nu verwijderd uit de doel toepassing wanneer deze buiten het bereik valt (uitgeschakeld, verwijderd, niet-toegewezen of het bereik filter niet heeft door gegeven). [Meer informatie](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Open bare Preview: beheerders kunnen nu aangepaste inhoud in het e-mail bericht toevoegen aan revisoren bij het maken van een toegangs beoordeling

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Identity governance
 
Wanneer een nieuwe toegangs beoordeling wordt gemaakt, ontvangt de revisor een e-mail met de vraag om de toegangs beoordeling te volt ooien. Veel klanten hebben gevraagd om aangepaste inhoud toe te voegen aan het e-mail bericht, zoals contact gegevens of andere aanvullende ondersteunings inhoud om de revisor te begeleiden. 

Beheerders kunnen nu in open bare preview aangepaste inhoud opgeven in het e-mail bericht dat wordt verzonden naar revisors door inhoud toe te voegen aan de sectie ' Geavanceerd ' van Azure AD-toegangs Beoordelingen. Zie [een toegangs beoordeling van groepen en toepassingen maken in azure AD-toegangs beoordelingen](../governance/create-access-review.md)voor hulp bij het maken van toegangs Beoordelingen.
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Autorisatie code stroom voor apps met één pagina beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
Vanwege de moderne Cookie beperkingen van derden, zoals Safari ITP, moet SPAs de autorisatie code stroom gebruiken in plaats van de impliciete stroom om SSO te onderhouden, en MSAL.js v 2. x wordt nu de autorisatie code stroom ondersteund. 

Er zijn overeenkomstige updates voor de Azure Portal, zodat u uw beveiligd-wachtwoord verificatie kunt bijwerken met het type Spa en de auth-code stroom moet gebruiken. Zie [gebruikers aanmelden en een toegangs token verkrijgen in een Java script-beveiligd-wachtwoord verificatie met behulp van de auth-code stroom](../develop/quickstart-v2-javascript-auth-code.md) voor verdere instructies.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD-toepassingsproxy ondersteunt nu de Extern bureaublad-services-webclient

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Azure AD-toepassingsproxy ondersteunt nu de Extern bureaublad-services (RDS)-webclient. Met de RDS-webclient kunnen gebruikers toegang krijgen tot Extern bureaublad-infra structuur via elke HTLM5 browser zoals micro soft Edge, Internet Explorer 11, Google Chrome, enzovoort. Gebruikers kunnen met externe apps of Bureau bladen werken, zoals bij een lokaal apparaat. Met behulp van Azure AD-toepassingsproxy kunt u de beveiliging van uw RDS-implementatie verbeteren door pre-authenticatie en beleid voor voorwaardelijke toegang af te dwingen voor alle typen uitgebreide client-apps. Zie [extern bureaublad publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)voor meer informatie.
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Volgende generatie Azure AD B2C gebruikers stromen in open bare preview

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Dankzij de vereenvoudigde ervaring voor gebruikers stroom kunt u de functie pariteit gebruiken met preview-functies en is de start voor alle nieuwe functies. Gebruikers kunnen nieuwe functies binnen dezelfde gebruikers stroom inschakelen, waardoor er minder nood zaak is om meerdere versies te maken met elke nieuwe functie versie. Ten slotte vereenvoudigt de nieuwe gebruikers vriendelijke UX de selectie en het maken van gebruikers stromen. Probeer het nu door [een gebruikers stroom te maken](../../active-directory-b2c/tutorial-create-user-flows.md). 

Zie [gebruikers stroom versies in azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows )voor meer informatie over gebruikers stromen.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-juli 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In juli 2020 zijn de volgende 55 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Clap Your handen](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor kluis](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect Connect](https://app.templafy.com/), [PeterConnects receptie](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [munten constructie Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [terugdraaien](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [communautair Spark](../saas-apps/community-spark-tutorial.md), [Chatwork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data hub eenmalige aanmelding](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [uitgang](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [rechter Cyber beveiliging ADI](https://right-hand.ai/), [Fyde Enter prise-verificatie](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [conrise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme bot](https://tellme365liteweb.azurewebsites.net/), [inspireren](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML connector](https://www.strata.io/identity-fabric/), [Smartschool (school beheersysteem)](https://smart-schoolapp.com/frmLoginForm), [Zepto-intelligent keurige](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp Kemp](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack eenmalige aanmelding](../saas-apps/browserstack-single-sign-on-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Lees de informatie hier om uw toepassing in de app-galerie van Azure AD weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-juli 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor de zojuist geïntegreerde app [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md).

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Roltoewijzingen weer geven voor alle bereiken en de mogelijkheid om deze te downloaden naar een CSV-bestand

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
U kunt nu roltoewijzingen weer geven voor alle bereiken voor een rol in het tabblad ' rollen en beheerders ' in de Azure AD-Portal. U kunt ook de roltoewijzingen voor elke rol naar een CSV-bestand downloaden. Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)voor hulp bij het weer geven en toevoegen van roltoewijzingen.
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Afschaffing van Azure Multi-Factor Authentication Software Development (Azure MFA SDK)

**Type:** Keur  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De Azure Multi-Factor Authentication Software Development (Azure MFA SDK) heeft het einde van de levens cyclus bereikt op 14 november 2018, zoals het eerst werd aangekondigd in november 2017. Micro soft zal de SDK-Service vanaf 30 september 2020 afsluiten. Aanroepen van de SDK zullen mislukken.

Als uw organisatie de Azure MFA SDK gebruikt, moet u de migratie op 30 september 2020:
- Azure MFA SDK voor MIM: als u de SDK met MIM gebruikt, moet u migreren naar Azure MFA-server en Privileged Access Management (PAM) activeren volgens deze [instructies](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK voor aangepaste apps: overweeg uw app te integreren in azure AD en gebruik voorwaardelijke toegang om MFA af te dwingen. Bekijk deze [pagina](../manage-apps/plan-an-application-integration.md)om aan de slag te gaan. 

---

## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Gebruikers risico voorwaarde in beleid voor voorwaardelijke toegang

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 

Met de ondersteuning voor gebruikers Risico's in het beleid voor voorwaardelijke toegang van Azure AD kunt u meerdere beleids regels voor gebruikers risico maken. Er zijn verschillende minimale gebruikers risico niveaus vereist voor verschillende gebruikers en apps. Op basis van het risico van gebruikers kunt u beleids regels maken om de toegang te blok keren, multi-factor Authentication, een beveiligd wacht woord wijzigen of omleiden naar Microsoft Cloud App Security om sessie beleid af te dwingen, zoals aanvullende controle.

Voor de gebruikers risico voorwaarde is Azure AD Premium P2 vereist, omdat het gebruikmaakt van Azure Identity Protection, een P2-aanbieding. Raadpleeg de [documentatie voor voorwaardelijke toegang van Azure AD](../conditional-access/index.yml)voor meer informatie over voorwaardelijke toegang.

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO ondersteunt nu apps waarvoor SPNameQualifier moet worden ingesteld wanneer daarom wordt gevraagd

**Type:** Vaste  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Voor sommige SAML-toepassingen moet SPNameQualifier worden geretourneerd in de bewering wanneer daarom wordt gevraagd. Azure AD reageert nu correct wanneer er een SPNameQualifier is aangevraagd in het beleid voor NameID van aanvragen. Dit geldt ook voor door SP geïnitieerde aanmelding en de IdP-aanmelding wordt gevolgd.  Zie het [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md)voor meer informatie over het SAML-protocol in azure Active Directory.

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B-samen werking biedt ondersteuning voor het uitnodigen van MSA-en Google-gebruikers in Azure Government tenants

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 

Azure Government tenants die gebruikmaken van de B2B-samenwerkings functies kunnen nu gebruikers uitnodigen met een micro soft-of Google-account. Als u wilt weten of uw Tenant deze mogelijkheden kan gebruiken, volgt u de instructies op [Hoe kan ik zien of B2B-samen werking beschikbaar is in mijn Azure US Government-Tenant?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Gebruikers object in MS Graph v1 bevat nu de eigenschappen externalUserState en externalUserStateChangedDateTime

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 

De eigenschappen externalUserState en externalUserStateChangedDateTime kunnen worden gebruikt om uitgenodigde B2B-gasten te vinden die hun uitnodigingen nog niet hebben geaccepteerd, evenals het bouwen van automatisering, zoals het verwijderen van gebruikers die hun uitnodigingen na een aantal dagen niet hebben geaccepteerd. Deze eigenschappen zijn nu beschikbaar in MS Graph v1. Raadpleeg het [resource type](/graph/api/resources/user?view=graph-rest-1.0)van de gebruiker voor hulp bij het gebruik van deze eigenschappen.
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Verificatie sessies beheren in azure AD voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Met de beheer mogelijkheden voor verificatie sessies kunt u configureren hoe vaak uw gebruikers aanmeldings referenties moeten opgeven en of ze referenties moeten opgeven na het sluiten en opnieuw openen van browsers om meer beveiliging en flexibiliteit in uw omgeving te bieden.
 
Daarnaast wordt het beheer van verificatie sessies alleen toegepast op de eerste factor Authentication op Azure AD join, hybride Azure AD toegevoegd en geregistreerde Azure AD-apparaten. Verificatie sessie beheer is nu ook van toepassing op MFA. Zie [verificatie sessie beheer met voorwaardelijke toegang configureren](../conditional-access/howto-conditional-access-session-lifetime.md)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-juni 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In juni 2020 zijn de volgende 29 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [mailgates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [gate keeper](https://www.gatekeeperhq.com/), [hub planner](../saas-apps/hub-planner-tutorial.md), [Ansira-partner go-to-Market-werkset](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [kisi fysieke beveiliging](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [Zero](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [edX voor bedrijven SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [veld-id](../saas-apps/field-id-tutorial.md), [curriculum SAML](../saas-apps/curricula-saml-tutorial.md) [,](https://smallstep.com/sso-ssh/) [Perforce Helix core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance](https://cloud.metacompliance.com/)  

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial . Raadpleeg de volgende informatie voor het weer geven van uw toepassing in de app-galerie van Azure AD: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>API-connectors voor externe identiteiten selfservice aanmelding is nu beschikbaar als open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Met API-connectors voor externe identiteiten kunt u gebruikmaken van web-Api's voor het integreren van self-service registratie met externe Cloud systemen. Dit betekent dat u nu web-Api's kunt aanroepen als specifieke stappen in een registratie stroom voor het activeren van op de cloud gebaseerde aangepaste werk stromen. U kunt bijvoorbeeld API-connectors gebruiken voor het volgende:

- Integreren met een aangepaste goedkeurings werk stroom.
- Identiteits controle uitvoeren
- Gebruikers invoer gegevens valideren
- Gebruikers kenmerken overschrijven
- Aangepaste bedrijfs logica uitvoeren

Zie [API-connectors gebruiken voor het aanpassen en uitbreiden van self-service registratie](../external-identities/api-connectors-overview.md), of [het aanpassen van externe identiteiten selfservice aanmelding met Web API-integraties](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)voor meer informatie over alle mogelijkheden van API-connectors.
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>On-demand inrichten en gebruikers in een paar seconden in uw apps ophalen

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De Azure AD-inrichtings service werkt momenteel op cyclische basis. De service wordt elke 40 minuten uitgevoerd. Met de [functie voor inrichting op aanvraag](https://aka.ms/provisionondemand) kunt u een gebruiker kiezen en deze in enkele seconden inrichten. Met deze mogelijkheid kunt u snel inrichtings problemen oplossen zonder opnieuw op te starten om ervoor te zorgen dat de inrichtings cyclus opnieuw wordt gestart. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nieuwe machtiging voor het gebruik van het beheer van rechten van Azure AD in Graph

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer rechten
 
Er is een nieuwe gedelegeerde machtiging EntitlementManagement. Read. all is nu beschikbaar voor gebruik met de API voor rechten beheer in Microsoft Graph bèta. Zie [werken met de Azure AD-API voor rechten beheer](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)voor meer informatie over de beschik bare api's.

---

### <a name="identity-protection-apis-available-in-v10"></a>Api's voor identiteits beveiliging die beschikbaar zijn in v 1.0

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De riskyUsers-en riskDetections-Microsoft Graph Api's zijn nu algemeen beschikbaar. Nu ze beschikbaar zijn op het eind punt van de v 1.0, nodigen we u uit om ze in productie te gebruiken. Raadpleeg de [Microsoft Graph-documenten](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)voor meer informatie.
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Gevoeligheids labels voor het Toep assen van beleid op Microsoft 365 groepen is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking
 

U kunt nu gevoeligheids labels maken en de label instellingen gebruiken om beleid toe te passen op Microsoft 365 groepen, waaronder privacy (openbaar of privé) en beleid voor externe gebruikers toegang. U kunt een label met het privacybeleid persoonlijk maken en beleid voor externe gebruikers toegang om gast gebruikers niet toe te staan. Wanneer een gebruiker dit label toepast op een groep, is de groep privé en kunnen gast gebruikers niet aan de groep worden toegevoegd. 

Gevoeligheids labels zijn belang rijk voor het beveiligen van uw bedrijfs kritieke gegevens en u kunt op een compatibele en veilige manier groepen op schaal beheren. Zie voor hulp bij het gebruik van gevoeligheids labels de [labels voor gevoeligheid toewijzen aan Microsoft 365 groepen in azure Active Directory (preview)](../users-groups-roles/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Updates voor de ondersteuning van Microsoft Identity Manager voor Azure AD Premium klanten

**Type:** Gewijzigde functie  
**Service categorie:** Microsoft Identity Manager  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Ondersteuning voor Azure is nu beschikbaar voor Azure AD-integratie onderdelen van Microsoft Identity Manager 2016, tot het einde van uitgebreide ondersteuning voor Microsoft Identity Manager 2016. Meer informatie vindt [u in de ondersteunings update voor Azure AD Premium klanten die gebruikmaken van Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Het gebruik van de voor waarden voor groepslid maatschappen in de SSO-claim configuratie is verhoogd

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Voorheen werd het aantal groepen dat u zou kunnen gebruiken wanneer u de claims op basis van het groepslid maatschap in een configuratie van één toepassing voorwaardelijk wijzigt, beperkt tot 10. Het gebruik van de voor waarden voor groepslid maatschappen in de SSO-claim configuratie is nu verhoogd naar Maxi maal 50 groepen. Raadpleeg voor meer informatie over het configureren van claims de configuratie van de [SSO-claim voor bedrijfs toepassingen](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Het inschakelen van basis opmaak op het onderdeel tekst van de aanmeldings pagina in de huis stijl van het bedrijf.

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
De functionaliteit voor het huis stijl van het bedrijf op de Azure AD/Microsoft 365-aanmeld ervaring is bijgewerkt, zodat de klant hyper links en eenvoudige opmaak kan toevoegen, waaronder vet letter type, onderstrepen en cursief. Zie [huisstijl toevoegen aan de aanmeldings pagina Azure Active Directory van uw organisatie](./customize-branding.md)voor meer informatie over het gebruik van deze functie.

---

### <a name="provisioning-performance-improvements"></a>Prestatie verbeteringen inrichten

**Type:** Gewijzigde functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De inrichtings service is bijgewerkt om de tijd te verkorten om een [incrementele cyclus](../app-provisioning/how-provisioning-works.md#incremental-cycles) te volt ooien. Dit betekent dat gebruikers en groepen sneller worden ingericht in hun toepassingen dan voorheen. Alle nieuwe inrichtings taken die zijn gemaakt na 6/10/2020, profiteren automatisch van de prestatie verbeteringen. Alle toepassingen die zijn geconfigureerd voor het inrichten vóór 6/10/2020, moeten na 6/10/2020 opnieuw worden opgestart om te profiteren van de prestatie verbeteringen. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Aankondiging van de afschaffing van ADAL en MS Graph-pariteit

**Type:** Keur  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Levenscyclus beheer van apparaten

Nu de micro soft-verificatie bibliotheken (MSAL) beschikbaar zijn, worden er geen nieuwe functies meer toegevoegd aan de Azure Active Directory authenticatie bibliotheken (ADAL) en worden er op 30 juni 2022 beveiligings patches afgesloten. Raadpleeg [toepassingen migreren naar micro soft Authentication Library (MSAL)](../develop/msal-migration.md)voor meer informatie over het migreren naar MSAL.

Daarnaast hebben we het werk voltooid om alle functionaliteit van Azure AD Graph beschikbaar te maken via MS Graph. Azure AD Graph-Api's ontvangen daarom alleen bugfix-en beveiligings oplossingen tot en met 30 juni 2022. Zie [uw toepassingen bijwerken voor het gebruik van micro soft-verificatie bibliotheek en Microsoft Graph-API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) voor meer informatie
 
---
 
## <a name="may-2020"></a>Mei 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Buiten gebruik stellen van eigenschappen in aanmeldingen-, riskyUsers-en riskDetections-Api's

**Type:** Plan voor wijziging  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op dit moment worden opgesomde typen gebruikt om de eigenschap riskType in de riskDetections-API en riskyUserHistoryItem (in Preview) weer te geven. Opgesomde typen worden ook gebruikt voor de eigenschap riskEventTypes in de aanmeldingen-API. Deze eigenschappen worden weer gegeven als teken reeksen. 

Klanten moeten overstappen op de eigenschap riskEventType in de API Beta riskDetections en riskyUserHistoryItem, en naar riskEventTypes_v2 eigenschap in de Beta aanmeldingen API van 9 september 2020. Op die datum wordt de huidige riskType-en riskEventTypes-eigenschappen buiten gebruik gesteld. Zie [wijzigingen in de eigenschappen van de risico gebeurtenis en api's voor identiteits beveiliging op Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)voor meer informatie.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Afschaffing van de eigenschap riskEventTypes in de aanmeldingen v 1.0 API op Microsoft Graph

**Type:** Plan voor wijziging  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Opgesomde typen worden overgeschakeld naar teken reeks typen bij het weer geven van eigenschappen van risico gebeurtenissen in Microsoft Graph september 2020. Deze wijziging is niet alleen van invloed op de preview-Api's en is ook van invloed op de in-Production aanmeldingen-API.

We hebben een nieuwe riskEventsTypes_v2-eigenschap (teken reeks) geïntroduceerd in de API van aanmeldingen v 1.0. De huidige riskEventTypes (enum)-eigenschap op 11 juni 2022 wordt buiten gebruik gesteld volgens ons Microsoft Graph afschaffing-beleid. Klanten moeten op 11 juni 2022 overstappen op de eigenschap riskEventTypes_v2 in de API van aanmeldingen v 1.0. Raadpleeg de [eigenschap riskEventTypes in aanmeldingen v 1.0 API op Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)voor meer informatie.

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Aanstaande wijzigingen in MFA-e-mail meldingen

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 

De volgende wijzigingen worden in de e-mail meldingen voor Cloud MFA aangebracht:

E-mail meldingen worden verzonden vanaf het volgende adres: azure-noreply@microsoft.com en msonlineservicesteam@microsoftonline.com . De inhoud van e-mail berichten voor fraude bestrijding worden bijgewerkt om beter te kunnen aangeven wat de vereiste stappen zijn voor het deblokkeren van het gebruik.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nieuwe self-service registratie voor gebruikers in federatieve domeinen die geen toegang hebben tot micro soft-teams omdat ze niet zijn gesynchroniseerd met Azure Active Directory.

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 

Op dit moment kunnen gebruikers die zich in domeinen bevinden in azure AD, maar die niet zijn gesynchroniseerd met de Tenant, geen toegang krijgen tot teams. Vanaf het einde van juni kan deze nieuwe functionaliteit dit doen door de bestaande registratie functie voor geverifieerde e-mail uit te breiden. Hierdoor kunnen gebruikers die zich aanmelden bij een federatieve IdP, maar die nog geen gebruikers object hebben in azure ID, een gebruikers object automatisch maken en worden geverifieerd voor teams. Hun gebruikers object wordt als ' self-service registreren ' gemarkeerd. Dit is een uitbrei ding van de bestaande mogelijkheid om e-mail berichten te laten verifiëren die gebruikers in beheerde domeinen kunnen uitvoeren en kunnen worden beheerd met dezelfde vlag. Deze wijziging wordt tijdens de volgende twee maanden voltooid. Kijk [hier](../users-groups-roles/directory-self-service-signup.md)voor documentatie-updates.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Aanstaande oplossing: het detectie document voor OIDC voor de Azure Government Cloud wordt bijgewerkt om te verwijzen naar de juiste grafiek eindpunten.

**Type:** Plan voor wijziging  
**Service categorie:** Soevereine Clouds  
**Product mogelijkheden:** Gebruikers verificatie
 
Vanaf juni zal het OIDC Discovery-document [micro soft Identity platform en OpenID Connect Connect protocol](../develop/v2-protocols-oidc.md) op het [Azure Government Cloud](../develop/authentication-national-cloud.md) -eind punt (login.microsoftonline.us) het juiste nationale eind punt voor de [Cloud grafiek](/graph/deployments) retour neren ( https://graph.microsoft.us of https://dod-graph.microsoft.us) , op basis van de beschik bare Tenant.  Het bevat momenteel het onjuiste grafiek eindpunt (graph.microsoft.com) msgraph_host veld.  

Deze fout oplossing wordt geleidelijk ongeveer 2 maanden uitgerold.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government gebruikers zich niet meer kunnen aanmelden bij login.microsoftonline.com

**Type:** Plan voor wijziging  
**Service categorie:** Soevereine Clouds  
**Product mogelijkheden:** Gebruikers verificatie
 
Op 1 juni 2018 is de officiële Azure Active Directory-instantie (Azure AD) voor Azure Government gewijzigd van https://login-us.microsoftonline.com naar https://login.microsoftonline.us . Als u eigenaar bent van een toepassing binnen een Azure Government-Tenant, moet u uw toepassing bijwerken om gebruikers in te schrijven op het eind punt. VS.

Vanaf 5 mei is Azure AD bezig met het afdwingen van de wijziging van het eind punt, waardoor Azure Government gebruikers zich kunnen aanmelden bij apps die worden gehost in Azure Government tenants met behulp van het open bare eind punt (microsoftonline.com). Met betrokken apps wordt een fout AADSTS900439-USGClientNotSupportedOnPublicEndpoint weer gegeven. 

Er wordt een geleidelijke implementatie van deze wijziging met betrekking tot de afdwinging die naar verwachting is voltooid voor alle apps juni 2020. Zie het [blog bericht Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)voor meer informatie.

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>De aanvraag voor eenmalige SAML-afmelding verzendt nu de juiste indeling voor NameID

**Type:** Vaste  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Wanneer een gebruiker op Afmelden klikt (bijvoorbeeld in de MyApps-Portal), verzendt Azure AD een single-afmeldings bericht via SAML naar elke app die actief is in de gebruikers sessie en dat er een afmeldings-URL is geconfigureerd. Deze berichten bevatten een NameID in een permanente indeling.

Als het oorspronkelijke SAML-aanmeldings token een andere indeling heeft gebruikt voor NameID (bijvoorbeeld e-mail/UPN), kan de SAML-app niet correleren in het afmeldings bericht naar een bestaande sessie (omdat de NameIDs die in beide berichten wordt gebruikt, anders is), waardoor het afmeldings bericht wordt veroorzaakt door de SAML-app en de gebruiker aangemeld blijft. Met deze oplossing wordt het afmeldings bericht consistent met het NameID dat is geconfigureerd voor de toepassing.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>De rol hybride identiteits beheerder is nu beschikbaar met Cloud inrichting

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-Cloud inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
IT-beheerders kunnen beginnen met het gebruik van de nieuwe rol hybride beheerder als de minst privileged Role voor het instellen van Azure ADConnect Cloud provisioning. Met deze nieuwe rol hoeft u niet langer de rol van globale beheerder te gebruiken voor het instellen en configureren van Cloud inrichting. [Meer informatie](../users-groups-roles/roles-delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-mei 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In mei 2020 hebben we de volgende 36 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC record Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-ingeschakeld](https://b-engaged.se/), [Competella contact centrum werk groep](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft identiteit](https://identity.imagesoftinc.com/), [mijn IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [wijzigings proces beheer](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [intelligent Global governance](../saas-apps/smart-global-governance-tutorial.md), [PREZI](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enter prise service platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO voor confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO voor BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO voor bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii,](../saas-apps/torii-tutorial.md) [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [humanisatie](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpander,](../saas-apps/textexpander-tutorial.md) [iedereen Home CRM](../saas-apps/anyone-home-crm-tutorial.md) [, askSpoke,](../saas-apps/askspoke-tutorial.md) [Ice contact centrum](../saas-apps/ice-contact-center-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial .

Lees de informatie in de app-galerie van Azure AD voor het weer geven van uw toepassing https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

[Met de modus alleen rapport voor voorwaardelijke toegang van Azure AD](../conditional-access/concept-conditional-access-report-only.md) kunt u het resultaat van een beleid evalueren zonder dat er toegangs beheer wordt afgedwongen. U kunt alleen beleids regels voor rapporten in uw organisatie testen en hun impact op hun eigen effect controleren, zodat de implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie van de modus alleen rapport weer gegeven, maar 26M gebruikers zijn al in het bereik van een beleid voor alleen een rapport. Met de aankondiging van vandaag worden nieuwe beleids regels voor voorwaardelijke toegang van Azure AD standaard in de modus alleen rapport gemaakt. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die de MS Graph-Api's gebruiken, kunt u ook op [een programmatische manier beleids regels voor rapporten beheren](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Self-service registratie voor gast gebruikers

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Met externe identiteiten in azure AD kunt u personen buiten uw organisatie toegang verlenen tot uw apps en resources, terwijl ze zich aanmelden met behulp van de identiteit van hun voor keur. Wanneer u een toepassing deelt met externe gebruikers, weet u mogelijk niet altijd vooraf wie toegang tot de toepassing nodig heeft. Met de [self-service registratie](../external-identities/self-service-sign-up-overview.md)kunt u gast gebruikers in staat stellen zich aan te melden en een gast account te verkrijgen voor uw LOB-apps (line-of-Business). De registratie stroom kan worden gemaakt en aangepast ter ondersteuning van Azure AD en sociale identiteiten. U kunt ook aanvullende informatie over de gebruiker verzamelen tijdens het aanmelden.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap voor inzichten en rapporten over voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De [werkmap inzichten en rapporten](../conditional-access/howto-conditional-access-insights-reporting.md) biedt beheerders een samen vatting van de voorwaardelijke toegang van Azure AD in hun Tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en eventuele wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in een paar minuten [na deze instructies](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)kunt instellen. Om het dash board beter detecteerbaar te maken, is het naar het tabblad nieuw inzicht en rapportage in het menu voorwaardelijke toegang van Azure AD verplaatst.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>De Blade beleids Details voor voorwaardelijke toegang is beschikbaar als open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op de [Blade nieuwe beleids Details](../conditional-access/troubleshoot-conditional-access.md) worden de toewijzingen, voor waarden en besturings elementen weer gegeven die zijn voldaan tijdens de evaluatie van het voorwaardelijke toegangs beleid. U kunt de Blade openen door een rij te selecteren op de tabbladen voorwaardelijke toegang of alleen rapport met alleen rapporten van de aanmeldings gegevens.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nieuwe query mogelijkheden voor Active Directory-objecten in Microsoft Graph zijn beschikbaar in de open bare preview

**Type:** Nieuwe functie  
**Service categorie:** MS Graph- **product mogelijkheid:** ervaring voor ontwikkel aars

Er worden nieuwe mogelijkheden geïntroduceerd voor Microsoft Graph Directory-object-Api's, het inschakelen van Count-, Zoek-, filter-en sorteer bewerkingen. Dit biedt ontwikkel aars de mogelijkheid om snel een query uit te brengen op onze Directory-objecten zonder tijdelijke oplossingen zoals in-Memory filtering en sorteren. Meer informatie vindt u in dit [blog bericht](https://aka.ms/CountFilterMSGraphAAD).

We zijn momenteel beschikbaar als open bare preview-versie en zoeken naar feedback. Stuur uw opmerkingen met deze [korte enquête](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Eenmalige aanmelding op basis van SAML configureren met behulp van Microsoft Graph-API (bèta)

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Ondersteuning voor het maken en configureren van een toepassing vanuit de Azure AD-galerie met behulp van MS Graph-Api's in Beta is nu beschikbaar. Als u eenmalige aanmelding op basis van SAML moet instellen voor meerdere exemplaren van een toepassing, kunt u tijd besparen door de Microsoft Graph-Api's te gebruiken om [de configuratie van eenmalige aanmelding op basis van SAML te automatiseren](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-mei 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure-console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML-token versleuteling is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Met [SAML-token versleuteling](../manage-apps/howto-saml-token-encryption.md) kunnen toepassingen worden geconfigureerd voor het ontvangen van versleutelde SAML-bevestigingen. De functie is nu algemeen beschikbaar in alle Clouds.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Groeps naam claims in toepassings tokens is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
De groeps claims die in een token zijn uitgegeven, kunnen nu worden beperkt tot alleen de groepen die zijn toegewezen aan de toepassing.  Dit is vooral belang rijk wanneer gebruikers lid zijn van een groot aantal groepen en er sprake is van een risico dat de limieten voor de token grootte worden overschreden. Als deze nieuwe mogelijkheid is ingesteld, is de mogelijkheid om [groeps namen aan tokens toe te voegen](../hybrid/how-to-connect-fed-group-claims.md) algemeen beschikbaar.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Voor de werkdag wordt nu ondersteuning geboden voor het instellen van werk telefoonnummer kenmerken

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
We hebben de app voor het terugschrijven van werk dagen uitgebreid om nu write-back van telefoon nummer en mobiele nummer kenmerken te ondersteunen. Naast e-mail en gebruikers naam kunt u nu de werk stroom voor het terugschrijven van werk dagen configureren voor het door sturen van telefoon nummer waarden van Azure AD naar workday. Raadpleeg voor meer informatie over het configureren van telefoon nummer terugschrijven de hand leiding voor het [terugschrijven van workday](https://aka.ms/WorkdayWriteback) -apps. 

---

### <a name="publisher-verification-preview"></a>Uitgever verificatie (preview-versie)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
De verificatie van de uitgever (preview) helpt beheerders en eind gebruikers inzicht te krijgen in de echtheid van toepassings ontwikkelaars die integreren met het micro soft Identity-platform. Raadpleeg de [Publisher-verificatie (preview)](../develop/publisher-verification-overview.md)voor meer informatie.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Autorisatie code stroom voor apps met één pagina

**Type:** Gewijzigde functie **service categorie:** verificatie **product mogelijkheid:** ontwikkelaars ervaring

Vanwege de moderne [Cookie beperkingen van derden, zoals Safari ITP](../develop/reference-third-party-cookies-spas.md), moet Spas de autorisatie code stroom gebruiken in plaats van de impliciete stroom om SSO te onderhouden. MSAL.js v 2. x ondersteunt nu de autorisatie code stroom. Er zijn gerelateerde updates voor de Azure Portal zodat u uw beveiligd-wachtwoord verificatie kunt bijwerken met het type ' Spa ' en de auth-code stroom moet gebruiken. Raadpleeg voor meer informatie [Quick Start: gebruikers aanmelden en een toegangs token verkrijgen in een Java script Spa met behulp van de verificatie code stroom](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Verbeterde filtering voor apparaten is beschikbaar in de open bare preview

**Type:** Gewijzigde functie   
**Service categorie:** **Product functionaliteit** voor Apparaatbeheer: levenscyclus beheer van apparaten
 
Voorheen werden de enige filters die u zou kunnen gebruiken ' ingeschakeld ' en ' activiteit datum '. Nu kunt u [uw lijst met apparaten filteren op meer eigenschappen](../devices/device-management-azure-portal.md#device-list-filtering-preview), waaronder type besturings systeem, type samen voeging, naleving en meer. Deze toevoegingen moeten het zoeken naar een bepaald apparaat vereenvoudigen.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>De nieuwe App-registraties-ervaring voor Azure AD B2C is nu algemeen verkrijgbaar

**Type:** Gewijzigde functie   
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De nieuwe App-registraties-ervaring voor Azure AD B2C is nu algemeen beschikbaar. 

Voorheen moest u uw B2C-consumenten toepassingen onafhankelijk van de rest van uw apps beheren met behulp van de oudere toepassingen. Dat heeft verschillende ervaring voor het maken van apps op verschillende locaties in Azure.

De nieuwe ervaring toont alle B2C en Azure AD-App-registraties op één locatie en biedt een consistente manier om deze te beheren. Of u nu een klant gerichte app of een app wilt beheren die toegang heeft tot Microsoft Graph om Azure AD B2C resources programmatisch te beheren, maar u hoeft slechts één manier te leren om dingen te doen.

U kunt de nieuwe ervaring bereiken door te navigeren door de Azure AD B2C-service en de App-registraties-Blade te selecteren. De ervaring is ook toegankelijk vanuit de Azure Active Directory-service.

De Azure AD B2C App-registraties-ervaring is gebaseerd op de [ervaring](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) voor de registratie van algemene apps voor Azure AD-tenants, maar is afgestemd op Azure AD B2C. De verouderde-ervaring voor toepassingen wordt in de toekomst afgeschaft.

Ga naar [de nieuwe app registratie-ervaring voor Azure AD B2C](https://aka.ms/b2cappregtraining)voor meer informatie.

---

## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>De registratie-ervaring voor gecombineerde beveiligings gegevens is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De gecombineerde registratie-ervaring voor Multi-Factor Authentication (MFA) en self-service voor wachtwoord herstel (SSPR) is nu algemeen beschikbaar. Met deze nieuwe registratie-ervaring kunnen gebruikers zich registreren voor MFA en SSPR in één stap-voor-stap proces. Wanneer u de nieuwe ervaring voor uw organisatie implementeert, kunnen gebruikers zich minder tijd en minder moeite registreren. Bekijk [hier](https://bit.ly/3etiRyQ)het blog bericht.

---

### <a name="continuous-access-evaluation"></a>Evaluatie van voortdurende toegang

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Evaluatie van doorlopende toegang is een nieuwe beveiligings functie die bijna in realtime beleids handhaving mogelijk maakt voor relying party's die Azure AD-toegangs tokens gebruiken wanneer er gebeurtenissen optreden in azure AD (zoals het verwijderen van een gebruikers account). Deze functie wordt eerst geïmplementeerd voor teams en Outlook-clients. Lees onze [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) en de  [documentatie](./concept-fundamentals-continuous-access-evaluation.md)voor meer informatie.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-aanmelding: Firstline-werk nemers kunnen zich aanmelden bij toepassingen met Azure AD-back-ups met hun telefoon nummer en zonder wacht woord

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Gebruikers verificatie

Office start een reeks mobiele, zakelijke apps die zijn vrijmaken voor niet-traditionele organisaties en aan werk nemers in grote organisaties die geen e-mail gebruiken als hun primaire communicatie methode. Deze apps richten zich op Frontline werk nemers, Deskless-werk nemers, veld agenten of werk nemers van de winkel, die mogelijk geen e-mail adres van hun werk gever ontvangen, toegang hebben tot een computer of naar de andere. Met dit project kunnen deze werk nemers zich aanmelden bij zakelijke toepassingen door een telefoon nummer en roundtripping een code in te voeren. Raadpleeg voor meer informatie onze [beheer documentatie](../authentication/howto-authentication-sms-signin.md) en documentatie voor [eind gebruikers](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Interne gebruikers uitnodigen voor het gebruik van B2B-samen werking

**Type:** Nieuwe functie

**Service categorie:** Business

**Product mogelijkheden:**

De functie voor B2B-uitnodiging wordt uitgebreid zodat bestaande interne accounts kunnen worden uitgenodigd om B2B-samenwerkings referenties vooruit te gebruiken. Dit doet u door het gebruikers object door te geven aan de API voor uitnodigen naast de gebruikelijke para meters zoals het e-mail adres dat u hebt uitgenodigd. De object-ID van de gebruiker, de UPN, het groepslid maatschap, de app-toewijzing, enzovoort blijven intact, maar ze worden doorgestuurd met B2B voor verificatie met hun Tenant referenties in plaats van de interne referenties die ze vóór de uitnodiging hebben gebruikt. Raadpleeg de [documentatie](../external-identities/invite-internal-users.md)voor meer informatie.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

[Met de modus alleen rapport voor voorwaardelijke toegang van Azure AD](../conditional-access/concept-conditional-access-report-only.md) kunt u het resultaat van een beleid evalueren zonder dat er toegangs beheer wordt afgedwongen. U kunt alleen beleids regels voor rapporten in uw organisatie testen en hun impact op hun eigen effect controleren, zodat de implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie van de modus alleen rapport gezien, met meer dan 26M gebruikers die reeds het bereik van een alleen-rapport beleid hebben. Met deze aankondiging wordt standaard het beleid voor voorwaardelijke toegang van Azure AD in de modus alleen rapport gemaakt. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die de MS Graph Api's gebruiken, kunt u ook beleid voor [alleen een rapport beheren via een programma](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap voor inzichten en rapporten over voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De Insights- [werkmap](../conditional-access/howto-conditional-access-insights-reporting.md) voor voorwaardelijke toegang biedt beheerders een samen vatting van de voorwaardelijke toegang van Azure AD in hun Tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en eventuele wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in een paar minuten [na deze instructies](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)kunt instellen. Om het dash board beter detecteerbaar te maken, is het naar het tabblad nieuw inzicht en rapportage in het menu voorwaardelijke toegang van Azure AD verplaatst.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>De Blade beleids Details voor voorwaardelijke toegang is beschikbaar als open bare preview

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op de [Blade nieuwe beleids Details](../conditional-access/troubleshoot-conditional-access.md) worden de toewijzingen, voor waarden en besturings elementen weer gegeven die zijn voldaan tijdens de evaluatie van het voorwaardelijke toegangs beleid. U kunt de Blade openen door een rij te selecteren op de tabbladen **voorwaardelijke toegang** of **alleen rapport met alleen rapporten** van de aanmeldings gegevens.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-april 2020

**Type:** Nieuwe functie

**Service categorie:** Zakelijke apps

**Product capaciteit:** integratie van derden

In april 2020 hebben we deze 31 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[SincroPool apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT inkoop Suite](../saas-apps/iwt-procurement-suite-tutorial.md) [Lunni](https://lunni.fi/), [EasySSO voor Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki dash board](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365](https://app.mover.io/login)-overschakeling, [spreker](https://speakerengage.com/login.php), [honestly](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 mensen](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [harmonie](../saas-apps/harmony-tutorial.md), [Timetabling oplossingen](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet Klik](../saas-apps/synchronet-click-tutorial.md), [Empower](https://www.made-in-office.com/en/), [veertig](../saas-apps/fortes-change-cloud-tutorial.md) [, litmus,](../saas-apps/litmus-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDb Cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS leren](../saas-apps/tickitlms-learn-tutorial.md), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro productiviteits pakket](../saas-apps/nitro-productivity-suite-tutorial.md) , [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph Delta-query ondersteuning voor oAuth2PermissionGrant beschikbaar voor open bare preview

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor oAuth2PermissionGrant is beschikbaar voor de open bare preview. U kunt nu wijzigingen bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. [Meer informatie.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph ondersteuning voor Delta query's voor contact personen in de organisatie algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor contact personen in de organisatie is algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. Vervang bestaande code die continu orgContact-gegevens doorzoekt door de Delta query om de prestaties aanzienlijk te verbeteren. [Meer informatie.](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph Delta-query ondersteuning voor de toepassing algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor toepassingen is over het algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. Vervang bestaande code die continu toepassings gegevens doorzoekt op Delta query's om de prestaties aanzienlijk te verbeteren. [Meer informatie.](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph Delta-query ondersteuning voor beheerders eenheden die beschikbaar zijn voor open bare preview

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** De Delta-query voor ontwikkelaars ervaring voor beheer eenheden is beschikbaar voor open bare preview. U kunt nu wijzigingen bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. [Meer informatie.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Telefoon nummers voor verificatie en meer beheren in nieuwe Microsoft Graph bèta-Api's

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

Deze Api's zijn een belang rijk hulp programma voor het beheren van de verificatie methoden van uw gebruikers. Nu kunt u de verificators die worden gebruikt voor MFA en self-service voor wachtwoord herstel (SSPR), vooraf registreren en beheren. Dit is een van de meest aangevraagde functies in de Azure MFA-, SSPR-en Microsoft Graph-ruimten. De nieuwe Api's die we in deze Wave hebben uitgebracht, bieden u de volgende mogelijkheden:

- De verificatie-telefoons van een gebruiker lezen, toevoegen, bijwerken en verwijderen
- Het wacht woord van een gebruiker opnieuw instellen
- SMS-aanmelden in-of uitschakelen

Zie [Azure AD-verificatie methoden API overview](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)(Engelstalig) voor meer informatie.

---

### <a name="administrative-units-public-preview"></a>Open bare preview van administratieve eenheden

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

Met beheereenheden kunt u beheerdersmachtigingen verlenen die zijn beperkt tot een afdeling, een regio of een ander door u gedefinieerd segment van uw organisatie. U kunt beheereenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om een beleid op een gedetailleerd niveau in te stellen. Beheerders van gebruikersaccounts kunnen bijvoorbeeld profielgegevens bijwerken, wachtwoorden opnieuw instellen en licenties toewijzen voor uitsluitend gebruikers die in hun beheereenheid zijn opgenomen.

Met beheer eenheden kan een centrale beheerder:

- Een administratieve eenheid maken voor gedecentraliseerd beheer van resources
- Een rol met beheerders machtigingen alleen toewijzen via Azure AD-gebruikers in een beheer eenheid
- Vul de beheer eenheden met gebruikers en groepen in als dat nodig is

Zie beheer [eenheden beheren in azure Active Directory (preview)](https://aka.ms/AdminUnitsDocs)voor meer informatie.

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Ingebouwde rollen van de printer beheerder en de printer technicus

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

**Printer beheerder**: gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printer configuraties in de micro soft-oplossing voor universele afdrukken beheren, met inbegrip van de instellingen voor de universele afdruk connector. Ze kunnen toestemming geven voor alle gedelegeerde afdruk machtigings aanvragen. Printer beheerders hebben ook toegang tot het afdrukken van rapporten. 

**Printer technicus**: gebruikers met deze rol kunnen printers registreren en printer status beheren in de micro soft Universal Print-oplossing. Ze kunnen ook alle connector gegevens lezen. Belangrijkste taken een printer technicus kan geen gebruikers machtigingen voor printers instellen en printers delen. [Meer informatie.](../users-groups-roles/directory-assign-admin-roles.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ingebouwde rol voor hybride identiteits beheerder

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

Gebruikers met deze rol kunnen services en instellingen met betrekking tot het inschakelen van hybride identiteit in azure AD inschakelen, configureren en beheren. Deze rol biedt de mogelijkheid om Azure AD te configureren voor een van de drie ondersteunde verificatie methoden&#8212;wacht woord-hash synchronisatie (PHS), Pass-Through-verificatie (PTA) of Federatie (AD FS of externe Federatie provider) &#8212;en een gerelateerde on-premises infra structuur te implementeren om deze in te scha kelen. On-premises infra structuur omvat het inrichten en PTA agents. Deze rol biedt de mogelijkheid om naadloze eenmalige aanmelding (S-SSO) in te scha kelen voor naadloze verificatie op niet-Windows 10-apparaten of niet-Windows Server 2016-computers. Daarnaast biedt deze rol de mogelijkheid om aanmeldings logboeken te bekijken en de status en analyse te openen voor het bewaken en oplossen van problemen. [Meer informatie.](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ingebouwde rol netwerk beheerder

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

Gebruikers met deze rol kunnen aanbevelingen van de netwerk architectuur beoordelen van micro soft die zijn gebaseerd op telemetrie van het netwerk vanaf hun gebruikers locaties. De netwerk prestaties voor Microsoft 365 zijn gebaseerd op een zorgvuldige netwerk verbindings architectuur van de Enter prise-klant. Dit is doorgaans een gebruikersspecifieke locatie. Deze rol maakt het mogelijk om de gedetecteerde gebruikers locaties en configuratie van de netwerk parameters voor die locaties te bewerken, zodat de telemetriegegevens en ontwerp aanbevelingen kunnen worden verbeterd. [Meer informatie.](../users-groups-roles/directory-assign-admin-roles.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Bulk activiteit en down loads in de Azure AD-beheer Portal

**Type:** Nieuwe functie

**Service categorie:** Gebruikers beheer

**Product mogelijkheden:** Uitvoermap

U kunt nu bulk activiteiten uitvoeren op gebruikers en groepen in azure AD door een CSV-bestand te uploaden in de Azure AD-beheer Portal. U kunt gebruikers maken, gebruikers verwijderen en gast gebruikers uitnodigen. En u kunt leden toevoegen aan en verwijderen uit een groep.

U kunt ook lijsten van Azure AD-resources downloaden vanuit de Azure AD-beheer Portal. U kunt de lijst met gebruikers in de Directory, de lijst met groepen in de Directory en de leden van een bepaalde groep downloaden.

Voor meer informatie raadpleegt u het volgende:

- [Gebruikers maken](../users-groups-roles/users-bulk-add.md) of [gast gebruikers uitnodigen](../external-identities/tutorial-bulk-invite.md)
- [Gebruikers verwijderen](../users-groups-roles/users-bulk-delete.md) of [Verwijderde gebruikers herstellen](../users-groups-roles/users-bulk-restore.md)
- [Lijst met gebruikers downloaden](../users-groups-roles/users-bulk-download.md) of [lijst met groepen downloaden](../users-groups-roles/groups-bulk-download.md)
- [Leden toevoegen (importeren)](../users-groups-roles/groups-bulk-import-members.md) of [leden verwijderen](../users-groups-roles/groups-bulk-remove-members.md) of de [lijst met leden](../users-groups-roles/groups-bulk-download-members.md) voor een groep downloaden

---

### <a name="my-staff-delegated-user-management"></a>Mijn personeel heeft gebruikers beheer gedelegeerd

**Type:** Nieuwe functie

**Service categorie:** Gebruikers beheer

**Product mogelijkheden:**

Mijn personeel stelt Firstline-managers, zoals een Store Manager, in staat om ervoor te zorgen dat hun mede werkers toegang hebben tot hun Azure AD-accounts. In plaats van te vertrouwen op een Central-Help Desk kunnen organisaties algemene taken, zoals het opnieuw instellen van wacht woorden of het wijzigen van telefoon nummers, overdragen aan een Firstline Manager. Met mijn mede werkers kunnen gebruikers die geen toegang hebben tot hun account, in slechts enkele klikken toegang krijgen, zonder dat er een helpdesk medewerker of IT-personeel nodig is. Zie voor meer informatie het [beheren van uw gebruikers met mijn personeel (preview)](https://aka.ms/MyStaffAdminDocs) en het [delegeren van gebruikers beheer met mijn personeel (preview)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Een bijgewerkte ervaring voor eind gebruikers in toegangs beoordelingen

**Type:** Gewijzigde functie

**Service categorie:** Toegangs beoordelingen

**Product mogelijkheden:** Identity governance

We hebben de revisor ervaring voor Azure AD-toegangs beoordelingen bijgewerkt in de portal mijn apps. Aan het einde van april zien uw revisoren die zijn aangemeld bij de Azure AD-toegangs beoordelingen, een banner dat hen in staat stelt om de bijgewerkte ervaring in mijn toegang te proberen. Houd er rekening mee dat de bijgewerkte ervaring voor toegangs beoordelingen dezelfde functionaliteit biedt als de huidige ervaring, maar met een verbeterde gebruikers interface bovenop nieuwe functies zodat uw gebruikers productief kunnen zijn. [Hier vindt u meer informatie over de bijgewerkte ervaring](../governance/perform-access-review.md). Deze open bare preview gaat tot eind juli 2020. Aan het einde van juli worden revisoren die zich niet bij de preview-ervaring hebben aangemeld, automatisch omgeleid naar mijn toegang om toegangs beoordelingen uit te voeren. Als u wilt dat uw revisoren de preview-versie in mijn Access nu blijven gebruiken, kunt u [hier een aanvraag](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)indienen.

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Werkdag inkomende gebruikers inrichten en write-back-apps ondersteunen nu de nieuwste versies van de workday Web Services-API

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** 

Op basis van feedback van klanten hebben we nu de werk dagen inkomend gebruikers inrichten en write-back-apps bijgewerkt in de galerie met zakelijke apps om de nieuwste versies van de WWS-API (workday Web Services) te ondersteunen. Met deze wijziging kunnen klanten de WWS API-versie opgeven die ze willen gebruiken in de connection string. Dit biedt klanten de mogelijkheid om meer HR-kenmerken op te halen die beschikbaar zijn in de releases van workday. In de workday-app voor het terugschrijven wordt nu gebruikgemaakt van de aanbevolen Change_Work_Contact_Info workday-webservice om de beperkingen van Maintain_Contact_Info te overwinnen.

Als er in de connection string geen versie is opgegeven, blijven de inkomende inrichtings-apps van de werkdag WWS v 21.1 gebruiken om over te scha kelen naar de laatste workday-Api's voor inkomend gebruikers inrichten, moeten klanten de connection string bijwerken zoals beschreven [in de zelf studie](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) en ook de XPath-waarden bijwerken die worden gebruikt voor workday-kenmerken zoals beschreven in de [Naslag Gids voor kenmerk dagen](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Als u de nieuwe API voor terugschrijven wilt gebruiken, zijn er geen wijzigingen vereist in de app write-inrichtings toepassing. Zorg ervoor dat op de dag van de workday het ISU-account (voor de werkdag Integration System) machtigingen heeft voor het aanroepen van het Change_Work_Contact bedrijfs proces zoals beschreven in de sectie zelf studie, de machtigingen voor het [beveiligings beleid voor het bedrijfs proces configureren](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

We hebben onze [hand leiding voor de zelf studie](../saas-apps/workday-inbound-tutorial.md) bijgewerkt met de nieuwe API-versie ondersteuning.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gebruikers met een standaard functie voor toegang zijn nu het bereik voor het inrichten

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** Beheer van identiteits levenscyclus

Gebruikers met de standaard-Access-rol hebben in het verleden geen bereik voor het inrichten. We hebben feedback gehoord die klanten willen dat gebruikers met deze rol binnen het bereik van het inrichten moeten zijn. Vanaf 16 april 2020 kunnen gebruikers met alle nieuwe inrichtings configuraties de standaard-Access-rol inrichten. Geleidelijk zullen we het gedrag voor bestaande inrichtings configuraties wijzigen om het inrichten van gebruikers met deze rol te ondersteunen. [Meer informatie.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>De gebruikers interface voor inrichting is bijgewerkt

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** Beheer van identiteits levenscyclus

Onze inrichtings ervaring is vernieuwd om een beter gerichtere beheer weergave te maken. Wanneer u navigeert naar de Blade inrichten voor een bedrijfs toepassing die al is geconfigureerd, kunt u de voortgang van het inrichten en beheren van acties op eenvoudige wijze bewaken, zoals het starten, stoppen en opnieuw starten van het inrichten. [Meer informatie.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Validatie van dynamische groeps regel is nu beschikbaar voor open bare preview

**Type:** Gewijzigde functie

**Service categorie:** Groeps beheer

**Product mogelijkheden:** Werking

Azure Active Directory (Azure AD) biedt nu de mogelijkheid om dynamische groeps regels te valideren. Op het tabblad **regels valideren** kunt u uw dynamische regel valideren op basis van voor beelden van groeps leden om te bevestigen dat de regel werkt zoals verwacht. Bij het maken of bijwerken van dynamische groeps regels willen beheerders weten of een gebruiker of een apparaat lid is van de groep. Dit helpt u te evalueren of een gebruiker of apparaat voldoet aan de regel criteria en hulp bij het oplossen van problemen wanneer het lidmaatschap niet wordt verwacht.

Zie [een dynamische regel voor groepslid maatschap valideren (preview)](../users-groups-roles/groups-dynamic-rule-validation.md)voor meer informatie.

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identiteits veilige Score-beveiligings standaards en updates voor de actie voor MFA-verbetering

**Type:** Gewijzigde functie

**Service categorie:** n.v.t.

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

**Ondersteuning voor de standaard instellingen voor de verbetering van de beveiliging van Azure AD:** Met micro soft Secure Score worden verbeteringen voor de verbetering bijgewerkt zodat de [standaard instellingen van de beveiliging in azure AD](./concept-fundamentals-security-defaults.md)worden ondersteund, waardoor het eenvoudiger wordt om uw organisatie te beschermen met vooraf geconfigureerde beveiligings instellingen voor veelvoorkomende aanvallen. Dit is van invloed op de volgende verbeterings acties:

- Zorg ervoor dat alle gebruikers multi-factor Authentication voor beveiligde toegang kunnen volt ooien
- MFA vereisen voor beheerders rollen
- Beleid inschakelen voor het blok keren van verouderde verificatie
 
**Updates voor de actie voor MFA-verbetering:** Voor de nood zaak van bedrijven om te zorgen voor de beste beveiliging bij het Toep assen van beleid dat samenwerkt met hun bedrijf, heeft micro soft Secure Score drie verbeteringen voor verbetering verwijderd die zijn gecentreerd rond multi-factor Authentication en twee worden toegevoegd.

Verbeterings acties verwijderd:

- Alle gebruikers registreren voor multi-factor Authentication
- MFA vereisen voor alle gebruikers
- MFA vereisen voor Azure AD-privileged roles

Toegevoegde acties voor verbetering:

- Zorg ervoor dat alle gebruikers multi-factor Authentication voor beveiligde toegang kunnen volt ooien
- MFA vereisen voor beheerders rollen

Voor deze nieuwe maat regelen voor verbetering moet u uw gebruikers of beheerders registreren voor multi-factor Authentication (MFA) in uw directory en de juiste set beleids regels instellen die voldoen aan de behoeften van uw organisatie. Het belangrijkste doel is om de flexibiliteit te bieden en ervoor te zorgen dat al uw gebruikers en beheerders kunnen verifiëren met meerdere factoren of prompts voor identiteits verificatie op basis van een risico. Dit kan de vorm hebben van meerdere beleids regels waarmee beslissingen met een bereik worden toegepast, of het instellen van standaard instellingen voor beveiliging (vanaf 16 maart) waarmee micro soft beslist wanneer gebruikers voor MFA moeten worden aangevraagd. [Lees meer over wat er nieuw is in micro soft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---
