---
title: Instantie groepen (preview-versie)
description: In dit artikel worden Azure SQL Database-instantie groepen (preview-versie) beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 8738d1ad54d3ab63d8d2efc939aa9daacbe91c13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810393"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Wat zijn SQL Database exemplaar groepen (preview)?

Exemplaar groepen zijn een nieuwe resource in Azure SQL Database die een handige en rendabele manier biedt om kleinere SQL-instanties op schaal te migreren naar de Cloud.

Met exemplaarpools kunt u rekenresources vooraf inrichten overeenkomstig uw totale migratievereisten. Vervolgens kunt u verschillende afzonderlijke beheerd exemplaren implementeren, tot uw vooraf ingerichte rekenniveau. Als u bijvoorbeeld 8 vCores vooraf inricht, kunt u twee vCore-en 4 vCore-instanties implementeren en vervolgens data bases migreren naar deze instanties. Voordat instantie groepen beschikbaar zijn, moeten kleinere en minder Compute-workloads vaak worden geconsolideerd in een groter beheerd exemplaar wanneer deze naar de cloud worden gemigreerd. De nood zaak om groepen data bases te migreren naar een grote instantie vereist meestal een zorgvuldige capaciteits planning en resource governance, extra veiligheids overwegingen en sommige extra gegevens consolidatie op het niveau van de instantie.

Daarnaast ondersteunen instantie groepen systeem eigen VNet-integratie zodat u meerdere exemplaar groepen en meerdere afzonderlijke instanties in hetzelfde subnet kunt implementeren.


## <a name="key-capabilities-of-instance-pools"></a>De belangrijkste mogelijkheden van instantie groepen

Exemplaar groepen bieden de volgende voor delen:

1. De mogelijkheid om 2 vCore-instanties te hosten. *\*alleen voor instanties in exemplaar groepen*.
2. Voorspel bare en snelle exemplaar implementatie tijd (Maxi maal 5 minuten).
3. Minimale toewijzing van IP-adressen.

In het volgende diagram ziet u een exemplaar groep met meerdere instanties die zijn geïmplementeerd in een subnet van een virtueel netwerk.

![exemplaar groep met meerdere exemplaren](./media/sql-database-instance-pools/instance-pools1.png)

Met instantie groepen kunnen meerdere exemplaren op dezelfde virtuele machine worden geïmplementeerd, waarbij de reken grootte van de virtuele machine is gebaseerd op het totale aantal vCores dat voor de pool is toegewezen. Met deze architectuur kan de virtuele machine in meerdere exemplaren worden *gepartitioneerd* . Dit kan elke ondersteunde grootte zijn, waaronder 2 vCores (2 vCore-instanties zijn alleen beschikbaar voor instanties in Pools).

