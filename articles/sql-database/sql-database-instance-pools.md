---
title: Instantiegroepen (voorbeeld)
description: In dit artikel worden de instantiegroepen azure SQL Database beschreven (voorbeeld).
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
ms.openlocfilehash: 0938fbe94cb0d1e6dae3dcb84950a11f90dd9db8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878150"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Wat zijn SQL Database-instantiegroepen (preview)?

Instantiegroepen zijn een nieuwe bron in Azure SQL Database die een handige en kostenefficiënte manier biedt om kleinere SQL-exemplaren op schaal naar de cloud te migreren.

Met exemplaarpools kunt u rekenresources vooraf inrichten overeenkomstig uw totale migratievereisten. Vervolgens kunt u verschillende afzonderlijke beheerd exemplaren implementeren, tot uw vooraf ingerichte rekenniveau. Als u bijvoorbeeld 8 vCores vooraf insdient, u twee vCore- en één vCore-exemplaren van 2 vCore implementeren en vervolgens databases naar deze exemplaren migreren. Voordat instantiegroepen beschikbaar zijn, moeten kleinere en minder rekenintensieve workloads vaak worden geconsolideerd in een groter beheerde instantie wanneer ze naar de cloud worden gemigreerd. De noodzaak om groepen databases te migreren naar een grote instantie vereiste doorgaans een zorgvuldige capaciteitsplanning en resourcegovernance, extra beveiligingsoverwegingen en wat extra gegevensconsolidatiewerk op instantieniveau.

Daarnaast ondersteunen instantiegroepen native VNet-integratie, zodat u meerdere instantiegroepen en meerdere afzonderlijke exemplaren in hetzelfde subnet implementeren.


## <a name="key-capabilities-of-instance-pools"></a>Belangrijkste mogelijkheden van instantiegroepen

Instantiegroepen bieden de volgende voordelen:

1. Mogelijkheid om 2 vCore-exemplaren te hosten. *Alleen voor instanties in instantiegroepen . \**
2. Voorspelbare en snelle implementatietijd van bijvoorbeelden (tot 5 minuten).
3. Minimale IP-adrestoewijzing.

In het volgende diagram wordt een instantiegroep geïllustreerd met meerdere exemplaren die zijn geïmplementeerd in een subnet van een virtueel netwerk.

![instantiegroep met meerdere exemplaren](./media/sql-database-instance-pools/instance-pools1.png)

Instantiegroepen maken het mogelijk om meerdere exemplaren te implementeren op dezelfde virtuele machine waarbij de rekengrootte van de virtuele machine is gebaseerd op het totale aantal vCores dat voor de groep is toegewezen. Deze architectuur maakt het mogelijk om de virtuele machine in meerdere exemplaren te *verdelen,* wat elke ondersteunde grootte kan zijn, waaronder 2 vCores (2 vCore-exemplaren zijn alleen beschikbaar voor exemplaren in groepen).

