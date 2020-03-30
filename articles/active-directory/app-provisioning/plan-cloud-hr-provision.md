---
title: Cloud HR-toepassing plannen voor Azure Active Directory-gebruikersvoorziening
description: In dit artikel wordt het implementatieproces beschreven voor de integratie van cloud HR-systemen, zoals Workday en SuccessFactors, met Azure Active Directory. De integratie van Azure AD met uw cloud HR-systeem resulteert in een compleet beheersysteem voor identiteitslevenscyclus.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522429"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Cloud HR-toepassing plannen voor Azure Active Directory-gebruikersvoorziening

It-medewerkers vertrouwden in het verleden op handmatige methoden om werknemers te maken, bij te werken en te verwijderen. Ze hebben methoden gebruikt, zoals het uploaden van CSV-bestanden of aangepaste scripts om werknemersgegevens te synchroniseren. Deze inrichtingsprocessen zijn foutgevoelig, onzeker en moeilijk te beheren.

Als u de identiteitslevenscyclus van werknemers, leveranciers of voorwaardelijke werknemers wilt beheren, biedt [azure Active Directory (Azure AD) service voor het inrichten van gebruikers](../app-provisioning/user-provisioning.md) integratie met HR-toepassingen (Human Resources) in de cloud. Voorbeelden van toepassingen zijn Workday of SuccessFactors.

Azure AD gebruikt deze integratie om de volgende cloud HR-toepassingswerkstromen (app)-werkstromen in te schakelen:

- **Gebruikers inrichten in Active Directory:** Inrichten van geselecteerde sets gebruikers vanuit een cloud HR-app in een of meer Active Directory-domeinen.
- **Cloudgebruikers inrichten in Azure AD:** In scenario's waarin Active Directory niet wordt gebruikt, u gebruikers rechtstreeks vanuit de HR-cloud-app naar Azure AD inrichten.
- **Schrijf terug naar de HR-cloud-app:** Schrijf de e-mailadressen en gebruikerskenmerken van Azure AD terug naar de HR-cloud-app.

