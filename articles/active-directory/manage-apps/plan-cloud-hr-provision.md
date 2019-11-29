---
title: Cloud-HR-toepassing plannen voor Azure Active Directory gebruikers inrichting
description: In dit artikel wordt het implementatie proces beschreven voor de integratie van Cloud-HR-systemen, zoals workday en Sucessfactors met Azure Active Directory. Het integreren van Azure AD met uw Cloud-HR-systeem resulteert in een volledig identiteits levenscyclus beheer systeem.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 5d55aafc29b3b022d1023077d2d8f459b0608ae7
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555650"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Cloud-HR-toepassing plannen voor Azure Active Directory gebruikers inrichting

Historische IT-mede werkers hebben de hand matige methoden voor het maken, bijwerken en verwijderen van werk nemers, zoals het uploaden van CSV-bestanden, of aangepaste scripts, gepaard met het synchroniseren van gegevens van werk nemers. Deze inrichtings processen zijn fout gevoelig, onveilig en moeilijk te beheren.

Om de end-to-end identiteits levenscyclus van werk nemers, leveranciers of voorwaardelijke werk nemers te beheren, biedt [Azure Active Directory (Azure AD) User Provisioning Service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) integratie met HR-toepassingen (Human Resources) zoals workday of SuccessFactors.

Azure AD gebruikt deze integratie om de volgende workflows voor de Cloud toepassing (app) in te scha kelen:

- **Gebruikers inrichten voor AD** -Provisioning-geselecteerde sets van gebruikers uit een Cloud-app in een of meer Active Directory (AD)-domeinen.
- **Alleen Cloud gebruikers inrichten voor Azure AD** -in SCENARIO'S waarbij ad niet wordt gebruikt, richt gebruikers rechtstreeks vanuit de Cloud HR-app naar Azure AD.
- **Schrijf terug naar de Cloud-app HR.** -Schrijf de e-mail adressen en de kenmerken van de gebruikers naam van Azure AD terug naar de Cloud HR-app.

