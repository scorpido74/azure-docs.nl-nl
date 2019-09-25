---
title: Azure-blauwdruk voor beveiliging en naleving-Analytics voor FedRAMP
description: Azure-blauwdruk voor beveiliging en naleving-Analytics voor FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 9850c5f064815315db6f85a931e7e175d605dcc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257597"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure-blauwdruk voor beveiliging en naleving: Analytics voor FedRAMP

## <a name="overview"></a>Overzicht

De [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) is een Verenigde Staten overheids programma dat een gestandaardiseerde benadering biedt voor de beveiligings beoordeling, de autorisatie en de voortdurende bewaking van Cloud producten en-services. Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen voor het leveren van een Microsoft Azure Analytics-architectuur die helpt bij het implementeren van een subset van FedRAMP High Controls. Deze oplossing biedt richt lijnen voor de implementatie en configuratie van Azure-resources voor een algemene referentie architectuur, waarbij u kunt zien hoe klanten aan specifieke vereisten voor beveiliging en naleving kunnen voldoen en als basis voor klanten worden gebruikt om bouw en configureer hun eigen analyse oplossingen in Azure.

Deze referentie architectuur, de bijbehorende beheer implementatie handleidingen en bedreigings modellen zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mogen niet worden gebruikt als in een productie omgeving. Het implementeren van een toepassing in deze omgeving zonder aanpassing is niet voldoende om volledig te voldoen aan de vereisten van de FedRAMP hoge basis lijn. Houd rekening met het volgende:
- De architectuur biedt een basis lijn om klanten te helpen bij het implementeren van werk belastingen naar Azure op een FedRAMP.
- Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Deze oplossing biedt een analyse platform waarop klanten hun eigen analyse hulpprogramma's kunnen bouwen. In de referentie architectuur wordt een algemene use-case beschreven waarbij klanten gegevens invoeren via bulksgewijze gegevens import door de SQL/gegevens beheerder of door middel van operationele gegevens updates via een operationele gebruiker. Beide werk stromen bevatten [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) voor het importeren van gegevens in de SQL database. Azure Functions moet door de klant worden geconfigureerd via de Azure-Portal om de import taken te kunnen afhandelen die uniek zijn voor de eigen analyse vereisten van elke klant.

Microsoft Azure biedt een aantal rapportage-en analyse Services voor de klant. deze oplossing bevat echter Azure Analysis Services in combi natie met Azure SQL Database om snel door gegevens te bladeren en sneller resultaten te leveren via een slimmere model lering van klant gegevens. Azure Analytics Services is een vorm van machine learning die is bedoeld om de query snelheden te verhogen door nieuwe relaties tussen gegevens sets te detecteren. Zodra de gegevens zijn getraind met verschillende statistische functies, kunnen Maxi maal zeven extra query Pools (8 met inbegrip van de klant server) worden gesynchroniseerd met dezelfde tabellaire modellen om de werk belasting van query's te spreiden en de reactie tijden te verminderen.

SQL-data bases kunnen worden geconfigureerd met column Store-indexen voor uitgebreide analyse en rapportage. Zowel Azure Analytics Services als SQL-data bases kunnen omhoog of omlaag worden geschaald of volledig worden afgesloten in reactie op het gebruik van de klant. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice, raadt Azure het gebruik aan van een vertrouwde certificerings instantie voor verbeterde beveiliging.

Zodra de gegevens zijn geüpload naar de Azure SQL Database en getraind door Azure Analysis Services, wordt deze door de operationele gebruiker en SQL/data-beheerder met Power BI gedigesteerd. Met Power BI worden gegevens intuïtief weer gegeven en worden gegevens in meerdere gegevens sets verzameld om meer inzicht te krijgen. Dankzij de hoge mate van aanpassing en eenvoudige integratie met Azure SQL Database zorgt u ervoor dat klanten deze kunnen configureren voor het afhandelen van een breed scala aan scenario's zoals vereist door hun bedrijfs behoeften.

De volledige oplossing is gebaseerd op een Azure Storage welke account klanten configureren via Azure Portal. Azure Storage versleutelt alle gegevens met Storage Service Encryption om de vertrouwelijkheid van de gegevens in rust te behouden.  Geografisch redundante opslag (GRS) zorgt ervoor dat een nadelige gebeurtenis op het primaire Data Center van de klant geen verlies van gegevens oplevert als een tweede kopie op een afzonderlijke locatie honderden mijlen wordt opgeslagen.

