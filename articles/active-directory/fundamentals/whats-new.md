---
title: Wat is nieuw? Release opmerkingen-Azure Active Directory | Microsoft Docs
description: Ontdek wat er nieuw is met Azure Active Directory, zoals de nieuwste opmerkingen bij de release, bekende problemen, oplossingen voor fouten, afgeschafte functionaliteit en aanstaande wijzigingen.
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
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611157"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` en te ![plakken: in uw](./media/whats-new/feed-icon-16x16.png) RSS feed reader pictogram feed lezer.

Azure AD ontvangt voortdurend verbeteringen. Dit artikel bevat informatie over het volgende om op de hoogte te blijven van de meest recente ontwikkelingen:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt. Ga daarom regel matig opnieuw te werk. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in het [Archief voor wat er nieuw is in azure Active Directory](whats-new-archive.md).

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

Evaluatie van doorlopende toegang is een nieuwe beveiligings functie die bijna in realtime beleids handhaving mogelijk maakt voor relying party's die Azure AD-toegangs tokens gebruiken wanneer er gebeurtenissen optreden in azure AD (zoals het verwijderen van een gebruikers account). Deze functie wordt eerst geïmplementeerd voor teams en Outlook-clients. Lees onze [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) en de [documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)voor meer informatie.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-aanmelding: Firstline-werk nemers kunnen zich aanmelden bij toepassingen met Azure AD-back-ups met hun telefoon nummer en zonder wacht woord

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Gebruikers verificatie