Beheer bewerkingen voor instanties in een groep zijn veel sneller wanneer de pool voor het eerst wordt geïmplementeerd. Deze bewerkingen zijn sneller omdat de implementatie of uitbrei ding van een [virtueel cluster](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (een toegewezen set virtuele machines) geen deel uitmaakt van het inrichten van het beheerde exemplaar.

Omdat alle instanties in een groep dezelfde virtuele machine delen, is de totale IP-toewijzing niet afhankelijk van het aantal geïmplementeerde instanties, wat handig is voor implementatie in subnetten met een beperkt IP-bereik.

Elke pool heeft een vaste IP-toewijzing van slechts negen IP-adressen (exclusief de vijf IP-adressen in het subnet die zijn gereserveerd voor eigen behoeften). Zie [vereisten voor de grootte van het subnet voor afzonderlijke instanties](sql-database-managed-instance-determine-size-vnet-subnet.md)voor meer informatie.

## <a name="application-scenarios-for-instance-pools"></a>Toepassings scenario's voor exemplaar groepen

De volgende lijst bevat de belangrijkste gebruiks situaties waarbij exemplaar groepen moeten worden overwogen:

- Migratie van *een groep SQL-exemplaren* op hetzelfde moment, waarbij de meerderheid een kleiner formaat heeft (bijvoorbeeld 2 of 4 vCores).
- Scenario's waarin het *maken of schalen van voorspel bare en korte instanties* belang rijk is. Bijvoorbeeld implementatie van een nieuwe Tenant in een multi tenant SaaS-toepassings omgeving waarvoor mogelijkheden op exemplaar niveau zijn vereist.
- Scenario's waarbij een *vaste kosten* -of *bestedings limiet* van belang is. Bijvoorbeeld: het uitvoeren van gedeelde ontwikkel-of demo omgevingen van een vaste (of niet vaak veranderende) grootte, waarbij u regel matig beheerde instanties implementeert wanneer dat nodig is.
- Scenario's waarbij *minimale toewijzing van IP-adressen* in een VNet-subnet belang rijk is. Alle exemplaren in een groep delen een virtuele machine, dus het aantal toegewezen IP-adressen is lager dan in het geval van één exemplaar.


## <a name="architecture-of-instance-pools"></a>Architectuur van instantie groepen

Exemplaar groepen hebben een vergelijk bare architectuur voor reguliere beheerde instanties (*afzonderlijke instanties*). Voor de ondersteuning van [implementaties in azure Virtual Networks (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) en voor het afschermen en beveiligen van klanten, zijn exemplaar groepen ook gebaseerd op [virtuele clusters](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Virtuele clusters vertegenwoordigen een specifieke set geïsoleerde virtuele machines die zijn geïmplementeerd in het subnet van het virtuele netwerk van de klant.

Het belangrijkste verschil tussen de twee implementatie modellen is dat instantie groepen meerdere implementaties van SQL Server processen toestaan op hetzelfde knoop punt van de virtuele machine, die resources best rijken met behulp van [Windows-taak objecten](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), terwijl afzonderlijke instanties altijd op een knoop punt van een virtuele machine.

In het volgende diagram ziet u een exemplaar groep en twee afzonderlijke instanties die in hetzelfde subnet zijn geïmplementeerd en worden de belangrijkste details van de architectuur voor beide implementatie modellen geïllustreerd:

![exemplaar groep en twee afzonderlijke exemplaren](./media/sql-database-instance-pools/instance-pools2.png)

Elke instantie groep maakt onder andere een afzonderlijk virtueel cluster. Instanties binnen een groep en single instances die in hetzelfde subnet zijn geïmplementeerd, delen geen reken resources die zijn toegewezen aan SQL Server processen en gateway onderdelen. Dit zorgt voor een snelle voorspel baarheid.

## <a name="instance-pools-resource-limitations"></a>Resource beperkingen van instantie groepen

Er bestaan verschillende resourcebeperkingen met betrekking tot exemplaarpools en exemplaren binnen pools:

- Exemplaar groepen zijn alleen beschikbaar op GEN5-hardware.
- Instanties in een groep hebben toegewezen CPU en RAM, dus het geaggregeerde aantal vCores voor alle exemplaren moet kleiner zijn dan of gelijk zijn aan het aantal vCores dat aan de pool is toegewezen.
- Alle [limieten op exemplaar niveau](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) zijn van toepassing op instanties die in een groep zijn gemaakt.
- Naast limieten op exemplaar niveau zijn er ook twee limieten opgelegd *voor het niveau van de instantie groep*:
  - Totale opslag grootte per pool (8 TB).
  - Totaal aantal data bases per pool (100).

De totale opslag toewijzing en het aantal data bases voor alle exemplaren moet lager zijn dan of gelijk zijn aan de limieten die worden weer gegeven door exemplaar groepen.

- Exemplaar groepen ondersteunen 8, 16, 24, 32, 40, 64 en 80 vCores.
- Beheerde instanties in Pools ondersteunen 2, 4, 8, 16, 24, 32, 40, 64 en 80 vCores.
- Beheerde instanties in Pools ondersteunen opslag grootten tussen 32 GB en 8 TB, met uitzonde ring van:
  - 2 vCore-instanties ondersteunen grootten tussen 32 GB en 640 GB
  - 4 vCore-instanties ondersteunen de omvang van 32 GB en 2 TB

De [service tier-eigenschap](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) is gekoppeld aan de resource groep van de instantie, zodat alle exemplaren in een pool dezelfde servicelaag moeten zijn als de servicelaag van de groep. Op dit moment is alleen de servicelaag Algemeen beschikbaar (Zie de volgende sectie over beperkingen in de huidige preview).

### <a name="public-preview-limitations"></a>Beperkingen voor openbare preview

De open bare preview heeft de volgende beperkingen:

- Momenteel is alleen de servicelaag Algemeen beschikbaar.
- Exemplaar groepen kunnen tijdens de open bare preview-periode niet worden geschaald, dus de planning van de capaciteit voordat de implementatie belang rijk is.
- Azure Portal ondersteuning voor het maken en configureren van een exemplaar groep is nog niet beschikbaar. Alle bewerkingen voor exemplaar groepen worden alleen ondersteund via Power shell. De initiële implementatie van een exemplaar in een vooraf gemaakte groep wordt ook alleen ondersteund via Power shell. Wanneer beheerde exemplaren eenmaal in een groep zijn geïmplementeerd, kunnen ze worden bijgewerkt met behulp van de Azure Portal.
- Beheerde instanties die buiten de groep zijn gemaakt, kunnen niet worden verplaatst naar een bestaande groep en instanties die in een groep zijn gemaakt, kunnen niet worden verplaatst naar een enkel exemplaar of een andere groep.
- Prijzen voor gereserveerde instanties (licentie inbegrepen of met Azure Hybrid Benefit) zijn niet beschikbaar.

## <a name="sql-features-supported"></a>Ondersteunde SQL-functies

Instanties die zijn gemaakt in Pools ondersteunen dezelfde [compatibiliteits niveaus en functies die worden ondersteund in single Managed instances](sql-database-managed-instance.md#sql-features-supported).

Elk beheerd exemplaar dat in een groep wordt geïmplementeerd, heeft een afzonderlijk exemplaar van SQL-Agent.

Optionele functies of functies waarvoor u moet kiezen welke specifieke waarden moeten worden gekozen (zoals sortering op exemplaar niveau, tijd zone, openbaar eind punt voor gegevens verkeer, failover-groepen) worden geconfigureerd op het niveau van de instantie en kunnen verschillen voor elk exemplaar van een groep.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Hoewel beheerde instanties binnen Pools toegewezen vCore en RAM-geheugen hebben, delen ze lokale schijf (voor gebruik van tempdb) en netwerk bronnen. Het is niet waarschijnlijk, maar het is wel mogelijk om te zien wat het effect van de ruis op het *lawaai* is als meerdere instanties in de groep tegelijkertijd een hoog Resource verbruik hebben. Als u dit gedrag ziet, kunt u deze instanties implementeren in een grotere groep of als afzonderlijke exemplaren.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Omdat instanties die in een groep zijn geïmplementeerd, dezelfde virtuele machine delen, kunt u overwegen om functies uit te scha kelen die hogere beveiligings Risico's veroorzaken, of om de toegangs machtigingen voor deze functies stevig te controleren. Bijvoorbeeld CLR-integratie, systeem eigen back-up en herstel, data base-e-mail, enzovoort.

## <a name="instance-pool-support-requests"></a>Ondersteunings aanvragen voor exemplaar groepen

Ondersteunings aanvragen voor exemplaar groepen maken en beheren in de [Azure Portal](https://portal.azure.com).

Als u problemen ondervindt met betrekking tot de implementatie van een exemplaar groep (maken of verwijderen), moet u ervoor zorgen dat u **instantie groepen** opgeeft in het veld **probleem subtype** .

![ondersteunings aanvraag voor groepen van instanties](./media/sql-database-instance-pools/support-request.png)

Als u problemen ondervindt met betrekking tot single instances of data bases in een groep, moet u een regel matig ondersteunings ticket maken voor Azure SQL Database Managed instances.

Als u grotere implementaties van beheerde exemplaren wilt maken (met of zonder exemplaar groepen), moet u mogelijk een groter regionaal quotum verkrijgen. Gebruik de [standaard Managed instance-procedure voor het aanvragen van een groter quotum](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance), maar houd er rekening mee dat als u exemplaar groepen gebruikt, het totale vCore-verbruik *op groeps niveau* wordt vergeleken met uw quotum om te bepalen of u het is toegestaan om nieuwe resources te maken zonder het quotum verder te verhogen.

## <a name="instance-pool-billing"></a>Facturering van exemplaar groep

Met instantie groepen kunnen reken kracht en opslag onafhankelijk worden geschaald. Klanten betalen voor de reken kracht die is gekoppeld aan de pool resource gemeten in vCores, en de opslag die is gekoppeld aan elk exemplaar dat wordt gemeten in gigabytes (de eerste 32 GB is voor elk exemplaar gratis).

de vCore prijs voor een pool wordt in rekening gebracht, ongeacht het aantal exemplaren dat in die groep is geïmplementeerd.

Voor de berekenings prijs (gemeten in vCores) zijn twee prijs opties beschikbaar:

  1. *Inbegrepen licentie*: pas bestaande SQL Server licenties met Software Assurance toe.
  2. *Azure Hybrid Benefit*: een gereduceerde prijs met inbegrip van Azure Hybrid Benefit voor SQL Server. Klanten kunnen zich op deze prijs aanmelden door gebruik te maken van hun bestaande SQL Server licenties met Software Assurance. Zie [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)voor Beschik baarheid en andere informatie.

Het is niet mogelijk om verschillende prijs opties in te stellen voor afzonderlijke exemplaren in een pool. Alle exemplaren in de bovenliggende groep moeten de prijs van de licentie of de Azure Hybrid Benefit prijs zijn. Het licentie model voor de groep kan worden gewijzigd nadat de groep is gemaakt.

> [!IMPORTANT]
> Als u een licentie model opgeeft voor het exemplaar dat verschilt van de pool, wordt de prijs van de groep gebruikt en wordt de waarde voor het exemplaar niveau genegeerd.

Als u exemplaar groepen maakt op [abonnementen die in aanmerking komen voor het voor deel van dev-test](https://azure.microsoft.com/pricing/dev-test/), ontvangt u automatisch kortings tarieven van maxi maal 55 procent op Azure SQL Managed instance.

Raadpleeg de sectie *exemplaar groepen* op de [pagina met prijzen voor beheerde exemplaren](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor volledige details over de prijzen van exemplaar groepen.

## <a name="next-steps"></a>Volgende stappen

- Als u aan de slag wilt gaan met exemplaar groepen, raadpleegt u de [hand leiding SQL database-instantie groepen](sql-database-instance-pools-how-to.md).
- Zie [Quick Start Guide (Engelstalig](sql-database-managed-instance-get-started.md)) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [algemene SQL-functies](sql-database-features.md)voor een lijst met functies en vergelijkingen.
- Zie [vnet-configuratie voor beheerde exemplaren](sql-database-managed-instance-connectivity-architecture.md)voor meer informatie over vnet-configuratie.
- Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor een Snelstartgids die een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-upbestand.
- Zie [migratie van beheerde exemplaren met](../dms/tutorial-sql-server-to-managed-instance.md)behulp van DMS voor een zelf studie met behulp van de Azure database MIGRATION service (DMS) voor migratie.
- Zie [Azure SQL database bewaken met behulp van Azure SQL-analyse](../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van beheerde exemplaar database prestaties met ingebouwde probleemoplossings informatie.
- Zie [prijzen van beheerde exemplaren SQL database](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.