Voor een betere beveiliging beheert deze architectuur bronnen met Azure Active Directory en Azure Key Vault. De systeem status wordt bewaakt via Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board.

Azure SQL Database wordt meestal beheerd via SQL Server Management Studio (SSMS), dat wordt uitgevoerd vanaf een lokale computer die is geconfigureerd voor toegang tot de Azure SQL Database via een beveiligde VPN-of ExpressRoute-verbinding. **U wordt aangeraden een VPN-of Azure ExpressRoute-verbinding te configureren voor beheer en het importeren van gegevens in de resource groep voor de referentie architectuur.**

![Analyse diagram voor FedRAMP-referentie architectuur](images/fedramp-analytics-reference-architecture.png?raw=true "Analyse diagram voor FedRAMP-referentie architectuur")

### <a name="roles"></a>Rollen
De analytische blauw druk geeft een scenario met drie algemene gebruikers typen: de operationele gebruiker, de SQL/gegevens beheerder en de systeem technicus. Met Access Control op basis van rollen (RBAC) van Azure kunt u de implementatie van nauw keurig toegangs beheer via ingebouwde aangepaste rollen. Er zijn resources beschikbaar voor het configureren van [op rollen gebaseerde Access Control](../../role-based-access-control/role-assignments-portal.md) en het maken van Overzichten en het implementeren van [vooraf gedefinieerde rollen](../../role-based-access-control/built-in-roles.md).

#### <a name="systems-engineer"></a>Systeem ingenieur
De systeem-Engineer is eigenaar van het abonnement van de klant voor Azure en configureert de implementatie van de oplossing via de Azure-Portal.

#### <a name="sqldata-administrator"></a>SQL/gegevens beheerder
De SQL/gegevens beheerder maakt de functie voor het importeren van bulk gegevens en de functie voor het bijwerken van de operationele gegevens voor het uploaden naar de Azure SQL database. De SQL/gegevens beheerder is niet verantwoordelijk voor het bijwerken van operationele gegevens in de data base, maar kan de gegevens wel bekijken via Power BI.

#### <a name="operational-user"></a>Operationele gebruiker
De operationele gebruiker werkt de gegevens regel matig bij en is eigenaar van de dagelijkse gegevens generatie. De operationele gebruiker interpreteert de resultaten ook via Power BI.

### <a name="azure-services"></a>Azure-services

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (Logboeken)
- Azure Storage
- ExpressRoute/VPN Gateway
- Power BI Dashboard

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de ontwikkelings-en implementatie-elementen.

![alternatieve tekst](images/fedramp-analytics-components.png?raw=true "Diagram analyse voor FedRAMP-onderdelen")

**Azure functions**: [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) zijn oplossingen voor het uitvoeren van kleine stukjes code in de Cloud via de meeste programmeer talen. Functies in deze oplossing worden geïntegreerd met Azure Storage om automatisch klant gegevens in de Cloud op te halen en de integratie met andere Azure-Services te vergemakkelijken. Functies zijn eenvoudig schaalbaar en worden alleen kosten in rekening gebracht wanneer ze worden uitgevoerd.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) biedt bedrijfsgegevens modellering en-integratie met Azure data platform-Services. Azure Analysis Service versnelt het bladeren door enorme hoeveel heden gegevens door gegevens uit meerdere bronnen te combi neren in één gegevens model.

**Power BI**: [Power bi](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) biedt analyse-en rapportage functionaliteit voor klanten die proberen meer inzicht te krijgen in hun gegevens verwerkings inspanningen.