Office start een reeks mobiele, zakelijke apps die zijn vrijmaken voor niet-traditionele organisaties en aan werk nemers in grote organisaties die geen e-mail gebruiken als hun primaire communicatie methode. Deze apps richten zich op Frontline werk nemers, Deskless-werk nemers, veld agenten of werk nemers van de winkel, die mogelijk geen e-mail adres van hun werk gever ontvangen, toegang hebben tot een computer of naar de andere. Met dit project kunnen deze werk nemers zich aanmelden bij zakelijke toepassingen door een telefoon nummer en roundtripping een code in te voeren. Raadpleeg voor meer informatie onze [beheer documentatie](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) en documentatie voor [eind gebruikers](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Interne gebruikers uitnodigen voor het gebruik van B2B-samen werking

**Type:** Nieuwe functie

**Service categorie:** Business

**Product mogelijkheden:**

De functie voor B2B-uitnodiging wordt uitgebreid zodat bestaande interne accounts kunnen worden uitgenodigd om B2B-samenwerkings referenties vooruit te gebruiken. Dit doet u door het gebruikers object door te geven aan de API voor uitnodigen naast de gebruikelijke para meters zoals het e-mail adres dat u hebt uitgenodigd. De object-ID van de gebruiker, de UPN, het groepslid maatschap, de app-toewijzing, enzovoort blijven intact, maar ze worden doorgestuurd met B2B voor verificatie met hun Tenant referenties in plaats van de interne referenties die ze vóór de uitnodiging hebben gebruikt. Raadpleeg de [documentatie](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)voor meer informatie.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

[Met de modus alleen rapport voor voorwaardelijke toegang van Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) kunt u het resultaat van een beleid evalueren zonder dat er toegangs beheer wordt afgedwongen. U kunt alleen beleids regels voor rapporten in uw organisatie testen en hun impact op hun eigen effect controleren, zodat de implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie van de modus alleen rapport gezien, met meer dan 26M gebruikers die reeds het bereik van een alleen-rapport beleid hebben. Met deze aankondiging wordt standaard het beleid voor voorwaardelijke toegang van Azure AD in de modus alleen rapport gemaakt. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die de MS Graph Api's gebruiken, kunt u ook beleid voor [alleen een rapport beheren via een programma](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap voor inzichten en rapporten over voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De Insights- [werkmap](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) voor voorwaardelijke toegang biedt beheerders een samen vatting van de voorwaardelijke toegang van Azure AD in hun Tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en eventuele wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in een paar minuten [na deze instructies](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)kunt instellen. Om het dash board beter detecteerbaar te maken, is het naar het tabblad nieuw inzicht en rapportage in het menu voorwaardelijke toegang van Azure AD verplaatst.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>De Blade beleids Details voor voorwaardelijke toegang is beschikbaar als open bare preview

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op de [Blade nieuwe beleids Details](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) worden de toewijzingen, voor waarden en besturings elementen weer gegeven die zijn voldaan tijdens de evaluatie van het voorwaardelijke toegangs beleid. U kunt de Blade openen door een rij te selecteren op de tabbladen **voorwaardelijke toegang** of **alleen rapport met alleen rapporten** van de aanmeldings gegevens.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-april 2020

**Type:** Nieuwe functie

**Service categorie:** Zakelijke apps

**Product capaciteit:** integratie van derden

In april 2020 hebben we deze 31 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[SincroPool apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT inkoop Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial) [Lunni](https://lunni.fi/), [EasySSO for Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki dash board](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365](https://app.mover.io/login)-overschakeling, [sprekering](https://speakerengage.com/login.php), [honestly](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 mensen](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [harmonie](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling oplossingen](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet Klik](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [Empower](https://www.made-in-office.com/en/), [veertigen](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [litmus, GroupTalk, Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [, MongoDb](https://recorder.grouptalk.com/), [TickitLMS Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [leren](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [Nitro productiviteit suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph Delta-query ondersteuning voor oAuth2PermissionGrant beschikbaar voor open bare preview

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor oAuth2PermissionGrant is beschikbaar voor de open bare preview. U kunt nu wijzigingen bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. [Meer informatie.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph ondersteuning voor Delta query's voor contact personen in de organisatie algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor contact personen in de organisatie is algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. Vervang bestaande code die continu orgContact-gegevens doorzoekt door de Delta query om de prestaties aanzienlijk te verbeteren. [Meer informatie.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph Delta-query ondersteuning voor de toepassing algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor toepassingen is over het algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. Vervang bestaande code die continu toepassings gegevens doorzoekt op Delta query's om de prestaties aanzienlijk te verbeteren. [Meer informatie.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph Delta-query ondersteuning voor beheerders eenheden die beschikbaar zijn voor open bare preview

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** De Delta-query voor ontwikkelaars ervaring voor beheer eenheden is beschikbaar voor open bare preview. U kunt nu wijzigingen bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. [Meer informatie.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Telefoon nummers voor verificatie en meer beheren in nieuwe Microsoft Graph bèta-Api's

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

Deze Api's zijn een belang rijk hulp programma voor het beheren van de verificatie methoden van uw gebruikers. Nu kunt u de verificators die worden gebruikt voor MFA en self-service voor wachtwoord herstel (SSPR), vooraf registreren en beheren. Dit is een van de meest aangevraagde functies in de Azure MFA-, SSPR-en Microsoft Graph-ruimten. De nieuwe Api's die we in deze Wave hebben uitgebracht, bieden u de volgende mogelijkheden:

- De verificatie-telefoons van een gebruiker lezen, toevoegen, bijwerken en verwijderen
- Het wacht woord van een gebruiker opnieuw instellen
- SMS-aanmelden in-of uitschakelen

Zie [Azure AD-verificatie methoden API overview](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)(Engelstalig) voor meer informatie.

---

### <a name="administrative-units-public-preview"></a>Open bare preview van administratieve eenheden

**Type:** Nieuwe functie

**Service categorie:** RBAC

**Product mogelijkheden:** Access Control

Met beheer eenheden kunt u beheerders machtigingen verlenen die zijn beperkt tot een afdeling, regio of ander segment van uw organisatie die u definieert. U kunt administratieve eenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om beleid op een gedetailleerd niveau in te stellen. Een gebruikers account beheerder kan bijvoorbeeld profiel gegevens bijwerken, wacht woorden opnieuw instellen en licenties voor gebruikers alleen in hun administratieve eenheid toewijzen.

Met beheer eenheden kan een centrale beheerder:

- Een administratieve eenheid maken voor gedecentraliseerd beheer van resources
- Een rol met beheerders machtigingen alleen toewijzen via Azure AD-gebruikers in een beheer eenheid
- Vul de beheer eenheden met gebruikers en groepen in als dat nodig is

Zie beheer [eenheden beheren in azure Active Directory (preview)](https://aka.ms/AdminUnitsDocs)voor meer informatie.

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Ingebouwde rollen van de printer beheerder en de printer technicus

**Type:** Nieuwe functie

**Service categorie:** RBAC

**Product mogelijkheden:** Access Control

**Printer beheerder**: gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printer configuraties in de micro soft-oplossing voor universele afdrukken beheren, met inbegrip van de instellingen voor de universele afdruk connector. Ze kunnen toestemming geven voor alle gedelegeerde afdruk machtigings aanvragen. Printer beheerders hebben ook toegang tot het afdrukken van rapporten. 

**Printer technicus**: gebruikers met deze rol kunnen printers registreren en printer status beheren in de micro soft Universal Print-oplossing. Ze kunnen ook alle connector gegevens lezen. Belangrijkste taken een printer technicus kan geen gebruikers machtigingen voor printers instellen en printers delen. [Meer informatie.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ingebouwde rol voor hybride identiteits beheerder

**Type:** Nieuwe functie

**Service categorie:** RBAC

**Product mogelijkheden:** Access Control

Gebruikers met deze rol kunnen services en instellingen met betrekking tot het inschakelen van hybride identiteit in azure AD inschakelen, configureren en beheren. Deze rol biedt de mogelijkheid om Azure AD te configureren voor een van de drie ondersteunde verificatie methoden&#8212;wacht woord-hash synchronisatie (PHS), Pass-Through-verificatie (PTA) of Federatie (AD FS of externe Federatie provider) &#8212;en een gerelateerde on-premises infra structuur te implementeren om deze in te scha kelen. On-premises infra structuur omvat het inrichten en PTA agents. Deze rol biedt de mogelijkheid om naadloze eenmalige aanmelding (S-SSO) in te scha kelen voor naadloze verificatie op niet-Windows 10-apparaten of niet-Windows Server 2016-computers. Daarnaast biedt deze rol de mogelijkheid om aanmeldings logboeken te bekijken en de status en analyse te openen voor het bewaken en oplossen van problemen. [Meer informatie.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ingebouwde rol netwerk beheerder

**Type:** Nieuwe functie

**Service categorie:** RBAC

**Product mogelijkheden:** Access Control

Gebruikers met deze rol kunnen aanbevelingen van de netwerk architectuur beoordelen van micro soft die zijn gebaseerd op telemetrie van het netwerk vanaf hun gebruikers locaties. De netwerk prestaties voor Office 365 zijn afhankelijk van de zorgvuldige netwerk verbindings architectuur van de Enter prise-klant. Dit is doorgaans een gebruikersspecifieke locatie. Deze rol maakt het mogelijk om de gedetecteerde gebruikers locaties en configuratie van de netwerk parameters voor die locaties te bewerken, zodat de telemetriegegevens en ontwerp aanbevelingen kunnen worden verbeterd. [Meer informatie.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Bulk activiteit en down loads in de Azure AD-beheer Portal

**Type:** Nieuwe functie

**Service categorie:** Gebruikers beheer

**Product mogelijkheden:** Uitvoermap

U kunt nu bulk activiteiten uitvoeren op gebruikers en groepen in azure AD door een CSV-bestand te uploaden in de Azure AD-beheer Portal. U kunt gebruikers maken, gebruikers verwijderen en gast gebruikers uitnodigen. En u kunt leden toevoegen aan en verwijderen uit een groep.

U kunt ook lijsten van Azure AD-resources downloaden vanuit de Azure AD-beheer Portal. U kunt de lijst met gebruikers in de Directory, de lijst met groepen in de Directory en de leden van een bepaalde groep downloaden.

Voor meer informatie raadpleegt u het volgende:

- [Gebruikers maken](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) of [gast gebruikers uitnodigen](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Gebruikers verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) of [Verwijderde gebruikers herstellen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Lijst met gebruikers downloaden](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) of [lijst met groepen downloaden](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Leden toevoegen (importeren)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) of [leden verwijderen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) of de [lijst met leden](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) voor een groep downloaden

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

We hebben de revisor ervaring voor Azure AD-toegangs beoordelingen bijgewerkt in de portal mijn apps. Aan het einde van april zien uw revisoren die zijn aangemeld bij de Azure AD-toegangs beoordelingen, een banner dat hen in staat stelt om de bijgewerkte ervaring in mijn toegang te proberen. Houd er rekening mee dat de bijgewerkte ervaring voor toegangs beoordelingen dezelfde functionaliteit biedt als de huidige ervaring, maar met een verbeterde gebruikers interface bovenop nieuwe functies zodat uw gebruikers productief kunnen zijn. [Hier vindt u meer informatie over de bijgewerkte ervaring](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Deze open bare preview gaat tot eind juli 2020. Aan het einde van juli worden revisoren die zich niet bij de preview-ervaring hebben aangemeld, automatisch omgeleid naar mijn toegang om toegangs beoordelingen uit te voeren. Als u wilt dat uw revisoren de preview-versie in mijn Access nu blijven gebruiken, kunt u [hier een aanvraag](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)indienen.

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Werkdag inkomende gebruikers inrichten en write-back-apps ondersteunen nu de nieuwste versies van de workday Web Services-API

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** 

Op basis van feedback van klanten hebben we nu de werk dagen inkomend gebruikers inrichten en write-back-apps bijgewerkt in de galerie met zakelijke apps om de nieuwste versies van de WWS-API (workday Web Services) te ondersteunen. Met deze wijziging kunnen klanten de WWS API-versie opgeven die ze willen gebruiken in de connection string. Dit biedt klanten de mogelijkheid om meer HR-kenmerken op te halen die beschikbaar zijn in de releases van workday. In de workday-app voor het terugschrijven wordt nu gebruikgemaakt van de aanbevolen Change_Work_Contact_Info workday-webservice om de beperkingen van Maintain_Contact_Info te overwinnen.

Als er in de connection string geen versie is opgegeven, blijven de inkomende inrichtings-apps van de werkdag WWS v 21.1 gebruiken om over te scha kelen naar de laatste workday-Api's voor inkomend gebruikers inrichten, moeten klanten de connection string bijwerken zoals beschreven [in de zelf studie](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) en ook de XPath-waarden bijwerken die worden gebruikt voor workday-kenmerken zoals beschreven in de [Naslag Gids voor kenmerk dagen](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

Als u de nieuwe API voor terugschrijven wilt gebruiken, zijn er geen wijzigingen vereist in de app write-inrichtings toepassing. Zorg ervoor dat op de dag van de workday het ISU-account (voor de werkdag Integration System) machtigingen heeft voor het aanroepen van het Change_Work_Contact bedrijfs proces zoals beschreven in de sectie zelf studie, de machtigingen voor het [beveiligings beleid voor het bedrijfs proces configureren](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

We hebben onze [hand leiding voor de zelf studie](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) bijgewerkt met de nieuwe API-versie ondersteuning.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gebruikers met een standaard functie voor toegang zijn nu het bereik voor het inrichten

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** Beheer van identiteits levenscyclus

Gebruikers met de standaard-Access-rol hebben in het verleden geen bereik voor het inrichten. We hebben feedback gehoord die klanten willen dat gebruikers met deze rol binnen het bereik van het inrichten moeten zijn. Vanaf 16 april 2020 kunnen gebruikers met alle nieuwe inrichtings configuraties de standaard-Access-rol inrichten. Geleidelijk zullen we het gedrag voor bestaande inrichtings configuraties wijzigen om het inrichten van gebruikers met deze rol te ondersteunen. [Meer informatie.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>De gebruikers interface voor inrichting is bijgewerkt

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** Beheer van identiteits levenscyclus

Onze inrichtings ervaring is vernieuwd om een beter gerichtere beheer weergave te maken. Wanneer u navigeert naar de Blade inrichten voor een bedrijfs toepassing die al is geconfigureerd, kunt u de voortgang van het inrichten en beheren van acties op eenvoudige wijze bewaken, zoals het starten, stoppen en opnieuw starten van het inrichten. [Meer informatie.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Validatie van dynamische groeps regel is nu beschikbaar voor open bare preview

**Type:** Gewijzigde functie

**Service categorie:** Groeps beheer

**Product mogelijkheden:** Werking

Azure Active Directory (Azure AD) biedt nu de mogelijkheid om dynamische groeps regels te valideren. Op het tabblad **regels valideren** kunt u uw dynamische regel valideren op basis van voor beelden van groeps leden om te bevestigen dat de regel werkt zoals verwacht. Bij het maken of bijwerken van dynamische groeps regels willen beheerders weten of een gebruiker of een apparaat lid is van de groep. Dit helpt u te evalueren of een gebruiker of apparaat voldoet aan de regel criteria en hulp bij het oplossen van problemen wanneer het lidmaatschap niet wordt verwacht.

Zie [een dynamische regel voor groepslid maatschap valideren (preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)voor meer informatie.

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identiteits veilige Score-beveiligings standaards en updates voor de actie voor MFA-verbetering

**Type:** Gewijzigde functie

**Service categorie:** n.v.t.

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

**Ondersteuning voor de standaard instellingen voor de verbetering van de beveiliging van Azure AD:** Met micro soft Secure Score worden verbeteringen voor de verbetering bijgewerkt zodat de [standaard instellingen van de beveiliging in azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)worden ondersteund, waardoor het eenvoudiger wordt om uw organisatie te beschermen met vooraf geconfigureerde beveiligings instellingen voor veelvoorkomende aanvallen. Dit is van invloed op de volgende verbeterings acties:

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

Voor deze nieuwe maat regelen voor verbetering moet u uw gebruikers of beheerders registreren voor multi-factor Authentication (MFA) in uw directory en de juiste set beleids regels instellen die voldoen aan de behoeften van uw organisatie. Het belangrijkste doel is om de flexibiliteit te bieden en ervoor te zorgen dat al uw gebruikers en beheerders kunnen verifiëren met meerdere factoren of prompts voor identiteits verificatie op basis van een risico. Dit kan de vorm hebben van meerdere beleids regels waarmee beslissingen met een bereik worden toegepast, of het instellen van standaard instellingen voor beveiliging (vanaf 16 maart) waarmee micro soft beslist wanneer gebruikers voor MFA moeten worden aangevraagd. [Lees meer over wat er nieuw is in micro soft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Maart 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Onbeheerde Azure Active Directory accounts in B2B-update voor maart 2021

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
**Vanaf 31 maart 2021**heeft micro soft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure Active Directory (Azure AD)-accounts en tenants voor B2B-samenwerkings scenario's. Ter voor bereiding van dit voor deel raden we u aan om te kiezen voor [verificatie met eenmalige wachtwoord code](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gebruikers met de standaard-Access-rol zullen binnen het bereik van de inrichting vallen

**Type:** Plan voor wijziging  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Gebruikers met de standaard-Access-rol hebben in het verleden geen bereik voor het inrichten. We hebben feedback gehoord die klanten willen dat gebruikers met deze rol binnen het bereik van het inrichten moeten zijn. Er wordt gewerkt aan het implementeren van een wijziging zodat gebruikers met de standaard-Access-rol kunnen worden ingericht. Geleidelijk zullen we het gedrag voor bestaande inrichtings configuraties wijzigen om het inrichten van gebruikers met deze rol te ondersteunen. Er is geen actie van de klant vereist. Zodra deze wijziging is doorgevoerd, zullen we een update naar onze [documentatie](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) posten.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samen werking is beschikbaar in Microsoft Azure beheerd door 21Vianet-tenants (Azure China 21Vianet)

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
De samenwerkings mogelijkheden van Azure AD B2B worden beschikbaar gesteld in Microsoft Azure die worden beheerd door 21Vianet-tenants (Azure China 21Vianet), waardoor gebruikers in een Azure China 21Vianet-Tenant naadloos kunnen samen werken met gebruikers in andere Azure China 21Vianet-tenants. Meer [informatie over Azure AD B2B-samen werking](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration-uitnodiging opnieuw ontwerpen

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
De [e-mail berichten](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) die door de uitnodiging van de Azure AD B2B-samen werking worden verzonden om gebruikers uit te nodigen voor de Directory, worden opnieuw ontworpen om de informatie over de uitnodiging en de volgende stappen van de gebruiker duidelijker te maken.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Wijzigingen in het HomeRealmDiscovery-beleid worden weer gegeven in de audit logboeken

**Type:** Vaste  
**Service categorie:** Controle  
**Product mogelijkheden:** & rapportage controleren
 
Er is een fout opgelost waarbij wijzigingen in het [HomeRealmDiscovery-beleid](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) niet zijn opgenomen in de audit Logboeken. U kunt nu zien wanneer en hoe het beleid is gewijzigd en door wie. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-maart 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In 2020 maart hebben we deze 51 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML app](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [IPoint service provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.ai bol](https://contexxt-sphere.com/login), [expertise verkregen by Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [flare Digital Signing](https://spark-dev.pixelnebula.com/login), [Logz.io-Cloud waarneembaar voor technici](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano-SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge learning suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [netvision kompas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM hub](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [rib A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), Zscaler [B2B gebruikers Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [Lift](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [, WatchTeams,](https://www.devfinition.com/) [Aster](https://demo.asterapp.io/login), [vaardigheden werk stroom](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [knooppunt inzicht](https://admin.nodeinsight.com/AADLogin.aspx), [IP-platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [Invision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [pipe drive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [show case](https://app.showcaseworkshop.com/), [GreenLight Integration platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [GreenLight compliant toegangs beheer](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [, bizagi](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [Studio voor Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuitex](https://www.insuite.jp/) [, sybo, Britive](https://www.systexsoftware.com.tw/), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-dagen](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective Sdn](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn-veer boot 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [campus café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](https://aka.ms/appstutorial). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](https://aka.ms/azureadapprequest)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-samen werking beschikbaar in Azure Government tenants

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
De functies van Azure AD B2B-samen werking zijn nu beschikbaar tussen enkele Azure Government-tenants.  Als u wilt weten of uw Tenant deze mogelijkheden kan gebruiken, volgt u de instructies op [Hoe kan ik zien of B2B-samen werking beschikbaar is in mijn Azure US Government-Tenant?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integratie voor Azure-Logboeken is nu beschikbaar in Azure Government

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Azure Monitor integratie met Azure AD-Logboeken is nu beschikbaar in Azure Government. U kunt Azure AD-Logboeken (audit-en aanmeld Logboeken) door sturen naar een opslag account, Event hub en Log Analytics. Raadpleeg de [gedetailleerde documentatie](https://aka.ms/aadlogsinamd) [en implementatie plannen voor rapportage en bewaking](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) voor Azure AD-scenario's.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Vernieuwen van identiteits beveiliging in Azure Government

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

We zijn enthousiast over het delen van de vernieuwde [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) ervaring in de [Microsoft Azure Government Portal](https://portal.azure.us/). Zie voor meer informatie ons [aankondigings blog bericht](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Herstel na nood geval: uw inrichtings configuratie downloaden en opslaan

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De Azure AD-inrichtings service biedt een uitgebreide set configuratie mogelijkheden. Klanten moeten hun configuratie kunnen opslaan zodat ze deze later kunnen raadplegen of terugdraaien naar een bekende goede versie. We hebben de mogelijkheid toegevoegd om uw inrichtings configuratie te downloaden als een JSON-bestand en deze te uploaden wanneer u deze nodig hebt. [Meer informatie](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (self-service voor wacht woord opnieuw instellen) vereist nu twee poorten voor beheerders in Microsoft Azure die worden beheerd door 21Vianet (Azure China 21Vianet) 

**Type:** Gewijzigde functie  
**Service categorie:** Self-service voor wacht woord opnieuw instellen  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Voorheen in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet), beheerders die gebruikmaken van selfservice voor wachtwoord herstel (SSPR) om hun eigen wacht woorden opnieuw in te stellen, was er slechts één ' Gate ' (uitdaging) nodig om hun identiteit te bewijzen. In open bare en andere nationale Clouds moeten beheerders doorgaans twee poorten gebruiken om hun identiteit te bewijzen wanneer ze SSPR gebruiken. Maar omdat we geen ondersteuning bieden voor SMS-of telefoon gesprekken in azure China 21Vianet, hebben we een wacht woord voor het opnieuw instellen van één poort per beheerder toegestaan.

Er wordt een SSPR-functie pariteit gemaakt tussen Azure China 21Vianet en de open bare Cloud. Beheerders moeten twee poorten gebruiken bij gebruik van SSPR. SMS-, telefoon oproepen en verificatie van de verificator-app worden ondersteund. [Meer informatie](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>De wachtwoord lengte is beperkt tot 256 tekens

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Om ervoor te zorgen dat de Azure AD-service betrouwbaar is, zijn gebruikers wachtwoorden nu beperkt tot 256 tekens. Gebruikers met een wacht woord dat langer is dan dit wordt gevraagd om hun wacht woorden te wijzigen bij de volgende aanmelding, door contact op te nemen met de beheerder of door gebruik te maken van de selfservice functie voor het opnieuw instellen van een wacht woord.

Deze wijziging is ingeschakeld op 13 maart, 2020, op 10AM PST (18:00 UTC) en de fout is AADSTS 50052, InvalidPasswordExceedsMaxLength. Raadpleeg de [melding](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) over het breken voor meer informatie.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Aanmeldings logboeken voor Azure AD zijn nu beschikbaar voor alle gratis tenants via de Azure Portal

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Vanaf nu kunnen klanten met gratis tenants toegang krijgen tot de [aanmeldings logboeken van Azure AD van de Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) gedurende 7 dagen. Voorheen werden aanmeldings Logboeken alleen beschikbaar voor klanten met Azure Active Directory Premium-licenties. Met deze wijziging kunnen alle tenants toegang krijgen tot deze logboeken via de portal.

> [!NOTE]
> Klanten hebben nog een Premium-licentie (Azure Active Directory Premium P1 of P2) nodig om toegang te krijgen tot de aanmeld logboeken via Microsoft Graph API en Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Uitschakeling van de optie voor de hele Directory groepen uit de algemene instellingen voor groepen op Azure Portal

**Type:** Keur  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

Om ervoor te zorgen dat klanten flexibele groepen kunnen maken die het beste aan hun behoeften voldoen, hebben we de optie voor de **hele Directory groepen** vervangen uit de**algemene** instellingen voor **groepen** > in de Azure Portal met een koppeling naar de [documentatie van dynamische groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). We hebben onze documentatie verbeterd met meer instructies zodat beheerders alle groepen kunnen maken die gast gebruikers bevatten of uitsluiten.

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
 
De nieuwe ervaring voor eind gebruikers van mijn account wordt in de volgende `https://myaccount.microsoft.com` maand bijgewerkt naar de URL. Meer informatie over de ervaring en alle accounts selfservice mogelijkheden voor eind gebruikers vindt u in de [Help van mijn account Portal](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

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

Als onderdeel van een beveiligd standaard model voor cookies wordt de Chrome 80-browser gewijzigd hoe cookies zonder het `SameSite` kenmerk worden behandeld. Elke cookie waarbij het `SameSite` kenmerk niet wordt opgegeven, wordt behandeld alsof het is ingesteld op `SameSite=Lax`. Hierdoor kunnen Chrome bepaalde scenario's voor het delen van cookies op meerdere domeinen blok keren waarvan uw app afhankelijk is. Als u het oudere Chrome-gedrag wilt behouden, kunt `SameSite=None` u het kenmerk gebruiken en `Secure` een extra kenmerk toevoegen, zodat cookies voor meerdere locaties alleen toegankelijk zijn via HTTPS-verbindingen. Chrome is gepland om deze wijziging te volt ooien op 4 februari 2020.

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
