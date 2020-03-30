---
title: Een automatische implementatie voor het inrichten van gebruikers plannen voor Azure Active Directory
description: Richtlijnen voor het plannen en uitvoeren van automatische gebruikersinrichting
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522407"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Implementatie van een automatische gebruikersinrichting plannen

Veel organisaties vertrouwen op software as a service (SaaS) applicaties zoals ServiceNow, Zscaler en Slack voor de productiviteit van eindgebruikers. It-medewerkers hebben zich in het verleden gebaseerd op handmatige inrichtingsmethoden, zoals het uploaden van CSV-bestanden of het gebruik van aangepaste scripts om gebruikersidentiteiten in elke SaaS-toepassing veilig te beheren. Deze processen zijn foutgevoelig, onzeker en moeilijk te beheren.

Automatische gebruikersinrichting van Azure Active Directory (Azure AD) vereenvoudigt dit proces door het maken, onderhouden en verwijderen van gebruikersidentiteiten in SaaS-toepassingen op basis van bedrijfsregels veilig te automatiseren. Met deze automatisering u uw identiteitsbeheersystemen effectief schalen op zowel cloud- als hybride omgevingen terwijl u hun afhankelijkheid van cloudgebaseerde oplossingen uitbreidt.

Zie [Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md) om de functionaliteit beter te begrijpen.

## <a name="learn"></a>Leren

Gebruikersinrichting creëert een basis voor doorlopende identiteitsgovernance en verbetert de kwaliteit van bedrijfsprocessen die afhankelijk zijn van gezaghebbende identiteitsgegevens.

### <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voordelen van het inschakelen van automatische gebruikersvoorzieningen zijn:

* **Verhoogde productiviteit**. U gebruikersidentiteiten beheren in SaaS-toepassingen met één gebruikersinrichtingsbeheerinterface. Deze interface heeft één set inrichtingsbeleid.

* **Risico's beheren**. U de beveiliging verhogen door wijzigingen te automatiseren op basis van de status van werknemers of groepslidmaatschappen die rollen en/of toegang definiëren.

* **Compliance en governance aanpakken**. Azure AD ondersteunt native auditlogs voor elke aanvraag voor het inrichten van gebruikers. Aanvragen worden uitgevoerd in zowel de bron- als doelsystemen. Hiermee u vanaf één scherm bijhouden wie toegang heeft tot toepassingen.

* **Verlaag de kosten**. Automatische gebruikersvoorziening vermindert de kosten door inefficiënties en menselijke fouten in verband met handmatige inname te voorkomen. Het vermindert de noodzaak van op maat ontwikkelde gebruikersinrichting seining oplossingen, scripts en audit logs.

### <a name="licensing"></a>Licentieverlening

