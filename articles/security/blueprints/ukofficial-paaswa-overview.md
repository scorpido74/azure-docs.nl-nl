---
title: Azure-blauwdruk voor beveiliging en naleving-PaaS voor het hosten van webtoepassingen voor de officiële workloads in het Verenigd Konink rijk
description: Azure-blauwdruk voor beveiliging en naleving-PaaS voor het hosten van webtoepassingen voor de officiële workloads in het Verenigd Konink rijk
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 1f6eeea85a348bb8e88a387fa0fc6bed55e41a5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262774"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure-blauwdruk voor beveiliging en naleving: Host voor PaaS-webtoepassingen voor de officiële workloads van het Verenigd Konink rijk

## <a name="azure-security-and-compliance-blueprints"></a>Blauwdrukken voor Azure-beveiliging en naleving

Azure-blauw drukken bestaat uit richt lijnen voor documenten en Automation-sjablonen die Cloud architecturen implementeren om oplossingen te bieden voor scenario's met betrekking tot accreditatie-of nalevings vereisten. Azure-blauw drukken zijn regels voor begeleiding en Automation-sjablonen waarmee Microsoft Azure klanten hun bedrijfs doelen sneller kunnen leveren door middel van het inrichten van een basis architectuur die kan worden uitgebreid om te voldoen aan alle verdere vereisten.

