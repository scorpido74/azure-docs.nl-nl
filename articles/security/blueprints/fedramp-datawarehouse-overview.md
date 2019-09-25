---
title: Azure-blauwdruk voor beveiliging en naleving-data warehouse voor FedRAMP-automatisering
description: Azure-blauwdruk voor beveiliging en naleving-data warehouse voor FedRAMP-automatisering
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 7fe5b45d7719b34fff8c09d08f510dc465f50104
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257406"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure-blauwdruk voor beveiliging en naleving: Data Warehouse voor FedRAMP-automatisering

## <a name="overview"></a>Overzicht

De [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) is een Verenigde Staten overheids programma dat een gestandaardiseerde benadering biedt voor de beveiligings beoordeling, de autorisatie en de voortdurende bewaking van Cloud producten en-services. Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen voor het leveren van een Microsoft Azure Data Warehouse-architectuur die helpt bij het implementeren van een subset van FedRAMP High Controls. Deze oplossing biedt richt lijnen voor de implementatie en configuratie van Azure-resources voor een algemene referentie architectuur, waarbij u kunt zien hoe klanten aan specifieke vereisten voor beveiliging en naleving kunnen voldoen en als basis voor klanten worden gebruikt om bouw en configureer hun eigen data warehouse-oplossingen in Azure.

Deze referentie architectuur, de bijbehorende beheer implementatie handleidingen en bedreigings modellen zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mogen niet worden gebruikt als in een productie omgeving. Het implementeren van een toepassing in deze omgeving zonder aanpassing is niet voldoende om volledig te voldoen aan de vereisten van de FedRAMP hoge basis lijn. Houd rekening met het volgende:
- De architectuur biedt een basis lijn om klanten te helpen bij het implementeren van werk belastingen naar Azure op een FedRAMP.
- Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Deze oplossing biedt een referentie architectuur voor data warehouses waarmee een hoogwaardig en veilig data warehouse in de Cloud wordt geïmplementeerd. Er zijn twee afzonderlijke gegevens lagen in deze architectuur: een locatie waar gegevens worden geïmporteerd, opgeslagen en gefaseerd worden uitgevoerd binnen een geclusterde SQL-omgeving, en een andere voor de Azure SQL Data Warehouse waar de gegevens worden geladen met een ETL-hulp programma (bijvoorbeeld [poly base](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-query's) voor verwerking. Wanneer de gegevens zijn opgeslagen in Azure SQL Data Warehouse, kan Analytics op grote schaal worden uitgevoerd.

Microsoft Azure biedt een aantal rapportage-en analyse Services voor de klant. Deze oplossing bevat SQL Server Reporting Services (SSRS) voor het snel maken van rapporten van de Azure SQL Data Warehouse. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice, raadt Azure het gebruik aan van een vertrouwde certificerings instantie voor verbeterde beveiliging.

Gegevens in de Azure SQL Data Warehouse worden opgeslagen in relationele tabellen met kolom opslag, een indeling waarmee de kosten voor de gegevens opslag aanzienlijk worden verminderd en de query prestaties worden verbeterd.  Afhankelijk van de gebruiks vereisten kunnen Azure SQL Data Warehouse Compute-resources omhoog of omlaag worden geschaald of volledig worden afgesloten als er geen actieve processen zijn die reken resources vereisen.

Een SQL-load balancer beheert SQL-verkeer, waardoor hoge prestaties worden gegarandeerd. Alle virtuele machines in deze referentie architectuur implementeren in een beschikbaarheidsset met SQL Server exemplaren die zijn geconfigureerd in een AlwaysOn-beschikbaarheids groep voor mogelijkheden met hoge Beschik baarheid en herstel na nood gevallen.

Deze referentie architectuur voor data warehouses bevat ook een Active Directory (AD)-laag voor het beheer van bronnen binnen de architectuur. Met het Active Directory-subnet kunt u eenvoudig de implementatie onder een grotere structuur van een AD-forest, waardoor de omgeving voortdurend kan worden geactiveerd, zelfs wanneer toegang tot het grotere forest niet beschikbaar is. Alle virtuele machines zijn gekoppeld aan de laag Active Directory en gebruiken Active Directory groeps beleid om configuraties voor beveiliging en naleving af te dwingen op het niveau van het besturings systeem.

Een virtuele machine fungeert als een management bastion-host en biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen. De gegevens worden in het faserings gebied geladen via deze beheer bastion-host. **U wordt aangeraden een VPN-of Azure ExpressRoute-verbinding te configureren voor beheer en gegevens import in het subnet met de referentie architectuur.**

![Data Warehouse voor FedRAMP-referentie architectuur diagram](images/fedramp-datawarehouse-architecture.png?raw=true "Data Warehouse voor FedRAMP-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

Azure Virtual Machines
-   (1) bastion-host
-   (2) Active Directory domein controller
-   (2) SQL Server cluster knooppunt
-   (1) SQL Server Witness

Beschikbaarheidssets
-   (1) Active Directory domein controllers
-   (1) SQL-cluster knooppunten en Witness

Virtueel netwerk
-   (4) subnetten
-   (4) netwerk beveiligings groepen

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Recovery Services-kluis

Azure Key Vault

Azure Monitor-logboeken

## <a name="deployment-architecture"></a>Implementatie architectuur

De volgende sectie bevat informatie over de ontwikkelings-en implementatie-elementen.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) is een edw (Enter prise Data Warehouse) dat gebruikmaakt van enorme parallelle verwerking (MPP) om snel complexe query's uit te voeren op PETA bytes met gegevens. Importeer big data in SQL Data Warehouse met eenvoudige poly Base T-SQL-query's en gebruik de kracht van MPP voor het uitvoeren van analyses met hoge prestaties.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) maakt het snel maken van rapporten met tabellen, grafieken, kaarten, meters, matrices en meer voor Azure SQL Data Warehouse.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers in staat stelt om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen in een veilige lijst toe te staan. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet u de bron van het verkeer definiëren in de netwerk beveiligings groep (NSG).

Een virtuele machine is gemaakt als een bastion-host die is gekoppeld aan een domein met de volgende configuraties:
-   [Uitbrei ding voor antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensie Azure Monitor logboeken](../../virtual-machines/windows/extensions-oms.md)
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) het gebruik van Azure Key Vault (opzichten Azure Government, PCI DSS, HIPAA en andere vereisten)
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van resources van de virtuele machine te verminderen wanneer het niet wordt gebruikt
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het actieve besturings systeem