Azure AD biedt selfservice-integratie van elke toepassing met behulp van sjablonen die worden aangeboden in het menu van de toepassingsgalerie. Zie [Azure AD-licentiepagina](https://azure.microsoft.com/pricing/details/active-directory/)voor een volledige lijst met licentievereisten.

#### <a name="application-licensing"></a>Toepassingslicenties

U hebt de juiste licenties nodig voor de toepassing(en) die u automatisch wilt inrichten. Bespreek met de eigenaren van de toepassing of de gebruikers die aan de toepassing zijn toegewezen, over de juiste licenties voor hun toepassingsrollen beschikken. Als Azure AD automatische inrichting beheert op basis van rollen, moeten de rollen die in Azure AD zijn toegewezen, worden uitgelijnd op toepassingslicenties. Onjuiste licenties in de toepassing kunnen leiden tot fouten tijdens het inrichten/bijwerken van een gebruiker.

### <a name="terms"></a>Voorwaarden

In dit artikel worden de volgende termen gebruikt:

* CRUD-bewerkingen - Acties op gebruikersaccounts: Maken, lezen, bijwerken, verwijderen.

* Single sign-on (SSO) - De mogelijkheid voor een gebruiker om zich eenmalig aan te melden en toegang te krijgen tot alle SSO-toepassingen. In het kader van de gebruikersinrichting is SSO het resultaat van gebruikers die één account hebben om toegang te krijgen tot alle systemen die gebruikmaken van automatische gebruikersinrichting.

* Bronsysteem - De opslagplaats van gebruikers waarvan de Azure AD-bepalingen afkomstig zijn. Azure AD is het bronsysteem voor de meeste vooraf geïntegreerde inrichtingsconnectoren. Er zijn echter enkele uitzonderingen voor cloudtoepassingen zoals SAP, Workday en AWS. Zie Bijvoorbeeld [Gebruikersinrichting van Werkdag naar AD](../saas-apps/workday-inbound-tutorial.md).

* Doelsysteem - De opslagplaats van gebruikers waaraan de Azure AD-voorzieningen worden getroffen. Het Target-systeem is meestal een SaaS-toepassing zoals ServiceNow, Zscaler en Slack. Het doelsysteem kan ook een on-premises systeem zijn, zoals AD.

* [Systeem voor Cross-domain Identity Management (SCIM)](https://aka.ms/scimoverview) - Een open standaard die de automatisering van de gebruikersinrichting mogelijk maakt. SCIM communiceert identiteitsgegevens van gebruikers tussen identiteitsproviders zoals Microsoft en serviceproviders zoals Salesforce of andere SaaS-apps waarvoor identiteitsgegevens van gebruikers nodig zijn.

### <a name="training-resources"></a>Opleidingsmiddelen

| Resources| Koppeling en beschrijving |
| - | - |
| Webinars op aanvraag| [Uw Enterprise-toepassingen beheren met Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Ontdek hoe Azure AD u kan helpen bij het realiseren van SSO voor uw zakelijke SaaS-toepassingen en aanbevolen procedures voor het beheren van toegang. |
| Video's| [Wat is gebruikersvoorziening in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Gebruikersvoorziening implementeren in Active Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Salesforce integreren met Azure AD: gebruikersvoorziening automatiseren](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Online cursussen| SkillUp Online: [Identiteiten beheren](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Meer informatie over het integreren van Azure AD met veel SaaS-toepassingen en het beveiligen van gebruikerstoegang tot die toepassingen. |
| Boeken| [Moderne verificatie met Azure Active Directory for Web Applications (Developer Reference) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Dit is een gezaghebbende, deep-dive gids voor het bouwen van Active Directory authenticatie oplossingen voor deze nieuwe omgevingen. |
| Zelfstudies| Bekijk de [lijst met zelfstudies over het integreren van SaaS-apps met Azure AD.](../saas-apps/tutorial-list.md) |
| Veelgestelde vragen| [Veelgestelde vragen](../app-provisioning/user-provisioning.md) over geautomatiseerde gebruikersinrichting |

### <a name="solution-architectures"></a>Oplossingsarchitecturen

De Azure AD-servicevoorziening voorgebruikers naar SaaS-apps en andere systemen door verbinding te maken met API-eindpunten voor gebruikersbeheer die door elke toepassingsleverancier worden geleverd. Met deze API-eindpunten voor gebruikersbeheer kan Azure AD gebruikers programmatisch maken, bijwerken en verwijderen.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatische gebruikersvoorziening voor hybride ondernemingen

In dit voorbeeld worden gebruikers en of groepen gemaakt in een HR-database die is gekoppeld aan een on-premises directory. De Azure AD-inrichtingsservice beheert de automatische gebruikersvoorziening voor de beoogde SaaS-toepassingen.

 ![gebruikersinrichting](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Beschrijving van de werkstroom:**

1. Gebruikers/groepen worden gemaakt in een on-premises HR-toepassing/-systeem, zoals SAP. 

1. **Azure AD Connect-agent** voert geplande synchronisaties van identiteiten (gebruikers en groepen) uit van het lokale AD naar Azure AD.

1. **Azure AD-inrichtingsservice** begint een [eerste cyclus](../app-provisioning/user-provisioning.md) ten opzichte van het bronsysteem en het doelsysteem. 

1. **Azure AD-servicequery's** het bronsysteem voor alle gebruikers en groepen die sinds de eerste cyclus zijn gewijzigd en duwt wijzigingen in [incrementele cycli](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatische gebruikersvoorziening voor alleen cloudbedrijven

In dit voorbeeld vindt het maken van gebruikers plaats in Azure AD en beheert de Azure AD-inrichtingsservice de automatische gebruikersvoorziening voor de doeltoepassingen (SaaS).

![Afbeelding 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Beschrijving van de werkstroom:**

1. Gebruikers/groepen worden gemaakt in Azure AD.

1. **Azure AD-inrichtingsservice** begint een [eerste cyclus](../app-provisioning/user-provisioning.md) ten opzichte van het bronsysteem en het doelsysteem. 

1. **Azure AD-servicequery's** het bronsysteem voor alle gebruikers en groepen die sinds de eerste cyclus zijn bijgewerkt en voert eventuele [incrementele cycli uit.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatische gebruikersinrichting voor hr-toepassingen in de cloud 

In dit voorbeeld worden de gebruikers en/of groepen gemaakt in een HR-cloudtoepassing zoals Workday en SuccessFactors. De Azure AD-inrichtingsservice en Azure AD Connect-inrichtingsagent voorziet de gebruikersgegevens van de tenant van de CLOUD HR-app in AD. Zodra de accounts zijn bijgewerkt in AD, wordt deze gesynchroniseerd met Azure AD via Azure AD Connect en kunnen de e-mailadressen en gebruikerskenmerken worden teruggeschreven naar de tenant van de HR-app in de cloud.

![Afbeelding 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Het HR-team** voert de transacties uit in de tenant van de CLOUD HR-app.
2.  **Azure AD-inrichtingsservice** voert de geplande cycli uit vanuit de tenant van de CLOUD HR-app en identificeert wijzigingen die moeten worden verwerkt om te worden gesynchroniseerd met AD.
3.  **Azure AD-inrichtingsservice** roept de Azure AD Connect-inrichtingsagent op met een aanvraagpayload met AD-accountmaken/bijwerken/inschakelen/uitschakelen bewerkingen.
4.  **Azure AD Connect-inrichtingsagent** gebruikt een serviceaccount om AD-accountgegevens te beheren.
5.  **Azure AD Connect** voert deltasynchronisatie uit om updates in AD uit te voeren.
6.  **AD-updates** worden gesynchroniseerd met Azure AD. 
7.  **Azure AD-inrichtingsservice** schrijft e-mailkenmerk en gebruikersnaam terug van Azure AD naar de tenant van de CLOUD HR-app.

## <a name="plan-the-deployment-project"></a>Het implementatieproject plannen

Houd rekening met uw organisatorische behoeften om de strategie te bepalen voor het implementeren van gebruikersvoorzieningen in uw omgeving.

### <a name="engage-the-right-stakeholders"></a>Betrek de juiste stakeholders

Wanneer technologieprojecten mislukken, is dit meestal vanwege de oneigenlijke verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden indienst maakt](https://aka.ms/deploymentplans) en dat de rollen van belanghebbenden in het project goed worden begrepen door de belanghebbenden en hun projectinput en accountates te documenteren.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is cruciaal voor het succes van elke nieuwe dienst. Communiceer proactief met uw gebruikers hoe hun ervaring zal veranderen, wanneer deze zal veranderen en hoe ze ondersteuning kunnen krijgen als ze problemen ervaren.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden u aan de initiële configuratie van automatische gebruikersinrichting in een testomgeving te bevinden met een kleine subset van gebruikers voordat u deze schaalt naar alle gebruikers in productie. Bekijk [best practices](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) voor het uitvoeren van een pilot.

#### <a name="best-practices-for-a-pilot"></a>Aanbevolen procedures voor een pilot  

Met een pilot u met een kleine groep testen voordat u een mogelijkheid voor iedereen implementeert. Zorg ervoor dat als onderdeel van uw testen elke use case binnen uw organisatie grondig wordt getest.

In uw eerste golf, richten IT, bruikbaarheid, en andere geschikte gebruikers die kunnen testen en feedback geven. Gebruik deze feedback om de communicatie en instructies die u naar uw gebruikers stuurt verder te ontwikkelen en om inzicht te geven in de soorten problemen die uw ondersteuningsmedewerkers kunnen zien.

Verbreed de uitrol naar grotere groepen gebruikers door het bereik van de beoogde groep(en) te vergroten. Dit kan worden gedaan door [middel van dynamisch groepslidmaatschap](../users-groups-roles/groups-dynamic-membership.md)of door gebruikers handmatig toe te voegen aan de doelgroep(en).

## <a name="plan-application-connections-and-administration"></a>Toepassingsverbindingen en beheer plannen

Gebruik de Azure AD-portal om alle toepassingen te bekijken en te beheren die voorzieningen ondersteunen. Zie [Uw apps zoeken in de portal](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Het type connector bepalen dat moet worden gebruikt

De werkelijke stappen die nodig zijn om automatische inrichting in te schakelen en te configureren, variëren afhankelijk van de toepassing. Als de toepassing die u automatisch wilt inrichten, wordt vermeld in de [azure AD SaaS-app-galerie,](../saas-apps/tutorial-list.md)moet u de [app-specifieke integratiezelfpas](../saas-apps/tutorial-list.md) selecteren om de vooraf geïntegreerde gebruikersinrichtingsconnector te configureren.

Zo niet, volg dan de onderstaande stappen:

1. [Maak een aanvraag](../develop/howto-app-gallery-listing.md) voor een vooraf geïntegreerde gebruikersinrichtingsconnector. Ons team zal met u en de applicatieontwikkelaar samenwerken om uw toepassing aan boord te nemen van ons platform als het SCIM ondersteunt.

1. Gebruik de [byoa SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) generieke gebruikersinrichting ondersteuning voor de app. Dit is een vereiste voor Azure AD om gebruikers aan de app te voorzien zonder een vooraf geïntegreerde inrichtingsconnector.

1. Als de toepassing de BYOA SCIM-connector kan gebruiken, raadpleegt u [byoa SCIM-integratiezelfstudie](../app-provisioning/use-scim-to-provision-users-and-groups.md) om de BYOA SCIM-connector voor de toepassing te configureren.

Zie [Welke toepassingen en systemen kan ik gebruiken met automatische gebruikersvoorziening van Azure AD voor](../app-provisioning/user-provisioning.md) meer informatie?

### <a name="collect-information-to-authorize-application-access"></a>Informatie verzamelen om toegang tot toepassingen te autoriseren

Het instellen van automatische gebruikersinrichting is een proces per toepassing. Voor elke toepassing moet u [beheerdersreferenties](../app-provisioning/configure-automatic-user-provisioning-portal.md) verstrekken om verbinding te maken met het eindpunt voor gebruikersbeheer van het doelsysteem.

De afbeelding hieronder toont een versie van de vereiste beheerdersreferenties:

![Inrichtingsscherm voor het beheren van instellingen voor het inrichten van gebruikersaccounts](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Hoewel voor sommige toepassingen de gebruikersnaam en het wachtwoord van de beheerder nodig zijn, hebben anderen mogelijk een token aan toonder nodig.

## <a name="plan-user-and-group-provisioning"></a>Gebruikers- en groepsinrichting plannen

Als u gebruikersinrichting voor bedrijfsapps inschakelt, bepaalt de [Azure-portal](https://portal.azure.com/) de kenmerkenswaarden via kenmerktoewijzing.

### <a name="determine-operations-for-each-saas-app"></a>Bewerkingen voor elke SaaS-app bepalen

Elke toepassing kan unieke gebruikers- of groepskenmerken hebben die moeten worden toegewezen aan de kenmerken in uw Azure AD. Toepassing kan alleen een subset van CRUD-bewerkingen beschikbaar hebben.

Documenteer voor elke aanvraag de volgende gegevens:

* CRUD-inrichtingsbewerkingen die moeten worden uitgevoerd op de gebruiker en of groep objecten voor de doelsystemen. Bijvoorbeeld, elke SaaS app bedrijfseigenaar kan niet alle mogelijke bewerkingen willen.

* Kenmerken die beschikbaar zijn in het bronsysteem

* Kenmerken die beschikbaar zijn in het doelsysteem

* Het in kaart brengen van kenmerken tussen systemen.

### <a name="choose-which-users-and-groups-to-provision"></a>Kiezen welke gebruikers en groepen u wilt inrichten

Voordat u automatische gebruikersinrichting implementeert, moet u bepalen welke gebruikers en groepen moeten worden ingericht voor uw toepassing.

* Gebruik [scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) om op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers zijn ingericht voor een toepassing.

* Gebruik vervolgens [gebruikers- en groepstoewijzingen](../manage-apps/assign-user-or-group-access-portal.md) als dat nodig is voor extra filtering.

### <a name="define-user-and-group-attribute-mapping"></a>Toewijzing van gebruikers- en groepskenmerken definiëren

Als u automatische gebruikersinrichting wilt implementeren, moet u de gebruikers- en groepskenmerken definiëren die nodig zijn voor de toepassing. Er is een vooraf geconfigureerde set kenmerken en [kenmerktoewijzingen](../app-provisioning/configure-automatic-user-provisioning-portal.md) tussen Azure AD-gebruikersobjecten en de gebruikersobjecten van elke SaaS-toepassing. Niet alle SaaS-apps schakelen groepskenmerken in.

Azure AD ondersteunt door directe toewijzing van attribuut tot attribuut, het verstrekken van constante waarden of [het schrijven van expressies voor kenmerktoewijzingen](../app-provisioning/functions-for-customizing-application-data.md). Deze flexibiliteit geeft u een goede controle over wat er wordt ingevuld in het kenmerk van het beoogde systeem. U [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) en Graph Explorer gebruiken om uw gebruikerstoewijzingen en -schema te exporteren naar een JSON-bestand en het opnieuw te importeren in Azure AD.

Zie [Gebruikersinrichting sas-toewijzingen voor SaaS-toepassingen aanpassen voor](../app-provisioning/customize-application-attributes.md)meer informatie in Azure Active Directory .

### <a name="special-considerations-for-user-provisioning"></a>Bijzondere overwegingen voor de gebruikersvoorziening

Houd rekening met het volgende om problemen na de implementatie te verminderen:

* Controleer of de kenmerken die worden gebruikt om gebruikers-/groepsobjecten tussen bron- en doeltoepassingen in kaart te brengen, veerkrachtig zijn. Ze mogen er niet voor zorgen dat gebruikers/groepen onjuist worden ingericht als de kenmerken veranderen (een gebruiker wordt bijvoorbeeld naar een ander deel van het bedrijf verplaatst).

* Toepassingen kunnen specifieke beperkingen en/of vereisten hebben waaraan moet worden voldaan om de inrichting van de gebruiker correct te laten werken. Slack voert bijvoorbeeld waarden voor bepaalde kenmerken af. Raadpleeg [automatische gebruikersinrichting tutorials](../saas-apps/tutorial-list.md) die specifiek zijn voor elke toepassing.

* Bevestig de schemaconsistentie tussen bron- en doelsystemen. Veelvoorkomende problemen zijn kenmerken zoals UPN of e-mail die niet overeenkomen. UPN in Azure AD is *john_smith@contoso.com* bijvoorbeeld ingesteld als en *jsmith@contoso.com*in de app. Zie De verwijzing [naar het gebruikers- en groepsschema](../app-provisioning/use-scim-to-provision-users-and-groups.md)voor meer informatie .

## <a name="plan-testing-and-security"></a>Testen en beveiliging plannen

In elke fase van uw implementatie u ervoor zorgen dat u test dat de resultaten zijn zoals verwacht en controleert u de inrichtingscycli.

### <a name="plan-testing"></a>Testen plannen

Zodra u de installatie van automatische gebruikers voor de toepassing hebt geconfigureerd, voert u testaanvragen uit om te controleren of deze oplossing voldoet aan de vereisten van uw organisatie.

| Scenario's| Verwachte resultaten |
| - | - |
| Gebruiker wordt toegevoegd aan een groep die is toegewezen aan het doelsysteem | Gebruikersobject is ingericht in het doelsysteem. <br>De gebruiker kan zich aanmelden bij het doelsysteem en de gewenste acties uitvoeren. |
| Gebruiker wordt verwijderd uit een groep die is toegewezen aan het doelsysteem | Het object van de gebruiker wordt gedeprovisioneerd in het doelsysteem.<br>De gebruiker kan zich niet aanmelden bij het doelsysteem. |
| Gebruikersgegevens worden op welke manier dan ook bijgewerkt in Azure AD | Bijgewerkte gebruikerskenmerken worden na een incrementele cyclus weergegeven in het doelsysteem |
| Gebruiker is buiten het bereik | Het object van de gebruiker is uitgeschakeld of verwijderd. <br>Opmerking: Dit gedrag wordt overschreven voor [workday provisioning](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Beveiliging plannen

Het is gebruikelijk dat een beveiligingsbeoordeling vereist is als onderdeel van een implementatie. Als u een beveiligingsbeoordeling nodig hebt, raadpleegt u de vele Azure [AD-whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) die een overzicht bieden voor identiteit als service.

### <a name="plan-rollback"></a>Terugdraaien plannen

Als de implementatie van de automatische gebruiker die de implementatie indient niet naar wens werkt in de productieomgeving, kunnen de volgende terugdraaistappen hieronder u helpen om terug te keren naar een eerdere bekende goede staat:

1. Controleer het [inrichtingsrapport](../app-provisioning/check-status-user-account-provisioning.md) en [de inrichtingslogboeken](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) om te bepalen welke onjuiste bewerkingen op de betrokken gebruikers en/of groepen hebben plaatsgevonden.

1. Gebruik het inrichten van controlelogboeken om de laatst bekende goede staat van de betrokken gebruikers en/of groepen te bepalen. Bekijk ook de bronsystemen (Azure AD of AD).

1. Werk samen met de eigenaar van de toepassing om de gebruikers en/of groepen die rechtstreeks in de toepassing zijn betrokken, bij te werken met behulp van de laatst bekende goede statuswaarden.

## <a name="deploy-automatic-user-provisioning-service"></a>Automatische service voor het inrichten van gebruikers implementeren

Kies de stappen die aansluiten bij uw oplossingsvereisten.

### <a name="prepare-for-the-initial-cycle"></a>Bereid je voor op de eerste cyclus

Wanneer de Azure AD-inrichtingsservice voor de eerste keer wordt uitgevoerd, maakt de eerste cyclus ten opzichte van het bronsysteem en doelsystemen een momentopname van alle gebruikersobjecten voor elk doelsysteem.

Bij het inschakelen van automatische inrichting voor een toepassing kan de eerste cyclus 20 minuten tot enkele uren duren. De duur is afhankelijk van de grootte van de Azure AD-map en het aantal gebruikers dat in de inrichtingsruimte is. Zie [hoe u de provisioning-prestaties verbeteren](../app-provisioning/application-provisioning-when-will-provisioning-finish.md).

De inrichtingsservice slaat de status van beide systemen na de eerste cyclus op, waardoor de prestaties van de volgende incrementele cycli worden verbeterd.

### <a name="configure-automatic-user-provisioning"></a>Automatische gebruikersinrichting configureren

Gebruik de [Azure-portal](https://portal.azure.com/) om automatische gebruikersaccountinrichting en ontvoorziening te beheren voor toepassingen die deze ondersteunen. De stappen uitvoeren in [Hoe stel ik automatische inrichting in voor een toepassing?](../app-provisioning/user-provisioning.md)

De Azure AD-service voor het inrichten van gebruikers kan ook worden geconfigureerd en beheerd met behulp van de [Microsoft Graph-API.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

## <a name="manage-automatic-user-provisioning"></a>Automatische gebruikersvoorziening beheren

Nu u de oplossing hebt geïmplementeerd, moet u de oplossing beheren.

### <a name="monitor-user-provisioning-operation-health"></a>Status van de gebruikersinrichtingsbewerking controleren

Na een succesvolle [eerste cyclus](../app-provisioning/user-provisioning.md)voert de Azure AD-inrichtingsservice incrementele updates voor onbepaalde tijd uit, met intervallen die specifiek zijn voor elke toepassing, totdat een van de volgende gebeurtenissen optreedt:

* De service wordt handmatig gestopt en een nieuwe eerste cyclus wordt geactiveerd met behulp van de [Azure-portal](https://portal.azure.com/)of met de juiste [Microsoft Graph API-opdracht.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* Een nieuwe eerste cyclus wordt geactiveerd door een wijziging in kenmerktoewijzingen of scopingfilters.

* Het inrichtingsproces gaat in quarantaine vanwege een hoog foutenpercentage en blijft langer dan vier weken in quarantaine wanneer het automatisch wordt uitgeschakeld.

Als u deze gebeurtenissen en alle andere activiteiten die door de inrichtingsservice worden uitgevoerd, wilt controleren, raadpleegt u Azure [AD-inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

Als u wilt begrijpen hoe lang de inrichtingscycli duren en de voortgang van de inrichtingstaak controleren, u [de status van de gebruikersinrichting controleren.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

### <a name="gain-insights-from-reports"></a>Krijg inzichten uit rapporten

Azure AD kan [aanvullende inzichten](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bieden in het gebruik en de operationele status van uw organisatie via controlelogboeken en rapporten.

Beheerders moeten het inrichtingsoverzichtsrapport controleren om de operationele status van de inrichtingstaak te controleren. Alle activiteiten die door de inrichtingsservice worden uitgevoerd, worden geregistreerd in de Azure AD-controlelogboeken. Zie [Zelfstudie: Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md).

We raden u aan eigenaar te worden van en deze rapporten te gebruiken op een cadans die voldoet aan de vereisten van uw organisatie. Azure AD bewaart de meeste controlegegevens gedurende 30 dagen.

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de volgende koppelingen om eventuele problemen op te lossen die tijdens de inrichting kunnen opduiken:

* [Probleem met het configureren van gebruikersinrichting voor een Azure AD Gallery-toepassing](../app-provisioning/application-provisioning-config-problem.md)

* [Een kenmerk synchroniseren van uw on-premises Active Directory met Azure AD voor inrichten op een toepassing](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Het inrichten van gebruikers op een Azure AD Gallery-toepassing duurt uren of langer](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Probleem met het opslaan van beheerdersreferenties tijdens het configureren van gebruikersvoorziening in een Azure Active Directory Gallery-toepassing](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Er worden geen gebruikers ingericht voor een Azure AD Gallery-toepassing](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Verkeerde set gebruikers wordt ingericht voor een Azure AD Gallery-toepassing](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Nuttige documentatie

* [Expressies schrijven voor kenmerktoewijzingen](../app-provisioning/functions-for-customizing-application-data.md)

* [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Verwijdering van gebruikersaccounts die buiten het bereik vallen overslaan](skip-out-of-scope-deletions.md)

* [Azure AD Connect Provisioning Agent: Releasegeschiedenis versie](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Resources

* [Productfeedback geven](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Blijf op de hoogte van wat er nieuw is met Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack overflow Azure AD-forum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Volgende stappen
* [Automatische gebruikersvoorziening configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Uw inrichtingsconfiguratie exporteren of importeren met behulp van Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Expressies schrijven voor kenmerktoewijzingen in azure Active-map](../app-provisioning/functions-for-customizing-application-data.md)