### <a name="networking"></a>Netwerken
**Netwerk beveiligings groepen**: [Nsg's](../../virtual-network/virtual-network-vnet-plan-design-arm.md) worden ingesteld voor het beheren van verkeer dat is gericht op geïmplementeerde bronnen en services. Netwerk beveiligings groepen worden ingesteld op een schema voor weigeren volgens standaard en geven alleen verkeer toe dat is opgenomen in de vooraf geconfigureerde Access Control lijst (ACL).

Voor elk van de Nsg's zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke NSG:
  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
  - [Azure monitor-logboeken](../../azure-monitor/insights/azure-networking-analytics.md) is verbonden met de diagnostische logboeken van de NSG.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Gegevens replicatie** Azure Government heeft twee opties voor [gegevens replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - De standaard instelling voor gegevens replicatie is **geografisch redundante opslag (GRS)** , waarmee klant gegevens asynchroon worden opgeslagen in een apart data centrum buiten de primaire regio. Hiermee wordt het herstel van gegevens in een totale verlies gebeurtenis voor het primaire Data Center gegarandeerd.
 - **Lokaal redundante opslag (LRS)** kan ook worden geconfigureerd via het Azure Storage-account. LRS repliceert gegevens in een opslag schaal eenheid, die wordt gehost in dezelfde regio waarin de klant hun account maakt. Alle gegevens worden gelijktijdig gerepliceerd, om ervoor te zorgen dat er geen back-upgegevens verloren gaan in een primaire opslag eenheid.

**Azure Storage** Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruiken alle services die zijn geïmplementeerd in deze referentie architectuur [Azure Storage](https://azure.microsoft.com/services/storage/), die gegevens opslaat met [Storage service Encryption](../../storage/common/storage-service-encryption.md).

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

### <a name="logging-and-audit"></a>Logboek registratie en controle
[Azure monitor](../../azure-monitor/overview.md) genereert een volledige weer gave van bewakings gegevens, waaronder activiteiten logboeken, metrische gegevens en diagnoses, waarmee klanten een volledig beeld van de systeem status kunnen maken.  
[Azure monitor logboeken](../azure-security-disk-encryption-overview.md) biedt uitgebreide logboek registratie van systeem-en gebruikers activiteiten, evenals de systeem status. Het verzamelt en analyseert gegevens die zijn gegenereerd door resources in Azure-en on-premises omgevingen.
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken en Azure Blob-opslag, tabellen en wachtrij Logboeken.
- **Firewall logboeken**: De Application Gateway biedt volledige diagnostische en toegangs logboeken. Er zijn firewall-logboeken beschikbaar voor WAF Application Gateway-resources.
- **Archivering van Logboeken**: Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering met een gedefinieerde Bewaar periode van 2 dagen. Deze logboeken maken verbinding met Azure Monitor logboeken voor verwerking, opslag en dashboard rapportage.

Daarnaast zijn de volgende bewakings oplossingen opgenomen als onderdeel van deze architectuur:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Met de Azure Automation oplossing worden runbooks opgeslagen, uitgevoerd en beheerd.
-   [Beveiliging en audit](../../security-center/security-center-intro.md): Het Beveiliging en audit-dash board biedt een hoog niveau inzicht in de beveiligings status van bronnen door metrische gegevens op te geven over beveiligings domeinen, belang rijke problemen, detecties, bedreigings informatie en algemene beveiligings query's.
-   [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Azure-activiteiten logboeken](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

### <a name="identity-management"></a>Identiteitsbeheer
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure AD om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die invloed hebben op de identiteiten van een organisatie, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoeken verdachte incidenten om deze problemen op te lossen.
-   [Met Access Control op basis van rollen (RBAC) van Azure](../../role-based-access-control/role-assignments-portal.md) kunt u het toegangs beheer voor Azure richten. Abonnements toegang is beperkt tot de abonnements beheerder.

Voor meer informatie over het gebruik van de beveiligings functies van Azure SQL Database raadpleegt u de voorbeeld [toepassing contoso Clinic demo](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="expressroute-and-vpn"></a>ExpressRoute en VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of een beveiligde VPN-tunnel moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze referentie architectuur voor gegevens analyse. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Door ExpressRoute of een VPN in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

### <a name="azure-active-directory-setup"></a>Azure Active Directory instellen
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van de toegang tot mede werkers met de omgeving. Een bestaande Windows Server-Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)met Aad worden geïntegreerd. Klanten kunnen de geïmplementeerde Active Directory-infra structuur (domein controllers) ook koppelen aan een bestaande AAD door de geïmplementeerde Active Directory-infra structuur een subdomein van een AAD-forest te maken.

### <a name="additional-services"></a>Extra services
#### <a name="iaas---vm-considerations"></a>IaaS-VM-overwegingen
In deze PaaS-oplossing zijn geen Azure IaaS-Vm's opgenomen. Een klant kan een virtuele machine van Azure maken om veel van deze PaaS-services uit te voeren. In dit geval kunnen specifieke functies en services voor bedrijfs continuïteit en Azure Monitor logboeken worden gebruikt:

##### <a name="business-continuity"></a>Bedrijfscontinuïteit
- **Hoge beschikbaarheid**: Server werkbelastingen worden gegroepeerd in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) om te zorgen voor een hoge Beschik baarheid van virtuele machines in Azure. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

- **Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van Azure virtual machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een IaaS-VM zonder de hele virtuele machine te herstellen, waardoor de herstel tijd sneller verloopt.

##### <a name="monitoring-solutions"></a>Bewakingsoplossingen
-   [Ad-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Anitmalware-evaluatie](../../security-center/security-center-install-endpoint-protection.md): De antimalware-oplossing rapporteert over malware, bedreigingen en de beveiligings status.
-   [Updatebeheer](../../automation/automation-update-management.md): Met de Updatebeheer oplossing kan het klant beheer van beveiligings updates van besturings systemen worden uitgevoerd, inclusief de status van beschik bare updates en het proces voor het installeren van vereiste updates.
-   [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Wijzigingen bijhouden](../../automation/change-tracking.md): Met de Wijzigingen bijhouden oplossing kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

##### <a name="security"></a>Beveiliging
- **Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor virtual machines biedt realtime beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen, met Configureer bare waarschuwingen wanneer bekende of ongewenste software probeert om op beveiligde virtuele machines installeren of uitvoeren.
- **Patch beheer**: Virtuele Windows-machines die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om virtuele machines te patchen wanneer dat nodig is.

#### <a name="azure-commercial"></a>Azure-Commercial
Hoewel deze architectuur voor gegevens analyse niet is bedoeld voor implementatie naar de [Azure-commerciële](https://azure.microsoft.com/overview/what-is-azure/) omgeving, kunnen vergelijk bare doel stellingen worden bereikt via de services die worden beschreven in deze referentie architectuur, evenals extra services die beschikbaar zijn alleen in de bedrijfs omgeving van Azure. Houd er rekening mee dat Azure Commercial een FedRAMP JAB P-ATO onderhoudt op het niveau van de gemiddelde impact, waardoor overheids instellingen en partners tamelijk gevoelige informatie kunnen implementeren in de Cloud met behulp van de Azure-commerciële omgeving.

Azure Commercial biedt een breed scala aan analyse Services om inzicht te krijgen in grote hoeveel heden gegevens:
-   [Azure machine learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), een onderdeel van de [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), ontwikkelt een voorspellend analyse model van een of meer gegevens bronnen. Statistische functies worden in verschillende iteraties gebruikt om een effectief model te genereren dat toepassingen zoals Power BI vervolgens kunnen gebruiken.
-   [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) maakt het vastleggen van gegevens van elke grootte, type en opname snelheid mogelijk op één plek voor operationele en experimentele analyses. Azure Data Lake Store is compatibel met de meeste open source-onderdelen in het Hadoop-ecosysteem en integreert mooi met andere Azure-Services.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram (GSD) voor deze referentie architectuur is beschikbaar om te worden [gedownload](https://aka.ms/blueprintanalyticsthreatmodel) . Hieronder vindt u een voor waarde:

## <a name="compliance-documentation"></a>Documentatie voor naleving
De [FedRAMP-matrix voor hoge klant verantwoordelijkheden bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/blueprinthighcrm) een lijst met alle beveiligings controles die vereist zijn voor de hoge basis lijn van FedRAMP. Op dezelfde manier worden in [Azure-blauwdruk voor beveiliging en naleving de FedRAMP-matrix met gematigde gebruikers verantwoordelijkheden](https://aka.ms/blueprintanalyticscimmod) alle beveiligings controles vermeld die vereist zijn voor de basis lijn FedRAMP. In beide documenten wordt weer gegeven of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of gedeeld door de twee.

De [FedRAMP-implementatie matrix voor hoge controle](https://aka.ms/blueprintanalyticscimhigh) van de Azure-blauwdruk voor beveiliging en naleving en de door de [Azure-blauwdruk voor beveiliging en naleving FedRAMP gematigde controle-implementatie matrix](https://aka.ms/blueprintanalyticscimmod) bieden informatie over de besturings elementen die worden gedekt door de analyse architectuur voor elke FedRAMP-basis lijn, inclusief gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