### <a name="virtual-network"></a>Virtueel netwerk
Deze referentie architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.0.0.0/16.

**Netwerk beveiligings groepen**: [Nsg's](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten Access Control-lijsten (acl's) waarmee verkeer binnen een VNet wordt toegestaan of geweigerd. Nsg's kan worden gebruikt om verkeer op een subnet of een afzonderlijk VM-niveau te beveiligen. De volgende Nsg's bestaan:
  - Een NSG voor de gegevenslaag (SQL Server clusters, SQL Server Witness en SQL Load Balancer)
  - Een NSG voor de bastion-host van het beheer
  - Een NSG voor Active Directory
  - Een NSG voor SQL Server Reporting Services

Voor elk van de Nsg's zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke NSG:
  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
  - Azure Monitor-logboeken zijn verbonden met de [Diagnostische gegevens van de NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende NSG.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage** Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling te bieden voor besturings systeem-en gegevens schijven. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL database** Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   [Ad-verificatie en-autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) maken het identiteits beheer mogelijk van database gebruikers en andere micro soft-Services op één centrale locatie.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   SQL Database is geconfigureerd voor het gebruik van [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), waarmee in realtime versleuteling en ontsleuteling van gegevens en logboek bestanden worden uitgevoerd om informatie te beveiligen. TDE biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [detectie van SQL-bedreigingen](../../sql-database/sql-database-threat-detection.md) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen.
-   [Always encrypted kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of app-servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
-   [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kunnen worden uitgevoerd nadat de referentie architectuur is geïmplementeerd. Klanten moeten de instellingen voor dynamische gegevens maskering aanpassen om te voldoen aan hun database schema.

### <a name="business-continuity"></a>Bedrijfscontinuïteit
**Hoge beschikbaarheid**: Server werkbelastingen worden gegroepeerd in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) om te zorgen voor een hoge Beschik baarheid van virtuele machines in Azure. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

**Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van Azure virtual machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een IaaS-VM zonder de hele virtuele machine te herstellen, waardoor de herstel tijd sneller verloopt.

### <a name="logging-and-audit"></a>Logboek registratie en controle
[Azure monitor logboeken](../azure-security-disk-encryption-overview.md) biedt uitgebreide logboek registratie van systeem-en gebruikers activiteiten, evenals de systeem status. De oplossing voor de [Azure monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) verzamelt en analyseert gegevens die zijn gegenereerd door resources in Azure-en on-premises omgevingen.
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken en Azure Blob-opslag, tabellen en wachtrij Logboeken.
- **Firewall logboeken**: De Application Gateway biedt volledige diagnostische en toegangs logboeken. Er zijn firewall-logboeken beschikbaar voor WAF Application Gateway-resources.
- **Archivering van Logboeken**: Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering met een gedefinieerde Bewaar periode van 2 dagen. Deze logboeken maken verbinding met Azure Monitor logboeken voor verwerking, opslag en dashboard rapportage.

Daarnaast zijn de volgende bewakings oplossingen opgenomen als onderdeel van deze architectuur:
-   [Ad-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Anitmalware-evaluatie](../../security-center/security-center-install-endpoint-protection.md): De antimalware-oplossing rapporteert over malware, bedreigingen en de beveiligings status.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Met de Azure Automation oplossing worden runbooks opgeslagen, uitgevoerd en beheerd.
-   [Beveiliging en audit](../../security-center/security-center-intro.md): Het Beveiliging en audit-dash board biedt een hoog niveau inzicht in de beveiligings status van bronnen door metrische gegevens op te geven over beveiligings domeinen, belang rijke problemen, detecties, bedreigings informatie en algemene beveiligings query's.
-   [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Updatebeheer](../../automation/automation-update-management.md): Met de Updatebeheer oplossing kan het klant beheer van beveiligings updates van besturings systemen worden uitgevoerd, inclusief de status van beschik bare updates en het proces voor het installeren van vereiste updates.
-   [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Azure-activiteiten logboeken](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.
-   [Wijzigingen bijhouden](../../azure-monitor/platform/collect-activity-logs.md): Met de Wijzigingen bijhouden oplossing kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor identiteits beheer in de Azure-omgeving:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) kan de multi tenant-Cloud Directory en identiteits beheer service van micro soft zijn. Alle gebruikers voor de oplossing zijn gemaakt in Azure Active Directory, met inbegrip van gebruikers die toegang hebben tot de SQL Database.
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure AD om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die invloed hebben op de identiteiten van een organisatie, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoeken verdachte incidenten om deze problemen op te lossen.
-   [Met Access Control op basis van rollen (RBAC) van Azure](../../role-based-access-control/role-assignments-portal.md) kunt u het toegangs beheer voor Azure richten. Abonnements toegang is beperkt tot de abonnements beheerder.

Voor meer informatie over het gebruik van de beveiligings functies van Azure SQL Database raadpleegt u de voorbeeld [toepassing contoso Clinic demo](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt.

**Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor virtual machines biedt realtime beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen, met Configureer bare waarschuwingen wanneer bekende of ongewenste software probeert om op beveiligde virtuele machines installeren of uitvoeren.

**Patch beheer**: Virtuele Windows-machines die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om virtuele machines te patchen wanneer dat nodig is.


## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="expressroute-and-vpn"></a>ExpressRoute en VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of een beveiligde VPN-tunnel moet worden geconfigureerd om een veilige verbinding te maken met de resources die zijn geïmplementeerd als onderdeel van deze data warehouse-referentie architectuur. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Door ExpressRoute of een VPN in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

### <a name="extract-transform-load-etl-process"></a>Proces voor het laden van de loads-trans formatie (ETL)
[Poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan gegevens in Azure SQL Data Warehouse laden zonder dat hiervoor een afzonderlijk ETL-of import programma nodig is. Poly Base biedt toegang tot gegevens via T-SQL-query's. De business intelligence-en analyse stack van micro soft, en hulpprogram ma's van derden die compatibel zijn met SQL Server, kunnen worden gebruikt met poly base.

### <a name="azure-active-directory-setup"></a>Azure Active Directory instellen
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van de toegang tot mede werkers met de omgeving. Een bestaande Windows Server-Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)met Aad worden geïntegreerd. Klanten kunnen de geïmplementeerde Active Directory-infra structuur (domein controllers) ook koppelen aan een bestaande AAD door de geïmplementeerde Active Directory-infra structuur een subdomein van een AAD-forest te maken.

### <a name="additional-services"></a>Extra services
Hoewel deze data warehouse-architectuur niet is bedoeld voor implementatie naar de [Azure-commerciële](https://azure.microsoft.com/overview/what-is-azure/) omgeving, kunnen vergelijk bare doel stellingen worden bereikt via de services die in deze referentie architectuur worden beschreven, evenals de extra services die beschikbaar zijn alleen in de bedrijfs omgeving van Azure. Houd er rekening mee dat Azure Commercial een FedRAMP JAB P-ATO onderhoudt op het niveau van de gemiddelde impact, waardoor overheids instellingen en partners tamelijk gevoelige informatie kunnen implementeren in de Cloud met behulp van de Azure-commerciële omgeving.

Azure Commercial biedt een breed scala aan services voor het verwerken van geformatteerde en niet-opgemaakte gegevens opslag en fase ring om te worden gebruikt in gegevens opslag, waaronder:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) is een beheerde Cloud service die is gebouwd voor complexe Hybrid extract-Transform-loads (ETL), uitpak-load-trans formatie (ELT) en gegevens integratie projecten. Klanten kunnen met behulp van Azure Data Factory gegevensgestuurde werk stromen maken en plannen die gegevens uit verschillende gegevens archieven opnemen. Klanten kunnen vervolgens de gegevens verwerken en transformeren voor uitvoer naar gegevens archieven zoals Azure SQL Data Warehouse.
-   [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) maakt het vastleggen van gegevens van elke grootte, type en opname snelheid mogelijk op één plek voor operationele en experimentele analyses. Azure Data Lake Store is compatibel met de meeste open source-onderdelen in het Hadoop-ecosysteem en integreert mooi met andere Azure-Services, zoals Azure SQL Data Warehouse.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram (GSD) voor deze referentie architectuur is beschikbaar om te worden [gedownload](https://aka.ms/blueprintdwthreatmodel) . Hieronder vindt u een voor waarde:

![Data Warehouse voor FedRAMP Threat model](images/fedramp-datawarehouse-threat-model.png?raw=true "Data Warehouse voor FedRAMP Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving
De [FedRAMP-matrix voor hoge klant verantwoordelijkheden bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/blueprinthighcrm) een lijst met alle beveiligings controles die vereist zijn voor de hoge basis lijn van FedRAMP. Op dezelfde manier worden in [Azure-blauwdruk voor beveiliging en naleving de FedRAMP-matrix met gematigde gebruikers verantwoordelijkheden](https://aka.ms/blueprintcrmmod) alle beveiligings controles vermeld die vereist zijn voor de basis lijn FedRAMP. In beide documenten wordt weer gegeven of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of gedeeld door de twee.

De [FedRAMP-implementatie matrix voor hoge controle](https://aka.ms/blueprintdwcimhigh) van de Azure-blauwdruk voor beveiliging en naleving en de door de [Azure-blauwdruk voor beveiliging en naleving FedRAMP gematigde controle-implementatie matrix](https://aka.ms/blueprintdwcimmod) bieden informatie over de besturings elementen die worden gedekt door de Data Warehouse-architectuur voor elke FedRAMP-basis lijn, inclusief gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