> [!NOTE]
> In dit implementatie plan wordt uitgelegd hoe u werk stromen voor de Cloud-app kunt implementeren met Azure AD-gebruikers inrichting. Voor informatie over het implementeren van automatische gebruikers inrichting voor SaaS-apps, raadpleegt u [een automatische implementatie van gebruikers inrichten plannen](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Ingeschakelde HR-scenario's

Met de Azure AD User Provisioning-Service kunnen de volgende HR-scenario's voor levenscyclus beheer worden geautomatiseerd:

- **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan de Cloud-app HR, wordt automatisch een gebruikers account in AD en Azure AD gemaakt met de optie om het e-mail adres en de kenmerken van de gebruikers naam terug te schrijven naar de Cloud HR-app.
- **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in de Cloud HR-app (zoals de naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt in AD en Azure AD.
- **Beëindiging van werk nemers** : wanneer een werk nemer wordt beëindigd in de Cloud-app HR, wordt het gebruikers account automatisch uitgeschakeld in AD en Azure AD.
- Opnieuw **inhuren van werk nemers** : wanneer een werk nemer opnieuw wordt ingehuurd in de Cloud HR-app, kan hun oude account automatisch opnieuw worden geactiveerd of worden ingericht voor AD en Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Voor wie is deze integratie het meest geschikt voor?

De integratie van Cloud-apps met Azure AD-gebruikers inrichten is in het ideale geval geschikt voor organisaties die:

- Ik wil een vooraf ontwikkelde, Cloud oplossing maken voor de gebruikers inrichting van de Cloud-HR
- directe gebruikers inrichting van de Cloud HR-app naar AD of Azure AD vereisen
- vereisen dat gebruikers worden ingericht met gegevens die zijn verkregen uit de Cloud-app voor HR
- vereisen dat gebruikers lid worden van een of meer AD-forests, domeinen en organisatie-eenheden op basis van gewijzigde informatie die is gedetecteerd in de Cloud-app HR
- Office 365 gebruiken voor e-mail

## <a name="learn"></a>Leren

Gebruikers inrichten maakt een basis voor de voortdurende identiteits bestuur en verbetert de kwaliteit van bedrijfs processen die afhankelijk zijn van gezaghebbende identiteits gegevens.

### <a name="terms"></a>Voorwaarden

In dit artikel worden de volgende termen gebruikt:

- **Bron systeem** : de opslag plaats van gebruikers die Azure AD-voorzieningen van (bijvoorbeeld een Cloud HR-app, zoals workday en SuccessFactors.)
- **Doel systeem** : de opslag plaats van gebruikers die door Azure AD worden ingericht (bijvoorbeeld AD, Azure AD, Office365 of andere SaaS-apps.)
- **Samen voegers-Verplaatsers-Leavers proces** : een term die wordt gebruikt voor nieuwe mede werkers, overdrachten en beëindiging met een Cloud-HR-app als een systeem van records. Het proces wordt voltooid wanneer de vereiste kenmerken door de service zijn ingericht naar het doel systeem.

### <a name="key-benefits"></a>Belangrijkste voordelen

Deze mogelijkheid van HR-IT-inrichting biedt belang rijke zakelijke voor delen, zoals hieronder wordt weer gegeven:

- **Verhoog de productiviteit** : u kunt nu de toewijzing van gebruikers accounts en Office365-licenties automatiseren en toegang bieden tot sleutel groepen. Als u toewijzingen automatiseert, hebben nieuwe mede werkers direct toegang tot hun taak hulpprogramma's en wordt de productiviteit verhoogd.
- **Risico beheren** : u kunt de beveiliging verhogen door wijzigingen te automatiseren op basis van de status van werk nemers of groepslid maatschappen met gegevens die worden uitgestroomt vanuit de HR-app in de Cloud. Als u wijzigingen automatiseert, zorgt u ervoor dat gebruikers identiteiten en toegang tot de sleutel-apps automatisch worden bijgewerkt wanneer gebruikers overstappen of de organisatie verlaten.
- **Naleving en** beheer van adressen: Azure AD biedt ondersteuning voor systeem eigen audit logboeken voor aanvragen voor gebruikers inrichting die worden uitgevoerd door apps van zowel de bron-als doel systemen. Met controle kunt u bijhouden wie vanaf één scherm toegang tot de apps heeft.
- **Beheer kosten** -automatische inrichting verlaagt de kosten door inefficiëntie en menselijke fout te voor komen die zijn gekoppeld aan hand matige inrichting. Het vermindert de behoefte aan aangepaste, ontwikkelde oplossingen voor het inrichten van gebruikers die in de loop van de tijd zijn gebouwd met verouderde en verouderde platforms.

### <a name="licensing"></a>Licentieverlening

Als u de Cloud-HR-app wilt configureren voor integratie met Azure AD-gebruikers, hebt u een geldige [licentie voor Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) en een licentie voor de werk-app in de Cloud, zoals workday of SuccessFactors.

U hebt ook een geldige abonnements licentie voor Azure AD Premium P1 of hoger nodig voor elke gebruiker die vanuit de Cloud-app wordt gebrond en ingericht voor AD of Azure AD. Een onjuist aantal licenties dat eigendom is van de Cloud HR-app kan leiden tot fouten tijdens het inrichten van de gebruiker.

### <a name="prerequisites"></a>Vereisten

- Globale beheerders toegang van Azure AD om de Azure AD Connect-inrichtings agent te configureren.
- Een test-en productie-exemplaar van de Cloud-HR-app.
- Beheerders machtigingen in de Cloud HR-app om een systeem integratie gebruiker te maken en wijzigingen aan te brengen voor het testen van werknemers gegevens voor test doeleinden.
- Een server met Windows Server 2012 of hoger met .NET 4.7.1 + runtime is vereist voor het hosten van de [Azure AD Connect inrichtings agent](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) voor het synchroniseren van gebruikers tussen AD en Azure AD.

### <a name="training-resources"></a>Trainings bronnen

| **Resources** | **Koppeling en beschrijving** |
|:-|:-|
| Video’s | [Wat is gebruikers inrichten in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Hoe kan ik de gebruikers inrichten in Active Azure Directory implementeren?](https://youtu.be/pKzyts6kfrw) |
| Zelfstudie | Zie de [lijst met zelf studies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Zelf studie: workday configureren voor het automatisch inrichten van gebruikers](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| Veelgestelde vragen | [Automatische gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#frequently-asked-questions) |
| | [Inrichten van workday naar Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architectuur voor de oplossing

In het volgende voor beeld wordt de end-to-end-oplossings architectuur voor gebruikers ingericht voor veelvoorkomende hybride omgevingen en bevat:

- **Gezaghebbende HR-gegevens stroom: vanuit de Cloud HR-app naar AD** -in deze stroom wordt de HR-gebeurtenis (samenvoegers-verplaatsingen-Leavers-proces) gestart in de Cloud HR app-Tenant. De Azure AD Provisioning-Service en Azure AD Connect inrichtings agent voorzien in de gebruikers gegevens van de Tenant van de HR-app van de cloud in AD. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in AD.
- **Synchroniseren met Azure AD en write-back-e-mail en gebruikers naam van on-premises AD naar Cloud HR-app** -zodra de accounts zijn bijgewerkt in AD, wordt deze gesynchroniseerd met Azure ad via Azure AD Connect en kunnen de kenmerken van het e-mail adres en de gebruikers naam worden teruggeschreven naar de Tenant van de HR-app in de Cloud.

![Werk stroom diagram](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beschrijving van werk stroom

De belangrijkste stappen die in het diagram worden aangegeven zijn:  

1. **HR-team** voert de trans acties uit in de Cloud HR app-Tenant.
2. De **Azure AD-inrichtings service** voert de geplande cycli uit van de Tenant van de HR-app in de Cloud en identificeert wijzigingen die moeten worden verwerkt voor synchronisatie met AD.
3. De **Azure AD-inrichtings service** roept de Azure AD Connect-inrichtings agent aan met een aanvraag lading die ad-account maken/bijwerken/inschakelen/uitschakelen heeft.
4. **Azure AD Connect-inrichtings agent** gebruikt een service account voor het beheren van AD-account gegevens.
5. **Azure AD Connect** voert Delta [synchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) uit voor het ophalen van updates in AD.
6. **Ad** -updates worden gesynchroniseerd met Azure AD.
7. **Azure AD Provisioning Service** materialiseren-e-mail kenmerk en gebruikers naam van Azure AD naar de Cloud HR app Tenant.

## <a name="plan-the-deployment-project"></a>Het implementatie project plannen

Houd rekening met de behoeften van uw organisatie terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden](https://aka.ms/deploymentplans) en de rol van belanghebbenden in het project goed begrijpen door de belanghebbenden en hun project invoer en accountabilities te documenteren.

U moet een vertegenwoordiger van de HR-organisatie bevatten die invoer kan bieden op basis van bestaande HR-bedrijfs processen en worker-identiteit + taak gegevens verwerkings vereisten.

### <a name="plan-communications"></a>Communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. U moet proactief communiceren met uw gebruikers hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als u problemen ondervindt.

### <a name="plan-a-pilot"></a>Een pilot plannen

Voor het integreren van bedrijfs processen en identiteits werk stromen van de Cloud HR-app naar doel systemen is een aanzienlijke hoeveelheid gegevens validatie, gegevens transformatie, het opschonen van gegevens en end-to-end tests vereist voordat u de oplossing in productie implementeert.

We raden u aan de initiële configuratie uit te voeren in een [test omgeving](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) voordat u deze schaalt naar alle gebruikers in productie.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Apps voor de inrichtings connector voor Clouds selecteren

Om Azure AD-inrichtings werk stromen tussen de Cloud HR-app en AD te vergemakkelijken, zijn er meerdere apps voor het inrichten van connectors die u kunt toevoegen vanuit de Azure AD-App-galerie:

- **Cloud HR-app naar AD-gebruikers inrichten** : deze inrichtings connector-app vereenvoudigt het inrichten van gebruikers accounts vanuit de HR-app in één AD-domein. Als u meerdere domeinen hebt, kunt u één exemplaar van deze app toevoegen vanuit de Azure AD-App-galerie voor elk AD-domein dat u wilt inrichten.
- **Cloud HR-app naar Azure AD-gebruikers inrichten** : Hoewel Azure AD Connect het hulp programma is dat moet worden gebruikt om AD-gebruikers te synchroniseren met Azure AD, kan deze app voor de inrichtings connector worden gebruikt om het inrichten van Cloud gebruikers vanuit de HR-app naar één Azure AD-Tenant te vergemakkelijken.
- **Back** -ups van Cloud-apps-deze inrichtings connector-app vereenvoudigt de write-back van de e-mail adressen van de gebruiker van Azure AD naar de Cloud-app.

In de onderstaande afbeelding ziet u bijvoorbeeld de workday-connector-apps die beschikbaar zijn in de Azure AD-App-galerie.

![Galerie met apps voor Azure Active Directory Portal](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Beslissings stroomdiagram

Gebruik het onderstaande diagram van de beslissings stroom om te bepalen welke Clouds voor het inrichten van apps relevant zijn voor uw scenario.

![Beslissings stroomdiagram](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Implementatie topologie voor de inrichtings agent ontwerpen Azure AD Connect

Voor de integratie van de inrichting van de Cloud-app voor HR en AD zijn de volgende vier onderdelen vereist:

- Cloud HR app-Tenant
- Connector-app inrichten
- Inrichtings agent Azure AD Connect
- AD-domein

De implementatie topologie voor de Azure AD Connect-inrichtings agent is afhankelijk van het aantal Cloud-apps en AD-onderliggende domeinen die u wilt integreren. Als u meerdere AD-domeinen hebt, is het afhankelijk van het feit of de AD-domeinen aaneengesloten of [gescheiden](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)zijn.

Kies op basis van uw beslissing een van de implementatie scenario's:

- Tenant-> voor één Cloud-app met één of meerdere onderliggende domeinen in een vertrouwd forest
- Tenant-> voor één Cloud-app met meerdere onderliggende domeinen in een niet-aaneengesloten AD-forest

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Tenant-app voor enkele Cloud-apps-> één of meerdere onderliggende AD-domeinen in een vertrouwd forest

We raden u aan de volgende productie configuratie te configureren:

|Vereiste|Aanbeveling|
|:-|:-|
|Aantal Azure AD Connect inrichten agents om te implementeren|2 (voor hoge Beschik baarheid en failover)
|Aantal te configureren apps voor de inrichtings connector|Eén app per onderliggend domein|
|Server host voor Azure AD Connect-inrichtings agent|Windows 2012 R2 + met een regel van het gezichts vermogen naar geo-locaties AD-domein controllers</br>Kan samen met Azure AD Connect service worden gecombineerd|

![Stroom naar on-premises agents](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Tenant-> voor één Cloud-app met meerdere onderliggende domeinen in een niet-aaneengesloten AD-forest

Dit scenario omvat het inrichten van gebruikers vanuit de Cloud HR-app naar domeinen in niet-aaneengesloten AD-forests.

We raden u aan de volgende productie configuratie te configureren:

|Vereiste|Aanbeveling|
|:-|:-|
|Aantal Azure AD Connect inrichten agents om on-premises te implementeren|2 per niet-aaneengesloten AD-forest|
|Aantal te configureren apps voor de inrichtings connector|Eén app per onderliggend domein|
|Server host voor Azure AD Connect-inrichtings agent|Windows 2012 R2 + met een regel van het gezichts vermogen naar geo-locaties AD-domein controllers</br>Kan samen met Azure AD Connect service worden gecombineerd|

![AD-forest voor HR-tenants met één Cloud](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Agent vereisten voor Azure AD Connect inrichten

Voor de oplossing voor het inrichten van een AD-HR-app voor gebruikers moet een of meer Azure AD Connect inrichtings agenten worden geïmplementeerd op servers met Windows 2012 R2 of hoger met een minimum van 4 GB RAM en .NET 4.7.1 + runtime. Zorg ervoor dat de hostserver netwerk toegang heeft tot het doel-AD-domein.

Om de on-premises omgeving voor te bereiden, wordt met de wizard agent configureren van Azure AD Connect de agent geregistreerd bij uw Azure AD-Tenant, worden [poorten geopend](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [krijgt u toegang tot url's](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)en worden de [uitgaande HTTPS-proxy configuratie](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)ondersteund.

De inrichtings agent gebruikt een service account om te communiceren met de AD-domein (en). Voordat de agent wordt geïnstalleerd, wordt u aangeraden een service account te maken in AD-gebruikers en-computers die voldoen aan de volgende vereisten:

- Een wacht woord dat niet verloopt
- Gemachtigde beheer machtigingen voor het lezen, maken, verwijderen en beheren van gebruikers accounts

U kunt domein controllers selecteren die inrichtings aanvragen moeten verwerken. Als u meerdere geografisch gedistribueerde domein controllers hebt, installeert u de inrichtings agent op dezelfde site als uw voorkeurs domein controller (s) om de betrouw baarheid en prestaties van de end-to-end-oplossing te verbeteren.

Voor maximale Beschik baarheid kunt u meer dan één Azure AD Connect inrichtings agent implementeren en deze registreren voor het afhandelen van dezelfde set on-premises AD-domeinen.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Bereik filters en kenmerk toewijzing plannen

Wanneer u inrichting van de Cloud-app in AD of Azure AD inschakelt, beheert de Azure Portal de kenmerk waarden via kenmerk toewijzing.

### <a name="define-scoping-filters"></a>Bereik filters definiëren

Gebruik [bereik filters](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) om de op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers moeten worden ingericht vanuit de Cloud-app in AD of Azure AD.

Wanneer u het proces voor samen voegers start, moet u de volgende vereisten verzamelen:

- Wordt de Cloud-app HR gebruikt om werk nemers en voorwaardelijke werk nemers aan boord te houden?
- Wilt u de Cloud HR-app gebruiken voor het inrichten van Azure AD-gebruikers om zowel werk nemers als voorwaardelijke werk nemers te beheren?
- Wilt u de implementatie van de Cloud-app naar Azure AD-gebruikers alleen inrichten voor een subset van de gebruikers in de Cloud-app, bijvoorbeeld alleen werk nemers.

Afhankelijk van uw vereisten kunt u, wanneer u kenmerk toewijzingen configureert, het bereik van het **bron object** instellen om te selecteren welke gebruikers sets in de Cloud-app in het bereik moeten worden ingericht voor het INRICHTEN van AD. Raadpleeg de zelf studie over Cloud-HR-apps voor veelgebruikte bereik filters.

### <a name="determine-matching-attributes"></a>Overeenkomende kenmerk (en) bepalen

Bij het inrichten beschikt u over de mogelijkheid om bestaande accounts te koppelen aan het bron-en doel systeem. Wanneer u de Cloud HR-app integreert met de Azure AD Provisioning-Service, kunt u [kenmerk toewijzing configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) om te bepalen welke gebruikers gegevens moeten stromen van de HR-app naar AD of Azure AD.

Wanneer u het proces voor samen voegers start, moet u de volgende vereisten verzamelen:

- Wat is de unieke ID in deze Cloud-HR-app die wordt gebruikt voor het identificeren van elke gebruiker?
- Hoe verwerkt u met een levens cyclus voor identiteiten? Blijven de oude werk nemer-id's in afnemen?
- Verwerkt u toekomstig gehuurde en gemaakte AD-accounts vooraf?
- Hoe kunt u vanuit een perspectief voor de levens cyclus van de identiteit werk nemers omzetten naar conversies van een voorwaardelijke werk nemer of anderszins?
- Behouden de geconverteerde gebruikers hun oude AD-account of krijgen ze nieuwe?

Afhankelijk van wat u nodig hebt, ondersteunt Azure AD door directe toewijzing van kenmerk naar kenmerk, waardoor er constante waarden zijn of het [schrijven van expressies voor kenmerk toewijzingen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Deze flexibiliteit geeft u de ultieme controle over wat er wordt ingevuld in het kenmerk van de doel toepassing. U kunt [Microsoft Graph-API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) en Graph Explorer gebruiken om de kenmerk toewijzingen en het schema voor het inrichten van gebruikers te exporteren naar een JSON-bestand en dit weer te importeren in azure AD.

Het kenmerk in de Cloud-HR-app dat de unieke werk nemer-ID vertegenwoordigt, wordt **standaard**gebruikt als het overeenkomende kenmerk dat is *toegewezen aan het unieke kenmerk in AD.* In het scenario van de workday-app wordt het *WorkerID* kenmerk workday bijvoorbeeld toegewezen aan het kenmerk AD *employeeID* .

U kunt meerdere overeenkomende kenmerken instellen en overeenkomende prioriteit toewijzen. Ze worden geëvalueerd op volg orde van overeenkomst. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

U kunt ook [de standaard kenmerk toewijzingen aanpassen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) , zoals bestaande kenmerk toewijzingen wijzigen of verwijderen, of nieuwe kenmerk toewijzingen maken op basis van de behoeften van uw bedrijf. Raadpleeg de zelf studie over Cloud-HR-apps (zoals [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) voor een lijst met aangepaste kenmerken die u wilt toewijzen.

### <a name="determine-user-account-status"></a>De status van het gebruikers account bepalen

Standaard wordt de status van het gebruikers **profiel** voor de inrichtings connector toegewezen aan de status van het **gebruikers account** in AD/Azure AD om te bepalen of het gebruikers account moet worden in-of uitgeschakeld.

Wanneer u het proces voor het samen voegen/de sluiters initieert, moet u de volgende vereisten verzamelen:

| Proces | Vereisten |
| - | - |
| **Samen voegers** | Hoe verwerkt u met een levens cyclus voor identiteiten? Blijven de oude werk nemer-id's in afnemen? |
| | Verwerkt u toekomstig gehuurde mede werkers en maakt u er vooraf een AD-account? Zijn deze accounts gemaakt met de status ingeschakeld/uitgeschakeld? |
| | Hoe kunt u vanuit een perspectief voor de levens cyclus van de identiteit werk nemers omzetten naar conversies van een voorwaardelijke werk nemer of anderszins? |
| | Behouden de geconverteerde gebruikers hun oude AD-account of krijgen ze nieuwe? |
| **Leavers** | Worden de beëindigingen voor werk nemers en voorwaardelijke werk nemers in AD anders afgehandeld? |
| | Welke ingangs datums worden er in rekening gehouden voor het verwerken van gebruikers beëindiging? |
| | Hoe beïnvloeden werk nemers en conversies van voorwaardelijke mede werkers bestaande AD-accounts? |
| | Hoe verwerkt u de bewerking ' Rescind ' in AD? Rescind-bewerkingen moeten worden verwerkt als toekomstige gehuurde activiteiten in AD worden gemaakt als onderdeel van het proces voor samen voegen. |

Afhankelijk van uw vereisten kunt u de toewijzings logica aanpassen met behulp van [Azure AD-expressies](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) , zodat het ad-account wordt in-of uitgeschakeld op basis van een combi natie van gegevens punten.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Cloud HR-app toewijzen aan AD-gebruikers kenmerken

Elke Cloud HR-app wordt geleverd met een standaard-HR-app voor AD-toewijzingen.

Wanneer u het proces voor het samen voegen/verplaatsen van de koppelingen start, verzamelt u de volgende vereisten:

| Proces | Vereisten |
| - | - |
| **Samen voegers** | Is het proces voor het maken van AD-accounts hand matig, geautomatiseerd of gedeeltelijk geautomatiseerd? |
| | Wilt u aangepaste kenmerken van de Cloud HR-app door geven aan AD? |
| **Verplaatsers** | Welke kenmerken wilt u verwerken wanneer een ' Moveers '-bewerking plaatsvindt in de Cloud HR-app? |
| | Voert u de validatie van specifieke kenmerken uit op het moment van de gebruikers updates? Zo ja, geef dan Details op. |
| **Leavers** | Worden de beëindigingen voor werk nemers en voorwaardelijke werk nemers in AD anders afgehandeld? |
| | Welke ingangs datums worden er in rekening gehouden voor het verwerken van gebruikers beëindiging? |
| | Hoe beïnvloeden werk nemers en conversies van voorwaardelijke mede werkers bestaande AD-accounts? |

Afhankelijk van uw vereisten kunt u de toewijzingen aanpassen om te voldoen aan uw integratie doelen. Raadpleeg de specifieke zelf studie over Cloud-HR-apps (zoals [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) voor een lijst met aangepaste kenmerken die u wilt toewijzen.

### <a name="generate-unique-attribute-value"></a>Unieke kenmerk waarde genereren

Wanneer u het proces voor samen voegers start, moet u mogelijk unieke kenmerk waarden genereren bij het instellen van kenmerken zoals CN, samAccountName en UPN met unieke beperkingen.

De Azure AD-functie [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) evalueert elke regel en controleert vervolgens de waarde die is gegenereerd voor uniekheid in het doel systeem. Zie voor beeld [unieke waarde genereren voor het kenmerk userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Deze functie wordt momenteel alleen ondersteund voor werk dagen Active Directory het inrichten van de gebruiker. Het kan niet worden gebruikt met andere inrichtings toepassingen.

### <a name="configure-ad-ou-container-assignment"></a>AD-OE-container toewijzing configureren

Het is een algemene vereiste om AD-gebruikers accounts in containers te plaatsen op basis van bedrijfs eenheden, locaties en afdelingen. Wanneer u een verplaatsings proces initieert en als er een wijziging in de organisatie van een leidinggevende is, moet u de gebruiker mogelijk van de ene organisatie-eenheid naar de andere verplaatsen in AD.

Gebruik de functie [Switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) om de bedrijfs logica voor de OE-toewijzing te configureren en toe te wijzen aan het AD-kenmerk *parentDistinguishedName*.

Als u bijvoorbeeld gebruikers in OE wilt maken op basis van het HR-kenmerk "gemeente", kunt u de volgende expressie gebruiken.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Als deze expressie is ingesteld op de waarde van de gemeente Rotterdam, Austin, Amsterdam of Londen, wordt het gebruikers account gemaakt in de bijbehorende organisatie-eenheid. Als er geen overeenkomst is, wordt het account gemaakt in de standaard organisatie-eenheid.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plannen voor het leveren van wacht woorden van nieuwe gebruikers accounts

Wanneer u het proces voor samen voegers start, moet u een tijdelijk wacht woord voor nieuwe gebruikers accounts instellen en leveren. Met Cloud HR naar Azure AD-gebruikers inrichten kunt u Azure AD [self-service voor wachtwoord herstel](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) voor de gebruiker op dag 1 implementeren.

SSPR is een eenvoudige manier om IT-beheerders in staat te stellen hun wacht woorden opnieuw in te stellen of hun accounts te ontgrendelen. U kunt het kenmerk **mobiel nummer** van de Cloud-app naar AD inrichten en deze synchroniseren met Azure AD. Zodra het **mobiele nummer** kenmerk zich in azure AD bevindt, kunt u SSPR inschakelen voor het account van de gebruiker, zodat de nieuwe gebruikers op dag 1 het geregistreerde en geverifieerde mobiele nummer voor verificatie kunnen gebruiken.

## <a name="plan-for-initial-cycle"></a>Plannen voor de eerste cyclus

Wanneer de Azure AD-inrichtings service voor de eerste keer wordt uitgevoerd, wordt er een [eerste cyclus](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning) voor de Cloud HR-app uitgevoerd om een moment opname te maken van alle gebruikers objecten in de Cloud-app HR. De tijd die nodig is voor de eerste cycli is direct afhankelijk van het aantal gebruikers dat in het bron systeem aanwezig is. De eerste cyclus voor sommige tenants in de Cloud-app van meer dan 100.000 gebruikers kan veel tijd in beslag nemen.

**Voor grote app-tenants in de Cloud (> 30.000 gebruikers) raden we** u aan de eerste cyclus in progressieve stadia uit te voeren en de incrementele updates alleen te starten nadat u hebt gecontroleerd of de juiste kenmerken in AD zijn ingesteld voor verschillende scenario's voor het inrichten van gebruikers. Volg de onderstaande volg orde:

1. Voer de eerste cyclus alleen uit voor een beperkt aantal gebruikers door het [bereik filter](#plan-scoping-filters-and-attribute-mapping)in te stellen.
2. Controleer de inrichting van AD-accounts en kenmerk waarden die zijn ingesteld voor de gebruikers die zijn geselecteerd voor de eerste uitvoering. Als het resultaat aan uw verwachtingen voldoet, vouwt u het filter bereik uit om meer gebruikers toe te voegen en de resultaten voor de tweede uitvoering te controleren.

Zodra u tevreden bent met de resultaten van de eerste cyclus voor test gebruikers, kunt u de [incrementele updates](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#incremental-cycles)starten.

## <a name="plan-testing-and-security"></a>Testen en beveiliging plannen

Zorg ervoor dat u tijdens elke fase van de implementatie van de eerste pilot door het inschakelen van de gebruikers inrichting test dat de resultaten naar verwachting zijn en de inrichtings cycli controleren.

### <a name="plan-testing"></a>Tests plannen

Zodra u de Cloud-HR-app hebt geconfigureerd voor Azure AD-gebruikers inrichten, moet u test cases uitvoeren om te controleren of deze oplossing voldoet aan de vereisten van uw organisatie.

|Scenario's|Verwachte resultaten|
|:-|:-|
|Nieuwe werk nemers in de Cloud HR-app inhuren| -Het gebruikers account is ingericht in AD.</br>-Gebruikers kunnen zich aanmelden bij de AD-domein-apps en de gewenste acties uitvoeren.</br>-Als AAD Connect-synchronisatie is geconfigureerd, wordt het gebruikers account ook gemaakt in azure AD.
|De gebruiker wordt beëindigd in de Cloud-app HR|-Het gebruikers account is uitgeschakeld in AD.</br>-De gebruiker kan zich niet aanmelden bij bedrijfs-apps die zijn beveiligd met AD.
|Organisatie van gebruikers toezicht is bijgewerkt in de Cloud-app HR|Op basis van de kenmerk toewijzing wordt het gebruikers account verplaatst van de ene OE naar een andere in AD.|
|HR werkt de Manager van de gebruiker bij in de Cloud-app voor HR|Het veld Manager in AD wordt bijgewerkt op basis van de naam van de nieuwe manager.|
|HR herhuurt een werk nemer in een nieuwe rol.|Gedrag is afhankelijk van hoe de Cloud-HR-app is geconfigureerd voor het genereren van werk nemer-Id's:</br>-Als de oude werk nemer-ID opnieuw wordt gebruikt voor het opnieuw inhuren, schakelt de connector het bestaande AD-account voor de gebruiker in.</br>-Als u een nieuwe werk nemer-ID wilt inhuren, maakt de connector een nieuw AD-account voor de gebruiker.|
|HR converteert de werk nemer naar een contract medewerker of andersom|Er wordt een nieuw AD-account gemaakt voor de nieuwe persoona en het oude account wordt uitgeschakeld op de ingangs datum van de conversie.|

Gebruik de bovenstaande resultaten om te bepalen hoe u de implementatie van de automatische gebruikers inrichting naar de productie omgeving kunt overstappen op basis van de ingestelde tijd lijnen.

> [!TIP]
> We raden u aan om technieken als gegevens reductie en data scrubbing te gebruiken bij het vernieuwen van de test omgeving met productie gegevens om gevoelige PII-gegevens (persoonlijk identificeer bare informatie) te verwijderen/maskeren om te voldoen aan de privacy-en beveiligings standaarden.

### <a name="plan-security"></a>Beveiliging plannen

Het is gebruikelijk dat een beveiligings beoordeling vereist is als onderdeel van de implementatie van een nieuwe service. Als een beveiligings beoordeling vereist is of nog niet is uitgevoerd, raadpleegt u de vele Azure AD- [witboeken](https://www.microsoft.com/download/details.aspx?id=36391) die een overzicht bieden van de identiteit als een service.

### <a name="plan-rollback"></a>Plan terugdraai actie

Als de implementatie van de door de gebruiker ingerichte werk ruimte in de Cloud niet naar wens werkt in de productie omgeving, kunnen de volgende terugdraai stappen u helpen bij het herstellen naar een eerdere bekende goede staat:

1. Bekijk het [inrichtings samenvattings rapport](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#getting-provisioning-reports-from-the-azure-management-portal) en de [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#provisioning-audit-logs) (Zie [Cloud HR app User Provisioning beheren](#manage-your-configuration)) om te bepalen wat de onjuiste bewerkingen zijn uitgevoerd op de betrokken gebruikers en/of groepen.
2. De laatste bekende juiste status van de betrokken gebruikers en/of groepen kan worden bepaald met behulp van de audit logboeken van de inrichting of door de doel systemen (Azure AD of AD) te controleren.
3. Werk samen met de eigenaar van de app voor het bijwerken van de gebruikers en/of groepen die in de app worden beïnvloed, met behulp van de laatste bekende juiste status waarden.

## <a name="deploy-the-cloud-hr-app"></a>De Cloud-HR-app implementeren

Kies de Cloud-HR-app die wordt afgestemd op uw oplossings vereisten.

**Workday** -Raadpleeg de [zelf studie: werk dagen configureren voor het automatisch inrichten van gebruikers](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) voor de stappen die u moet uitvoeren om werknemers profielen van workday te importeren in zowel ad als Azure AD, met optionele terugschrijf bewerking van het e-mail adres en de gebruikers naam naar workday.

## <a name="manage-your-configuration"></a>Uw configuratie beheren

Azure AD kan inzicht geven in het gebruik van gebruikers en de operationele status van uw organisatie via controle logboeken en-rapporten.

### <a name="gain-insights-from-reports-and-logs"></a>Inzicht verkrijgen in rapporten en Logboeken

Na een geslaagde [eerste cyclus](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)zullen de Azure AD-inrichtings service voor onbepaalde tijd back-to-back-incrementele updates uitvoeren, op intervallen die zijn gedefinieerd in de zelf studies die specifiek zijn voor elke app, totdat een van de volgende gebeurtenissen zich voordoet:

- De service wordt hand matig gestopt en er wordt een nieuwe eerste cyclus geactiveerd met behulp van de [Azure Portal](https://portal.azure.com/) of met de juiste [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) -opdracht.
- Er wordt een nieuwe eerste cyclus geactiveerd als gevolg van een wijziging in kenmerk toewijzingen of filter bereik.
- Het inrichtings proces gaat in quarantaine als gevolg van een hoge fout frequentie en blijft in quarantaine gedurende meer dan vier weken waarop het automatisch wordt uitgeschakeld.

Als u deze gebeurtenissen wilt controleren en alle andere activiteiten die door de inrichtings service worden uitgevoerd, [leert u hoe u Logboeken kunt controleren en rapporten kunt ophalen over inrichtings activiteiten](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Alle activiteiten die worden uitgevoerd door de inrichtings service worden vastgelegd in de **Azure AD-audit logboeken**. U kunt Azure AD-controle logboeken door sturen naar Azure Monitor logboeken voor verdere analyse. Met **Azure monitor-Logboeken (ook wel log Analytics-werk ruimte genoemd)** kunt u een query uitvoeren op gegevens om gebeurtenissen te vinden, trends te analyseren en de correlatie tussen verschillende gegevens bronnen uit te voeren. Bekijk deze [video](https://youtu.be/MP5IaCTwkQg) voor meer informatie over de voor delen van het gebruik van Azure monitor logboeken voor Azure AD-Logboeken in praktische gebruikers scenario's.

U kunt de [log Analytics-weer gaven voor Azure AD-activiteiten logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) installeren om toegang te krijgen tot [vooraf gemaakte rapporten](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) rondom het inrichten van gebeurtenissen in uw omgeving.

Zie [de Azure AD-activiteiten logboeken analyseren met uw Azure monitor-logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) voor meer informatie

### <a name="manage-personal-data"></a>Persoonlijke gegevens beheren

De Azure AD Connect-inrichtings agent die op de Windows-Server is geïnstalleerd, maakt Logboeken in het Windows-gebeurtenis logboek die persoonlijke gegevens kunnen bevatten, afhankelijk van uw Cloud HR-app naar AD-kenmerk toewijzingen. Om te voldoen aan de verplichtingen van de privacy van de gebruiker, kunt u een geplande Windows-taak instellen om het gebeurtenis logboek te wissen en ervoor te zorgen dat er geen gegevens meer dan 48 uur worden bewaard.

De Azure AD-inrichtings service genereert geen rapporten, voert geen analyses uit of biedt meer dan dertig dagen geen inzichten. Daarom slaat de Azure AD-inrichtings service geen gegevens die na 30 dagen worden opgeslagen, verwerkt of bewaard. 

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de volgende koppelingen voor het oplossen van problemen die kunnen optreden tijdens het inrichten:

- [Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Een kenmerk van uw on-premises Active Directory naar Azure AD synchroniseren voor het inrichten van een toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Gebruikers die een Azure AD-galerie toepassing inrichten, nemen uren of meer in beslag](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Probleem bij het opslaan van de beheerders referenties tijdens het configureren van de gebruikers inrichting voor een Azure Active Directory galerie-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Er worden geen gebruikers ingericht voor een Azure AD-galerie toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [Er wordt een verkeerde set gebruikers ingericht voor een Azure AD-galerie toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Problemen met Windows Logboeken instellen voor agent probleem oplossing](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Azure Portal audit logboeken instellen voor het oplossen van services](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Informatie over Logboeken voor het maken van AD-gebruikers accounts](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Logboeken voor update bewerkingen voor managers](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Veelvoorkomende fouten oplossen](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Volgende stappen

- [Expressies schrijven voor kenmerk toewijzingen](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Overzicht van Azure AD-synchronisatie-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Verwijdering van gebruikers accounts die buiten het bereik vallen, overs Laan](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Azure AD Connect-inrichtings agent: release geschiedenis van versie](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
