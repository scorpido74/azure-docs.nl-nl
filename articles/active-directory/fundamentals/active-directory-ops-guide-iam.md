---
title: Verwijzingshandleiding voor azure Active Directory-gegevens en beheerbewerkingen voor toegangsbeheer
description: Deze handleiding voor bewerkingen beschrijft de controles en acties die u moet uitvoeren om identiteits- en toegangsbeheerbewerkingen te beveiligen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298611"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Verwijzingshandleiding voor azure Active Directory-gegevens en beheerbewerkingen voor toegangsbeheer

In dit gedeelte van de [naslaggids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet overwegen om de levenscyclus van identiteiten en hun toewijzingen te beveiligen en te beheren.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de publicatiedatum, maar kunnen in de loop van de tijd veranderen. Organisaties moeten hun identiteitspraktijken voortdurend evalueren terwijl Microsoft-producten en -services in de loop van de tijd evolueren.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigenaren toewijzen aan belangrijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering vereist van belangrijke operationele taken en processen die mogelijk geen deel uitmaken van een implementatieproject. Het is nog steeds belangrijk dat u deze taken instelt om uw omgeving te onderhouden. De belangrijkste taken en de aanbevolen eigenaren zijn:

| Taak | Eigenaar |
| :- | :- |
| Het proces definiëren voor het maken van Azure-abonnementen | Verschilt per organisatie |
| Bepaal wie Enterprise Mobility + Security-licenties krijgt | IAM Operations Team |
| Bepalen wie Office 365-licenties krijgt | Productiviteitsteam |
| Bepaal wie andere licenties krijgt, bijvoorbeeld Dynamics, VSO | Toepassings-eigenaar |
| Licenties toewijzen | IAM Operations Team |
| Fouten in licentietoewijzing oplossen en herstellen | IAM Operations Team |
| Identiteiten inrichten op toepassingen in Azure AD | IAM Operations Team |

Als u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken die een eigenaar missen of het eigendom aanpassen voor taken met eigenaren die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="assigning-owners-recommended-reading"></a>Eigenaren toewijzen raden lezen aan

- [Beheerdersrollen toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>On-premises identiteitssynchronisatie

### <a name="identify-and-resolve-synchronization-issues"></a>Synchronisatieproblemen identificeren en oplossen

Microsoft raadt u aan een goede basislijn en inzicht te hebben in de problemen in uw on-premises omgeving die kunnen leiden tot synchronisatieproblemen met de cloud. Aangezien geautomatiseerde hulpprogramma's zoals [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) en [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) een groot aantal fout-positieven kunnen genereren, raden we u aan synchronisatiefouten te identificeren die langer dan 100 dagen ongeadresseerd zijn gebleven door deze objecten foutloos op te schonen. Lange termijn onopgeloste synchronisatiefouten kunnen ondersteuningsincidenten genereren. [Het oplossen](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) van fouten tijdens synchronisatie biedt een overzicht van verschillende typen synchronisatiefouten, enkele van de mogelijke scenario's die deze fouten veroorzaken en mogelijke manieren om de fouten op te lossen.

### <a name="azure-ad-connect-sync-configuration"></a>Configuratie van Azure AD Connect-synchronisatie

Om alle hybride ervaringen, apparaatgebaseerde beveiligingshouding en integratie met Azure AD in te schakelen, is het vereist dat u gebruikersaccounts synchroniseert die uw werknemers gebruiken om in te loggen op hun desktops.

Als u de bosgebruikers niet synchroniseert, moet u de synchronisatie wijzigen om uit het juiste forest te komen.

#### <a name="synchronization-scope-and-object-filtering"></a>Synchronisatiebereik en objectfiltering

Het verwijderen van bekende buckets van objecten die niet hoeven te worden gesynchroniseerd, heeft de volgende operationele voordelen:

- Minder bronnen van synchronisatiefouten
- Snellere synchronisatiecycli
- Minder 'garbage' om door te voeren vanuit on-premises, bijvoorbeeld vervuiling van de wereldwijde adreslijst voor on-premises serviceaccounts die niet relevant zijn in de cloud

> [!NOTE]
> Als u merkt dat u veel objecten importeert die niet naar de cloud worden geëxporteerd, moet u filteren op ou of specifieke kenmerken.

Voorbeelden van objecten die moeten worden uitgesloten zijn:

- Serviceaccounts die niet worden gebruikt voor cloudtoepassingen
- Groepen die niet bedoeld zijn om te worden gebruikt in cloudscenario's, zoals die welke worden gebruikt om toegang te verlenen tot bronnen
- Gebruikers of contactpersonen die externe identiteiten zijn die moeten worden vertegenwoordigd met Azure AD B2B Collaboration
- Computeraccounts waar werknemers geen toegang hebben tot cloudtoepassingen van bijvoorbeeld servers

> [!NOTE]
> Als één menselijke identiteit meerdere accounts heeft die zijn ingericht vanuit iets zoals een verouderde domeinmigratie, fusie of overname, moet u het account dat door de gebruiker wordt gebruikt alleen synchroniseren op een dagelijkse basis, bijvoorbeeld wat ze gebruiken om in te loggen op hun computer .

Idealiter wilt u een evenwicht vinden tussen het verminderen van het aantal objecten dat moet worden gesynchroniseerd en de complexiteit in de regels. Over het algemeen is een combinatie tussen [OU/containerfiltering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) plus een eenvoudige kenmerktoewijzing aan het kenmerk cloudFiltered een effectieve filtercombinatie.

> [!IMPORTANT]
> Als u groepsfiltering in de productie gebruikt, moet u overschakelen naar een andere filterbenadering.

#### <a name="sync-failover-or-disaster-recovery"></a>Failover of herstel na noodgevallen synchroniseren

Azure AD Connect speelt een belangrijke rol in het inrichtingsproces. Als de synchronisatieserver om welke reden dan ook offline gaat, kunnen wijzigingen in on-premises niet worden bijgewerkt in de cloud en kunnen ze leiden tot toegangsproblemen voor gebruikers. Daarom is het belangrijk om een failoverstrategie te definiëren waarmee beheerders de synchronisatie snel kunnen hervatten nadat de synchronisatieserver offline is gegaan. Dergelijke strategieën kunnen in de volgende categorieën vallen:

- **Azure AD Connect Server(s) implementeren in staging-modus** - stelt een beheerder in staat om de staging-server te 'promoten' naar productie via een eenvoudige configuratieswitch.
- **Virtualisatie gebruiken** - Als de Azure AD-verbinding wordt geïmplementeerd in een virtuele machine (VM), kunnen beheerders hun virtualisatiestack gebruiken om te wonen en de VM snel opnieuw te implementeren en daarom de synchronisatie te hervatten.

Als uw organisatie geen strategie voor herstel en failover voor noodgevallen heeft voor Synchronisatie, moet u niet aarzelen om Azure AD Connect in de faseringsmodus te implementeren. Als er een mismatch is tussen uw productie- en faseringsconfiguratie, moet u de Azure AD Connect-faseringsmodus opnieuw basislijn en de productieconfiguratie weer aanpassen, inclusief softwareversies en -configuraties.

![Een schermafbeelding van de configuratie van de faseringsmodus van Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Blijf op de hoogte

Microsoft werkt Azure AD Connect regelmatig bij. Blijf op de hoogte om te profiteren van de prestatieverbeteringen, bugfixes en nieuwe mogelijkheden die elke nieuwe versie biedt.

Als uw Azure AD Connect-versie meer dan zes maanden achterloopt, moet u upgraden naar de meest recente versie.

#### <a name="source-anchor"></a>Bronanker

Met behulp van **ms-DS-consistencyguid** als [bronanker](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) u objecten gemakkelijker over forests en domeinen verwijderen, wat gebruikelijk is in ad-domeinconsolidatie/-opruiming, fusies, overnames en desinvesteringen.

Als u **ObjectGuid** momenteel als bronanker gebruikt, raden we u aan over te schakelen naar **ms-DS-ConsistencyGuid.**

#### <a name="custom-rules"></a>Aangepaste regels

Aangepaste azure AD Connect-regels bieden de mogelijkheid om de stroom van kenmerken tussen on-premises objecten en cloudobjecten te beheren. Het overmaken of misbruiken van aangepaste regels kan echter de volgende risico's met zich meebrengen:

- Complexiteit van problemen oplossen
- Prestatiedegradatie bij het uitvoeren van complexe bewerkingen tussen objecten
- Grotere kans op divergentie van configuratie tussen de productieserver en stagingserver
- Extra overhead bij het upgraden van Azure AD Connect als aangepaste regels worden gemaakt binnen de prioriteit groter dan 100 (gebruikt door ingebouwde regels)

Als u te complexe regels gebruikt, moet u de redenen voor de complexiteit onderzoeken en mogelijkheden voor vereenvoudiging vinden. Als u aangepaste regels hebt gemaakt met een voorrangswaarde van meer dan 100, moet u de regels zo vaststellen dat ze geen risico lopen of in strijd zijn met de standaardset.

Voorbeelden van misbruik van aangepaste regels zijn:

- **Compenseer voor vuile gegevens in de directory** - In dit geval is het raadzaam om samen te werken met de eigenaren van het AD-team en de gegevens in de directory op te schonen als een hersteltaak, en processen aan te passen om opnieuw inteintroduceren van slechte gegevens te voorkomen.
- **Eenmalige herstel van individuele gebruikers** - Het is gebruikelijk om regels te vinden die speciale zaak uitschieters, meestal als gevolg van een probleem met een specifieke gebruiker.
- **Overgecompliceerde "CloudFiltering"** - Hoewel het verminderen van het aantal objecten een goede praktijk is, bestaat het risico dat synchronisatiebereik wordt gemaakt en overcomplicated wordt gebruikt met behulp van veel synchronisatieregels. Als er complexe logica is om objecten buiten de OU-filtering op te nemen/uit te sluiten, wordt aanbevolen om deze logica buiten synchronisatie om te gaan en de objecten te labelen met een eenvoudig kenmerk 'cloudFiltered' dat kan stromen met een eenvoudige synchronisatieregel.

#### <a name="azure-ad-connect-configuration-documenter"></a>Configuratiedocumenter azure AD Connect

De [Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) is een hulpmiddel dat u gebruiken om documentatie van een Azure AD Connect-installatie te genereren om een beter begrip van de synchronisatieconfiguratie mogelijk te maken, vertrouwen te wekken in het goed krijgen van dingen en om te weten wat er is gewijzigd toen u een nieuwe build of configuratie van Azure AD Connect toepaste of aangepaste synchronisatieregels hebt toegevoegd of bijgewerkt. De huidige mogelijkheden van de tool zijn:

- Documentatie van de volledige configuratie van Azure AD Connect-synchronisatie.
- Documentatie van eventuele wijzigingen in de configuratie van twee Azure AD Connect-synchronisatieservers of wijzigingen ten opzichte van een bepaalde configuratiebasislijn.
- Het genereren van een PowerShell-implementatiescript om de verschillen of aanpassingen van de synchronisatieregel van de ene server naar de andere te migreren.

## <a name="assignment-to-apps-and-resources"></a>Toewijzing aan apps en resources

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenties op basis van groepen voor Microsoft-cloudservices

Azure Active Directory stroomlijnt het beheer van licenties via [groepslicenties](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) voor Microsoft-cloudservices. Op deze manier biedt IAM de groepsinfrastructuur en gedelegeerd beheer van deze groepen aan de juiste teams in de organisaties. Er zijn meerdere manieren om het lidmaatschap van groepen in Azure AD in te stellen, waaronder:

- **Gesynchroniseerd vanuit on-premises** - Groepen kunnen afkomstig zijn van on-premises mappen, wat een goede pasvorm kan zijn voor organisaties die groepsbeheerprocessen hebben opgezet die kunnen worden uitgebreid om licenties toe te wijzen in office 365.

- **Op kenmerken gebaseerd / dynamisch** - Groepen kunnen worden gemaakt in de cloud op basis van een expressie op basis van gebruikerskenmerken, bijvoorbeeld afdeling is gelijk aan 'verkoop'. Azure AD onderhoudt de leden van de groep en houdt deze in overeenstemming met de gedefinieerde expressie. Als u dit soort groepen gebruikt voor licentietoewijzing, kan een op kenmerken gebaseerde licentietoewijzing worden gemaakt, wat goed past bij organisaties die een hoge gegevenskwaliteit in hun directory hebben.

- **Gedelegeerd eigendom** - Groepen kunnen worden gemaakt in de cloud en kunnen worden aangewezen eigenaren. Op deze manier u bedrijfseigenaren, bijvoorbeeld Collaboration team of BI-team, in staat stellen om te bepalen wie toegang moet hebben.

Als u momenteel een handmatig proces gebruikt om licenties en onderdelen toe te wijzen aan gebruikers, raden we u aan groepslicenties te implementeren. Als uw huidige proces geen licentiefouten controleert of wat is toegewezen versus beschikbaar, moet u verbeteringen in het proces definiëren om licentiefouten aan te pakken en de licentietoewijzing te controleren.

Een ander aspect van licentiebeheer is de definitie van serviceplannen (onderdelen van de licentie) die moeten worden ingeschakeld op basis van taakfuncties in de organisatie. Als u toegang verleent tot serviceplannen die niet nodig zijn, kunnen gebruikers hulpprogramma's in de Office-portal zien waarvoor ze niet zijn opgeleid of waarvoor ze niet mogen worden gebruikt. Het kan extra helpdeskvolume, onnodige inrichting en risico's voor naleving en beheer in gevaar brengen, bijvoorbeeld bij het inrichten van OneDrive voor Bedrijven aan personen die mogelijk geen inhoud mogen delen.

Gebruik de volgende richtlijnen om serviceplannen voor gebruikers te definiëren:

- Beheerders moeten "bundels" van serviceplannen definiëren die aan gebruikers moeten worden aangeboden op basis van hun rol, bijvoorbeeld bedienden versus vloerarbeider.
- Maak groepen per cluster en wijs de licentie toe met serviceplan.
- Optioneel kan een kenmerk worden gedefinieerd om de pakketten voor gebruikers vast te houden.

> [!IMPORTANT]
> Groepsgebaseerde licenties in Azure AD introduceert het concept van gebruikers in een licentiefoutstatus. Als u licentiefouten opmerkt, moet u eventuele problemen met licentietoewijzing onmiddellijk [identificeren en oplossen.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems)

![Een schermafbeelding van een automatisch gegenereerde beschrijving van een computerscherm](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Levenscyclusbeheer

Als u momenteel een tool gebruikt, zoals [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) of een systeem van derden, dat is gebaseerd op een on-premises infrastructuur, raden we u aan toewijzing uit het bestaande hulpprogramma te verwijderen, groepsgebaseerde licenties te implementeren en een groepslevenscyclusbeheer te definiëren op basis van [groepen.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups) Als uw bestaande proces geen rekening houdt met nieuwe werknemers of werknemers die de organisatie verlaten, moet u op groepen gebaseerde licenties implementeren op basis van dynamische groepen en een levenscyclus van groepslidmaatschapdefiniëren. Ten slotte u, als groepslicenties worden geïmplementeerd tegen on-premises groepen die geen levenscyclusbeheer hebben, overwegen cloudgroepen te gebruiken om mogelijkheden in te schakelen, zoals gedelegeerd eigendom of dynamisch lidmaatschap op basis van kenmerken.

### <a name="assignment-of-apps-with-all-users-group"></a>Toewijzing van apps met groep 'Alle gebruikers'

Broneigenaren kunnen van mening zijn dat de groep **Alle gebruikers** alleen **Enterprise-werknemers** bevat wanneer ze daadwerkelijk zowel **bedrijfswerknemers** als **gasten**kunnen bevatten. Daarom moet u extra voorzichtig zijn bij het gebruik van de groep **Alle gebruikers** voor toepassingstoewijzing en het verlenen van toegang tot bronnen zoals SharePoint-inhoud of -toepassingen.

> [!IMPORTANT]
> Als de groep **Alle gebruikers** is ingeschakeld en wordt gebruikt voor beleid voor voorwaardelijke toegang, app- of resourcetoewijzing, moet u de [groep beveiligen](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) als u niet wilt dat gastgebruikers worden opgenomen. Bovendien moet u uw licentietoewijzingen oplossen door groepen te maken en toe te wijst die alleen **bedrijfswerknemers** bevatten. Als u daarentegen vindt dat de groep **Alle gebruikers** is ingeschakeld, maar niet wordt gebruikt om toegang tot resources te verlenen, moet u ervoor zorgen dat de operationele richtlijnen van uw organisatie zijn om die groep opzettelijk te gebruiken (waaronder zowel **bedrijfsmedewerkers** als **gasten).**

### <a name="automated-user-provisioning-to-apps"></a>Geautomatiseerde gebruikersinrichting voor apps

[Geautomatiseerde gebruikersinrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) voor toepassingen is de beste manier om een consistente inrichting, deprovisioning en levenscyclus van identiteiten in meerdere systemen te creëren.

Als u momenteel apps op een ad-hocmanier inricht of dingen gebruikt zoals CSV-bestanden, JIT of een on-premises oplossing die het levenscyclusbeheer niet aanpakt, raden we u aan [toepassingsinrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) te implementeren met Azure AD voor ondersteunde toepassingen en een consistent patroon te definiëren voor toepassingen die nog niet worden ondersteund door Azure AD.

![Azure AD-inrichtingsservice](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Basislijn deltasynchronisatiecyclus van Azure AD Connect

Het is belangrijk om het volume van de veranderingen in uw organisatie te begrijpen en ervoor te zorgen dat het niet te lang duurt om een voorspelbare synchronisatietijd te hebben.

De [standaard deltasynchronisatiefrequentie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) is 30 minuten. Als de deltasynchronisatie consistent langer dan 30 minuten duurt of er aanzienlijke verschillen zijn tussen de deltasynchronisatieprestaties van fasering en productie, moet u de factoren onderzoeken en bekijken [die van invloed zijn op de prestaties van Azure AD Connect.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Aanbevolen lezen van Azure AD Connect-probleemoplossing

- [Adreslijstkenmerken voorbereiden voor synchronisatie met Office 365 met het hulpprogramma IdFix - Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: fouten oplossen tijdens synchronisatie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Samenvatting

Er zijn vijf aspecten aan een veilige infrastructuur van de Identiteit. Met deze lijst u snel de nodige acties vinden en uitvoeren om de levenscyclus van identiteiten en hun rechten in uw organisatie te beveiligen en te beheren.

- Eigenaren toewijzen aan belangrijke taken.
- Synchronisatieproblemen zoeken en oplossen.
- Definieer een failoverstrategie voor noodherstel.
- Stroomlijn het beheer van licenties en de toewijzing van apps.
- Automatiseer gebruikersinrichting voor apps.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [verificatiebeheercontroles en -acties.](active-directory-ops-guide-auth.md)