Beheerbewerkingen op instanties in een groep zijn veel sneller zodra de groep in eerste instantie is geïmplementeerd. Deze bewerkingen zijn sneller omdat implementatie of uitbreiding van een [virtueel cluster](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (speciale set virtuele machines) geen deel uitmaakt van het inrichten van de beheerde instantie.

Omdat alle exemplaren in een groep dezelfde virtuele machine delen, is de totale IP-toewijzing niet afhankelijk van het aantal geïmplementeerde exemplaren, wat handig is voor implementatie in subnetten met een beperkt IP-bereik.

Elke groep heeft een vaste IP-toewijzing van slechts negen IP-adressen (exclusief de vijf IP-adressen in het subnet die zijn gereserveerd voor zijn eigen behoeften). Zie [subnetgroottevereisten voor afzonderlijke instanties voor](sql-database-managed-instance-determine-size-vnet-subnet.md)meer informatie .

## <a name="application-scenarios-for-instance-pools"></a>Toepassingsscenario's voor bijvoorbeeld groepen

In de volgende lijst worden de belangrijkste gebruiksvoorbeelden weergegeven waarin instantiegroepen in aanmerking moeten worden genomen:

- Migratie van *een groep SQL-exemplaren* op hetzelfde moment, waarbij de meerderheid een kleiner formaat is (bijvoorbeeld 2 of 4 vCores).
- Scenario's waarbij *voorspelbare en korte instantie creatie of schaling* belangrijk is. Bijvoorbeeld de implementatie van een nieuwe tenant in een SaaS-toepassingsomgeving met meerdere tenants waarvoor mogelijkheden op instantieniveau vereist zijn.
- Scenario's waarbij een *vaste kosten-* of *bestedingslimiet* belangrijk is. Bijvoorbeeld het uitvoeren van gedeelde dev-test- of demoomgevingen met een vaste (of zelden veranderende) grootte, waarbij u periodiek beheerde exemplaren implementeert wanneer dat nodig is.
- Scenario's waarbij *minimale IP-adrestoewijzing* in een VNet-subnet belangrijk is. Alle exemplaren in een groep delen een virtuele machine, dus het aantal toegewezen IP-adressen is lager dan in het geval van afzonderlijke exemplaren.


## <a name="architecture-of-instance-pools"></a>Architectuur van instantiepools

Instantiegroepen hebben een vergelijkbare architectuur als reguliere beheerde exemplaren *(afzonderlijke exemplaren).* Om [implementaties binnen Azure Virtual Networks (VNets)](../virtual-network/virtual-network-for-azure-services.md) te ondersteunen en klanten isolatie en beveiliging te bieden, zijn bijvoorbeeld groepen ook afhankelijk van [virtuele clusters.](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) Virtuele clusters vertegenwoordigen een speciale set van geïsoleerde virtuele machines geïmplementeerd in het virtuele netwerk subnet van de klant.

Het belangrijkste verschil tussen de twee implementatiemodellen is dat instantiegroepen meerdere SQL Server-procesimplementaties toestaan op hetzelfde virtuele machineknooppunt, die worden beheerd met [Windows-taakobjecten,](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)terwijl afzonderlijke exemplaren altijd alleen zijn op een knooppunt voor virtuele machines.

In het volgende diagram ziet u een instantiegroep en twee afzonderlijke instanties die in hetzelfde subnet zijn geïmplementeerd en worden de belangrijkste architectonische details voor beide implementatiemodellen weergegeven:

![instantiegroep en twee afzonderlijke instanties](./media/sql-database-instance-pools/instance-pools2.png)

Elke instantiegroep maakt er een apart virtueel cluster onder. Exemplaren binnen een groep en afzonderlijke instanties die in hetzelfde subnet worden geïmplementeerd, delen geen rekenresources die zijn toegewezen aan SQL Server-processen en gatewaycomponenten, dit zorgt voor voorspelbaarheid van de prestaties.

## <a name="instance-pools-resource-limitations"></a>Resourcebeperkingen voor instantiegroepen

Er bestaan verschillende resourcebeperkingen met betrekking tot exemplaarpools en exemplaren binnen pools:

- Instantiepools zijn alleen beschikbaar op Gen5-hardware.
- Exemplaren binnen een groep hebben speciale CPU en RAM, dus het geaggregeerde aantal vCores in alle exemplaren moet kleiner zijn dan of gelijk zijn aan het aantal vCores dat aan de groep is toegewezen.
- Alle [limieten voor instantieniveaus](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) zijn van toepassing op instanties die in een groep zijn gemaakt.
- Naast de limieten op instantieniveau zijn er ook twee limieten opgelegd *op het niveau van de instantiepool:*
  - Totale opslaggrootte per groep (8 TB).
  - Totaal aantal databases per groep (100).

De totale opslagtoewijzing en het aantal databases in alle instanties moeten lager of gelijk zijn aan de limieten die door instantiegroepen worden blootgesteld.

- Instance pools ondersteunen 8, 16, 24, 32, 40, 64 en 80 vCores.
- Beheerde exemplaren in pools ondersteunen 2, 4, 8, 16, 24, 32, 40, 64 en 80 vCores.
- Beheerde exemplaren in pools ondersteunen opslagformaten tussen 32 GB en 8 TB, behalve:
  - 2 vCore-exemplaren ondersteunen formaten tussen 32 GB en 640 GB
  - 4 vCore-exemplaren ondersteunen formaten tussen 32 GB en 2 TB

De [eigenschap servicelaag](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) is gekoppeld aan de instantiegroepbron, zodat alle instanties in een groep dezelfde servicelaag moeten hebben als de servicelaag van de groep. Op dit moment is alleen de servicelaag voor algemeen gebruik beschikbaar (zie de volgende sectie over beperkingen in het huidige voorbeeld).

### <a name="public-preview-limitations"></a>Beperkingen voor openbare preview

De openbare preview heeft de volgende beperkingen:

- Momenteel is alleen de servicelaag voor algemeen gebruik beschikbaar.
- Instantiegroepen kunnen niet worden geschaald tijdens de openbare preview, zodat een zorgvuldige capaciteitsplanning voordat de implementatie belangrijk is.
- Azure portal ondersteuning voor bijvoorbeeld pool creatie en configuratie is nog niet beschikbaar. Alle bewerkingen op instantiepools worden alleen via PowerShell ondersteund. De implementatie van initiële instanties in een vooraf gemaakte groep wordt ook alleen via PowerShell ondersteund. Eenmaal geïmplementeerd in een groep kunnen beheerde exemplaren worden bijgewerkt met behulp van de Azure-portal.
- Beheerde exemplaren die buiten de groep zijn gemaakt, kunnen niet worden verplaatst naar een bestaande groep en instanties die binnen een groep zijn gemaakt, kunnen niet als één instantie of naar een andere groep worden verplaatst.
- Gereserveerde instantieprijzen (inclusief licentie of met Azure Hybrid Benefit) zijn niet beschikbaar.

## <a name="sql-features-supported"></a>SQL-functies ondersteund

Instanties die in groepen zijn gemaakt, ondersteunen dezelfde [compatibiliteitsniveaus en functies die worden ondersteund in afzonderlijke beheerde instanties](sql-database-managed-instance.md#sql-features-supported).

Elke beheerde instantie die in een groep wordt geïmplementeerd, heeft een afzonderlijke instantie van SQL Agent.

Optionele functies of functies waarvoor u specifieke waarden moet kiezen (zoals collatie op instantieniveau, tijdzone, openbaar eindpunt voor gegevensverkeer, failovergroepen) zijn geconfigureerd op instantieniveau en kunnen voor elke instantie in een groep verschillend zijn.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Hoewel beheerde exemplaren binnen pools wel speciale vCore en RAM hebben, delen ze lokale schijf (voor tempdb-gebruik) en netwerkbronnen. Het is niet waarschijnlijk, maar het is mogelijk om het *luidruchtige neighbor-effect* te ervaren als meerdere instanties in de groep tegelijkertijd een hoog resourceverbruik hebben. Als u dit gedrag waarneemt, u overwegen deze exemplaren te implementeren in een grotere groep of als afzonderlijke exemplaren.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Omdat instanties die in een groep worden geïmplementeerd dezelfde virtuele machine delen, u overwegen functies uit te schakelen die hogere beveiligingsrisico's met zich meebrengen of om de toegangsmachtigingen voor deze functies stevig te beheren. Bijvoorbeeld CLR-integratie, native back-up en herstel, database e-mail, enz.

## <a name="instance-pool-support-requests"></a>Ondersteuningsaanvragen voor instantiegroep

Ondersteuningsaanvragen maken en beheren voor bijvoorbeeld groepen in de [Azure-portal.](https://portal.azure.com)

Als u problemen ondervindt in verband met de implementatie van instantiegroepen (maken of verwijderen), moet u **instantiegroepen** opgeven in het **subtype Probleem.**

![Ondersteuningsaanvraag voor instantiegroepen](./media/sql-database-instance-pools/support-request.png)

Als u problemen ondervindt met betrekking tot afzonderlijke exemplaren of databases in een groep, moet u een regelmatig ondersteuningsticket maken voor beheerde exemplaren van Azure SQL Database.

Als u grotere beheerde instantieimplementaties wilt maken (met of zonder instantiegroepen), moet u mogelijk een groter regionaal quotum verkrijgen. Zie [Quotaverhogingen voor Azure SQL Database aanvragen voor](quota-increase-request.md)meer informatie. Houd er rekening mee dat als u instantiegroepen gebruikt, de implementatielogica het totale vCore-verbruik *op poolsniveau* vergelijkt met uw quotum om te bepalen of u nieuwe resources mag maken zonder uw quotum verder te verhogen.

## <a name="instance-pool-billing"></a>Facturering van instantiespool

Instantiegroepen maken het mogelijk om compute en storage onafhankelijk te schalen. Klanten betalen voor compute in verband met de poolbron gemeten in vCores, en opslag die is gekoppeld aan elke instantie gemeten in gigabytes (de eerste 32 GB zijn gratis voor elke instantie).

VCore-prijs voor een groep wordt in rekening gebracht, ongeacht het aantal exemplaren dat in die groep wordt geïmplementeerd.

Voor de Compute-prijs (gemeten in vCores) zijn twee prijsopties beschikbaar:

  1. *Licentie inbegrepen*: Prijs van SQL-licenties is inbegrepen. Dit is voor de klanten die ervoor kiezen om bestaande SQL Server-licenties niet toe te passen met Software Assurance.
  2. *Azure Hybrid Benefit:* een gereduceerde prijs die Azure Hybrid Benefit voor SQL Server omvat. Klanten kunnen zich met Software Assurance aanmelden voor deze prijs door hun bestaande SQL Server-licenties te gebruiken. Zie [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)voor geschiktheid en andere details.

Het instellen van verschillende prijsopties is niet mogelijk voor afzonderlijke exemplaren in een groep. Alle exemplaren in de bovenliggende groep moeten een licentieprijs of een Azure Hybrid Benefit-prijs hebben. Het licentiemodel voor het zwembad kan worden gewijzigd nadat de pool is gemaakt.

> [!IMPORTANT]
> Als u een licentiemodel opgeeft voor de instantie die anders is dan in de groep, wordt de poolprijs gebruikt en wordt de waarde van het instantieniveau genegeerd.

Als u instantiegroepen maakt voor [abonnementen die in aanmerking komen voor dev-testvoordeel,](https://azure.microsoft.com/pricing/dev-test/)ontvangt u automatisch kortingstarieven tot 55 procent op azure SQL-beheerde instantie.

Raadpleeg de sectie *instantiepools* op de [pagina beheerde instantieprijzen](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor meer informatie over de prijzen van instantiegroepen .

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database-instantiegroepen how-to-guide](sql-database-instance-pools-how-to.md)om aan de slag te gaan met instantiepools.
- Zie [Quickstart-handleiding](sql-database-managed-instance-get-started.md)voor meer informatie over het maken van uw eerste beheerde instantie.
- Zie [ALGEMENE SQL-functies voor](sql-database-features.md)een lijst met functies en vergelijkingsfuncties .
- Zie [VNet-configuratie beheerde instantie voor](sql-database-managed-instance-connectivity-architecture.md)meer informatie over vNet-configuratie.
- Zie [een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een snellestart waarmee een beheerde instantie wordt gemaakt en een database wordt hersteld uit een back-upbestand.
- Zie [Beheerde instantiemigratie met DMS](../dms/tutorial-sql-server-to-managed-instance.md)voor een zelfstudie met de Azure Database Migration Service (DMS) voor migratie.
- Zie Azure SQL Database met [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)controleren voor geavanceerde bewaking van beheerde instantiedatabaseprestaties met ingebouwde informatie over probleemoplossing.
- Zie [SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijsinformatie voor prijsinformatie .
