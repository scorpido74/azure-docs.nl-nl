---
title: Veelgestelde vragen over beheerdere exemplaren
description: VEELgestelde vragen voor SQL Database-beheerde instantie (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364163"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>VEELgestelde vragen voor SQL Database-beheerde instantie (FAQ)

Dit artikel bevat veel van de meest voorkomende vragen over [SQL Database beheerde instantie](sql-database-managed-instance.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund op beheerde instantie?**

Zie Azure SQL Database versus SQL [Server](sql-database-features.md)voor een lijst met ondersteunde functies in beheerde instantie.

Zie [T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md)voor verschillen in syntaxis en gedrag tussen de door Azure SQL Database beheerde instantie en on-premises SQL Server.


## <a name="tech-spec--resource-limits"></a>Technische specificaties & resourcelimieten
 
**Waar vind ik technische kenmerken en resourcelimieten voor beheerde bijvoorbeeld?**

Voor beschikbare hardware generatie kenmerken, zie [technische verschillen in hardware generaties](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Zie [technische verschillen tussen servicelagen](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)voor beschikbare servicelagen en hun kenmerken.

## <a name="known-issues--bugs"></a>Bekende problemen & bugs

**Waar vind ik bekende problemen en bugs?**

Voor bugs en bekende problemen zie [bekende problemen](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nieuwe functies

**Waar vind ik de nieuwste functies en de functies in openbare preview?**

Zie [releasenotes](sql-database-release-notes.md?tabs=managed-instance)voor nieuwe en voorbeeldfuncties.

## <a name="deployment-times"></a>Implementatietijden 

**Hoeveel tijd kost het om een instantie te maken of bij te werken of om een database te herstellen?**

De verwachte tijd om nieuwe beheerde instantie te maken of de servicelaag (vCores, opslag) te wijzigen, is afhankelijk van verschillende factoren. Bekijk de [managementactiviteiten](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Naming

**Kan een beheerde instantie dezelfde naam hebben als on-premises SQL Server?**

Het wijzigen van de naam van beheerde instantie wordt niet ondersteund.

Beheerde instantie standaard DNS-zone *.database.windows.net* kan worden gewijzigd. 

Als u een andere DNS-zone wilt gebruiken in plaats van de standaardinstelling, *contoso.com*bijvoorbeeld : 
- Gebruik CliConfig om een alias te definiëren. De tool is slechts een register instellingen wrapper, dus het kan worden gedaan met behulp van groepsbeleid of script ook.
- Gebruik de optie *CNAME* met *TrustServerCertificate=true.*

## <a name="move-db-from-mi"></a>DB verplaatsen van MI 

**Hoe kan ik de database verplaatsen van beheerde instantie terug naar SQL Server of Azure SQL Database?**

U [de database exporteren naar BACPAC](sql-database-export.md) en vervolgens het [BACPAC-bestand importeren.]( sql-database-import.md) Dit is een aanbevolen aanpak als uw database kleiner is dan 100 GB.

Transactionele replicatie kan worden gebruikt als alle tabellen in de database primaire sleutels hebben.

Native `COPY_ONLY` back-ups uit beheerde instantie kunnen niet worden hersteld naar SQL Server omdat beheerde instantie een hogere databaseversie heeft in vergelijking met SQL Server.

## <a name="migrate-instance-db"></a>Instantie DB migreren

**Hoe kan ik mijn instantiedatabase migreren naar één Azure SQL-database?**

Een optie is om [de database te exporteren naar een BACPAC](sql-database-export.md) en vervolgens het [BACPAC-bestand te importeren.](sql-database-import.md) 

Dit is de aanbevolen aanpak als uw database kleiner is dan 100 GB. Transactionele replicatie kan worden gebruikt als alle tabellen in de database primaire sleutels hebben.

## <a name="switch-hardware-generation"></a>Switch-hardwaregeneratie 

**Kan ik mijn beheerde instantie hardware generatie tussen Gen 4 en Gen 5 online?**

Geautomatiseerd online schakelen tussen hardwaregeneraties is mogelijk als beide hardwaregeneraties beschikbaar zijn in de regio waar uw beheerde instantie is ingericht. In dit geval u [de overzichtspagina van het vCore-model](sql-database-service-tiers-vcore.md) bekijken waarin u uitlegt hoe u schakelen tussen hardwaregeneraties.

Dit is een langlopende bewerking, omdat een nieuw beheerd exemplaar op de achtergrond wordt ingericht en databases automatisch worden overgedragen tussen de oude en de nieuwe instantie met een snelle failover aan het einde van het proces. 

Als beide hardwaregeneraties niet in dezelfde regio worden ondersteund, is het wijzigen van de hardwaregeneratie mogelijk, maar moet handmatig worden uitgevoerd. Dit vereist dat u een nieuw exemplaar indient in de regio waar de gewenste hardwaregeneratie beschikbaar is, en handmatig gegevens back-ups en herstellen tussen het oude en het nieuwe exemplaar.


## <a name="tune-performance"></a>Prestaties afstemmen

**Hoe stem ik de prestaties van mijn beheerde instantie af?**

Algemeen doel beheerde instantie maakt gebruik van externe opslag vanwege welke grootte van gegevens en logbestanden belangrijk is voor de prestaties. Zie [Impact van de bestandsgrootte van het logboek op de prestaties van beheerde instantie voor algemeen gebruik](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)voor meer informatie .

Als uw werkbelasting uit veel kleine transacties bestaat, u overwegen het verbindingstype over te schakelen van proxy naar omleidingsmodus.

## <a name="maximum-storage-size"></a>Maximale opslaggrootte

**Wat is de maximale opslaggrootte voor beheerde bijvoorbeeld?**

De opslaggrootte voor beheerde instantie is afhankelijk van de geselecteerde servicelaag (Algemeen doel of Bedrijfskritiek). Zie [Kenmerk servicelaag](sql-database-service-tiers-general-purpose-business-critical.md)voor opslagbeperkingen van deze servicelagen .

## <a name="back-up-storage-cost"></a>Back-ups van opslagkosten 

**Wordt de back-upopslag afgetrokken van mijn beheerde instantieopslag?**

Nee, back-upopslag wordt niet afgetrokken van de opslagruimte van uw beheerde instantie. De back-upopslag is onafhankelijk van de opslagruimte van de instantie en is niet beperkt in omvang. Back-upopslag wordt beperkt door de periode om de back-up van uw instantiedatabases te behouden, configureerbaar van 7 tot 35 dagen. Zie [Geautomatiseerde back-ups voor](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)meer informatie.

## <a name="track-billing"></a>Facturering bijhouden

**Is er een manier om mijn factureringskosten voor mijn beheerde exemplaar bij te houden?**

U dit doen met de [Azure Cost Management-oplossing.](/azure/cost-management/) Navigeer naar **abonnementen** in de [Azure-portal](https://portal.azure.com) en selecteer **Kostenanalyse**. 

Gebruik de optie **Geaccumuleerde kosten** en `microsoft.sql/managedinstances`filter vervolgens op het type **Resource** als . 
  
## <a name="inbound-nsg-rules"></a>Binnenkomende NSG-regels

**Hoe kan ik inkomende NSG-regels instellen voor beheerpoorten?**

Beheerde instantie controle vliegtuig handhaaft NSG regels die beheerpoorten te beschermen.

Hier is wat beheerpoorten worden gebruikt voor:

Havens 9000 en 9003 worden gebruikt door de Service Fabric-infrastructuur. De primaire rol van servicestructuur is om het virtuele cluster gezond te houden en de doelstatus te behouden in termen van aantal componentreplica's.

Poorten 1438, 1440 en 1452 worden gebruikt door knooppuntagent. Node-agent is een toepassing die binnen het cluster wordt uitgevoerd en wordt gebruikt door het besturingsvlak om beheeropdrachten uit te voeren.

Naast NSG-regels beschermt de ingebouwde firewall de instantie op de netwerklaag. Op de toepassingslaag wordt de communicatie met de certificaten beveiligd.
  
Zie [Azure SQL Database managed instance built-in firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)voor meer informatie en hoe u de ingebouwde firewall verifiëren.


## <a name="mitigate-data-exfiltration-risks"></a>Risico's voor gegevensexfiltratie beperken  

**Hoe kan ik risico's op data-exfiltratie beperken?**

Om eventuele risico's voor gegevensexfiltratie te beperken, wordt klanten aangeraden een reeks beveiligingsinstellingen en -besturingselementen toe te passen:

- Schakel [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in op alle databases.
- Common Language Runtime (CLR) uitschakelen. Dit wordt ook ter plaatse aanbevolen.
- Gebruik alleen Azure Active Directory (AAD)-verificatie.
- Toegang instantie met een laag geprivilegieerd DBA-account.
- Configureer JiT jumpbox access voor sysadmin account.
- Schakel [SQL-auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)in en integreer deze met waarschuwingsmechanismen.
- Schakel de [bedreigingsdetectie](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) in vanuit de [ADS-suite (Advanced Data Security).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Kostenbesparende use cases

**Waar vind ik use cases en daaruit voortvloeiende kostenbesparingen met beheerde instantie?**

Beheerde casestudies voor bijvoorbeeld:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Om een beter inzicht te krijgen in de voordelen, kosten en risico's die verbonden zijn aan het implementeren van azure SQL Database-beheerde instantie, is er ook een onderzoek van Forrester: [Total Economic Impact of MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS-vernieuwing 

**Kan ik DNS vernieuwen?**

Momenteel bieden we geen functie om dns-serverconfiguratie voor beheerde bijvoorbeeld te vernieuwen.

DNS-configuratie wordt uiteindelijk vernieuwd:

- Wanneer DHCP lease verloopt.
- Op platform upgrade.

Als tijdelijke oplossing u de beheerde instantie downgraden naar 4 vCore en daarna opnieuw upgraden. Dit heeft een neveneffect van het vernieuwen van de DNS-configuratie.


## <a name="ip-address"></a>IP-adres

**Kan ik verbinding maken met beheerde instantie via ip-adres?**

Verbinding maken met beheerde instantie via IP-adres wordt niet ondersteund. Beheerde instantiehostnaamkaarten voor het laden van balancer voor het virtuele cluster van beheerde instantie. Aangezien een virtueel cluster meerdere beheerde instanties kan hosten, kan de verbinding niet worden doorgestuurd naar de juiste beheerde instantie zonder de naam op te geven.

Zie [Virtuele clusterconnectiviteitsarchitectuur](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)voor meer informatie over beheerde voorbeeldarchitectuur.

**Kan een beheerde instantie een statisch IP-adres hebben?**

In zeldzame maar noodzakelijke situaties moeten we mogelijk een online migratie van een beheerde instantie naar een nieuw virtueel cluster doen. Indien nodig is deze migratie het gevolg van veranderingen in onze technologiestack die gericht zijn op het verbeteren van de veiligheid en betrouwbaarheid van de service. Als u migreert naar een nieuw virtueel cluster, wordt het IP-adres gewijzigd dat is toegewezen aan de hostnaam van de beheerde instantie. De beheerde instantieservice claimt geen statische IP-adresondersteuning en behoudt zich het recht voor om deze zonder kennisgeving te wijzigen als onderdeel van reguliere onderhoudscycli.

Om deze reden raden we ten zeerste af te vertrouwen op onveranderlijkheid van het IP-adres, omdat het onnodige downtime kan veroorzaken.

## <a name="change-time-zone"></a>Tijdzone wijzigen

**Kan ik de tijdzone voor een bestaand beheerd exemplaar wijzigen?**

Tijdzoneconfiguratie kan worden ingesteld wanneer een beheerde instantie voor de eerste keer is ingericht. Het wijzigen van de tijdzone van de bestaande beheerde instantie wordt niet ondersteund. Zie [tijdzonebeperkingen](sql-database-managed-instance-timezone.md#limitations)voor meer informatie .

Tijdelijke oplossingen omvatten het maken van een nieuw beheerde instantie met de juiste tijdzone en vervolgens het uitvoeren van een handmatige back-up en herstel, of wat we aanbevelen, het uitvoeren van een [cross-instance point-in-time herstellen](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Prestatieproblemen oplossen

**Hoe los ik prestatieproblemen op met mijn beheerde instantie?**

Voor een prestatievergelijking tussen beheerde instantie en SQL Server is een goed uitgangspunt [Best practices voor prestatievergelijking tussen Azure SQL managed instance en SQL](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) Server-artikel.

Het laden van gegevens is vaak trager op beheerde instantie dan in SQL Server vanwege het verplichte volledige herstelmodel en [beperkingen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) op de doorvoer van transactielogboekschrijftransacties. Soms kan dit worden verwerkt door tijdelijke gegevens in tempdb te laden in plaats van in de gebruikersdatabase, of door geclusterde kolomarchief of geheugengeoptimaliseerde tabellen te gebruiken.


## <a name="restore-encrypted-backup"></a>Versleutelde back-up herstellen

**Kan ik mijn versleutelde database herstellen naar beheerde instantie?**

Ja, u hoeft uw database niet te decoderen om deze te kunnen herstellen naar beheerde instantie. U moet een certificaat/sleutel die wordt gebruikt als een encryptiesleutelbeschermer in het bronsysteem aan de beheerde instantie verstrekken om gegevens uit het versleutelde back-upbestand te kunnen lezen. Er zijn twee mogelijke manieren om het te doen:

- *Upload certificaatbeschermer naar de beheerde instantie*. Het kan alleen met PowerShell worden gedaan. Het [voorbeeldscript](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) beschrijft het hele proces.
- *Upload asymmetrische key-protector naar Azure Key Vault (AKV) en punt beheerde instantie aan.* Deze aanpak lijkt op bring-your-own-key (BYOK) TDE use case die ook akv-integratie gebruikt om de encryptiesleutel op te slaan. Als u de sleutel niet wilt gebruiken als een encryptiesleutelbeschermer en alleen de sleutel beschikbaar wilt stellen voor beheerde bijvoorbeeld om versleutelde database(s) te herstellen, instructies voor [het instellen van BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)op te volgen en het selectievakje niet aan te vinken *Maak de geselecteerde sleutel de standaard TDE-beveiliging*.

Zodra u de versleutelingsbeveiliging beschikbaar stelt aan beheerde instantie, u doorgaan met de standaardprocedure voor het herstellen van de database.

## <a name="migrate-from-single-db"></a>Migreren van enkele DB 

**Hoe kan ik migreren van Azure SQL Database single of elastic pool naar beheerde instantie?**

Beheerde instantie biedt dezelfde prestatieniveaus per reken- en opslaggrootte als andere implementatieopties van Azure SQL Database. Als u gegevens wilt consolideren op één exemplaar of als u gewoon een functie nodig hebt die uitsluitend in beheerde instantie wordt ondersteund, u uw gegevens migreren met behulp van bacpac-functionaliteit (export/import).
