---
title: Een automatische implementatie van gebruikers inrichten plannen voor Azure Active Directory
description: Richt lijnen voor het plannen en uitvoeren van automatische gebruikers inrichting
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 944ecaaceedbff6ed1f86c4b8eb5786ce2b5bae5
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706229"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Implementatie van een automatische gebruikersinrichting plannen

Veel organisaties zijn afhankelijk van SaaS-toepassingen (Software as a Service) zoals ServiceNow, Zscaler en toegestane vertraging voor de productiviteit van eind gebruikers. Historische IT-mede werkers hebben gebruikgemaakt van hand matige inrichtings methoden zoals het uploaden van CSV-bestanden, of het gebruik van aangepaste scripts om gebruikers identiteiten in elke SaaS-toepassing veilig te beheren. Deze processen zijn fout gevoelig, onveilig en moeilijk te beheren.

Azure Active Directory (Azure AD) automatische gebruikers inrichting vereenvoudigt dit proces door het maken, onderhouden en verwijderen van gebruikers identiteiten in SaaS-toepassingen op basis van bedrijfs regels te automatiseren. Met deze automatisering kunt u uw identiteits beheersystemen effectief schalen op zowel Cloud-en hybride omgevingen als u de afhankelijkheid ervan uitbreidt op Cloud oplossingen.

Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) om meer inzicht te krijgen in de functionaliteit.

## <a name="learn"></a>Leren

Gebruikers inrichten maakt een basis voor de voortdurende identiteits bestuur en verbetert de kwaliteit van bedrijfs processen die afhankelijk zijn van gezaghebbende identiteits gegevens.

### <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voor delen van het inschakelen van automatische gebruikers inrichting zijn:

* **Verhoogde productiviteit**. U kunt gebruikers identiteiten in SaaS-toepassingen beheren met één gebruikers inrichten-beheer interface. Deze interface heeft één set inrichtings beleidsregels.

* **Risico's beheren**. U kunt de beveiliging verhogen door wijzigingen te automatiseren op basis van de status van werk nemers of groepslid maatschappen waarmee rollen en/of toegang worden gedefinieerd.

* **Naleving en**beheer van adressen. Azure AD biedt ondersteuning voor systeem eigen controle logboeken voor elke aanvraag voor het inrichten van gebruikers. Aanvragen worden uitgevoerd in de bron-en doel systemen. Zo kunt u bijhouden wie toegang heeft tot toepassingen vanaf één scherm.

* **Kosten verlagen**. Automatische gebruikers inrichting vermindert de kosten door inefficiëntie en menselijke fout te voor komen die zijn gekoppeld aan hand matige inrichting. Het vermindert de behoefte aan aangepaste, ontwikkelde oplossingen voor gebruikers inrichten, scripts en audit Logboeken.

### <a name="licensing"></a>Licentieverlening

