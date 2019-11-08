---
title: Veelgestelde vragen over beheerdere exemplaren
description: Veelgestelde vragen over SQL Database beheerde exemplaren
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 34ffcd63879920c3c744deb4cf0b28684081dc25
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825736"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over SQL Database beheerde exemplaren

Dit artikel bevat veel van de meest voorkomende vragen over [SQL database beheerde instantie](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-supported-on-managed-instance"></a>Waar vind ik een lijst met functies die worden ondersteund in een beheerd exemplaar?

Zie [Azure SQL database versus SQL Server](sql-database-features.md)voor een lijst met ondersteunde functies in het beheerde exemplaar.

Zie voor verschillen in de syntaxis en het gedrag tussen Azure SQL Database beheerde instantie en on-premises SQL Server [T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Waar vind ik technische kenmerken en resource limieten voor een beheerd exemplaar?

Zie [technische verschillen in hardware generaties](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)voor de beschik bare kenmerken voor het genereren van hardware.
Zie [technische verschillen tussen service lagen](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)voor de beschik bare service lagen en hun kenmerken.

## <a name="where-can-i-find-known-issues-and-bugs"></a>Waar vind ik bekende problemen en bugs?

Zie [bekende problemen](sql-database-managed-instance-transact-sql-information.md#Issues)voor bugs en bekende problemen.

## <a name="where-can-i-find-latest-features-and-the-features-in-public-preview"></a>Waar vind ik de nieuwste functies en de functies van de open bare preview?

Zie [release opmerkingen](/azure/sql-database/sql-database-release-notes?tabs=managed-instance)voor nieuwe en preview-functies.

## <a name="how-much-time-takes-to-create-or-update-instance-or-to-restore-a-database"></a>Hoe lang duurt het om een exemplaar te maken of bij te werken of om een Data Base te herstellen?

De verwachte tijd voor het maken van een nieuw beheerd exemplaar of het wijzigen van de servicelaag (vCores, opslag) is afhankelijk van verschillende factoren. Bekijk de [beheer bewerkingen](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Kan een beheerd exemplaar dezelfde naam hebben als de on-premises SQL Server?

Het beheerde exemplaar moet een naam hebben die eindigt op *database.Windows.net*. Als u een andere DNS-zone wilt gebruiken in plaats van de standaard waarde, bijvoorbeeld **mi-een andere naam**. contoso.com: 
- Gebruik CliConfig voor het definiëren van een alias. Het hulp programma is slechts een wrapper voor register instellingen en kan daarom ook worden uitgevoerd met groeps beleid of script.
- Gebruik *CNAME* met de optie *TrustServerCertificate = True* .

## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Hoe kan ik data base van een beheerd exemplaar terugplaatsen naar SQL Server of Azure SQL Database?

U kunt [Data Base exporteren naar BACPAC](sql-database-export.md) en vervolgens [het BACPAC-bestand importeren]( sql-database-import.md). Dit is een aanbevolen benadering als uw data base kleiner is dan 100 GB.

Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

Systeem eigen `COPY_ONLY` back-ups die zijn gemaakt van het beheerde exemplaar, kunnen niet worden hersteld naar SQL Server omdat het beheerde exemplaar een hogere database versie heeft dan SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Hoe kan ik mijn exemplaar database migreren naar een enkele Azure SQL Database?

U kunt [de data base ook exporteren naar een BACPAC](sql-database-export.md) en vervolgens [het BACPAC-bestand importeren](sql-database-import.md). 

Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB. Transactionele replicatie kan worden gebruikt als alle tabellen in de data base primaire sleutels hebben.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Hoe kan ik kiezen tussen generatie van gen 4 en generatie 5 hardware voor een beheerd exemplaar?

Het is afhankelijk van uw werk belasting, omdat bepaalde typen werk belastingen beter zijn dan de andere. Hoewel het onderwerp van de prestaties in plaats van complex is om te vereenvoudigen, worden de volgende verschillen tussen de hardware gegenereerd die van invloed zijn op de prestaties van de werk belasting:
- Gen 4 biedt een betere reken ondersteuning, omdat deze is gebaseerd op fysieke processors, en op basis van de vCore-processors. Het kan handiger zijn voor computerintensieve werk belastingen.
- Gen 5 biedt ondersteuning voor versneld netwerken, wat leidt tot een betere IO-band breedte naar externe opslag. Dit kan handig zijn voor intensieve i/o-werk belastingen op Algemeen Service lagen. In Gen 5 worden lokale schijven met een hogere SSD vergeleken met gen 4. Dit kan handig zijn voor werk belasting intensieve workloads op kritieke service lagen.

Het wordt ten zeerste aangeraden de prestaties van werkelijke workloads te testen die bestemd zijn voor productie voordat ze live gaan gebruiken om te bepalen welke hardware-generatie beter in een specifiek geval zal werken.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Kan ik de hardware-generatie van beheerde exemplaren tussen gen 4 en Gen 5 online veranderen? 

Geautomatiseerde online switches tussen de hardware is mogelijk als beide generaties beschikbaar zijn in de regio waar uw beheerde exemplaar is ingericht. In dit geval kunt u [script uit een blog bericht](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Change-hardware-generation-on-Managed-Instance/ba-p/699824) gebruiken om uit te leggen hoe u tussen de hardware gegenereerd gaat.

Dit is een langlopende bewerking als een nieuw beheerd exemplaar wordt ingericht op de achtergrond en data bases worden automatisch overgebracht tussen het oude en het nieuwe exemplaar met een snelle failover aan het einde van het proces. 

Als beide generaties niet in dezelfde regio worden ondersteund, is het wijzigen van de hardware mogelijk, maar moet u deze hand matig uitvoeren. Hiervoor moet u een nieuw exemplaar inrichten in de regio waar de gewenste hardware-generatie beschikbaar is, en hand matig back-ups maken en gegevens herstellen tussen het oude en het nieuwe exemplaar.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>De prestaties van mijn beheerde exemplaar Hoe kan ik afstemmen? 

Algemeen beheerde instantie gebruikt externe opslag, omdat de grootte van de gegevens en logboek bestanden op de prestaties van belang is. Zie [impact van de grootte van het logboek bestand op algemeen prestaties van het beheerde exemplaar](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)voor meer informatie.

Voor intensieve IO-workloads kunt u gebruikmaken van generatie 5-Hardware en met behulp van gen 4 voor computerintensieve werk belastingen. Zie [Hoe kan ik kiezen tussen gen 4 en Gen 5](#how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance)voor meer informatie.

Als uw werk belasting uit veel kleine trans acties bestaat, kunt u overwegen om het verbindings type van de proxy naar de omleidings modus te scha kelen.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Wat is de maximale opslag grootte voor het beheerde exemplaar? 

De opslag grootte voor het beheerde exemplaar is afhankelijk van de geselecteerde servicelaag (Algemeen of Bedrijfskritiek). Zie [kenmerk](sql-database-service-tiers-general-purpose-business-critical.md)van de servicelaag voor opslag beperkingen van deze service lagen.

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Wordt de back-upopslag afgetrokken van mijn beheerde exemplaar opslag? 

Nee, back-upopslag wordt niet afgetrokken van de opslag ruimte voor uw beheerde exemplaar. De back-upopslag is onafhankelijk van de opslag ruimte van het exemplaar en is niet beperkt. Back-upopslag wordt beperkt door de tijds periode voor het bewaren van de back-up van uw exemplaar databases, te configureren van 7 tot 35 dagen. Zie [automatische back-ups](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)voor meer informatie.
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Hoe kan ik binnenkomende NSG-regels instellen voor beheer poorten?

Met de ingebouwde firewall functie wordt Windows Firewall geconfigureerd op alle virtuele machines in het cluster om binnenkomende verbindingen van IP-adresbereiken toe te staan die alleen zijn gekoppeld aan micro soft-beheer-en-implementatie computers en om ervoor te zorgen dat beveiligde beheer werkstations effectief worden voor komen indringers via de netwerklaag.

Hier ziet u welke poorten worden gebruikt voor:

Poorten 9000 en 9003 worden gebruikt door Service Fabric-infra structuur. Service Fabric primaire rol moet het virtuele cluster in orde blijven en de doel status in termen van het aantal onderdeel replica's blijven.

Poorten 1438, 1440 en 1452 worden gebruikt door de knooppunt agent. Knoop punt-agent is een toepassing die in het cluster wordt uitgevoerd en die wordt gebruikt door het besturings vlak om beheer opdrachten uit te voeren.

Naast de ingebouwde firewall op de netwerklaag, wordt communicatie ook beveiligd met certificaten.
  
Zie [Azure SQL database Managed instance built-in Firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)voor meer informatie over het controleren van de ingebouwde firewall.


## <a name="how-can-i-mitigate-networking-risks"></a>Hoe kan ik de netwerk Risico's beperken? 

Klanten wordt aangeraden een aantal beveiligings instellingen en-besturings elementen toe te passen om eventuele netwerk Risico's te beperken:

- Schakel [transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in voor alle data bases.
- Common language runtime (CLR) uitschakelen. Dit wordt ook aanbevolen voor on-premises.
- Alleen Azure Active Directory (AAD)-verificatie gebruiken.
- Access-exemplaar met een account met beperkte bevoegdheden.
- Configureer de JiT JumpBox-toegang voor het sysadmin-account.
- Schakel [SQL auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)in en integreer deze met waarschuwings mechanismen.
- Schakel de [detectie van bedreigingen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) in het pakket [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) in.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Waar vind ik use cases en resulterende kosten besparingen met een beheerd exemplaar?

Case-study's voor beheerde instanties:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Om een beter inzicht te krijgen in de voor delen, kosten en risico's die zijn gekoppeld aan het implementeren van Azure SQL Database beheerde instantie, is er ook een Forrester studie: [totale economische impact van Mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Kan ik DNS vernieuwen? 
  
Momenteel bieden we geen functie voor het vernieuwen van de DNS-server configuratie voor een beheerd exemplaar.

De DNS-configuratie wordt uiteindelijk vernieuwd:

- Wanneer de DHCP-lease verloopt.
- Op platform upgrade.

Als tijdelijke oplossing kunt u het beheerde exemplaar downgradeen naar 4 vCore en het later opnieuw bijwerken. Dit heeft een neven effect van het vernieuwen van de DNS-configuratie.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Kan een beheerd exemplaar een statisch IP-adres hebben?

In zeldzame maar nood zakelijke situaties moet u mogelijk een online migratie van een beheerd exemplaar uitvoeren naar een nieuw virtueel cluster. Als dat nodig is, wordt deze migratie veroorzaakt door wijzigingen in onze technologie stack, die gericht is op het verbeteren van de beveiliging en betrouw baarheid van de service. Migreren naar een nieuw virtueel cluster resulteert in het wijzigen van het IP-adres dat is toegewezen aan de hostnaam van het beheerde exemplaar. De service Managed instance claimt geen ondersteuning voor statische IP-adressen en behoudt zich het recht voor om deze te wijzigen zonder kennisgeving als onderdeel van normale onderhouds cycli.

Daarom raden wij u ten zeerste aan om te vertrouwen op Onveranderbaarheid van het IP-adres, omdat dit onnodig downtime kan veroorzaken.

## <a name="can-i-move-a-managed-instance-or-its-vnet-to-another-resource-group"></a>Kan ik een beheerd exemplaar of het VNet verplaatsen naar een andere resource groep?

Nee, dit is de beperking van het huidige platform. Nadat een beheerd exemplaar is gemaakt, wordt het beheerde exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Kan ik de tijd zone wijzigen voor een bestaande beheerde instantie?

De configuratie van de tijd zone kan worden ingesteld wanneer een beheerd exemplaar voor de eerste keer wordt ingericht. Het wijzigen van de tijd zone van het bestaande beheerde exemplaar wordt niet ondersteund. Zie [beperkingen voor tijd zones](sql-database-managed-instance-timezone.md#limitations)voor meer informatie.

Tijdelijke oplossingen zijn onder andere het maken van een nieuw beheerd exemplaar met de juiste tijd zone en vervolgens een hand matige back-up maken en herstellen, of wat wij adviseren, het [herstellen van een exemplaar naar een ander tijdstip](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)uitvoeren.


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Prestatie problemen Hoe kan ik oplossen met mijn beheerde exemplaar

Voor een vergelijking van de prestaties tussen het beheerde exemplaar en SQL Server, is een goed uitgangs punt [Aanbevolen procedures voor de prestatie vergelijking tussen Azure SQL Managed instance en SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) artikel.

Het laden van gegevens is vaak langzamer op het beheerde exemplaar dan in SQL Server vanwege een verplicht volledig herstel model en [limieten](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) voor schrijf doorvoer in transactie logboek. Dit kan soms worden omzeild door het laden van tijdelijke gegevens in TempDB in plaats van de gebruikers database, of met behulp van geclusterde column Store of tabellen die zijn geoptimaliseerd voor geheugen.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Kan ik mijn versleutelde data base herstellen naar een beheerd exemplaar?

Ja, u hoeft uw data base niet te ontsleutelen zodat deze kan worden hersteld naar een beheerd exemplaar. U moet een certificaat/sleutel opgeven die wordt gebruikt als een versleutelings sleutel beveiliging in het bron systeem naar het beheerde exemplaar om gegevens te kunnen lezen uit het versleutelde back-upbestand. Er zijn twee manieren waarop u dit kunt doen:

- *Upload certificaat beveiliging naar het beheerde exemplaar*. Dit kan alleen worden gedaan met behulp van Power shell. In het [voorbeeld script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) wordt het hele proces beschreven.
- De *asymmetrische sleutel beveiliging uploaden naar Azure Key Vault (Azure) en het beheerde exemplaar*. Deze benadering lijkt op het gebruik van de BYOK-TDE-use-case (uw eigen sleutel), die ook gebruikmaakt van Azure-integratie om de versleutelings sleutel op te slaan. Als u de sleutel niet wilt gebruiken als een versleutelings sleutel beveiliging en alleen de sleutel beschikbaar wilt maken voor een beheerd exemplaar om versleutelde data bases te herstellen, volgt u de instructies voor het [instellen van BYOK TDe](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)en schakelt u het selectie vakje *de geselecteerde sleutel maken de standaard TDE-beveiliging*.

Wanneer u de versleutelings beveiliging hebt gemaakt voor een beheerd exemplaar, kunt u door gaan met de standaard procedure voor het herstellen van data bases.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Hoe kan ik migreren van Azure SQL Database enkele of elastische pool naar een beheerd exemplaar? 

Beheerde instantie biedt dezelfde prestatie niveaus per Compute en opslag grootte als andere implementatie opties van Azure SQL Database. Als u gegevens wilt consolideren voor één exemplaar of als u alleen een functie nodig hebt die uitsluitend wordt ondersteund in het beheerde exemplaar, kunt u uw gegevens migreren met behulp van de functionaliteit voor exporteren/importeren (BACPAC).
