---
title: Veelgestelde vragen over beheerdere exemplaren
description: Veelgestelde vragen over SQL Database beheerde exemplaren
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 3ffa4bc905a08c1757865db7bab828193ff3c7ea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770139"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over SQL Database beheerde exemplaren

Dit artikel bevat veel van de meest voorkomende vragen over [SQL database beheerde instantie](sql-database-managed-instance.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund in een beheerd exemplaar?**

Zie [Azure SQL database versus SQL Server](sql-database-features.md)voor een lijst met ondersteunde functies in het beheerde exemplaar.

Zie voor verschillen in de syntaxis en het gedrag tussen Azure SQL Database beheerde instantie en on-premises SQL Server [T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Technische specificaties & resource limieten
 
**Waar vind ik technische kenmerken en resource limieten voor een beheerd exemplaar?**

Zie [technische verschillen in hardware generaties](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)voor de beschik bare kenmerken voor het genereren van hardware.
Zie [technische verschillen tussen service lagen](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)voor de beschik bare service lagen en hun kenmerken.

## <a name="known-issues--bugs"></a>Bekende problemen & bugs

**Waar vind ik bekende problemen en bugs?**

Zie [bekende problemen](sql-database-release-notes.md#known-issues)voor bugs en bekende problemen.

## <a name="new-features"></a>Nieuwe functies

**Waar vind ik de nieuwste functies en de functies van de open bare preview?**

Zie [release opmerkingen](sql-database-release-notes.md?tabs=managed-instance)voor nieuwe en preview-functies.

## <a name="deployment-times"></a>Implementatie tijden 

**Hoe lang duurt het om een exemplaar te maken of bij te werken of om een Data Base te herstellen?**

De verwachte tijd voor het maken van een nieuw beheerd exemplaar of het wijzigen van de servicelaag (vCores, opslag) is afhankelijk van verschillende factoren. Bekijk de [beheer bewerkingen](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Naamgevings Conventie

**Kan een beheerd exemplaar dezelfde naam hebben als de on-premises SQL Server?**

Het wijzigen van de naam van een beheerd exemplaar wordt niet ondersteund.

Standaard-DNS-zone van het beheerde exemplaar *. database.Windows.net* kan worden gewijzigd. 

Als u een andere DNS-zone wilt gebruiken in plaats van de standaard instelling, bijvoorbeeld *. contoso.com*: 
- Gebruik CliConfig voor het definiëren van een alias. Het hulp programma is slechts een wrapper voor register instellingen en kan daarom ook worden uitgevoerd met groeps beleid of script.
- Gebruik *CNAME* met de optie *TrustServerCertificate = True* .

## <a name="move-db-from-mi"></a>Data base verplaatsen van MI 

**Hoe kan ik data base van een beheerd exemplaar terugplaatsen naar SQL Server of Azure SQL Database?**

U kunt [Data Base exporteren naar BACPAC](sql-database-export.md) en vervolgens [het BACPAC-bestand importeren]( sql-database-import.md). Dit is een aanbevolen benadering als uw data base kleiner is dan 100 GB.

Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

Systeem eigen `COPY_ONLY` back-ups die zijn gemaakt van het beheerde exemplaar, kunnen niet worden hersteld naar SQL Server omdat het beheerde exemplaar een hogere database versie heeft dan SQL Server.

## <a name="migrate-instance-db"></a>Instantie-data base migreren

**Hoe kan ik mijn exemplaar database migreren naar een enkele Azure SQL Database?**

U kunt [de data base ook exporteren naar een BACPAC](sql-database-export.md) en vervolgens [het BACPAC-bestand importeren](sql-database-import.md). 

Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB. Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

## <a name="switch-hardware-generation"></a>Hardware-generatie scha kelen 

**Kan ik de hardware-generatie van beheerde exemplaren tussen gen 4 en Gen 5 online veranderen?**

Geautomatiseerde online switches tussen de hardware is mogelijk als beide generaties beschikbaar zijn in de regio waar uw beheerde exemplaar is ingericht. In dit geval kunt u de [overzichts pagina](sql-database-service-tiers-vcore.md) van het vCore-model controleren, waarin wordt uitgelegd hoe u tussen de hardware gegenereerd.

Dit is een langlopende bewerking als een nieuw beheerd exemplaar wordt ingericht op de achtergrond en data bases worden automatisch overgebracht tussen het oude en het nieuwe exemplaar met een snelle failover aan het einde van het proces. 

**Wat gebeurt er als beide hardware gegenereerd worden in dezelfde regio?**

Als beide generaties niet in dezelfde regio worden ondersteund, is het wijzigen van de hardware mogelijk, maar moet u deze hand matig uitvoeren. Hiervoor moet u een nieuw exemplaar inrichten in de regio waar de gewenste hardware-generatie beschikbaar is, en hand matig back-ups maken en gegevens herstellen tussen het oude en het nieuwe exemplaar.

**Wat gebeurt er als er geen IP-adressen zijn voor het uitvoeren van een update bewerking?**

Als er geen IP-adressen zijn in het subnet waar uw beheerde exemplaar is ingericht, moet u een nieuw subnet en een nieuw beheerd exemplaar maken. We raden ook aan dat er een nieuw subnet wordt gemaakt met meer IP-adressen alocated zodat toekomstige update bewerkingen een vergelijk bare situatie kunnen voor komen (voor propper-subnet grootte, de [grootte van het vnet-subnet bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md). Nadat het nieuwe exemplaar is ingericht, kunt u hand matig back-ups maken van gegevens en deze herstellen tussen het oude en het nieuwe exemplaar of een [herstel punt voor meerdere tijdstippen](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell)uitvoeren. 


## <a name="tune-performance"></a>Prestaties afstemmen

**De prestaties van mijn beheerde exemplaar Hoe kan ik afstemmen?**

Algemeen beheerde instantie gebruikt externe opslag, omdat de grootte van de gegevens en logboek bestanden op de prestaties van belang is. Zie [impact van de grootte van het logboek bestand op algemeen prestaties van het beheerde exemplaar](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)voor meer informatie.

Als uw werk belasting uit veel kleine trans acties bestaat, kunt u overwegen om het verbindings type van de proxy naar de omleidings modus te scha kelen.

## <a name="maximum-storage-size"></a>Maximale opslag grootte

**Wat is de maximale opslag grootte voor het beheerde exemplaar?**

De opslag grootte voor het beheerde exemplaar is afhankelijk van de geselecteerde servicelaag (Algemeen of Bedrijfskritiek). Zie [kenmerk](sql-database-service-tiers-general-purpose-business-critical.md)van de servicelaag voor opslag beperkingen van deze service lagen.

## <a name="back-up-storage-cost"></a>Back-ups van opslag kosten 

**Wordt de back-upopslag afgetrokken van mijn beheerde exemplaar opslag?**

Nee, back-upopslag wordt niet afgetrokken van de opslag ruimte voor uw beheerde exemplaar. De back-upopslag is onafhankelijk van de opslag ruimte van het exemplaar en is niet beperkt. Back-upopslag wordt beperkt door de tijds periode voor het bewaren van de back-up van uw exemplaar databases, te configureren van 7 tot 35 dagen. Zie [automatische back-ups](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)voor meer informatie.

## <a name="track-billing"></a>Facturering bijhouden

**Is er een manier om mijn facturerings kosten voor mijn beheerde exemplaar bij te houden?**

U kunt dit doen met behulp van de [Azure Cost Management-oplossing](/azure/cost-management/). Navigeer naar **abonnementen** in het [Azure Portal](https://portal.azure.com) en selecteer **kosten analyse**. 

Gebruik de optie **geaccumuleerde kosten** en filter vervolgens op het **resource type** als `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Inkomende NSG-regels

**Hoe kan ik binnenkomende NSG-regels instellen voor beheer poorten?**

Beheer van beheerde exemplaren beheert NSG-regels die beheer poorten beveiligen.

Hier ziet u welke beheer poorten worden gebruikt voor:

Poorten 9000 en 9003 worden gebruikt door Service Fabric-infra structuur. Service Fabric primaire rol moet het virtuele cluster in orde blijven en de doel status in termen van het aantal onderdeel replica's blijven.

Poorten 1438, 1440 en 1452 worden gebruikt door de knooppunt agent. Knoop punt-agent is een toepassing die in het cluster wordt uitgevoerd en die wordt gebruikt door het besturings vlak om beheer opdrachten uit te voeren.

Naast de NSG-regels wordt het exemplaar in de netwerklaag beveiligd door de ingebouwde firewall. Op de Application Layer-communicatie wordt beveiligd met de certificaten.
  
Zie [Azure SQL database Managed instance built-in Firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)voor meer informatie over het controleren van de ingebouwde firewall.


## <a name="mitigate-data-exfiltration-risks"></a>Gegevens exfiltration Risico's beperken  

**Hoe kan ik de gegevens exfiltration Risico's beperken?**

Klanten wordt aangeraden een aantal beveiligings instellingen en-besturings elementen toe te passen om eventuele gegevens exfiltration Risico's te beperken:

- Schakel [transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in voor alle data bases.
- Common language runtime (CLR) uitschakelen. Dit wordt ook aanbevolen voor on-premises.
- Alleen Azure Active Directory (AAD)-verificatie gebruiken.
- Access-exemplaar met een account met beperkte bevoegdheden.
- Configureer de JiT JumpBox-toegang voor het sysadmin-account.
- Schakel [SQL auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)in en integreer deze met waarschuwings mechanismen.
- Schakel de [detectie van bedreigingen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) in het pakket [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) in.


## <a name="cost-saving-use-cases"></a>Gebruiks voorbeelden besparen

**Waar vind ik use cases en resulterende kosten besparingen met een beheerd exemplaar?**

Case-study's voor beheerde instanties:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Om een beter inzicht te krijgen in de voor delen, kosten en risico's die zijn gekoppeld aan het implementeren van Azure SQL Database beheerde instantie, is er ook een Forrester studie: [totale economische impact van Mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS vernieuwen 

**Kan ik DNS vernieuwen?**

Momenteel bieden we geen functie voor het vernieuwen van de DNS-server configuratie voor een beheerd exemplaar.

De DNS-configuratie wordt uiteindelijk vernieuwd:

- Wanneer de DHCP-lease verloopt.
- Op platform upgrade.

Als tijdelijke oplossing kunt u het beheerde exemplaar downgradeen naar 4 vCore en het later opnieuw bijwerken. Dit heeft een neven effect van het vernieuwen van de DNS-configuratie.


## <a name="ip-address"></a>IP-adres

**Kan ik verbinding maken met een beheerd exemplaar met behulp van een IP-adres?**

Het is niet mogelijk om verbinding te maken met een beheerd exemplaar met een IP-adres. De hostnaam van het beheerde exemplaar wordt toegewezen aan load balancer voor het virtuele cluster van het beheerde exemplaar. Als één virtueel cluster kan hosten voor meerdere beheerde exemplaren kan de verbinding niet worden gerouteerd naar de juiste beheerde instantie zonder de naam op te geven.

Zie [connectiviteits architectuur voor virtuele clusters](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)voor meer informatie over de architectuur van een beheerd exemplaar van een virtueel cluster.

**Kan een beheerd exemplaar een statisch IP-adres hebben?**

In zeldzame maar nood zakelijke situaties moet u mogelijk een online migratie van een beheerd exemplaar uitvoeren naar een nieuw virtueel cluster. Als dat nodig is, wordt deze migratie veroorzaakt door wijzigingen in onze technologie stack, die gericht is op het verbeteren van de beveiliging en betrouw baarheid van de service. Migreren naar een nieuw virtueel cluster resulteert in het wijzigen van het IP-adres dat is toegewezen aan de hostnaam van het beheerde exemplaar. De service Managed instance claimt geen ondersteuning voor statische IP-adressen en behoudt zich het recht voor om deze te wijzigen zonder kennisgeving als onderdeel van normale onderhouds cycli.

Daarom raden wij u ten zeerste aan om te vertrouwen op Onveranderbaarheid van het IP-adres, omdat dit onnodig downtime kan veroorzaken.

## <a name="change-time-zone"></a>Tijd zone wijzigen

**Kan ik de tijd zone wijzigen voor een bestaande beheerde instantie?**

De configuratie van de tijd zone kan worden ingesteld wanneer een beheerd exemplaar voor de eerste keer wordt ingericht. Het wijzigen van de tijd zone van het bestaande beheerde exemplaar wordt niet ondersteund. Zie [beperkingen voor tijd zones](sql-database-managed-instance-timezone.md#limitations)voor meer informatie.

Tijdelijke oplossingen zijn onder andere het maken van een nieuw beheerd exemplaar met de juiste tijd zone en vervolgens een hand matige back-up maken en herstellen, of wat wij adviseren, het [herstellen van een exemplaar naar een ander tijdstip](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)uitvoeren.


## <a name="resolve-performance-issues"></a>Prestatie problemen oplossen

**Prestatie problemen Hoe kan ik oplossen met mijn beheerde exemplaar?**

Voor een vergelijking van de prestaties tussen het beheerde exemplaar en SQL Server, is een goed uitgangs punt [Aanbevolen procedures voor de prestatie vergelijking tussen Azure SQL Managed instance en SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) artikel.

Het laden van gegevens is vaak langzamer op het beheerde exemplaar dan in SQL Server vanwege een verplicht volledig herstel model en [limieten](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) voor schrijf doorvoer in transactie logboek. Dit kan soms worden omzeild door het laden van tijdelijke gegevens in TempDB in plaats van de gebruikers database, of met behulp van geclusterde column Store of tabellen die zijn geoptimaliseerd voor geheugen.


## <a name="restore-encrypted-backup"></a>Versleutelde back-up herstellen

**Kan ik mijn versleutelde data base herstellen naar een beheerd exemplaar?**

Ja, u hoeft uw data base niet te ontsleutelen zodat deze kan worden hersteld naar een beheerd exemplaar. U moet een certificaat/sleutel opgeven die wordt gebruikt als een versleutelings sleutel beveiliging in het bron systeem naar het beheerde exemplaar om gegevens te kunnen lezen uit het versleutelde back-upbestand. Er zijn twee manieren waarop u dit kunt doen:

- *Upload certificaat beveiliging naar het beheerde exemplaar*. Dit kan alleen worden gedaan met behulp van Power shell. In het [voorbeeld script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) wordt het hele proces beschreven.
- De *asymmetrische sleutel beveiliging uploaden naar Azure Key Vault (Azure) en het beheerde exemplaar*. Deze benadering lijkt op het gebruik van de BYOK-TDE-use-case (uw eigen sleutel), die ook gebruikmaakt van Azure-integratie om de versleutelings sleutel op te slaan. Als u de sleutel niet wilt gebruiken als een versleutelings sleutel beveiliging en alleen de sleutel beschikbaar wilt maken voor een beheerd exemplaar om versleutelde data bases te herstellen, volgt u de instructies voor het [instellen van BYOK TDe](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)en schakelt u het selectie vakje de *geselecteerde sleutel de standaard TDe-Protector maken*in.

Wanneer u de versleutelings beveiliging hebt gemaakt voor een beheerd exemplaar, kunt u door gaan met de standaard procedure voor het herstellen van data bases.

## <a name="migrate-from-single-db"></a>Migreren van één data base 

**Hoe kan ik migreren van Azure SQL Database enkele of elastische pool naar een beheerd exemplaar?**

Beheerde instantie biedt dezelfde prestatie niveaus per Compute en opslag grootte als andere implementatie opties van Azure SQL Database. Als u gegevens wilt consolideren voor één exemplaar of als u alleen een functie nodig hebt die uitsluitend wordt ondersteund in het beheerde exemplaar, kunt u uw gegevens migreren met behulp van de functionaliteit voor exporteren/importeren (BACPAC).