> [!NOTE]
> Dit implementatieplan laat zien hoe u uw cloud-HR-app-werkstromen implementeert met Azure AD-gebruikersinrichting. Zie [Een automatische implementatie van gebruikersinrichting](https://aka.ms/deploymentplans/provisioning)plannen voor informatie over het implementeren van automatische gebruikersinrichting naar software as a service (SaaS) apps.

## <a name="enabled-hr-scenarios"></a>Ingeschakelde HR-scenario's

De Azure AD-service voor het inrichten van gebruikers maakt automatisering van de volgende HR-gebaseerde scenario's voor het beheer van de identiteitslevenscyclus mogelijk:

- **Nieuwe medewerker inhuren:** Wanneer een nieuwe werknemer wordt toegevoegd aan de HR-cloud-app, wordt er automatisch een gebruikersaccount gemaakt in Active Directory en Azure AD met de optie om de e-mailadres- en gebruikersnaamkenmerken terug te schrijven naar de HR-cloud-app.
- **Werknemerattribuut en profielupdates:** Wanneer een werknemer record zoals naam, titel of manager wordt bijgewerkt in de cloud HR app, hun gebruikersaccount wordt automatisch bijgewerkt in Active Directory en Azure AD.
- **Beëindigingen van werknemers:** Wanneer een werknemer wordt beëindigd in de HR-cloud-app, wordt zijn gebruikersaccount automatisch uitgeschakeld in Active Directory en Azure AD.
- **Werknemer rehires:** Wanneer een werknemer opnieuw wordt aangenomen in de HR-cloud-app, kan zijn oude account automatisch opnieuw worden geactiveerd of opnieuw worden ingericht voor Active Directory en Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Voor wie is deze integratie het meest geschikt?

De integratie van de CLOUD HR-app met Azure AD-gebruikersinrichting is bij uitstek geschikt voor organisaties die:

- Zoek een vooraf gebouwde, cloudgebaseerde oplossing voor het inrichten van CLOUD HR-gebruikers.
- Directe gebruikersvoorziening vereisen vanuit de cloud HR-app naar Active Directory of Azure AD.
- Vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit de HR-cloud-app.
- Gebruikers moeten worden gesynchroniseerd, verplaatst en laten synchroniseren met een of meer Active Directory-forests, domeinen en -gegevens op basis van wijzigingsgegevens die zijn gedetecteerd in de HR-cloud-app.
- Gebruik Office 365 voor e-mail.

## <a name="learn"></a>Leren

Gebruikersinrichting creëert een basis voor doorlopende identiteitsgovernance. Het verbetert de kwaliteit van bedrijfsprocessen die afhankelijk zijn van gezaghebbende identiteitsgegevens.

### <a name="terms"></a>Voorwaarden

In dit artikel worden de volgende termen gebruikt:

- **Bronsysteem:** de opslagplaats van gebruikers waaruit Azure AD voorziet. Een voorbeeld is een cloud HR-app zoals Workday of SuccessFactors.
- **Doelsysteem:** de opslagplaats van gebruikers waaraan de Azure AD-voorzieningen worden getroffen. Voorbeelden zijn Active Directory, Azure AD, Office 365 of andere SaaS-apps.
- **Joiners-Movers-Leavers proces**: Een term die wordt gebruikt voor nieuwe medewerkers, transfers en beëindiging met behulp van een cloud HR-app als een systeem van records. Het proces wordt voltooid wanneer de service de benodigde kenmerken van het doelsysteem indient.

### <a name="key-benefits"></a>Belangrijkste voordelen

Deze mogelijkheid van HR-gestuurde IT-provisioning biedt de volgende belangrijke zakelijke voordelen:

- **Verhoog de productiviteit:** U nu de toewijzing van gebruikersaccounts en Office 365-licenties automatiseren en toegang bieden tot belangrijke groepen. Het automatiseren van opdrachten geeft nieuwe medewerkers direct toegang tot hun jobtools en verhoogt de productiviteit.
- **Risico's beheren:** U de beveiliging verhogen door wijzigingen te automatiseren op basis van de status van werknemers of groepslidmaatschappen, waarbij gegevens binnenkomen vanuit de HR-cloud-app. Het automatiseren van wijzigingen zorgt ervoor dat gebruikersidentiteiten en toegang tot belangrijke apps automatisch worden bijgewerkt wanneer gebruikers de organisatie verlaten of verlaten.
- **Naleving en governance aanpakken:** Azure AD ondersteunt native auditlogs voor gebruikersinrichtingsverzoeken die worden uitgevoerd door apps van zowel bron- als doelsystemen. Met controle u vanaf één scherm bijhouden wie toegang heeft tot de apps.
- **Kosten beheren:** Automatische inrichting vermindert de kosten door inefficiënties en menselijke fouten in verband met handmatige inlevering te voorkomen. Het vermindert de behoefte aan op maat ontwikkelde oplossingen voor het inrichten van gebruikers die in de loop van de tijd zijn gebouwd met behulp van verouderde en verouderde platforms.

### <a name="licensing"></a>Licentieverlening

Als u de CLOUD HR-app wilt configureren voor azure AD-gebruikersinrichtingsintegratie, hebt u een geldige [Azure AD Premium-licentie](https://azure.microsoft.com/pricing/details/active-directory/) en een licentie nodig voor de HR-cloud-app, zoals Werkdag of Succesfactoren.

U hebt ook een geldige Azure AD Premium P1- of hogere abonnementslicentie nodig voor elke gebruiker die afkomstig is uit de HR-cloud-app en is ingericht voor Active Directory of Azure AD. Een onjuist aantal licenties in de HR-cloud-app kan leiden tot fouten tijdens de inrichting van de gebruiker.

### <a name="prerequisites"></a>Vereisten

- Toegang tot globale ad-beheerder van Azure AD om de azure AD Connect-inrichtingsagent te configureren.
- Een test- en productieexemplaar van de cloud HR-app.
- Beheerdersmachtigingen in de HR-cloud-app om een systeemintegratiegebruiker te maken en wijzigingen aan te brengen in het testen van werknemersgegevens voor testdoeleinden.
- Voor gebruikersbepaling in Active Directory is een server met Windows Server 2012 of hoger met .NET 4.7.1+ runtime vereist om de [Azure AD Connect-inrichtingsagent](https://go.microsoft.com/fwlink/?linkid=847801)te hosten.
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) voor het synchroniseren van gebruikers tussen Active Directory en Azure AD.

### <a name="training-resources"></a>Opleidingsmiddelen

| **Resources** | **Koppeling en beschrijving** |
|:-|:-|
| Video's | [Wat is gebruikersvoorziening in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Gebruikersvoorziening implementeren in Active Azure Directory](https://youtu.be/pKzyts6kfrw) |
| Zelfstudies | [Lijst met zelfstudies over het integreren van SaaS-apps met Azure AD](../saas-apps/tutorial-list.md) |
| | [Zelfstudie: Werkdag configureren voor automatische gebruikersinrichting](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Veelgestelde vragen | [Geautomatiseerde gebruikersinrichting](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Inrichten van Workday naar Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architectuur voor de oplossing

In het volgende voorbeeld wordt de end-to-end-inrichtingsoplossingsarchitectuur voor veelvoorkomende hybride omgevingen beschreven en wordt het volgende beschreven:

- **Gezaghebbende HR-gegevensstroom van de HR-app in de cloud naar Active Directory.** In deze flow wordt het HR-event (Joiners-Movers-Leavers process) gestart in de cloud HR app tenant. De Azure AD-inrichtingsservice en Azure AD Connect-inrichtingsagent voorzien de gebruikersgegevens van de tenant van de CLOUD HR-app in Active Directory. Afhankelijk van de gebeurtenis kan dit leiden tot het maken, bijwerken, inschakelen en uitschakelen van bewerkingen in Active Directory.
- **Synchroniseer met Azure AD en schrijf e-mail en gebruikersnaam terug van de on-premises Active Directory naar de HR-cloud-app.** Nadat de accounts zijn bijgewerkt in Active Directory, wordt deze gesynchroniseerd met Azure AD via Azure AD Connect. De e-mailadressen en gebruikerskenmerken kunnen worden teruggeschreven naar de tenant van de HR-app in de cloud.

![Werkstroomdiagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beschrijving van de werkstroom

De volgende belangrijke stappen worden aangegeven in het diagram:  

1. **Het HR-team** voert de transacties uit in de tenant van de CLOUD HR-app.
2. **Azure AD-inrichtingsservice** voert de geplande cycli uit vanuit de tenant van de CLOUD HR-app en identificeert wijzigingen die moeten worden verwerkt voor synchronisatie met Active Directory.
3. **Azure AD-inrichtingsservice** roept de Azure AD Connect-inrichtingsagent op met een payload van het verzoek die Active Directory-account maakt, bijwerkt, inschakelt en uitschakelt.
4. **Azure AD Connect-inrichtingsagent** gebruikt een serviceaccount om Active Directory-accountgegevens te beheren.
5. **Azure AD Connect** voert [deltasynchronisatie](../hybrid/how-to-connect-sync-whatis.md) uit om updates op te halen in Active Directory.
6. **Active Directory-updates** worden gesynchroniseerd met Azure AD.
7. **Azure AD-inrichtingsservice** schrijft e-mailkenmerk en gebruikersnaam terug van Azure AD naar de tenant voor de HR-app in de cloud.

## <a name="plan-the-deployment-project"></a>Het implementatieproject plannen

Houd rekening met uw organisatorische behoeften terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>Betrek de juiste stakeholders

Wanneer technologieprojecten mislukken, doen ze dit meestal vanwege de onafwijkende verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden indienst gaat.](https://aka.ms/deploymentplans) Zorg er ook voor dat de rollen van belanghebbenden in het project goed worden begrepen. Documenteer de stakeholders en hun projectinput en accountabilities.

Neem een vertegenwoordiger van de HR-organisatie op die input kan leveren voor bestaande HR-bedrijfsprocessen en werknemersidentiteit plus vereisten voor het verwerken van taakgegevens.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is cruciaal voor het succes van elke nieuwe dienst. Communiceer proactief met uw gebruikers over wanneer en hoe hun ervaring zal veranderen. Laat ze weten hoe ze ondersteuning kunnen krijgen als ze problemen ervaren.

### <a name="plan-a-pilot"></a>Een pilot plannen

Het integreren van HR-bedrijfsprocessen en identiteitsworkflows vanuit de HR-cloud-app naar doelsystemen vereist een aanzienlijke hoeveelheid gegevensvalidatie, gegevenstransformatie, gegevensreiniging en end-to-end-tests voordat u de oplossing in productie nemen.

Voer de eerste configuratie uit in een [pilotomgeving](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) voordat u deze schaalt naar alle gebruikers in productie.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecteer cloud HR-inrichtingsconnector-apps

Als u Azure AD-inrichtingswerkstromen tussen de HR-cloud-app en Active Directory wilt faciliteren, u meerdere inrichtingsconnector-apps toevoegen vanuit de azure AD-app-galerie:

- **Cloud HR-app naar Active Directory-gebruikersinrichting:** deze inrichtingsconnector-app vergemakkelijkt het inrichten van gebruikersaccounts vanuit de CLOUD HR-app naar één Active Directory-domein. Als u meerdere domeinen hebt, u één exemplaar van deze app toevoegen vanuit de Azure AD-app-galerie voor elk Active Directory-domein waaraan u moet inrichten.
- **Cloud HR-app naar Azure AD-gebruikersinrichting**: Hoewel Azure AD Connect het hulpmiddel is dat moet worden gebruikt om Active Directory-gebruikers te synchroniseren met Azure AD, kan deze inrichtingsconnector-app worden gebruikt om de inrichting van cloudgebruikers vanuit de cloud-HR-app naar één Azure AD-tenant te vergemakkelijken.
- **Terugschrijven van de Cloud HR-app**: Deze inrichtingsconnector-app vergemakkelijkt het terugschrijven van de e-mailadressen van de gebruiker van Azure AD naar de HR-cloud-app.

In de volgende afbeelding worden bijvoorbeeld de Workday-connector-apps weergegeven die beschikbaar zijn in de azure AD-app-galerie.

![Azure Active Directory-portal-app-galerie](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Beslissingsstroomdiagram

Gebruik het volgende beslissingsstroomdiagram om te bepalen welke HR-provisioning-apps in de cloud relevant zijn voor uw scenario.

![Beslissingsstroomdiagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>De topologie van de Azure AD Connect-inrichtingsagent-implementatie ontwerpen

De integratie tussen de HR-cloud-app en Active Directory vereist vier componenten:

- Cloud HR-app-tenant
- Inrichtingsconnector-app
- Azure AD Connect-inrichtingsagent
- Active Directory-domein

De topologie van de Azure AD Connect-inrichtingsagent is afhankelijk van het aantal tenants in de CLOUD-HR-app en de Active Directory-onderliggende domeinen die u wilt integreren. Als u meerdere Active Directory-domeinen hebt, hangt dit af van de vraag of de Active Directory-domeinen aaneengesloten of [disjunctuur](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)zijn.

Kies op basis van uw beslissing een van de implementatiescenario's:

- Eenenkele tenant voor HR-app in één cloud > één of meerdere Active Directory-onderliggende domeinen targeten in een vertrouwd forest
- Tenant voor één cloud-HR-app -> meerdere onderliggende domeinen targeten in een onsamenhangend Active Directory-forest

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Eenenkele tenant voor HR-app in één cloud > één of meerdere Active Directory-onderliggende domeinen targeten in een vertrouwd forest

Wij raden de volgende productieconfiguratie aan:

|Vereiste|Aanbeveling|
|:-|:-|
|Aantal Azure AD Connect-inrichtingsagents dat moet worden geïmplementeerd|Twee (voor hoge beschikbaarheid en failover)
|Aantal inrichtingsconnector-apps dat moet worden geconfigureerd|Eén app per onderliggend domein|
|Serverhost voor Azure AD Connect-inrichtingsagent|Windows 2012 R2+ met zichtlijn naar geolocatie Active Directory-domeincontrollers</br>Kan naast azure AD Connect-service bestaan|

![Stroom naar agenten op het terrein](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Tenant voor één cloud-HR-app -> meerdere onderliggende domeinen targeten in een onsamenhangend Active Directory-forest

Dit scenario omvat het inrichten van gebruikers uit de CLOUD HR-app naar domeinen in onsamenhangende Active Directory-forests.

Wij raden de volgende productieconfiguratie aan:

|Vereiste|Aanbeveling|
|:-|:-|
|Aantal Azure AD Connect-inrichtingsagents om on-premises te implementeren|Twee per disjoint Active Directory-forest|
|Aantal inrichtingsconnector-apps dat moet worden geconfigureerd|Eén app per onderliggend domein|
|Serverhost voor Azure AD Connect-inrichtingsagent|Windows 2012 R2+ met zichtlijn naar geolocatie Active Directory-domeincontrollers</br>Kan naast azure AD Connect-service bestaan|

![Single cloud HR-app tenant disjoint Active Directory forest](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Vereisten voor azure AD Connect-inrichtingsagent

De oplossing voor het inrichten van Active Directory-gebruikers in active directory vereist dat u een of meer Azure AD Connect-provisioningagents implementeert op servers waarop Windows 2012 R2 of hoger wordt uitgevoerd. De servers moeten minimaal 4 GB RAM en .NET 4.7.1+ runtime hebben. Controleer of de hostserver netwerktoegang heeft tot het doelActive Directory-domein.

Om de on-premises omgeving voor te bereiden, registreert de wizard Azure AD Connect-inrichtingsagent configuratie de agent met uw Azure AD-tenant, [opent poorten,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [biedt toegang tot URL's](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)en ondersteunt [de uitgaande HTTPS-proxyconfiguratie](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

De inrichtingsagent gebruikt een serviceaccount om te communiceren met de Active Directory-domeinen. Voordat u de agent installeert, maakt u een serviceaccount in Active Directory: gebruikers en computers dat aan de volgende vereisten voldoet:

- Een wachtwoord dat niet verloopt
- Gedelegeerde controlemachtigingen voor het lezen, maken, verwijderen en beheren van gebruikersaccounts

U domeincontrollers selecteren die inrichtingsaanvragen moeten afhandelen. Als u meerdere geografisch gedistribueerde domeincontrollers hebt, installeert u de inrichtingsagent op dezelfde site als de domeincontrollers van uw voorkeur. Deze positionering verbetert de betrouwbaarheid en prestaties van de end-to-end oplossing.

Voor hoge beschikbaarheid u meer dan één Azure AD Connect-inrichtingsagent implementeren. Registreer de agent om dezelfde set on-premises Active Directory-domeinen te verwerken.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Scopingfilters en kenmerktoewijzing plannen

Wanneer u inrichten vanuit de CLOUD HR-app naar Active Directory of Azure AD inschakelt, bepaalt de Azure-portal de kenmerkwaarden via kenmerktoewijzing.

### <a name="define-scoping-filters"></a>Scopingfilters definiëren

Gebruik [scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) om de op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers vanuit de HR-cloud-app moeten worden ingericht in Active Directory of Azure AD.

Wanneer u het Joiners-proces start, verzamelt u de volgende vereisten:

- Wordt de cloud HR-app gebruikt om zowel werknemers als voorwaardelijke werknemers aan boord te brengen?
- Bent u van plan om de cloud HR-app te gebruiken voor azure AD-gebruikersinrichting om zowel werknemers als voorwaardelijke werknemers te beheren?
- Bent u van plan om de cloud HR-app uit te rollen naar Azure AD-gebruikersvoorziening alleen voor een subset van de gebruikers van de CLOUD HR-app? Een voorbeeld hiervan zijn alleen werknemers.

Afhankelijk van uw vereisten u bij het configureren van kenmerktoewijzingen het veld **Bronobjectbereik** instellen om te selecteren welke sets gebruikers in de HR-cloud-app in het bereik moeten zijn voor de inrichting van Active Directory. Zie de zelfstudie van de CLOUD HR-app voor veelgebruikte scopingfilters voor meer informatie.

### <a name="determine-matching-attributes"></a>Overeenkomende kenmerken bepalen

Met provisioning krijgt u de mogelijkheid om bestaande accounts tussen het bron- en doelsysteem te matchen. Wanneer u de CLOUD HR-app integreert met de Azure AD-inrichtingsservice, u [kenmerktoewijzing configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) om te bepalen welke gebruikersgegevens vanuit de HR-cloud-app naar Active Directory of Azure AD moeten worden gebracht.

Wanneer u het Joiners-proces start, verzamelt u de volgende vereisten:

- Wat is de unieke ID in deze HR-cloud-app die wordt gebruikt om elke gebruiker te identificeren?
- Hoe ga je om met rehires vanuit het perspectief van de identiteitslevenscyclus? Houden rehires hun oude werknemer-iD's?
- Verwerkt u toekomstige gestarte huren en maakt u van tevoren Active Directory-accounts voor deze bestanden?
- Hoe gaat u vanuit het perspectief van de identiteitslevenscyclus om met de conversie van werknemers naar voorwaardelijke werknemers, of anderszins?
- Houden geconverteerde gebruikers hun oude Active Directory-accounts of krijgen ze nieuwe accounts?

Afhankelijk van uw vereisten ondersteunt Azure AD directe toewijzing van attribuut tot attribuut door constante waarden of [schrijfexpressies voor kenmerktoewijzingen](../app-provisioning/functions-for-customizing-application-data.md)te verstrekken. Deze flexibiliteit geeft u de ultieme controle over wat er wordt ingevuld in het doelkenmerk van de app. U de [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) en Graph Explorer gebruiken om de toewijzingen en het schema voor het inrichten van kenmerktoewijzingen en -schema voor gebruikers te exporteren naar een JSON-bestand en het opnieuw te importeren in Azure AD.

Het kenmerk in de HR-cloud-app dat de unieke werknemers-id vertegenwoordigt, wordt standaard gebruikt als het overeenkomende kenmerk *dat is toegewezen aan het unieke kenmerk in Active Directory.* In het app-scenario van Workday wordt bijvoorbeeld het kenmerk **Workday** **WorkerID** toegewezen aan het kenmerk Active Directory **employeeID.**

U meerdere overeenkomende kenmerken instellen en overeenkomende prioriteit toewijzen. Ze worden beoordeeld op overeenkomende voorrang. Zodra een overeenkomst wordt gevonden, worden geen verdere overeenkomende kenmerken geëvalueerd.

U ook [de standaardtoewijzingen voor kenmerken aanpassen,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)zoals het wijzigen of verwijderen van bestaande kenmerktoewijzingen. U ook nieuwe kenmerktoewijzingen maken op basis van uw bedrijfsbehoeften. Zie de zelfstudie van de CLOUD HR-app (zoals [Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)voor een lijst met aangepaste kenmerken die u wilt toewijzen.

### <a name="determine-user-account-status"></a>Gebruikersaccountstatus bepalen

Standaard wordt in de inrichtingsconnector-app de status van het HR-gebruikersprofiel toegewezen aan de status van het gebruikersaccount in Active Directory of Azure AD om te bepalen of het gebruikersaccount moet worden in- of uitgeschakeld.

Wanneer u het Joiners-Leavers-proces start, verzamelt u de volgende vereisten.

| Proces | Vereisten |
| - | - |
| **Joiners** | Hoe ga je om met rehires vanuit het perspectief van de identiteitslevenscyclus? Houden rehires hun oude werknemer-iD's? |
| | Verwerkt u toekomstige gestarte huren en maakt u van tevoren Active Directory-accounts voor deze bestanden? Zijn deze accounts gemaakt in een ingeschakelde of uitgeschakelde status? |
| | Hoe gaat u vanuit het perspectief van de identiteitslevenscyclus om met de conversie van werknemers naar voorwaardelijke werknemers, of anderszins? |
| | Houden geconverteerde gebruikers hun oude Active Directory-accounts of krijgen ze nieuwe accounts? |
| **Leavers** | Worden beëindigingen anders behandeld voor werknemers en voorwaardelijke werknemers in Active Directory? |
| | Welke effectieve data worden in aanmerking genomen voor het verwerken van gebruikersbeëindiging? |
| | Welke invloed hebben werknemers- en voorwaardelijke werknemersconversies op bestaande Active Directory-accounts? |
| | Hoe verwerkt u de bewerking Intrekken in Active Directory? Intrekken van bewerkingen moet worden afgehandeld als toekomstige gedateerde aanwervingen worden gemaakt in Active Directory als onderdeel van het Joiner-proces. |

Afhankelijk van uw vereisten u de toewijzingslogica aanpassen met [Azure AD-expressies,](../app-provisioning/functions-for-customizing-application-data.md) zodat het Active Directory-account is ingeschakeld of uitgeschakeld op basis van een combinatie van gegevenspunten.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Cloud HR-app toewijzen aan active directory-gebruikerskenmerken

Elke HR-cloud-app wordt geleverd met de standaard cloud HR-app naar Active Directory-toewijzingen.

Wanneer u het Joiners-Movers-Leavers-proces start, verzamelt u de volgende vereisten.

| Proces | Vereisten |
| - | - |
| **Joiners** | Is het proces voor het maken van Active Directory-account handmatig, geautomatiseerd of gedeeltelijk geautomatiseerd? |
| | Bent u van plan aangepaste kenmerken uit de HR-cloud-app te propageren naar Active Directory? |
| **Verhuizers** | Welke eigenschappen wilt u verwerken wanneer een Movers-bewerking plaatsvindt in de cloud HR-app? |
| | Voert u specifieke attribuutvalidaties uit op het moment van gebruikersupdates? Zo ja, geef details. |
| **Leavers** | Worden beëindigingen anders behandeld voor werknemers en voorwaardelijke werknemers in Active Directory? |
| | Welke effectieve data worden in aanmerking genomen voor het verwerken van gebruikersbeëindiging? |
| | Welke invloed hebben werknemers- en voorwaardelijke werknemersconversies op bestaande Active Directory-accounts? |

Afhankelijk van uw vereisten u de toewijzingen aanpassen om aan uw integratiedoelen te voldoen. Zie de specifieke zelfstudie van de HR-app in de cloud (zoals [Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)voor een lijst met aangepaste kenmerken die u wilt toewijzen.

### <a name="generate-a-unique-attribute-value"></a>Een unieke kenmerkwaarde genereren

Wanneer u het Joiners-proces start, moet u mogelijk unieke kenmerkwaarden genereren wanneer u kenmerken instelt zoals CN, samAccountName en het UPN, dat unieke beperkingen heeft.

De Azure AD-functie [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) evalueert elke regel en controleert vervolgens de waarde die is gegenereerd op uniciteit in het doelsysteem. Zie Bijvoorbeeld [unieke waarde genereren voor het kenmerk USERPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Deze functie wordt momenteel alleen ondersteund voor Workday naar Active Directory-gebruikersinrichting. Het kan niet worden gebruikt met andere inrichting apps.

### <a name="configure-active-directory-ou-container-assignment"></a>Containertoewijzing voor Active Directory-OU configureren

Het is een veelvoorkomende vereiste om Active Directory-gebruikersaccounts in containers te plaatsen op basis van bedrijfseenheden, locaties en afdelingen. Wanneer u een verhuizersproces initieert en als er een wijziging van de toezichthoudende organisatie is, moet u de gebruiker mogelijk van de ene organisatie voor auto's naar de andere verplaatsen in Active Directory.

Gebruik de functie [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) om de bedrijfslogica voor de toewijzing van de organisatie-eenheid te configureren en deze toe te wijs aan de bovenliggende naam Van active **directory-kenmerkDistinguishedName**.

Als u bijvoorbeeld gebruikers wilt maken in OU op basis van het HR-kenmerk **Gemeente,** u de volgende expressie gebruiken:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Met deze uitdrukking wordt, als de gemeentewaarde Dallas, Austin, Seattle of Londen is, het gebruikersaccount in de bijbehorende ou gemaakt. Als er geen overeenkomst is, wordt het account gemaakt in de standaard-eenheid.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plannen voor het bezorgen van wachtwoorden van nieuwe gebruikersaccounts

Wanneer u het Joiners-proces start, moet u een tijdelijk wachtwoord van nieuwe gebruikersaccounts instellen en leveren. Met de inrichten van de Cloud HR naar Azure AD-gebruiker u de SSPR-mogelijkheid (Azure AD [self-service password reset)](../authentication/quickstart-sspr.md) voor de gebruiker op dag één uitrollen.

SSPR is een eenvoudig middel voor IT-beheerders om gebruikers in staat te stellen hun wachtwoorden te resetten of hun accounts te ontgrendelen. U het kenmerk **Mobiel nummer** vanuit de HR-cloud-app inrichten op Active Directory en synchroniseren met Azure AD. Nadat het kenmerk **Mobiele nummer** zich in Azure AD bevindt, u SSPR inschakelen voor het account van de gebruiker. Vervolgens kan de nieuwe gebruiker op dag één het geregistreerde en geverifieerde mobiele nummer gebruiken voor verificatie.

## <a name="plan-for-initial-cycle"></a>Plannen voor de eerste cyclus

Wanneer de Azure AD-inrichtingsservice voor de eerste keer wordt uitgevoerd, wordt een [eerste cyclus](../app-provisioning/how-provisioning-works.md#initial-cycle) uitgevoerd ten opzichte van de HR-cloud-app om een momentopname te maken van alle gebruikersobjecten in de HR-cloud-app. De tijd die nodig is voor de eerste cycli is direct afhankelijk van hoeveel gebruikers aanwezig zijn in het bronsysteem. De eerste cyclus voor sommige cloud HR-app-tenants met meer dan 100.000 gebruikers kan lang duren.

**Voor grote hr-app-tenants in de cloud (>30.000 gebruikers)** voert u de eerste cyclus in progressieve fasen uit. Start de incrementele updates pas nadat u hebt gevalideerd dat de juiste kenmerken zijn ingesteld in Active Directory voor verschillende scenario's voor het inrichten van gebruikers. Volg de bestelling hier.

1. Voer de eerste cyclus alleen uit voor een beperkte groep gebruikers door het [scopingfilter](#plan-scoping-filters-and-attribute-mapping)in te stellen.
2. Controleer de inrichting van Active Directory-accounts en de kenmerkwaarden die zijn ingesteld voor de gebruikers die voor de eerste uitvoering zijn geselecteerd. Als het resultaat aan uw verwachtingen voldoet, breidt u het scopingfilter uit om geleidelijk meer gebruikers op te nemen en de resultaten voor de tweede run te verifiëren.

Nadat u tevreden bent met de resultaten van de eerste cyclus voor testgebruikers, start u de [incrementele updates](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Testen en beveiliging plannen

Controleer in elke fase van uw implementatie vanaf de eerste pilot tot het inschakelen van gebruikersvoorzieningen dat u test of de resultaten zoals verwacht zijn en controleert u de inrichtingscycli.

### <a name="plan-testing"></a>Testen plannen

Nadat u de HR-app in de cloud hebt geconfigureerd voor azure AD-gebruikersinrichting, voert u testcases uit om te controleren of deze oplossing voldoet aan de vereisten van uw organisatie.

|Scenario's|Verwachte resultaten|
|:-|:-|
|Nieuwe medewerker wordt aangenomen in de cloud HR app.| - Het gebruikersaccount is opgenomen in Active Directory.</br>- De gebruiker kan zich aanmelden bij Active Directory-domein-apps en de gewenste acties uitvoeren.</br>- Als Azure AD Connect-synchronisatie is geconfigureerd, wordt het gebruikersaccount ook gemaakt in Azure AD.
|De gebruiker wordt beëindigd in de HR-cloud-app.|- Het gebruikersaccount is uitgeschakeld in Active Directory.</br>- De gebruiker kan zich niet aanmelden bij bedrijfsapps die worden beschermd door Active Directory.
|Gebruikerstoezichtorganisatie wordt bijgewerkt in de HR-cloud-app.|Op basis van de kenmerktoewijzing wordt het gebruikersaccount verplaatst van de ene organisatieorganisatie naar de andere in Active Directory.|
|HR werkt de manager van de gebruiker bij in de HR-cloud-app.|Het veld Manager in Active Directory wordt bijgewerkt om de naam van de nieuwe manager weer te geven.|
|HR huurt een medewerker in een nieuwe rol.|Gedrag is afhankelijk van hoe de HR-cloud-app is geconfigureerd om werknemers-i-apps te genereren:</br>- Als de oude werknemers-id opnieuw wordt gebruikt voor een herhuur, schakelt de connector het bestaande Active Directory-account voor de gebruiker in.</br>- Als de opnieuw aangenomen werknemer-id een nieuwe werknemers-id krijgt, maakt de connector een nieuw Active Directory-account voor de gebruiker.|
|HR zet de werknemer om in een contractwerknemer of vice versa.|Er wordt een nieuw Active Directory-account gemaakt voor de nieuwe persona en het oude account wordt uitgeschakeld op de ingangsdatum van de conversie.|

Gebruik de vorige resultaten om te bepalen hoe u de implementatie van uw automatische gebruiker inproductie naar productie overzetten op basis van uw vastgestelde tijdlijnen.

> [!TIP]
> Gebruik technieken zoals gegevensreductie en het schrobben van gegevens wanneer u de testomgeving vernieuwt met productiegegevens om gevoelige persoonlijke gegevens te verwijderen of te maskeren om te voldoen aan privacy- en beveiligingsnormen. 

### <a name="plan-security"></a>Beveiliging plannen

Het is gebruikelijk dat een beveiligingsbeoordeling vereist is als onderdeel van de implementatie van een nieuwe service. Als een beveiligingscontrole vereist is of niet is uitgevoerd, raadpleegt u de vele [whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) van Azure AD die een overzicht geven van de identiteit als service.

### <a name="plan-rollback"></a>Terugdraaien plannen

De implementatie van de cloud HR-gebruiker die de implementatie indient, werkt mogelijk niet zoals gewenst in de productieomgeving. Als dat het zo is, kunnen de volgende terugdraaistappen u helpen om terug te keren naar een eerdere bekende goede staat.

1. Controleer het [inrichtingsrapport](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) en [de inrichtingslogboeken](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) om te bepalen welke onjuiste bewerkingen zijn uitgevoerd op de betrokken gebruikers of groepen. Zie Gebruikersinrichting van [de CLOUD HR-app beheren](#manage-your-configuration)voor meer informatie over het inrichten van overzichtsrapport en logboeken.
2. De laatst bekende goede staat van de betrokken gebruikers of groepen kan worden bepaald door middel van de provisioning audit logs of door de doelsystemen (Azure AD of Active Directory) te bekijken.
3. Werk samen met de eigenaar van de app om de gebruikers of groepen die rechtstreeks in de app worden betrokken bij te werken met behulp van de laatst bekende goede statuswaarden.

## <a name="deploy-the-cloud-hr-app"></a>De HR-cloud-app implementeren

Kies de HR-cloud-app die aansluit bij uw oplossingsvereisten.

**Werkdag:** als u werknemersprofielen van Workday wilt importeren in Active Directory en Azure AD, raadpleegt u [Zelfstudie: Werkdag configureren voor automatische gebruikersinrichting](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Optioneel u het e-mailadres en de gebruikersnaam terugschrijven naar Workday.

## <a name="manage-your-configuration"></a>Uw configuratie beheren

Azure AD kan aanvullende inzichten bieden in het gebruik en de operationele status van uw organisatie via controlelogboeken en rapporten.

### <a name="gain-insights-from-reports-and-logs"></a>Krijg inzichten uit rapporten en logboeken

Na een succesvolle [eerste cyclus](../app-provisioning/how-provisioning-works.md#initial-cycle)blijft de Azure AD-inrichtingsservice voor onbepaalde tijd back-to-back incrementele updates uitvoeren met intervallen die zijn gedefinieerd in de zelfstudies die specifiek zijn voor elke app, totdat een van de volgende gebeurtenissen optreedt:

- De service wordt handmatig gestopt. Een nieuwe eerste cyclus wordt geactiveerd door de [Azure-portal](https://portal.azure.com/) of de juiste [Microsoft Graph API-opdracht](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) te gebruiken.
- Een nieuwe eerste cyclus wordt geactiveerd als gevolg van een wijziging in kenmerktoewijzingen of scopingfilters.
- Het inrichtingsproces gaat in quarantaine vanwege een hoog foutenpercentage. Het blijft in quarantaine voor meer dan vier weken, op welk moment het automatisch is uitgeschakeld.

Als u deze gebeurtenissen en alle andere activiteiten van de inrichtingsservice wilt bekijken, [leest u hoe u logboeken bekijkt en rapporten over inrichtingsactiviteiten ophaalt.](../app-provisioning/check-status-user-account-provisioning.md)

#### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Alle activiteiten die door de inrichtingsservice worden uitgevoerd, worden geregistreerd in de Azure AD-controlelogboeken. U Azure AD-controlelogboeken doorsturen naar Azure Monitor-logboeken voor verdere analyse. Met Azure Monitor-logboeken (ook wel Log Analytics-werkruimte genoemd) u gegevens opvragen om gebeurtenissen te vinden, trends te analyseren en correlatie uit te voeren tussen verschillende gegevensbronnen. Bekijk deze [video](https://youtu.be/MP5IaCTwkQg) voor meer informatie over de voordelen van het gebruik van Azure Monitor-logboeken voor Azure AD-logboeken in praktische gebruikersscenario's.

Installeer de [logboekanalyseweergaven voor Azure AD-activiteitslogboeken](../reports-monitoring/howto-install-use-log-analytics-views.md) om toegang te krijgen tot [vooraf gebouwde rapporten](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) rond inrichtingsgebeurtenissen in uw omgeving.

Zie voor meer informatie hoe [u de AZURE AD-activiteitslogboeken analyseren met uw Azure Monitor-logboeken.](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)

### <a name="manage-personal-data"></a>Persoonlijke gegevens beheren

De Azure AD Connect-inrichtingsagent die op de Windows-server is geïnstalleerd, maakt logboeken in het Windows-gebeurtenislogboek die mogelijk persoonlijke gegevens bevatten, afhankelijk van uw HR-cloud-app, voor Active Directory-toewijzingen. Als u wilt voldoen aan de privacyverplichtingen van de gebruiker, stelt u een geplande Windows-taak in om het gebeurtenislogboek te wissen en ervoor te zorgen dat er geen gegevens langer dan 48 uur worden bewaard.

Azure AD-inrichtingsservice genereert geen rapporten, voert geen analyses uit of biedt geen inzichten na 30 dagen omdat de service geen gegevens opslaat, verwerkt of bewaart die langer zijn dan 30 dagen.

### <a name="troubleshoot"></a>Problemen oplossen

Zie de volgende artikelen voor het oplossen van problemen die tijdens de inrichting kunnen opduiken:

- [Probleem met het configureren van gebruikersinrichting voor een Azure AD Gallery-toepassing](application-provisioning-config-problem.md)
- [Een kenmerk synchroniseren van uw on-premises Active Directory met Azure AD voor inrichten op een toepassing](user-provisioning-sync-attributes-for-mapping.md)
- [Het inrichten van gebruikers op een Azure AD Gallery-toepassing duurt uren of langer](application-provisioning-when-will-provisioning-finish.md)
- [Probleem met het opslaan van beheerdersreferenties tijdens het configureren van gebruikersvoorziening in een Azure Active Directory Gallery-toepassing](application-provisioning-config-problem-storage-limit.md)
- [Er worden geen gebruikers ingericht voor een Azure AD Gallery-toepassing](application-provisioning-config-problem-no-users-provisioned.md)
- [Verkeerde set gebruikers wordt ingericht voor een Azure AD Gallery-toepassing](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Windows Logboeken instellen voor het oplossen van problemen met agenten](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Azure-portalcontrolelogboeken instellen voor het oplossen van serviceproblemen](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Logboeken voor het maken van AD-gebruikersaccounts begrijpen](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Logboeken voor managerupdatebewerkingen begrijpen](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Vaak voorkomende fouten oplossen](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Volgende stappen

- [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
- [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Verwijdering van gebruikersaccounts die buiten het bereik vallen overslaan](skip-out-of-scope-deletions.md)
- [Azure AD Connect Provisioning Agent: Releasegeschiedenis versie](provisioning-agent-release-version-history.md)