Azure AD biedt Self-Service-integratie van elke toepassing met behulp van sjablonen in het menu van de toepassings galerie. Zie de [pagina Azure AD-licentie verlening](https://azure.microsoft.com/pricing/details/active-directory/)voor een volledige lijst met licentie vereisten.

#### <a name="application-licensing"></a>Toepassings licenties

U hebt de juiste licenties nodig voor de toepassing (en) die u automatisch wilt inrichten. Bespreek met de eigen aren van de toepassing of de gebruikers die zijn toegewezen aan de toepassing, over de juiste licenties beschikken voor hun toepassings rollen. Als Azure AD automatische inrichting beheert op basis van rollen, moeten de rollen die zijn toegewezen in azure AD worden uitgelijnd op toepassings licenties. Onjuiste licenties die eigendom zijn van de toepassing, kunnen leiden tot fouten tijdens het inrichten/bijwerken van een gebruiker.

### <a name="terms"></a>Voorwaarden

In dit artikel worden de volgende termen gebruikt:

* RUWE bewerkingen-acties die worden uitgevoerd voor gebruikers accounts: maken, lezen, bijwerken, verwijderen.

* Eenmalige aanmelding (SSO): de mogelijkheid voor een gebruiker om zich eenmaal aan te melden en om toegang te krijgen tot alle SSO-toepassingen. In de context van het inrichten van gebruikers is SSO een resultaat van gebruikers die één account hebben voor toegang tot alle systemen die gebruikmaken van automatische gebruikers inrichting.

* Bron systeem: de opslag plaats van gebruikers die de Azure AD-bepalingen van hebben. Azure AD is het bron systeem voor de meeste vooraf geïntegreerde inrichtings connectors. Er zijn echter enkele uitzonde ringen voor Cloud toepassingen zoals SAP, workday en AWS. Zie bijvoorbeeld [Gebruikers inrichten van workday naar AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial).

* Doel systeem: de opslag plaats van gebruikers die door de Azure AD worden ingericht. Het doel systeem is doorgaans een SaaS-toepassing, zoals ServiceNow, Zscaler en toegestane vertraging. Het doel systeem kan ook een on-premises systeem zijn, zoals AD.

* [Systeem voor Cross-Domain Identity Management (scim)](https://aka.ms/scimoverview) : een open standaard die het automatiseren van het inrichten van gebruikers mogelijk maakt. SCIM communiceert gebruikers identiteits gegevens tussen id-providers zoals micro soft, en service providers zoals Sales Force of andere SaaS-apps waarvoor gebruikers identiteits gegevens zijn vereist.

### <a name="training-resources"></a>Trainings bronnen

| Bronnen| Koppeling en beschrijving |
| - | - |
| On-demand webinars| [Uw bedrijfs toepassingen beheren met Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Meer informatie over hoe u met Azure AD eenmalige aanmelding kunt verkrijgen voor uw zakelijke SaaS-toepassingen en aanbevolen procedures voor het beheren van de toegang. |
| Video’s| [Wat is gebruikers inrichten in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Hoe kan ik de gebruikers inrichten in Active Azure Directory implementeren?](https://youtu.be/pKzyts6kfrw) <br> [Sales Force integreren met Azure AD: gebruikers inrichten automatiseren](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Onlinecursussen| SkillUp online: [identiteiten beheren](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Leer hoe u Azure AD integreert met veel SaaS-toepassingen en gebruikers toegang tot deze toepassingen kunt beveiligen. |
| Boeken| [Moderne verificatie met Azure Active Directory voor webtoepassingen (Naslag informatie voor ontwikkel aars) 1e editie](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Dit is een gezaghebbende, diep gaande hand leiding voor het bouwen van Active Directory verificatie oplossingen voor deze nieuwe omgevingen. |
| Zelfstudie| Zie de [lijst met zelf studies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list). |
| Veelgestelde vragen| [Veelgestelde vragen](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) over het automatisch inrichten van gebruikers |

### <a name="solution-architectures"></a>Architecturen voor oplossingen

De Azure AD-inrichtings service voorziet gebruikers van SaaS-apps en andere systemen door verbinding te maken met gebruikers beheer-API-eind punten die door elke leverancier van de toepassing worden geleverd. Met deze gebruikers beheer-API-eind punten kan Azure AD programmatisch gebruikers maken, bijwerken en verwijderen.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatische gebruikers inrichting voor hybride ondernemingen

In dit voor beeld worden gebruikers en of groepen gemaakt in een HR-data base die is verbonden met een on-premises Directory. De Azure AD-inrichtings service beheert het automatisch inrichten van gebruikers aan de doel-SaaS-toepassingen.

 ![Gebruikers inrichten](media/auto-user-provision-dp/hybridprovisioning.png)

**Beschrijving van werk stroom:**

1. Gebruikers/groepen worden gemaakt in een on-premises HR-toepassing/systeem, zoals SAP. 

1. Azure AD Connect agent voert geplande synchronisaties van identiteiten (gebruikers en groepen) uit van de lokale AD naar Azure AD.

1. De Azure AD-inrichtings service start een [eerste cyclus](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) op basis van het bron systeem en het doel systeem. 

1. Azure AD Provisioning service voert een query uit op het bron systeem voor gebruikers en groepen die zijn gewijzigd sinds de eerste cyclus en tijdens het pushen van wijzigingen in [incrementele cycli](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatische gebruikers inrichting voor Cloud ondernemingen

In dit voor beeld vindt het maken van een gebruiker plaats in azure AD en de Azure AD-inrichtings service beheert automatische gebruikers inrichting voor de doel-en SaaS-toepassingen.

![Afbeelding 2](media/auto-user-provision-dp/cloudprovisioning.png)

**Beschrijving van werk stroom:**

1. Gebruikers/groepen worden gemaakt in azure AD.

1. De Azure AD-inrichtings service start een [eerste cyclus](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) op basis van het bron systeem en het doel systeem. 

1. De Azure AD-inrichtings service vraagt het bron systeem voor alle gebruikers en groepen die zijn bijgewerkt sinds de eerste cyclus en voert eventuele [incrementele cycli](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)uit.

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatische gebruikers inrichting voor Cloud-HR-toepassingen 

In dit voor beeld worden de gebruikers en of groepen gemaakt in een Cloud HR-toepassing, zoals workday en SuccessFactors. De Azure AD Provisioning-Service en Azure AD Connect inrichtings agent voorzien in de gebruikers gegevens van de Tenant van de HR-app van de cloud in AD. Zodra de accounts zijn bijgewerkt in AD, wordt deze gesynchroniseerd met Azure AD via Azure AD Connect en kunnen de kenmerken van het e-mail adres en de gebruikers naam worden teruggeschreven naar de Tenant van de HR-app in de Cloud.

![Afbeelding 2](media/auto-user-provision-dp/workdayprovisioning.png)

1.  **HR-team** voert de trans acties uit in de Cloud HR app-Tenant.
2.  De **Azure AD-inrichtings service** voert de geplande cycli uit van de Tenant van de HR-app in de Cloud en identificeert wijzigingen die moeten worden verwerkt voor synchronisatie met AD.
3.  De **Azure AD-inrichtings service** roept de Azure AD Connect-inrichtings agent aan met een aanvraag lading die ad-account maken/bijwerken/inschakelen/uitschakelen heeft.
4.  **Azure AD Connect-inrichtings agent** gebruikt een service account voor het beheren van AD-account gegevens.
5.  **Azure AD Connect** voert Delta synchronisatie uit voor het ophalen van updates in AD.
6.  **Ad** -updates worden gesynchroniseerd met Azure AD. 
7.  **Azure AD Provisioning Service** materialiseren-e-mail kenmerk en gebruikers naam van Azure AD naar de Cloud HR app Tenant.

## <a name="plan-the-deployment-project"></a>Het implementatie project plannen

Bedenk wat uw organisatie nodig heeft om de strategie te bepalen voor het implementeren van gebruikers inrichten in uw omgeving.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, is dit doorgaans het gevolg van niet-overeenkomende verwachtingen wat betreft impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](https://aka.ms/deploymentplans) en dat de rol van belanghebbenden in het project goed worden begrepen door de belanghebbenden en hun project invoer en accountabilities te documenteren.

### <a name="plan-communications"></a>Communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Communiceer proactief met uw gebruikers hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als u problemen ondervindt.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden u aan de initiële configuratie van automatische gebruikers inrichting in een test omgeving met een kleine subset van gebruikers te maken voordat u deze naar alle gebruikers in productie kunt schalen.

#### <a name="best-practices-for-a-pilot"></a>Aanbevolen procedures voor een pilot  

Met een pilot kunt u testen met een kleine groep voordat u een mogelijkheid voor iedereen implementeert. Zorg ervoor dat als onderdeel van uw test, elke use-case binnen uw organisatie grondig wordt getest.

In uw eerste golf, richt IT, bruikbaarheid en andere geschikte gebruikers die kunnen testen en feedback geven. Gebruik deze feedback om de communicatie en instructies die u naar uw gebruikers verzendt verder te ontwikkelen en om inzicht te krijgen in de soorten problemen die uw ondersteunings medewerkers kunnen zien.

Breid de implementatie uit voor grotere groepen gebruikers door het bereik van de doel groep (en) te verg Roten. Dit kan worden gedaan via een [dynamisch groepslid maatschap](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)of door gebruikers hand matig toe te voegen aan de doel groep (en).

## <a name="plan-application-connections-and-administration"></a>Toepassings verbindingen en-beheer plannen

Gebruik de Azure AD-portal voor het weer geven en beheren van alle toepassingen die ondersteuning bieden voor inrichting. Zie [uw apps zoeken in de portal](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal).

### <a name="determine-the-type-of-connector-to-use"></a>Bepalen welk type connector moet worden gebruikt

De stappen die nodig zijn om automatische inrichting in te scha kelen en te configureren variëren afhankelijk van de toepassing. Als de toepassing die u wilt laten inrichten, wordt vermeld in de [Galerie met Azure AD SaaS-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), selecteert u de [app-specifieke integratie-zelf studie](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) voor het configureren van de vooraf geïntegreerde User Provisioning connector.

Als dat niet het geval is, volgt u de onderstaande stappen:

1. [Een aanvraag maken](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) voor een vooraf geïntegreerde User Provisioning connector. Ons team zal samen werken met u en met de ontwikkelaar van de toepassing om uw toepassing op ons platform uit te laten staan als SCIM wordt ondersteund.

1. Gebruik de algemene ondersteuning voor het inrichten van [BYOA scim](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) voor de app. Dit is een vereiste voor Azure AD om gebruikers in te richten op de app zonder vooraf geïntegreerde inrichtings connector.

1. Als de toepassing de BYOA SCIM-connector kan gebruiken, raadpleegt u de [zelf studie over BYOA scim-integratie](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) om de BYOA scim-connector voor de toepassing te configureren.

Zie [welke toepassingen en systemen kan ik gebruiken met automatische gebruikers inrichting van Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) voor meer informatie.

### <a name="collect-information-to-authorize-application-access"></a>Gegevens verzamelen om toegang tot toepassingen te verlenen

Het instellen van automatische gebruikers inrichting is een proces per toepassing. Voor elke toepassing moet u [beheerders referenties](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) opgeven om verbinding te maken met het eind punt voor gebruikers beheer van het doel systeem.

In de onderstaande afbeelding ziet u één versie van de vereiste beheerders referenties:

![Inrichtings scherm voor het beheren van inrichtings instellingen voor gebruikers accounts](media/auto-user-provision-dp/userprovisioning-admincredentials.png)

Hoewel voor sommige toepassingen de gebruikers naam en het wacht woord van de beheerder zijn vereist, kunnen anderen een Bearer-token vereisen.

## <a name="plan-user-and-group-provisioning"></a>Gebruikers-en groeps inrichting plannen

Als u het inrichten van gebruikers inschakelt voor zakelijke apps, bepaalt de [Azure Portal](https://portal.azure.com/) de kenmerk waarden via kenmerk toewijzing.

### <a name="determine-operations-for-each-saas-app"></a>Bewerkingen voor elke SaaS-app bepalen

Elke toepassing kan unieke gebruikers-of groeps kenmerken hebben die moeten worden toegewezen aan de kenmerken in uw Azure AD. De toepassing heeft mogelijk slechts een subset van ruwe bewerkingen die beschikbaar zijn.

Documenteer de volgende informatie voor elke toepassing:

* RUWE inrichtings bewerkingen die moeten worden uitgevoerd voor de gebruiker en of groeps objecten voor de doel systemen. Zo is het bijvoorbeeld mogelijk dat elke SaaS-app van het bedrijf niet alle mogelijke bewerkingen wil.

* Beschik bare kenmerken in het bron systeem

* Beschik bare kenmerken in het doel systeem

* Toewijzing van kenmerken tussen systemen.

### <a name="choose-which-users-and-groups-to-provision"></a>Kies welke gebruikers en groepen u wilt inrichten

Voordat u de automatische gebruikers inrichting implementeert, moet u bepalen welke gebruikers en groepen moeten worden ingericht voor uw toepassing.

* Gebruik [bereik filters](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) om op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers worden ingericht voor een toepassing.

* Gebruik vervolgens de [gebruikers-en groeps toewijzingen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) als dat nodig is voor extra filters.

### <a name="define-user-and-group-attribute-mapping"></a>Toewijzing van gebruikers-en groeps kenmerken definiëren

Als u het automatisch inrichten van gebruikers wilt implementeren, moet u de gebruikers-en groeps kenmerken definiëren die nodig zijn voor de toepassing. Er is een vooraf geconfigureerde set met kenmerken en [kenmerk toewijzingen](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) tussen Azure AD-gebruikers objecten en de gebruikers objecten van elke SaaS-toepassing. Niet alle SaaS-apps hebben groeps kenmerken ingeschakeld.

Azure AD ondersteunt door directe toewijzing van kenmerk naar kenmerk, voor het leveren van constante waarden of het [schrijven van expressies voor kenmerk toewijzingen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Met deze flexibiliteit kunt u precies bepalen wat er wordt ingevuld in het kenmerk van het doel systeem. U kunt [Microsoft Graph-API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) en Graph Explorer gebruiken om de kenmerk toewijzingen en het schema voor het inrichten van gebruikers te exporteren naar een JSON-bestand en dit weer te importeren in azure AD.

Zie voor meer informatie [Gebruikers inrichten kenmerk toewijzingen voor SaaS-toepassingen in azure Active Directory aanpassen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

### <a name="special-considerations-for-user-provisioning"></a>Speciale overwegingen voor het inrichten van gebruikers

Houd rekening met het volgende om problemen na de implementatie te verminderen:

* Zorg ervoor dat de kenmerken die worden gebruikt voor het toewijzen van gebruikers-of groeps objecten tussen bron-en doel toepassingen, robuust zijn. Gebruikers/groepen mogen niet onjuist worden ingericht als de kenmerken worden gewijzigd (bijvoorbeeld wanneer een gebruiker naar een ander deel van het bedrijf gaat).

* Toepassingen kunnen specifieke beperkingen en/of vereisten hebben waaraan moet worden voldaan om de gebruikers inrichting correct te laten werken. Met een toegestane vertraging worden bijvoorbeeld waarden voor bepaalde kenmerken afgekapt. Raadpleeg de [zelf studies voor automatische gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) die specifiek zijn voor elke toepassing.

* Controleer de schema consistentie tussen de bron-en doel systemen. Veelvoorkomende problemen zijn kenmerken zoals UPN of e-mail die niet overeenkomt. Bijvoorbeeld: UPN in azure AD is ingesteld als *john_smith@contoso.com* en in de app *jsmith@contoso.com* . Zie de [verwijzing naar het gebruikers-en groeps schema](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)voor meer informatie.

## <a name="plan-testing-and-security"></a>Testen en beveiliging plannen

Zorg er in elke fase van de implementatie voor dat u test dat de resultaten naar verwachting worden uitgevoerd en de inrichtings cycli controleren.

### <a name="plan-testing"></a>Tests plannen

Zodra u automatische gebruikers inrichting voor de toepassing hebt geconfigureerd, voert u test cases uit om te controleren of deze oplossing voldoet aan de vereisten van uw organisatie.

| Scenario's| Verwachte resultaten |
| - | - |
| Gebruiker wordt toegevoegd aan een groep die is toegewezen aan het doel systeem | Het gebruikers object is ingericht in het doel systeem. <br>De gebruiker kan zich aanmelden bij het doel systeem en de gewenste acties uitvoeren. |
| De gebruiker wordt verwijderd uit een groep die is toegewezen aan het doel systeem | Het gebruikers object wordt ongedaan gemaakt in het doel systeem.<br>Gebruiker kan zich niet aanmelden bij het doel systeem. |
| Gebruikers gegevens worden op elke manier bijgewerkt in azure AD | Bijgewerkte gebruikers kenmerken worden weer gegeven in het doel systeem na een incrementele cyclus |
| De gebruiker valt buiten het bereik | Gebruikers object is uitgeschakeld of verwijderd. <br>Opmerking: dit gedrag wordt genegeerd bij het [inrichten](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)van werk dagen. |

### <a name="plan-security"></a>Beveiliging plannen

Het is gebruikelijk dat een beveiligings beoordeling vereist is als onderdeel van een implementatie. Als u een beveiligings beoordeling nodig hebt, raadpleegt u de vele Azure AD- [witboeken](https://www.microsoft.com/download/details.aspx?id=36391) die een overzicht bieden van de identiteit als een service.

### <a name="plan-rollback"></a>Plan terugdraai actie

Als de implementatie van de automatische gebruikers inrichting niet naar wens werkt in de productie omgeving, kunnen de volgende terugdraai stappen u helpen bij het herstellen naar een eerdere bekende goede staat:

1. Bekijk het [inrichtings samenvattings rapport](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) en de [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) om te bepalen wat de onjuiste bewerkingen hebben plaatsgevonden voor de betrokken gebruikers en/of groepen.

1. Gebruik audit logboeken voor het inrichten om de laatste bekende juiste status van de betrokken gebruikers en/of groepen te bepalen. Bekijk ook de bron systemen (Azure AD of AD).

1. Werk samen met de eigenaar van de toepassing om de gebruikers en/of groepen die rechtstreeks in de toepassing worden betrokken, bij te werken met de laatste bekende juiste status waarden.

## <a name="deploy-automatic-user-provisioning-service"></a>Automatische gebruikers Provisioning Service implementeren

Kies de stappen die zijn afgestemd op uw oplossings vereisten.

### <a name="prepare-for-the-initial-cycle"></a>Voor bereiding voor de eerste cyclus

Wanneer de Azure AD-inrichtings service voor de eerste keer wordt uitgevoerd, maakt de eerste cyclus voor het bron systeem en de doel systemen een moment opname van alle gebruikers objecten voor elk doel systeem.

Bij het inschakelen van automatische inrichting voor een toepassing, kan de eerste cyclus van 20 minuten tot enkele uren duren. De duur is afhankelijk van de grootte van de Azure AD-map en het aantal gebruikers in het bereik voor inrichting. Zie de [prestaties van het inrichten verbeteren](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish).

De inrichtings service slaat de status van beide systemen op na de eerste cyclus, waardoor de prestaties van de volgende incrementele cycli worden verbeterd.

### <a name="configure-automatic-user-provisioning"></a>Automatische gebruikersinrichting configureren

Gebruik de [Azure Portal](https://portal.azure.com/) voor het beheren van automatische toewijzing van gebruikers accounts en het ongedaan maken van de inrichting voor toepassingen die deze ondersteunen. Volg de stappen in [Hoe kan ik automatische inrichting instellen voor een toepassing?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

De Azure AD User Provisioning Service kan ook worden geconfigureerd en beheerd met behulp van de [Microsoft Graph-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Automatische gebruikers inrichting beheren

Nu u hebt geïmplementeerd, moet u de oplossing beheren.

### <a name="monitor-user-provisioning-operation-health"></a>Status van bewerkings bewerking door gebruiker bewaken

Na een geslaagde [eerste cyclus](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)zullen de Azure AD-inrichtings service voor onbepaalde tijd incrementele updates uitvoeren, met intervallen die specifiek zijn voor elke toepassing, totdat een van de volgende gebeurtenissen zich voordoet:

* De service wordt hand matig gestopt en er wordt een nieuwe eerste cyclus geactiveerd met behulp van de [Azure Portal](https://portal.azure.com/), of met behulp van de juiste [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) -opdracht.

* Een nieuwe eerste cyclus wordt geactiveerd door een wijziging in kenmerk toewijzingen of op filter bereik.

* Het inrichtings proces gaat in quarantaine vanwege een hoge fout frequentie en blijft meer dan vier weken in quarantaine, wanneer deze automatisch wordt uitgeschakeld.

Zie Azure AD- [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/manage-apps/context/manage-apps-context)voor het controleren van deze gebeurtenissen en alle andere activiteiten die worden uitgevoerd door de inrichtings service.

Als u wilt weten hoe lang het inrichten van de inrichtings cyclus duurt en de voortgang van de inrichtings taak bewaken, kunt u [de status van het inrichten van gebruikers controleren](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

### <a name="gain-insights-from-reports"></a>Inzichten verkrijgen van rapporten

Azure AD kan [inzicht](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) geven in het gebruik van gebruikers en de operationele status van uw organisatie via controle logboeken en-rapporten.

Beheerders moeten het samenvattings rapport van de inrichting controleren om de operationele status van de inrichtings taak te controleren. Alle activiteiten die worden uitgevoerd door de inrichtings service worden vastgelegd in de Azure AD-audit Logboeken. Zie [zelf studie: rapportage over het automatisch inrichten van gebruikers accounts](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

We raden u aan de eigenaar van de rapporten te gebruiken voor een uitgebracht die voldoet aan de vereisten van uw organisatie. Azure AD behoudt de meeste controle gegevens gedurende 30 dagen.

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de volgende koppelingen voor het oplossen van problemen die kunnen optreden tijdens het inrichten:

* [Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)

* [Een kenmerk van uw on-premises Active Directory naar Azure AD synchroniseren voor het inrichten van een toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)

* [Gebruikers die een Azure AD-galerie toepassing inrichten, nemen uren of meer in beslag](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)

* [Probleem bij het opslaan van de beheerders referenties tijdens het configureren van de gebruikers inrichting voor een Azure Active Directory galerie-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)

* [Er worden geen gebruikers ingericht voor een Azure AD-galerie toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

* [Er wordt een verkeerde set gebruikers ingericht voor een Azure AD-galerie toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)

### <a name="helpful-documentation"></a>Nuttige documentatie

* [Expressies schrijven voor kenmerk toewijzingen](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)

* [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Verwijdering van gebruikers accounts die buiten het bereik vallen, overs Laan](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)

* [Azure AD Connect-inrichtings agent: release geschiedenis van versie](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)

#### <a name="resources"></a>Bronnen

* [Product feedback geven](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Blijf op de hoogte van wat er nieuw is in azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack overflow-Azure AD-forum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Volgende stappen
* [Automatische gebruikers inrichting configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)

* [Uw inrichtings configuratie exporteren of importeren met behulp van Microsoft Graph-API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)

* [Expressies schrijven voor kenmerk toewijzingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