## <a name="overview"></a>Overzicht

Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen en automatiserings scripts voor het leveren van een Microsoft Azure [platform as a Service (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) gehoste webtoepassings architectuur die geschikt is voor het verwerken van werk belastingen die zijn geclassificeerd als [UK-ambtenaar ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Deze beveiligings classificatie omvat het meren deel van de gegevens die zijn gemaakt of verwerkt door de open bare sector. Dit omvat routine bedrijfs activiteiten en-services, die als verloren, gestolen of uitgegeven in de media worden uitgevoerd, waarvan sommige nadelige gevolgen kunnen hebben. Het typische bedreigings profiel voor de officiële classificatie is veel hetzelfde als een persoonlijke onderneming die waardevolle informatie en services biedt. In het Verenigd Konink rijk wordt gekeken naar de nood zaak om gegevens of services van Britse UK te beschermen tegen bedreigingen of inbreuken door aanvallers met gebonden mogelijkheden en resources, zoals (maar niet beperkt tot) hactivists, op het onderhandelings journalisten; bevoegde individuele hackers en de meerderheid van criminele individuen en groepen.

Deze blauw druk is gecontroleerd door het UK National Cyber Security Center (NCSC) en is afgestemd op de NCSC 14 Cloud Security Principles.

De architectuur maakt gebruik van Azure [platform as a Service](https://azure.microsoft.com/overview/what-is-paas/) -onderdelen om een omgeving te bieden waarmee klanten de kosten en complexiteit van het kopen van software licenties kunnen vermijden, van het beheren van de onderliggende toepassings infrastructuur en middleware of de Ontwikkel hulpprogramma's en andere bronnen. Klanten beheren de toepassingen en services die ze ontwikkelen, gericht op het leveren van bedrijfs waarde, terwijl Microsoft Azure de andere Azure-resources, zoals virtuele machines, opslag en netwerken, beheert, waardoor meer van de [divisie van verantwoordelijk](../fundamentals/paas-deployments.md) voor het beheer van de infra structuur op het Azure-platform. [Azure-app Services](https://azure.microsoft.com/services/app-service/) biedt automatisch schalen, hoge Beschik baarheid, ondersteunt Windows en Linux en maakt automatische implementaties mogelijk vanuit github, Azure DevOps of een Git-opslag plaats als standaard services. Dankzij het gebruik van App Services kunnen ontwikkel aars zich concentreren op het leveren van bedrijfs waarde zonder de overhead van het beheer van de infra structuur. Het is mogelijk om ontwikkel nieuwe Java-, PHP-, node. js-, python- C# , HTML-of webtoepassingen te bouwen, of om bestaande Cloud-of on-premises webtoepassingen te migreren naar Azure-app-Services (hoewel uitgebreide mogelijkheden en testen voor het bevestigen van de prestaties vereist).

Deze blauw druk is gericht op het inrichten van een beveiligd Foundation- [platform als een](https://azure.microsoft.com/overview/what-is-paas/) webinterface op basis van een service voor open bare en ook Back-Office-gebruikers. In dit scenario van de blauw druk wordt het gebruik van door Azure gehoste webservices beschouwd, waarbij een open bare gebruiker veilig gevoelige gegevens kan indienen, bekijken en beheren. het is ook mogelijk dat de gevoelige gegevens die de open bare gebruiker heeft verzonden, veilig kunnen worden verwerkt door een back Office-of overheids operator. Voor dit scenario gelden de volgende cases:

- Een gebruiker die een belasting aangifte indient, met een overheids operator die de verzen ding verwerkt;
- Een gebruiker die een service aanvraagt via een webtoepassing, met een back-Office-gebruiker die de service valideert en levert; of
- Een gebruiker die open bare domein Help-informatie zoekt en bekijkt met betrekking tot een overheids service.

Met behulp van [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sjablonen en Azure Command line interface scripts implementeert de blauw druk een omgeving die wordt afgestemd op het UK National Cyber NCSC-beveiligings centrum voor de [Cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) , en het Center voor Internet Security (CIS) [essentiële beveiligings controles](https://www.cisecurity.org/critical-controls.cfm). De NCSC raadt aan de Cloud beveiligings principes door klanten te worden gebruikt om de beveiligings eigenschappen van de service te evalueren en om inzicht te krijgen in de verdeling van de verantwoordelijkheid tussen de klant en de leverancier. Micro soft heeft informatie over elk van deze principes verstrekt, zodat u beter inzicht krijgt in de splitsing van verantwoordelijkheden. Deze architectuur en bijbehorende Azure Resource Manager sjablonen worden ondersteund door de micro soft-white paper- [beveiligings controles voor de 14-Cloud voor UK in de Cloud met behulp van Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Deze architectuur is gecontroleerd door de NCSC en is afgestemd op de beveiligings principes van het Verenigd Konink rijk van NCSC 14, waardoor organisaties in de publieke sector snel hun capaciteit kunnen volgen om te voldoen aan de nalevings verplichtingen met behulp van Cloud Services die wereld wijd en in het Verenigd Konink rijk zijn op de Microsoft Azure Cloud. Met deze sjabloon wordt de infra structuur voor de werk belasting geïmplementeerd. Toepassings code en ondersteunende software voor bedrijfslaag en gegevenslaag moeten door klanten worden geïnstalleerd en geconfigureerd. Gedetailleerde implementatie-instructies zijn [hier](https://aka.ms/ukofficial-paaswa-repo/)beschikbaar.

Deze blauw druk is een kern architectuur. Onze klanten kunnen deze blauw druk gebruiken als basis voor hun officiële classificatie op het web gebaseerde workloads en de sjablonen en resources uitbreiden met hun eigen vereisten. Deze blauw druk is gebaseerd op de beginselen van de [OFFICAL met drie lagen IaaS webtoepassingen](https://aka.ms/ukofficial-iaaswa) die onze klanten zowel [Infrastructure as a Service (IaaS) als](https://azure.microsoft.com/overview/what-is-iaas/) PaaS implementatie opties bieden voor het hosten van op internet gebaseerde workloads.

Als u deze blauw druk wilt implementeren, is een Azure-abonnement vereist. Als u geen Azure-abonnement hebt, kunt u zich snel en eenvoudig op een gratis manier aanmelden: Aan de slag met Azure. Klik [hier](https://aka.ms/ukofficial-paaswa-repo/) voor implementatie-instructies.

## <a name="architecture-and-components"></a>Architectuur en onderdelen

Deze blauw druk biedt een oplossing voor het hosten van webtoepassingen in een Azure-cloud omgeving die ondersteuning biedt voor de officiële workloads van UK. De architectuur levert een beveiligde omgeving die gebruikmaakt van Azure-platform als service mogelijkheden. Binnen de omgeving worden twee App Service web-apps geïmplementeerd (één voor open bare gebruikers en één voor een back-Office-gebruiker), met een API-app-laag om de zakelijke services voor de Webfront-end te bieden. Een Azure SQL Database wordt geïmplementeerd als een beheerd relationeel gegevens Archief voor de toepassing. Connectiviteit met deze onderdelen van buiten het platform en tussen deze onderdelen wordt versleuteld met TLS 1,2 om gegevens te garanderen in Trans Port-privacy, waarbij toegang is geverifieerd door Azure Active Directory.

![PaaS webtoepassing hosten voor de referentie architectuur diagram van de officiële workloads van het Verenigd Konink rijk](images/ukofficial-paaswa-architecture.png?raw=true "PaaS webtoepassing hosten voor de referentie architectuur diagram van de officiële workloads van het Verenigd Konink rijk")

Als onderdeel van de implementatie architectuur, het inrichten van beveiligde opslag, bewaking & logboek registratie, Unified Security Management & Advanced Threat Protection en beheer mogelijkheden, worden ook geïmplementeerd om ervoor te zorgen dat klanten over alle hulpprogram ma's beschikken die nodig zijn om Beveilig en bewaak hun omgeving voor deze oplossing.

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Azure Active Directory
- App Service
- Web-app
- API-app
- Azure DNS
- Key Vault
- Azure Monitor (Logboeken)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Implementatie architectuur

De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

### <a name="security"></a>Beveiliging

#### <a name="identity-and-authentication"></a>Identiteit en verificatie

Deze blauw druk zorgt ervoor dat de toegang tot bronnen wordt beschermd via Directory-en identiteits beheer Services. Deze architectuur maakt volledig gebruik van [identiteit als de beveiligings verbinding](../fundamentals/paas-deployments.md). 

De volgende technologieën bieden mogelijkheden voor identiteits beheer in de Azure-omgeving:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Alle gebruikers voor de oplossing zijn gemaakt in Azure Active Directory, met inbegrip van gebruikers die toegang hebben tot de SQL Database.
- Verificatie voor de operator die is gericht op de webtoepassing en de toegang tot het beheer van de Azure-resources wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie.
- Database kolom versleuteling maakt gebruik van Azure AD om de toepassing te verifiëren voor Azure SQL Database. Zie [always encrypted voor meer informatie: Beveilig gevoelige gegevens in SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- De burger Facing Web-toepassing is geconfigureerd voor open bare toegang. Als u het maken en verifiëren van accounts wilt toestaan via Active Directory of id-providers voor sociale netwerken [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , indien nodig, kunnen worden geïntegreerd.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen en Risk ante accounts voorziet in aanbevelingen voor het verbeteren van de beveiligings postuur van de identiteiten van uw organisatie, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteiten van uw organisatie en verdachte incidenten onderzoeken en de juiste actie ondernemen om ze op te lossen.
- [Met Access Control op basis van rollen (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) kunt u nauw keurig toegang krijgen tot het beheer van Azure. Abonnements toegang is beperkt tot de abonnements beheerder en Azure Key Vault toegang is beperkt tot gebruikers die toegang tot sleutel beheer nodig hebben.
- Dankzij het gebruik van [Azure Active Directory voorwaardelijke toegang](../../active-directory/active-directory-conditional-access-azure-portal.md) kunnen klanten extra beveiligings controles afdwingen voor toegang tot apps of gebruikers in hun omgeving op basis van specifieke voor waarden, zoals locatie, apparaat, status en aanmeldings risico.
- [Azure DDoS Protection](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) in combi natie met aanbevolen procedures voor het ontwerpen van toepassingen, biedt bescherming tegen DDoS-aanvallen, met AlwaysOn verkeers bewaking en real-time beperking van veelvoorkomende aanvallen op netwerk niveau. Met een PaaS-architectuur is platform niveau DDoS-beveiliging transparant voor de klant en is deze opgenomen in het platform, maar het is belang rijk te weten dat de gebruiker verantwoordelijk is voor het ontwerp van de toepassings beveiliging.

#### <a name="data-in-transit"></a>Actieve gegevens

Gegevens worden door Voer buiten en tussen Azure-onderdelen beveiligd met [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), waarbij gebruik wordt gemaakt van symmetrische crypto grafie op basis van een gedeeld geheim om de communicatie te versleutelen wanneer deze via het netwerk worden verzonden. Netwerk verkeer wordt standaard beveiligd met TLS 1,2.

#### <a name="security-and-malware-protection"></a>Beveiliging en bescherming tegen schadelijke software

[Azure Security Center](https://azure.microsoft.com/services/security-center/) biedt een gecentraliseerde weer gave van de beveiligings status van al uw Azure-resources. In één oogopslag kunt u controleren of de juiste beveiligings maatregelen aanwezig zijn en op de juiste wijze zijn geconfigureerd. u kunt snel alle resources identificeren die aandacht vereisen.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) is een gepersonaliseerde Cloud consultant die u helpt bij het volgen van de aanbevolen procedures voor het optimaliseren van uw Azure-implementaties. Het analyseert uw resourceconfiguratie en gebruikstelemetrie, en raadt vervolgens oplossingen aan die u kunnen helpen de kosteneffectiviteit, prestaties, hoge beschikbaarheid en beveiliging van uw Azure-resources te verbeteren.

[Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) is een real-time beschermings functie waarmee u virussen, spyware en andere schadelijke software kunt herkennen en verwijderen. Dit wordt standaard geïnstalleerd op de onderliggende infra structuur van de virtuele PaaS-machine en wordt door de Azure-Fabric transparant beheerd voor de klant.

### <a name="paas-services-in-this-blueprint"></a>PaaS Services in deze blauw druk

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service biedt een volledig beheerde omgeving voor webhosting voor webtoepassingen die zijn ontwikkeld in Java, PHP, node. js C# PYTHON, HTML en zonder dat u de infra structuur hoeft te beheren. Het biedt automatisch schalen en hoge Beschik baarheid, ondersteunt zowel Windows als Linux, en maakt automatische implementaties mogelijk vanuit [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) of op Git gebaseerde opslag plaats.

App Service is [compatibel met ISO, SOC en PCI](https://www.microsoft.com/TrustCenter/) en kan gebruikers verifiëren met [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) of met sociale aanmelding ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)en [micro soft-verificatie](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)).

Basis-, standaard-en Premium-abonnementen zijn voor productie-workloads en worden uitgevoerd op toegewezen virtuele-machine-instanties. Elk exemplaar kan meerdere toepassingen en domeinen ondersteunen. App Services biedt ook ondersteuning voor [IP-adres beperkingen](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) voor het beveiligen van verkeer naar vertrouwde IP-adressen, indien nodig en ook [beheerde identiteiten voor Azure-bronnen](https://docs.microsoft.com/azure/app-service/overview-managed-identity) voor een beveiligde verbinding met andere PaaS-Services, zoals [Key Vault](https://azure.microsoft.com/services/key-vault/) en [Azure SQL Data base](https://azure.microsoft.com/services/sql-database/). Wanneer extra beveiliging is vereist, hebben we uw apps in een persoonlijke, exclusieve Azure-omgeving gehost en zijn ze ideaal voor apps waarvoor beveiligde verbindingen met uw on-premises netwerk of extra prestaties en schaal zijn vereist.

Met deze sjabloon worden de volgende App Service-functies geïmplementeerd:

- [Standaard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service plan tier
- Meerdere App Service [implementatie sleuven](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Dev, preview, QA, bedoeld en training Production (standaard sleuf).
- [Beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/app-service/overview-managed-identity) om verbinding te maken met [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (dit kan ook worden gebruikt om toegang te verlenen tot [Azure SQL database](https://azure.microsoft.com/services/sql-database/) 
- Integratie met [Azure-toepassing Insights](../../azure-monitor/app/azure-web-apps.md) voor het bewaken van de prestaties
- [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) 
- Metrische [waarschuwingen](../../azure-monitor/app/alerts.md) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL Database is een algemene, beheerde relationele databaseservice in Microsoft Azure die ondersteuning biedt voor structuren zoals relationele gegevens, JSON, ruimtelijke gegevens en XML. SQL Database biedt beheerde single SQL-data bases, beheerde SQL-data bases in een [elastische pool](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)en SQL [Managed instances](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (in open bare preview). De service biedt [dynamisch schaalbare prestaties](../../sql-database/sql-database-purchase-models.md) en opties zoals [columnstore-indexen](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) voor krachtige analyses en rapportages, en [in-memory OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) voor veeleisende transactieverwerking. Microsoft verzorgt op naadloze wijze alle patching en updating van de SQL-codebasis en heeft het beheer van de onderliggende infrastructuur volledig weggewerkt.

Azure SQL Database in deze blauw druk

Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:

- [Firewall regels op server-en database niveau](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), of via [Virtual Network Service-eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) met behulp van regels voor het [virtuele netwerk](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) helpt Azure SQL database en Azure Data Warehouse te beschermen tegen de dreiging van schadelijke activiteiten. Er wordt in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden in rust uitgevoerd zonder dat de toepassing hoeft te worden gewijzigd.
- [Azure AD-verificatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)kunt u de identiteiten van database gebruikers en andere micro soft-services centraal beheren op één centrale locatie. Centraal-ID-beheer biedt één locatie voor het beheren van database gebruikers en het vereenvoudigt het beheer van machtigingen.
- Gebruik van Azure Active Directory voor database beheer
- [Audit logboeken](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) naar opslag accounts
- Metrische [waarschuwingen](../../azure-monitor/app/alerts.md) voor mislukte DB-verbindingen
- [SQL-bedreigings detectie](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Micro soft [Azure Storage](https://azure.microsoft.com/services/storage/) is een door micro soft beheerde Cloud service die opslag biedt die Maxi maal beschikbaar is, veilig, duurzaam, schaalbaar en redundant is. Azure Storage bestaat uit Blob Storage, File Storage en Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Azure Storage in deze blauw druk

Deze sjabloon maakt gebruik van de volgende Azure Storage onderdelen:

- [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Alleen HTTPS-verbindingen toestaan

#### <a name="data-at-rest"></a>Data-at-rest

Via [Storage service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) alle gegevens die naar Azure Storage zijn geschreven, zijn versleuteld via 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn. U kunt door micro soft beheerde versleutelings sleutels met SSE gebruiken of u kunt [uw eigen versleutelings sleutels](../../storage/common/storage-encryption-keys-portal.md)gebruiken.

Opslag accounts kunnen worden beveiligd via [Virtual Network Service-eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) met behulp van regels voor het [virtuele netwerk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Gedetailleerde informatie over het beveiligen van Azure Storage vindt u in de [beveiligings handleiding](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Geheimen beheren

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) wordt gebruikt om toepassings sleutels en geheimen te beveiligen om ervoor te zorgen dat ze niet toegankelijk zijn voor externe partijen. Key Vault is niet bedoeld als archief voor wachtwoorden van gebruikers. U kunt meerdere veilige containers maken, ook wel kluizen genoemd. Deze kluizen worden ondersteund door hardware security modules (HSM's). Kluizen verminderen de kans op onbedoeld verlies van beveiligingsinformatie door de opslag van toepassingsgeheimen te centraliseren. Sleutelkluizen regelen en registreren ook de toegang tot alles wat erin is opgeslagen. Azure Key Vault kan het aanvragen en vernieuwen van Transport Layer Security-certificaten (TLS) verwerken, met behulp van de functies die vereist zijn voor een robuuste oplossing voor het beheren van de levenscyclus van certificaten.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault in deze blauw druk

- Bevat de toegangs sleutel voor opslag, met lees toegang die is verleend aan de [beheerde identiteit](https://docs.microsoft.com/azure/app-service/overview-managed-identity) van de klant gerichte web-app
- Bevat het SQL Server DBA-wacht woord (in een afzonderlijke kluis)
- Logboek registratie van diagnostische gegevens

### <a name="monitoring-logging-and-audit"></a>Bewaking, logboek registratie en controle

#### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) is een service in azure die u helpt bij het verzamelen en analyseren van gegevens die zijn gegenereerd door resources in uw Cloud-en on-premises-omgevingen.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Azure Monitor Logboeken in deze blauw druk

- SQL-evaluatie
- Diagnostische gegevens Key Vault
- Application Insights verbinding
- Azure-activiteitenlogboek

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Voor het bewaken van Live webtoepassingen worden automatisch prestatie afwijkingen gedetecteerd, worden de prestaties geanalyseerd, worden problemen opgespoord en wordt uitgelegd hoe gebruikers met de app werken. Application Insights kan worden geïmplementeerd op platforms zoals .NET, node. js en Java EE, lokaal gehost of in de Cloud. De service kan ook worden geïntegreerd met uw DevOps-proces en bevat verbindingspunten naar verschillende hulpmiddelen voor ontwikkelaars.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights in deze blauw druk

Deze sjabloon maakt gebruik van de volgende Application Insights onderdelen:

- Application Insights dash board per site (operator, klant en API)

#### <a name="azure-activity-logs"></a>Activiteiten logboeken van Azure

In [Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) worden controle gebeurtenissen voor uw abonnementen gecontroleerd. Met het activiteiten logboek kunt u de ' wat, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) maakt kern bewaking voor Azure-Services mogelijk door het verzamelen van metrische gegevens, activiteiten logboeken en diagnostische Logboeken toe te staan. Azure Monitor biedt metrische infrastructuurgegevens en logboeken op basisniveau voor de meeste services in Microsoft Azure.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/ukofficial-paaswa-tm) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![Hosten van PaaS-webtoepassingen voor het risico model officiële workloads van het Verenigd Konink rijk](images/ukofficial-paaswa-threat-model.png?raw=true "Hosten van PaaS-webtoepassingen voor het risico model officiële workloads van het Verenigd Konink rijk")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Nalevings documentatie voor NCSC Cloud Security Principles

De kroon commerciële dienst (een instantie die de commerciële en aanschaffings activiteiten door de overheid verbetert), vernieuwt de classificatie van micro soft in-Scope Enter prise Cloud Services naar G-Cloud V6, waarbij alle aanbiedingen op het officiële niveau worden gedekt. Details van Azure en G-Cloud vindt u in het [overzicht van Azure UK G-Cloud Security Assessment](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Deze blauw druk is afgestemd op de 14 beveiligings principes voor de cloud die zijn gedocumenteerd in de NCSC voor [Cloud beveiliging](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) om ervoor te zorgen dat een omgeving wordt ondersteund die werk belastingen ondersteunt die zijn geclassificeerd als UK-ambtenaar.

In de [officiële matrix voor de gebruikers verantwoordelijkheid van Azure-BLAUWDRUK voor beveiliging en naleving UK](https://aka.ms/ukofficial-crm) (Excel-werkmap) worden alle 14 beveiligings principes voor de Cloud vermeld en wordt voor elk principe (of principe subdeel) aangegeven of de implementatie van het principe de de verantwoordelijkheid van micro soft, de klant of het gedeeld tussen de twee.

De [Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor het Verenigd Konink rijk principle-implementatie matrix](https://aka.ms/ukofficial-paaswa-pim) (Excel-werkmap) bevat alle 14 beveiligings principes voor de Cloud en geeft voor elk principe (of principe subdeel) die is aangewezen als de verantwoordelijkheid van de klant in de matrix van klant verantwoordelijkheden, 1) als de blauw druk het principe implementeert en 2) een beschrijving van de manier waarop de implementatie wordt uitgelijnd met de vereiste voor Schriften.  

Daarnaast publiceerde de Cloud Security Alliance (CSA) de Cloud beheer matrix ter ondersteuning van klanten in de evaluatie van cloud providers en om vragen te identificeren die moeten worden beantwoord voordat ze naar Cloud Services kunnen worden verplaatst. Als antwoord heeft Microsoft Azure de CSA consensus Assessment Initiative vragen lijst ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)) beantwoord, waarin wordt beschreven hoe micro soft de voorgestelde principes verhelpt.

## <a name="third-party-assessment"></a>Beoordeling van derden

Deze blauw druk is gecontroleerd door het UK National Cyber Security Center (NCSC) en is afgestemd op de NCSC 14 Cloud Security Principles

De Automation-sjablonen zijn getest door het Azure Cloud Solution Architects team van UK Customer-oplossing en door onze micro soft-partner, [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>De oplossing implementeren

Deze Azure-blauwdruk voor beveiliging en naleving automatisering bestaat uit JSON-configuratie bestanden en Power shell-scripts die worden verwerkt door de API-service van Azure Resource Manager om resources te implementeren in Azure. Gedetailleerde implementatie-instructies zijn [hier](https://aka.ms/ukofficial-paaswa-repo)beschikbaar.

Er zijn drie benaderingen voor de implementatie. Een eenvoudige ' Express ' [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) die geschikt is voor het snel bouwen van een test omgeving; een door para meters [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -benadering die een grotere configuratie biedt voor werkbelasting omgevingen; en een implementatie op basis van Azure Portal waarbij de operator de implementatie parameters kan opgeven via de Azure Portal. 

1.  Kloon of down load [deze](https://aka.ms/ukofficial-paaswa-repo) github-opslag plaats naar uw lokale werk station.
2.  Controle [methode 1: Azure CLI 2 (Express-versie](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) ) en de gegeven opdrachten uit te voeren.
3.  Beoordelings [methode 1a: Azure CLI 2 (de implementatie configureren via script argumenten)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) en de gegeven opdrachten uitvoeren
4.  Controle [methode 2: Implementatie proces](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) Azure Portal en de vermelde opdrachten uitvoeren

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="api-management"></a>API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) kan worden gebruikt vóór de API-app service om extra beveiligings lagen te bieden, beperkingen en controles uit te voeren om api's weer te geven, te beperken en te beveiligen.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) kan worden geïmplementeerd als een besturings element waarmee gebruikers zich kunnen registreren, een identiteit kunnen maken en autorisatie-en toegangs beheer voor de open bare webtoepassing kunnen inschakelen.

## <a name="disclaimer"></a>Vrijwaring

- Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
- Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
- Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
- Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
- Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
- Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
