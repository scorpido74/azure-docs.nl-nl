---
title: Veelgestelde vragen (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Veelgestelde vragen over Azure SQL SQL Managed instance
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: c1a7f22314af472037194150b78e881395c14c2e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117386"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure SQL Managed instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat de meest voorkomende vragen over [Azure SQL Managed instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund in een SQL Managed instance?**

Zie [functies van Azure SQL Managed instance](../database/features-comparison.md)voor een lijst met ondersteunde functies in SQL Managed instance.

Zie voor verschillen in de syntaxis en het gedrag tussen Azure SQL Managed instance en on-premises SQL Server [T-SQL-verschillen van SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Technische specificaties & resource limieten
 
**Waar vind ik technische kenmerken en resource limieten voor SQL Managed instance?**

Zie [technische verschillen in hardware generaties](resource-limits.md#hardware-generation-characteristics)voor de beschik bare kenmerken voor het genereren van hardware.
Zie [technische verschillen tussen service lagen](resource-limits.md#service-tier-characteristics)voor de beschik bare service lagen en hun kenmerken.

## <a name="known-issues--bugs"></a>Bekende problemen & bugs

**Waar vind ik bekende problemen en bugs?**

Zie [bekende problemen](../database/doc-changes-updates-release-notes.md#known-issues)voor bugs en bekende problemen.

## <a name="new-features"></a>Nieuwe functies

**Waar vind ik de nieuwste functies en de functies van de open bare preview?**

Zie [release opmerkingen](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)voor nieuwe en preview-functies.

## <a name="deployment-times"></a>Implementatie tijden 

**Hoe lang duurt het om een exemplaar te maken of bij te werken of om een Data Base te herstellen?**

De verwachte tijd voor het maken van een SQL Managed instance of het wijzigen van de servicelaag (vCores, Storage) is afhankelijk van verschillende factoren. Bekijk de [beheer bewerkingen](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Naamgevings Conventie

**Kan een SQL Managed instance dezelfde naam hebben als de on-premises SQL Server?**

Het wijzigen van de naam van een SQL-beheerde exemplaar wordt niet ondersteund.

De standaard DNS-zone van het SQL-beheerde exemplaar *. database.Windows.net* kan worden gewijzigd. 

Als u een andere DNS-zone wilt gebruiken in plaats van de standaard instelling, bijvoorbeeld *. contoso.com*: 
- Gebruik CliConfig voor het definiëren van een alias. Het hulp programma is slechts een wrapper voor register instellingen en kan daarom ook worden uitgevoerd met groeps beleid of script.
- Gebruik *CNAME* met de optie *TrustServerCertificate = True* .

## <a name="move-db-from-mi"></a>Data base verplaatsen van MI 

**Hoe kan ik data base verplaatsen van een SQL Managed instance naar SQL Server of Azure SQL Database?**

U kunt [Data Base exporteren naar BACPAC](../database/database-export.md) en vervolgens [het BACPAC-bestand importeren]( ../database/database-import.md). Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB.

Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

Systeem eigen `COPY_ONLY` back-ups die zijn gemaakt op basis van een SQL-beheerd exemplaar, kunnen niet worden hersteld naar SQL Server omdat SQL Managed instance een hogere database versie heeft dan SQL Server.

## <a name="migrate-instance-db"></a>Instantie-data base migreren

**Hoe kan ik mijn exemplaar database migreren naar een enkele Azure SQL Database?**

U kunt [de data base ook exporteren naar een BACPAC](../database/database-export.md) en vervolgens [het BACPAC-bestand importeren](../database/database-import.md). 

Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB. Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

## <a name="switch-hardware-generation"></a>Hardware-generatie scha kelen 

**Kan ik de generatie van de hardware van SQL Managed instance tussen gen 4 en Gen 5 online veranderen?**

De geautomatiseerde online switch tussen de generaties van de hardware is mogelijk als beide generaties beschikbaar zijn in de regio waar uw door SQL beheerde exemplaar is ingericht. In dit geval kunt u de [overzichts pagina](../database/service-tiers-vcore.md) van het vCore-model controleren, waarin wordt uitgelegd hoe u tussen de hardware gegenereerd.

Dit is een langlopende bewerking als een nieuw, door SQL beheerd exemplaar wordt ingericht op de achtergrond en data bases worden automatisch overgebracht tussen het oude en het nieuwe exemplaar met een snelle failover aan het einde van het proces. 

**Wat gebeurt er als beide hardware gegenereerd worden in dezelfde regio?**

Als beide generaties niet in dezelfde regio worden ondersteund, is het wijzigen van de hardware mogelijk, maar moet u deze hand matig uitvoeren. Hiervoor moet u een nieuw exemplaar inrichten in de regio waar de gewenste hardware-generatie beschikbaar is, en hand matig back-ups maken en gegevens herstellen tussen het oude en het nieuwe exemplaar.

**Wat gebeurt er als er geen IP-adressen zijn voor het uitvoeren van een update bewerking?**

Als er geen IP-adressen zijn in het subnet waar uw beheerde exemplaar is ingericht, moet u een nieuw subnet en een nieuw beheerd exemplaar maken. We raden ook aan dat er een nieuw subnet wordt gemaakt met meer IP-adressen die zijn toegewezen, zodat toekomstige update bewerkingen een vergelijk bare situatie kunnen voor komen (voor een juiste subnet-grootte kunt [u de grootte van het vnet-subnet bepalen](vnet-subnet-determine-size.md). Nadat het nieuwe exemplaar is ingericht, kunt u hand matig back-ups maken van gegevens en deze herstellen tussen het oude en het nieuwe exemplaar of een [herstel punt voor meerdere tijdstippen](point-in-time-restore.md?tabs=azure-powershell)uitvoeren. 


## <a name="tune-performance"></a>Prestaties afstemmen

**De prestaties van mijn SQL Managed instance Hoe kan ik afstemmen?**

Algemeen SQL Managed instance gebruikt externe opslag, omdat de grootte van de gegevens en logboek bestanden op de prestaties van belang is. Zie [impact van de grootte van het logboek bestand op algemeen prestaties van SQL Managed instance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)voor meer informatie.

Als uw werk belasting uit veel kleine trans acties bestaat, kunt u overwegen om het verbindings type van de proxy naar de omleidings modus te scha kelen.

## <a name="maximum-storage-size"></a>Maximale opslag grootte

**Wat is de maximale opslag grootte voor een SQL Managed instance?**

De opslag grootte voor het SQL Managed instance is afhankelijk van de geselecteerde servicelaag (Algemeen of Bedrijfskritiek). Zie [kenmerk](../database/service-tiers-general-purpose-business-critical.md)van de servicelaag voor opslag beperkingen van deze service lagen.

## <a name="back-up-storage-cost"></a>Back-ups van opslag kosten 

**Wordt de back-upopslag afgetrokken van mijn SQL Managed Instance Storage?**

Nee, back-upopslag wordt niet afgetrokken van uw door SQL beheerde exemplaar opslag ruimte. De back-upopslag is onafhankelijk van de opslag ruimte van het exemplaar en is niet beperkt. Back-upopslag wordt beperkt door de tijds periode voor het bewaren van de back-up van uw exemplaar databases, te configureren van 7 tot 35 dagen. Zie [automatische back-ups](../database/automated-backups-overview.md)voor meer informatie.

## <a name="track-billing"></a>Facturering bijhouden

**Is er een manier om mijn facturerings kosten bij te houden voor mijn SQL Managed instance?**

U kunt dit doen met behulp van de [Azure Cost Management-oplossing](/azure/cost-management/). Navigeer naar **abonnementen** in het [Azure Portal](https://portal.azure.com) en selecteer **kosten analyse**. 

Gebruik de optie **geaccumuleerde kosten** en filter vervolgens op het **resource type** als `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Inkomende NSG-regels

**Hoe kan ik binnenkomende NSG-regels instellen voor beheer poorten?**

Het besturings vlak SQL SQL Managed instance Control beheert NSG-regels voor het beveiligen van beheer poorten.

Hier ziet u welke beheer poorten worden gebruikt voor:

Poorten 9000 en 9003 worden gebruikt door Service Fabric-infra structuur. Service Fabric primaire rol moet het virtuele cluster in orde blijven en de doel status in termen van het aantal onderdeel replica's blijven.

Poorten 1438, 1440 en 1452 worden gebruikt door de knooppunt agent. Knoop punt-agent is een toepassing die in het cluster wordt uitgevoerd en die wordt gebruikt door het besturings vlak om beheer opdrachten uit te voeren.

Naast de NSG-regels wordt het exemplaar in de netwerklaag beveiligd door de ingebouwde firewall. Op de Application Layer-communicatie wordt beveiligd met de certificaten.
  
Zie de [ingebouwde firewall van Azure SQL Managed instance](management-endpoint-verify-built-in-firewall.md)voor meer informatie en het controleren van de ingebouwde firewall.


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

**Waar vind ik use cases en resulterende kosten besparingen met SQL Managed instance?**

Case-study's voor SQL Managed instance:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Als u een beter inzicht wilt krijgen in de voor delen, kosten en risico's die zijn gekoppeld aan het implementeren van SQL Managed instance, is er ook een Forrester studie: [totale economische impact van Mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS vernieuwen 

**Kan ik DNS vernieuwen?**

Op dit moment bieden we geen functie voor het vernieuwen van de DNS-server configuratie voor SQL Managed instance.

De DNS-configuratie wordt uiteindelijk vernieuwd:

- Wanneer de DHCP-lease verloopt.
- Op platform upgrade.

Als tijdelijke oplossing kunt u het SQL Managed instance downgradeen naar 4 vCore en het later opnieuw bijwerken. Dit heeft een neven effect van het vernieuwen van de DNS-configuratie.


## <a name="ip-address"></a>IP-adres

**Kan ik verbinding maken met een SQL-beheerd exemplaar met behulp van een IP-adres?**

Het is niet mogelijk om verbinding te maken met een SQL-beheerd exemplaar met een IP-adres. De hostnaam van het SQL-beheerde exemplaar wordt toegewezen aan een load balancer vóór het virtuele cluster van het SQL-beheerde exemplaar. Omdat één virtueel cluster meerdere door SQL beheerde exemplaren kan hosten, kunnen er geen verbindingen worden doorgestuurd naar het juiste SQL Managed instance zonder de naam expliciet op te geven.

Zie [connectiviteits architectuur voor virtuele clusters](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)voor meer informatie over de architectuur van een virtueel cluster voor SQL-beheerde exemplaren.

**Kan een SQL Managed instance een statisch IP-adres hebben?**

In zeldzame, maar nood zakelijke situaties moet u mogelijk een online migratie uitvoeren van een SQL-beheerd exemplaar naar een nieuw virtueel cluster. Als dat nodig is, wordt deze migratie veroorzaakt door wijzigingen in onze technologie stack, die gericht is op het verbeteren van de beveiliging en betrouw baarheid van de service. Migreren naar een nieuw virtueel cluster resulteert in het wijzigen van het IP-adres dat is toegewezen aan de hostnaam van het SQL Managed instance-exemplaar. De service SQL Managed instance claimt geen ondersteuning voor statische IP-adressen en behoudt zich het recht voor om deze te wijzigen zonder kennisgeving als onderdeel van normale onderhouds cycli.

Daarom raden wij u ten zeerste aan om te vertrouwen op Onveranderbaarheid van het IP-adres, omdat dit onnodig downtime kan veroorzaken.

## <a name="change-time-zone"></a>Tijd zone wijzigen

**Kan ik de tijd zone voor een bestaand exemplaar van SQL Managed wijzigen?**

De configuratie van de tijd zone kan worden ingesteld wanneer een door SQL beheerd exemplaar voor de eerste keer wordt ingericht. Het wijzigen van de tijd zone van de bestaande SQL Managed instance wordt niet ondersteund. Zie [beperkingen voor tijd zones](timezones-overview.md#limitations)voor meer informatie.

Tijdelijke oplossingen zijn onder andere het maken van een nieuw SQL Managed-exemplaar met de juiste tijd zone en vervolgens het uitvoeren van een hand matige back-up en herstel bewerking, of wat wij adviseren, het [herstellen van een cross-instance naar](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)een bepaald tijdstip.


## <a name="resolve-performance-issues"></a>Prestatie problemen oplossen

**Kunt u prestatie problemen Hoe kan ik oplossen met mijn SQL Managed instance?**

Voor een vergelijking van de prestaties tussen het beheerde exemplaar van SQL en SQL Server, is een goed uitgangs punt [Aanbevolen procedures voor de prestaties van de prestatie vergelijking tussen Azure SQL Managed instance en SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) artikel.

Het laden van gegevens is vaak langzamer op het SQL-beheerde exemplaar dan in SQL Server vanwege een verplicht volledig herstel model en [limieten](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) voor schrijf doorvoer in transactie logboek. Dit kan soms worden omzeild door het laden van tijdelijke gegevens in TempDB in plaats van de gebruikers database, of met behulp van geclusterde column Store of tabellen die zijn geoptimaliseerd voor geheugen.


## <a name="restore-encrypted-backup"></a>Versleutelde back-up herstellen

**Kan ik mijn versleutelde data base herstellen naar een SQL Managed instance?**

Ja, u hoeft uw data base niet te ontsleutelen om deze te herstellen naar een SQL-beheerd exemplaar. U moet een certificaat/sleutel opgeven die wordt gebruikt als de versleutelings sleutel beveiliging op het bron systeem naar het beheerde exemplaar van SQL om gegevens van het versleutelde back-upbestand te kunnen lezen. Er zijn twee manieren waarop u dit kunt doen:

- *Upload certificaat beveiliging naar het door SQL beheerde exemplaar*. Dit kan alleen worden gedaan met behulp van Power shell. In het [voorbeeld script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) wordt het hele proces beschreven.
- De *asymmetrische sleutel beveiliging uploaden naar de Azure Key Vault (Azure) en het beheerde exemplaar van SQL*. Deze benadering lijkt op het gebruik van de BYOK-TDE-use-case (uw eigen sleutel), die ook gebruikmaakt van Azure-integratie om de versleutelings sleutel op te slaan. Als u de sleutel niet wilt gebruiken als een versleutelings sleutel beveiliging en alleen de sleutel beschikbaar wilt maken voor SQL Managed instance om versleutelde data bases te herstellen, volgt u de instructies voor het [instellen van BYOK TDe](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)en schakelt u het selectie vakje de *geselecteerde sleutel de standaard TDe-Protector maken*in.

Zodra u de versleutelings beveiliging beschikbaar maakt voor een SQL-beheerd exemplaar, kunt u door gaan met de standaard procedure voor het herstellen van data bases.

## <a name="migrate-from-sql-database"></a>Migreren vanaf SQL Database 

**Hoe kan ik migreren van Azure SQL Database naar een beheerd exemplaar van SQL?**

SQL Managed instance biedt dezelfde prestatie niveaus per reken-en opslag grootte als Azure SQL Database. Als u gegevens wilt consolideren voor één exemplaar of als u alleen een functie nodig hebt die uitsluitend wordt ondersteund in een SQL Managed instance, kunt u uw gegevens migreren met behulp van de functionaliteit voor exporteren/importeren (BACPAC).
