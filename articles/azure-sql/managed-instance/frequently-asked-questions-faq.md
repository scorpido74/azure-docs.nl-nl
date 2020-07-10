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
ms.openlocfilehash: d2e4b07c97e09fce5cdaa034e2fe67a18ef0d7f1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171156"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure SQL Managed instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat de meest voorkomende vragen over [Azure SQL Managed instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund in een SQL Managed instance?**

Zie [functies van Azure SQL Managed instance](../database/features-comparison.md)voor een lijst met ondersteunde functies in SQL Managed instance.

Zie voor verschillen in de syntaxis en het gedrag tussen Azure SQL Managed instance en SQL Server [T-SQL-verschillen van SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technische specificatie, resource limieten en andere beperkingen
 
**Waar vind ik technische kenmerken en resource limieten voor SQL Managed instance?**

Zie [technische verschillen in hardware generaties](resource-limits.md#hardware-generation-characteristics)voor de beschik bare kenmerken voor het genereren van hardware.
Zie [technische verschillen tussen service lagen](resource-limits.md#service-tier-characteristics)voor de beschik bare service lagen en hun kenmerken.

**Van welke servicelaag ben ik in aanmerking?**

Elke klant komt in aanmerking voor een servicelaag. Als u echter uw bestaande licenties wilt uitwisselen voor kortings tarieven op Azure SQL Managed instance met behulp van [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), moet u er rekening mee houdt dat SQL Server Enterprise Edition-klanten met Software Assurance in aanmerking komen voor de [Algemeen](../database/service-tier-general-purpose.md) -of [bedrijfskritiek](../database/service-tier-business-critical.md) prestatie lagen en SQL Server Standard Edition-klanten met software assurance in aanmerking komen voor de algemeen-laag voor prestaties. Zie [specifieke rechten van de Ahb](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)voor meer informatie.

**Welke typen abonnementen worden ondersteund voor een SQL Managed instance?**

Zie [ondersteunde abonnements typen](resource-limits.md#supported-subscription-types)voor een lijst met ondersteunde typen abonnementen. 

**Welke Azure-regio's worden ondersteund?**

Beheerde exemplaren kunnen worden gemaakt in de meeste Azure-regio's. Zie [ondersteunde regio's voor SQL Managed instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar nodig hebt in een regio die momenteel niet wordt ondersteund, [verzendt u een ondersteunings aanvraag via de Azure Portal](../database/quota-increase-request.md).

**Zijn er quota beperkingen voor implementaties van SQL Managed instances?**

Het beheerde exemplaar heeft twee standaard limieten: de limiet voor het aantal subnetten dat u kunt gebruiken en een limiet voor het aantal vCores dat u kunt inrichten. De limieten zijn afhankelijk van de typen abonnementen en regio's. Zie voor de lijst met regionale resource beperkingen per abonnements type tabel van [regionale resource beperking](resource-limits.md#regional-resource-limitations). Dit zijn zachte limieten die op aanvraag kunnen worden verhoogd. Als u meer beheerde exemplaren in uw huidige regio's wilt inrichten, verzendt u een ondersteunings aanvraag om het quotum te verhogen met behulp van de Azure Portal. Zie [aanvraag quotum verhogingen voor Azure SQL database](../database/quota-increase-request.md)voor meer informatie.

**Kan ik het aantal data bases limiet (100) op mijn beheerde exemplaar op aanvraag verhogen?**

Nee, en op dit moment zijn er geen vastgelegde plannen om het aantal data bases op een SQL Managed instance te verg Roten. 

**Waar kan ik migreren als ik meer dan 8TB aan gegevens heb?**
U kunt overwegen om te migreren naar andere Azure-versies die geschikt zijn voor uw workload: [Azure SQL database grootschalige](../database/service-tier-hyperscale.md) of [SQL Server op Azure virtual machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Waar kan ik migreren als ik specifieke hardwarevereisten, zoals grotere RAM, voor een vCore-verhouding of meer Cpu's?**
U kunt overwegen om te migreren naar [SQL Server op Azure virtual machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) of [Azure SQL database](../database/sql-database-paas-overview.md) geheugen/CPU geoptimaliseerd.

## <a name="known-issues--bugs"></a>Bekende problemen & bugs

**Waar vind ik bekende problemen en bugs?**

Zie [bekende problemen](../database/doc-changes-updates-release-notes.md#known-issues)voor bugs en bekende problemen.

## <a name="new-features"></a>Nieuwe functies

**Waar vind ik de nieuwste functies en de functies van de open bare preview?**

Zie [release opmerkingen](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)voor nieuwe en preview-functies.

## <a name="create-update-delete-or-move-sql-managed-instance"></a>SQL Managed instance maken, bijwerken, verwijderen of verplaatsen

**Hoe kan ik een SQL Managed instance inrichten?**

U kunt een exemplaar inrichten via [Azure Portal](instance-create-quickstart.md), [Power shell](scripts/create-configure-managed-instance-powershell.md), [Azure cli](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) en [arm-sjablonen](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Kan ik beheerde exemplaren inrichten in een bestaand abonnement?**

Ja, u kunt een beheerd exemplaar inrichten in een bestaand abonnement als dat abonnement deel uitmaakt van de [ondersteunde typen abonnementen](resource-limits.md#supported-subscription-types).

**Waarom kan ik geen beheerd exemplaar inrichten in het subnet waarvan de naam begint met een cijfer?**

Dit is een huidige beperking voor het onderliggende onderdeel dat de subnetnaam verifieert met de reguliere expressie ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Alle namen die de regex door geven en geldige subnet-namen zijn, worden momenteel ondersteund.

**Hoe kan ik het formaat van mijn beheerde exemplaar schalen?**

U kunt uw beheerde exemplaar schalen vanuit [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [Power shell](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure cli](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update) of [arm-sjablonen](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Kan ik mijn beheerde exemplaar verplaatsen van de ene regio naar een andere?**

Ja, dat kunt u. Zie [resources verplaatsen tussen regio's](../database/move-resources-across-regions.md)voor instructies.

**Hoe kan ik mijn beheerde exemplaar verwijderen?**

U kunt beheerde exemplaren verwijderen via Azure Portal, [Power shell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0), [Azure cli](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete) of [Resource Manager rest-api's](https://docs.microsoft.com/rest/api/sql/managedinstances/delete).

**Hoe lang duurt het om een instantie te maken of bij te werken of om een Data Base te herstellen?**

De verwachte tijd voor het maken van een nieuw beheerd exemplaar of het wijzigen van service lagen (vCores, opslag) is afhankelijk van verschillende factoren. Zie [beheer bewerkingen](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Naamconventies

**Kan een beheerd exemplaar dezelfde naam hebben als een SQL Server on-premises exemplaar?**

Het wijzigen van de naam van een beheerd exemplaar wordt niet ondersteund.

**Kan ik het voor voegsel van de DNS-zone wijzigen?**

Ja, de standaard DNS-zone van het beheerde exemplaar *. database.Windows.net* kan worden gewijzigd. 

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

  
## <a name="networking-requirements"></a>Netwerk vereisten 

**Wat zijn de huidige binnenkomende/uitgaande NSG-beperkingen op het subnet van het beheerde exemplaar?**

De vereiste NSG-en UDR-regels worden [hier](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)gedocumenteerd en automatisch ingesteld door de service.
Houd er rekening mee dat deze regels alleen de voor waarden hebben die we nodig hebben om de service te onderhouden. Als u verbinding wilt maken met een beheerd exemplaar en andere functies wilt gebruiken, moet u aanvullende, specifieke regels voor de functie opgeven die u wilt behouden.

**Hoe kan ik binnenkomende NSG-regels instellen voor beheer poorten?**

SQL Managed instance is verantwoordelijk voor het instellen van regels voor beheer poorten. Dit wordt bereikt via de functionaliteit met de naam [service-aided subnet-configuratie](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Dit is om te zorgen voor een ononderbroken stroom van beheer verkeer om te voldoen aan een SLA.

**Kan ik de bron-IP-adresbereiken ophalen die worden gebruikt voor het inkomende beheer verkeer?**

Ja. U kunt het verkeer dat afkomstig is van uw netwerk beveiligings groep analyseren door [Network Watcher flow logboeken te configureren](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**Kan ik NSG instellen om de toegang tot het gegevens eindpunt te beheren (poort 1433)?**

Ja. Nadat een beheerd exemplaar is ingericht, kunt u NSG instellen die de toegang tot de poort 1433 beheren. Het is raadzaam het IP-bereik zo veel mogelijk te beperken.

**Kan ik de NVA of on-premises firewall zo instellen dat het uitgaande beheer verkeer wordt gefilterd op basis van FQDN-namen?**

Nee. Dit kan om verschillende redenen niet worden ondersteund:
-   Routerings verkeer dat antwoord geeft op inkomende beheer aanvragen, is asymmetrisch en kan niet worden gebruikt.
-   Route ring van verkeer dat naar de opslag gaat, wordt beïnvloed door doorvoer beperkingen en latentie, zodat we de verwachte kwaliteit en beschik baarheid van de service niet kunnen leveren.
-   Op basis van de ervaring zijn deze configuraties gevoelig en niet-ondersteund.

**Kan ik de NVA of firewall instellen voor het uitgaande niet-beheer verkeer?**

Ja. De eenvoudigste manier om dit te doen is door 0/0-regel toe te voegen aan een UDR die is gekoppeld aan het subnet van het beheerde exemplaar om verkeer door te sturen via NVA.
 
**Hoeveel IP-adressen heb ik nodig voor een beheerd exemplaar?**

Het subnet moet voldoende beschik bare [IP-adressen](connectivity-architecture-overview.md#network-requirements)hebben. Zie de [vereiste subnet grootte en het bereik voor een beheerd exemplaar bepalen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet)om de VNet-subnet grootte voor het beheerde exemplaar van SQL te bepalen. 

**Wat gebeurt er als er onvoldoende IP-adressen zijn voor het uitvoeren van een update bewerking voor een instantie?**

Als er onvoldoende [IP-adressen](connectivity-architecture-overview.md#network-requirements) zijn in het subnet waar uw beheerde exemplaar is ingericht, moet u een nieuw subnet en een nieuw beheerd exemplaar maken. We raden ook aan dat het nieuwe subnet wordt gemaakt met meer IP-adressen die zijn toegewezen zodat toekomstige update bewerkingen voor komen dat er soort gelijke situaties zijn. Nadat het nieuwe exemplaar is ingericht, kunt u hand matig een back-up maken van gegevens en deze herstellen tussen de oude en nieuwe instanties of het [herstel punt in de tijd](point-in-time-restore.md?tabs=azure-powershell)van meerdere exemplaren uitvoeren.

**Heb ik een leeg subnet nodig om een beheerd exemplaar te maken?**

Nee. U kunt een leeg subnet of een subnet gebruiken dat al een beheerd exemplaar (en) bevat. 

**Kan ik het adres bereik van het subnet wijzigen?**

Niet als er beheerde exemplaren in zijn. Dit is een beperking van de Azure-netwerk infrastructuur. U mag alleen [extra adres ruimte toevoegen aan een leeg subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Kan ik mijn beheerde exemplaar verplaatsen naar een ander subnet?**

Nee. Dit is een huidig ontwerp beperking voor het beheerde exemplaar. U kunt echter een nieuw exemplaar in een ander subnet inrichten en hand matig back-ups maken en gegevens herstellen tussen het oude en het nieuwe exemplaar, of een [herstel punt voor meerdere tijdstippen](point-in-time-restore.md?tabs=azure-powershell)uitvoeren.

**Heb ik een leeg virtueel netwerk nodig om een beheerd exemplaar te maken?**

Dit is niet vereist. U kunt ofwel [een virtueel netwerk maken voor Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) of [een bestaand virtueel netwerk configureren voor Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Kan ik een beheerd exemplaar met andere services in een subnet plaatsen?**

Nee. Momenteel wordt het plaatsen van beheerde exemplaren in een subnet dat al andere bron typen bevat, niet ondersteund.


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


## <a name="dns"></a>DNS

**Kan ik een aangepaste DNS voor een SQL-beheerd exemplaar configureren?**

Ja. Zie [How to configure a Custom DNS for Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)(Engelstalig).

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


## <a name="security-and-database-encryption"></a>Beveiliging en database versleuteling

**Is de serverrol sysadmin beschikbaar voor SQL Managed instance?**

Ja, klanten kunnen aanmeldingen maken die lid zijn van de rol sysadmin.  Klanten die ervan uitgaan dat de sysadmin-bevoegdheid ook verantwoordelijk is voor de werking van het exemplaar, waardoor de SLA-toezeg ging nadelig kan worden beïnvloed. Zie [Azure AD-verificatie](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication)voor het toevoegen van aanmelding aan de sysadmin-server functie.

**Wordt Transparent Data Encryption ondersteund voor SQL Managed instance?**

Ja, Transparent Data Encryption wordt ondersteund voor SQL Managed instance. Zie [transparent Data Encryption voor SQL Managed instance](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)(Engelstalig) voor meer informatie.

**Kan ik gebruikmaken van het model ' uw eigen sleutel meenemen ' voor TDE?**

Ja, Azure Key Vault voor het BYOK-scenario is beschikbaar voor Azure SQL Managed instance. Zie [transparent Data Encryption met door de klant beheerde sleutel](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)voor meer informatie.

**Kan ik een versleutelde SQL Server-Data Base migreren?**

Ja, dat kunt u. Als u een versleutelde SQL Server-Data Base wilt migreren, moet u uw bestaande certificaten exporteren en importeren naar een beheerd exemplaar, vervolgens een volledige back-up van de data base maken en deze herstellen in een beheerd exemplaar. 

U kunt ook [Azure database Migration service](https://azure.microsoft.com/services/database-migration/) gebruiken om de versleutelde TDe-data bases te migreren.

**Hoe kan ik de rotatie van TDE-beveiliging voor een SQL Managed instance configureren?**

U kunt TDE-Protector voor een beheerd exemplaar draaien met behulp van Azure Cloud Shell. Zie [transparent Data Encryption in SQL Managed instance met uw eigen sleutel vanuit Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md)voor instructies.

**Kan ik mijn versleutelde data base herstellen naar een SQL Managed instance?**

Ja, u hoeft uw data base niet te ontsleutelen om deze te herstellen naar een SQL-beheerd exemplaar. U moet een certificaat/sleutel opgeven die wordt gebruikt als de versleutelings sleutel beveiliging op het bron systeem naar SQL Managed instance om gegevens van het versleutelde back-upbestand te kunnen lezen. Er zijn twee manieren waarop u dit kunt doen:

- *Upload certificaat beveiliging naar een beheerd exemplaar van SQL*. Dit kan alleen worden gedaan met behulp van Power shell. In het [voorbeeld script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) wordt het hele proces beschreven.
- De *asymmetrische sleutel beveiliging uploaden naar Azure Key Vault en naar de SQL-beheerde instantie verwijzen*. Deze benadering lijkt op het gebruik van uw BYOK-TDE-toepassing (your-own-Key) die ook Key Vault-integratie gebruikt om de versleutelings sleutel op te slaan. Als u de sleutel niet wilt gebruiken als een versleutelings sleutel beveiliging en alleen de sleutel beschikbaar wilt maken voor SQL Managed instance om versleutelde data bases te herstellen, volgt u de instructies voor het [instellen van BYOK TDe](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)en schakelt u het selectie vakje de **geselecteerde sleutel de standaard TDe-Protector maken**in.

Zodra u de versleutelings beveiliging beschikbaar maakt voor een SQL-beheerd exemplaar, kunt u door gaan met de standaard procedure voor het herstellen van data bases.

## <a name="purchasing-models-and-benefits"></a>Modellen en voor delen kopen

**Welke inkoop modellen zijn er beschikbaar voor SQL Managed instance?**

SQL Managed instance biedt [vCore-gebaseerd aankoop model](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Welke kosten voordelen zijn beschikbaar voor SQL Managed instance?**

U kunt kosten besparen met de voor delen van Azure SQL op de volgende manieren:
-   Maximaliseer bestaande investeringen in on-premises licenties en Bespaar tot 55 procent met [Azure Hybrid Benefit](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-   Leg een reserve ring toe voor reken resources en Bespaar tot 33 procent met een [gereserveerde instantie voordelen](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Combi neer dit met Azure Hybrid voordelen voor een besparing van Maxi maal 82 procent. 
-   Bespaar tot 55 procent tegenover lijst prijzen met de [prijs voordelen van Azure dev/test](https://azure.microsoft.com/pricing/dev-test/) , die kortings tarieven bieden voor uw lopende ontwikkelings-en test werkbelastingen.

**Wie komt in aanmerking voor het voor deel van een gereserveerde instantie?**

Als u in aanmerking wilt komen voor een gereserveerde instantie, moet uw abonnements type een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Zie voor [delen van gereserveerde instanties](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)voor meer informatie over reserve ringen. 

**Is het mogelijk om reserve ringen te annuleren, te ruilen of terug te betalen?**

U kunt reserve ringen annuleren, uitwisselen of terugbetalen met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) voor meer informatie.

## <a name="billing-for-managed-instance-and-backup-storage"></a>Facturering voor beheerd exemplaar en back-upopslag

**Wat zijn de prijs opties voor SQL Managed instance?**

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)als u de opties voor het Managed instance-exemplaar wilt verkennen.

**Hoe kan ik de facturerings kosten voor mijn beheerde exemplaar volgen?**

U kunt dit doen met behulp van de [Azure Cost Management-oplossing](https://docs.microsoft.com/azure/cost-management-billing/). Navigeer naar **abonnementen** in het [Azure Portal](https://portal.azure.com) en selecteer **kosten analyse**. 

Gebruik de optie **geaccumuleerde kosten** en filter vervolgens op het **resource type** als `microsoft.sql/managedinstances` .

**Hoeveel automatische back-ups worden kosten?**

U krijgt de gelijke hoeveelheid beschik bare opslag ruimte voor back-ups als de gereserveerde opslag ruimte die is aangeschaft, ongeacht de Bewaar periode van de back-up. Als het verbruik van de back-upopslag binnen de toegewezen vrije back-upopslag ruimte ligt, zullen automatische back-ups op het beheerde exemplaar geen extra kosten voor u hebben, dus gratis zijn. Als u het gebruik van back-upopslag boven de beschik bare ruimte overschrijdt, worden de kosten van $0,20 tot $0,24 per GB/maand in Amerikaanse regio's, of raadpleegt u de pagina met prijzen voor meer informatie over uw regio. Zie [back-upopslag verbruik wordt uitgelegd](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)voor meer informatie.

**Hoe kan ik de facturerings kosten voor mijn back-upopslags volgen?**

U kunt de kosten voor back-upopslag bewaken via Azure Portal. Zie [kosten bewaken voor automatische back-ups](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs)voor instructies. 

**Hoe kan ik mijn back-upopslagkosten op het beheerde exemplaar optimaliseren?**

Zie [fijnafstelling van nauw keurigheid van SQL Managed instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)voor meer informatie over het optimaliseren van de opslag kosten voor back-ups.

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

## <a name="azure-feedback-and-support"></a>Feedback en ondersteuning van Azure

**Waar kan ik mijn ideeën voor verbeteringen van SQL Managed instance verlaten?**

U kunt stemmen voor een nieuwe functie van een beheerd exemplaar of een nieuw idee voor verbetering nemen over stemmen op het [Feedback forum van SQL Managed instance](https://feedback.azure.com/forums/915676-sql-managed-instance). Op deze manier kunt u bijdragen aan de ontwikkeling van het product en ons helpen om onze mogelijke verbeteringen te bepalen.

**Hoe kan ik een ondersteunings aanvraag voor Azure maken?**

Zie [Azure-ondersteunings aanvraag maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie over het maken van een ondersteunings aanvraag voor Azure.

