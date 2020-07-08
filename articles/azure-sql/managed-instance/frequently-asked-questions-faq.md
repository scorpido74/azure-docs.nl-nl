---
title: Veelgestelde vragen
titleSuffix: Azure SQL Managed Instance
description: Veelgestelde vragen over Azure SQL Managed instance (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708855"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure SQL Managed instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat de meest voorkomende vragen over [Azure SQL Managed instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund in een SQL Managed instance?**

Zie [functies van Azure SQL Managed instance](../database/features-comparison.md)voor een lijst met ondersteunde functies in SQL Managed instance.

Zie voor verschillen in de syntaxis en het gedrag tussen Azure SQL Managed instance en SQL Server [T-SQL-verschillen van SQL Server](transact-sql-tsql-differences-sql-server.md).


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

Verwachte tijd voor het maken van een beheerd exemplaar of het wijzigen van de servicelaag (vCores, opslag) is afhankelijk van verschillende factoren. Bekijk de [beheer bewerkingen](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-conventions"></a>Naamconventies

**Kan een beheerd exemplaar dezelfde naam hebben als een SQL Server on-premises exemplaar?**

Het wijzigen van de naam van een beheerd exemplaar wordt niet ondersteund.

De standaard-DNS-zone *. database.Windows.net* voor een beheerd exemplaar kan worden gewijzigd. 

Als u een andere DNS-zone wilt gebruiken in plaats van de standaard instelling, bijvoorbeeld *. contoso.com*: 
- Gebruik CliConfig voor het definiëren van een alias. Het hulp programma is slechts een wrapper voor register instellingen, zodat het kan worden uitgevoerd met behulp van groeps beleid of een script.
- Gebruik *CNAME* met de optie *TrustServerCertificate = True* .

## <a name="move-a-database-from-sql-managed-instance"></a>Een Data Base verplaatsen van een SQL-beheerd exemplaar 

**Hoe kan ik een Data Base van een SQL Managed instance weer verplaatsen naar SQL Server of Azure SQL Database?**

U kunt [een Data Base exporteren naar BACPAC](../database/database-export.md) en vervolgens [het BACPAC-bestand importeren](../database/database-import.md). Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB.

Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

Systeem eigen `COPY_ONLY` back-ups die zijn gemaakt op basis van een SQL-beheerd exemplaar, kunnen niet worden hersteld naar SQL Server omdat SQL Managed instance een hogere database versie heeft dan SQL Server.

## <a name="migrate-an-instance-database"></a>Een exemplaar database migreren

**Hoe kan ik mijn exemplaar database migreren naar Azure SQL Database?**

U kunt [de data base ook exporteren naar een BACPAC](../database/database-export.md) en vervolgens [het BACPAC-bestand importeren](../database/database-import.md). 

Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB. Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

## <a name="switch-hardware-generation"></a>Hardware-generatie scha kelen 

**Kan ik de generatie van de hardware van SQL Managed instance tussen gen 4 en Gen 5 online veranderen?**

Geautomatiseerde online switches tussen de hardware is mogelijk als beide generaties beschikbaar zijn in de regio waar SQL Managed instance is ingericht. In dit geval kunt u de [overzichts pagina](../database/service-tiers-vcore.md)van het vCore-model controleren, waarin wordt uitgelegd hoe u kunt scha kelen tussen hardware gegenereerd.

Dit is een langlopende bewerking, omdat een nieuw beheerd exemplaar wordt ingericht op de achtergrond en data bases automatisch worden overgedragen tussen de oude en nieuwe instanties, met een snelle failover aan het einde van het proces. 

**Wat gebeurt er als beide hardware gegenereerd worden in dezelfde regio?**

Als beide generaties niet in dezelfde regio worden ondersteund, is het wijzigen van de hardware mogelijk, maar moet u deze hand matig uitvoeren. Hiervoor moet u een nieuw exemplaar inrichten in de regio waar de gewenste hardware-generatie beschikbaar is, en hand matig back-ups maken en gegevens herstellen tussen de oude en nieuwe instanties.

**Wat gebeurt er als er onvoldoende IP-adressen zijn voor het uitvoeren van een update bewerking?**

Als er onvoldoende IP-adressen zijn in het subnet waar uw beheerde exemplaar is ingericht, moet u een nieuw subnet en een nieuw beheerd exemplaar maken. We raden ook aan dat het nieuwe subnet wordt gemaakt met meer IP-adressen die zijn toegewezen zodat toekomstige update bewerkingen voor komen dat er soort gelijke situaties zijn. (Zie [de grootte van een VNet-subnet bepalen voor de juiste grootte van het](vnet-subnet-determine-size.md)subnet.) Nadat het nieuwe exemplaar is ingericht, kunt u hand matig een back-up maken van gegevens en deze herstellen tussen de oude en nieuwe instanties of het [herstel punt in de tijd](point-in-time-restore.md?tabs=azure-powershell)van meerdere exemplaren uitvoeren. 


## <a name="tune-performance"></a>Prestaties afstemmen

**De prestaties van het SQL Managed instance Hoe kan ik verfijnen?**

SQL Managed instance in de laag Algemeen maakt gebruik van externe opslag, zodat de grootte van de gegevens en logboek bestanden op de prestaties van belang is. Zie [impact van de grootte van het logboek bestand op algemeen prestaties van SQL Managed instance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)voor meer informatie.

Als uw werk belasting uit veel kleine trans acties bestaat, kunt u overwegen om het verbindings type van de proxy naar de omleidings modus te scha kelen.

## <a name="maximum-storage-size"></a>Maximale opslag grootte

**Wat is de maximale opslag grootte voor een SQL Managed instance?**

De opslag grootte voor het SQL Managed instance is afhankelijk van de geselecteerde servicelaag (Algemeen of Bedrijfskritiek). Zie [kenmerken](../database/service-tiers-general-purpose-business-critical.md)van de servicelaag voor opslag beperkingen van deze service lagen.

## <a name="backup-storage-cost"></a>Kosten voor back-upopslag 

**Wordt de back-upopslag afgetrokken van mijn SQL Managed Instance Storage?**

Nee, back-upopslag wordt niet afgetrokken van uw door SQL beheerde exemplaar opslag ruimte. De back-upopslag is onafhankelijk van de opslag ruimte van het exemplaar en is niet beperkt. Back-upopslag wordt beperkt door de tijds periode voor het bewaren van de back-up van uw exemplaar databases, te configureren van 7 tot 35 dagen. Zie [automatische back-ups](../database/automated-backups-overview.md)voor meer informatie.

## <a name="track-billing"></a>Facturering bijhouden

**Is er een manier om mijn facturerings kosten voor een SQL Managed instance bij te houden?**

U kunt dit doen met behulp van de [Azure Cost Management-oplossing](/azure/cost-management/). Navigeer naar **abonnementen** in het [Azure Portal](https://portal.azure.com) en selecteer **kosten analyse**. 

Gebruik de optie **geaccumuleerde kosten** en filter vervolgens op het **resource type** als `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Inkomende NSG-regels

**Hoe kan ik binnenkomende NSG-regels instellen voor beheer poorten?**

Het besturings vlak SQL Managed instance beheert onderhoudt NSG regels voor het beveiligen van beheer poorten.

Hier ziet u welke beheer poorten worden gebruikt voor:

De poorten 9000 en 9003 worden gebruikt door de Azure Service Fabric-infra structuur. De Service Fabric primaire rol is om het virtuele cluster in orde te stellen en de doel status in termen van het aantal onderdeel replica's te blijven gebruiken.

Poorten 1438, 1440 en 1452 worden gebruikt door de knooppunt agent. De knooppunt agent is een toepassing die in het cluster wordt uitgevoerd en die wordt gebruikt door het besturings vlak om beheer opdrachten uit te voeren.

Naast NSG-regels beschermt de ingebouwde firewall het exemplaar in de netwerklaag. Op de toepassingslaag wordt de communicatie beveiligd met de certificaten.

Zie de [ingebouwde firewall van Azure SQL Managed instance](management-endpoint-verify-built-in-firewall.md)voor meer informatie en voor informatie over het controleren van de ingebouwde firewall.


## <a name="mitigate-data-exfiltration-risks"></a>Gegevens exfiltration Risico's beperken  

**Hoe kan ik de gegevens exfiltration Risico's beperken?**

Klanten wordt aangeraden een aantal beveiligings instellingen en-besturings elementen toe te passen om eventuele gegevens exfiltration Risico's te beperken:

- Schakel [transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in voor alle data bases.
- Common language runtime (CLR) uitschakelen. Dit wordt ook aanbevolen voor on-premises.
- Gebruik alleen Azure Active Directory-verificatie (Azure AD).
- Toegang tot het exemplaar met een account met beperkte bevoegdheden.
- Configureer JIT JumpBox-toegang voor het account sysadmin.
- Schakel [SQL auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)in en integreer deze met waarschuwings mechanismen.
- Schakel de [detectie van bedreigingen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) in vanuit het pakket [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Gebruiks voorbeelden voor kosten besparing

**Waar vind ik use cases en resulterende kosten besparingen met SQL Managed instance?**

Case-study's voor SQL Managed instance:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Als u een beter inzicht wilt krijgen in de voor delen, kosten en risico's die zijn gekoppeld aan het implementeren van Azure SQL Managed instance, is er ook een Forrester-studie: [de totale economische impact van Microsoft Azure SQL database Managed instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS vernieuwen 

**Kan ik DNS vernieuwen?**

Op dit moment bieden we geen functie voor het vernieuwen van de DNS-server configuratie voor SQL Managed instance.

De DNS-configuratie wordt uiteindelijk vernieuwd:

- Wanneer de DHCP-lease verloopt.
- Op platform upgrade.

U kunt dit probleem omzeilen door SQL Managed instance te downgradeen naar 4 vCores en deze later opnieuw bij te werken. Dit heeft een neven effect van het vernieuwen van de DNS-configuratie.


## <a name="ip-address"></a>IP-adres

**Kan ik verbinding maken met een SQL-beheerd exemplaar met behulp van een IP-adres?**

Het is niet mogelijk om verbinding te maken met een SQL-beheerd exemplaar met behulp van een IP-adres. De hostnaam van het SQL Managed instance-exemplaar wordt toegewezen aan een load balancer vóór het virtuele cluster van het SQL-beheerde exemplaar. Als één virtueel cluster meerdere beheerde exemplaren kan hosten, kunnen er geen verbindingen worden doorgestuurd naar het juiste beheerde exemplaar zonder de naam expliciet op te geven.

Zie [connectiviteits architectuur voor virtuele clusters](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)voor meer informatie over de architectuur van een virtueel cluster voor SQL-beheerde exemplaren.

**Kan SQL Managed instance een statisch IP-adres hebben?**

In zeldzame maar nood zakelijke situaties moet u mogelijk een online migratie uitvoeren van een SQL-beheerd exemplaar naar een nieuw virtueel cluster. Als dat nodig is, wordt deze migratie veroorzaakt door wijzigingen in onze technologie stack, die gericht is op het verbeteren van de beveiliging en betrouw baarheid van de service. Migreren naar een nieuw virtueel cluster resulteert in het wijzigen van het IP-adres dat is toegewezen aan de hostnaam van het SQL Managed instance-exemplaar. De service SQL Managed instance claimt geen ondersteuning voor statische IP-adressen en behoudt zich het recht voor om deze te wijzigen zonder kennisgeving als onderdeel van normale onderhouds cycli.

Daarom raden wij u ten zeerste aan om te vertrouwen op Onveranderbaarheid van het IP-adres, omdat dit onnodig downtime kan veroorzaken.

## <a name="change-time-zone"></a>Tijd zone wijzigen

**Kan ik de tijd zone wijzigen voor een bestaande beheerde instantie?**

De configuratie van de tijd zone kan worden ingesteld wanneer een beheerd exemplaar voor de eerste keer wordt ingericht. Het wijzigen van de tijd zone van een bestaand beheerd exemplaar wordt niet ondersteund. Zie [beperkingen voor tijd zones](timezones-overview.md#limitations)voor meer informatie.

Tijdelijke oplossingen zijn onder andere het maken van een nieuw beheerd exemplaar met de juiste tijd zone en vervolgens het uitvoeren van een hand matige back-up en herstel bewerking, of voor de aanbevolen procedure voor het uitvoeren [van een cross-instance-in-time herstel bewerking](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Prestatie problemen oplossen

**Kunt u prestatie problemen met SQL Managed instance Hoe kan ik oplossen?**

Voor een vergelijking van de prestaties tussen het beheerde exemplaar van SQL en SQL Server, is een goed uitgangs punt [Aanbevolen procedures voor de prestaties van de prestatie vergelijking tussen Azure SQL Managed instance en SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Het laden van gegevens is vaak langzamer op het SQL-beheerde exemplaar dan in SQL Server vanwege het verplichte volledige herstel model en de [maximale](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) schrijf doorvoer voor transactie Logboeken. Soms kan dit worden omzeild door tijdelijke gegevens in TempDB te laden in plaats van de gebruikers database, of door gebruik te maken van geclusterde column Store-of geheugen-geoptimaliseerde tabellen.


## <a name="restore-encrypted-backup"></a>Versleutelde back-up herstellen

**Kan ik mijn versleutelde data base herstellen naar een SQL Managed instance?**

Ja, u hoeft uw data base niet te ontsleutelen om deze te herstellen naar een SQL-beheerd exemplaar. U moet een certificaat/sleutel opgeven die wordt gebruikt als de versleutelings sleutel beveiliging op het bron systeem naar SQL Managed instance om gegevens van het versleutelde back-upbestand te kunnen lezen. Er zijn twee manieren waarop u dit kunt doen:

- *Upload certificaat beveiliging naar een beheerd exemplaar van SQL*. Dit kan alleen worden gedaan met behulp van Power shell. In het [voorbeeld script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) wordt het hele proces beschreven.
- De *asymmetrische sleutel beveiliging uploaden naar Azure Key Vault en naar de SQL-beheerde instantie verwijzen*. Deze benadering lijkt op het gebruik van uw BYOK-TDE-toepassing (your-own-Key) die ook Key Vault-integratie gebruikt om de versleutelings sleutel op te slaan. Als u de sleutel niet wilt gebruiken als een versleutelings sleutel beveiliging en alleen de sleutel beschikbaar wilt maken voor SQL Managed instance om versleutelde data bases te herstellen, volgt u de instructies voor het [instellen van BYOK TDe](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)en schakelt u het selectie vakje de **geselecteerde sleutel de standaard TDe-Protector maken**in.

Zodra u de versleutelings beveiliging beschikbaar maakt voor een SQL-beheerd exemplaar, kunt u door gaan met de standaard procedure voor het herstellen van data bases.

## <a name="migrate-from-sql-database"></a>Migreren vanaf SQL Database 

**Hoe kan ik migreren van Azure SQL Database naar een beheerd exemplaar van SQL?**

SQL Managed instance biedt dezelfde prestatie niveaus per reken-en opslag grootte als Azure SQL Database. Als u gegevens wilt consolideren voor één exemplaar of als u alleen een functie nodig hebt die uitsluitend wordt ondersteund in een SQL Managed instance, kunt u uw gegevens migreren met behulp van de functionaliteit voor exporteren/importeren (BACPAC).

## <a name="password-policy"></a>Wachtwoord beleid 

**Welke beleids regels voor wacht woorden worden toegepast op SQL-aanmeldingen voor SQL Managed instance?**

Het wachtwoord beleid voor SQL-beheerde exemplaren voor SQL-aanmeldingen neemt het Azure-platform beleid over dat wordt toegepast op de virtuele machines die het beheerde exemplaar hebben. Op het moment is het niet mogelijk om een van deze instellingen te wijzigen, omdat deze instellingen worden gedefinieerd door Azure en worden overgenomen door een beheerd exemplaar.

 > [!IMPORTANT]
 > Het Azure-platform kan beleids vereisten wijzigen zonder dat u op de hoogte hoeft te zijn van de services die op dat beleid zijn gebaseerd.

**Wat is het huidige Azure-platform beleid?**

Bij elke aanmelding moet het wacht woord bij aanmelding worden ingesteld en het bijbehorende wacht woord worden gewijzigd nadat het maximum aantal leeftijden is bereikt.

| **Beleid** | **Beveiligingsinstelling** |
| --- | --- |
| Maximale wachtwoord duur | 42 dagen |
| Minimale wachtwoord duur | 1 dag |
| Minimale wachtwoordlengte | 10 tekens |
| Wacht woord moet voldoen aan complexiteits vereisten | Ingeschakeld |

**Is het mogelijk om wachtwoord complexiteit en verloop tijd in SQL Managed instance op aanmeldings niveau uit te scha kelen?**

Ja, u kunt CHECK_POLICY en CHECK_EXPIRATION velden op aanmeldings niveau beheren. U kunt de huidige instellingen controleren door de volgende T-SQL-opdracht uit te voeren:

```sql
SELECT *
FROM sys.sql_logins
```

Daarna kunt u de opgegeven aanmeldings instellingen wijzigen door het volgende uit te voeren:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(Vervang ' test ' door de gewenste aanmeldings naam en pas het beleid en de verval waarden aan)
