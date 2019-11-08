---
title: Resources verplaatsen naar een andere regio
description: Meer informatie over het verplaatsen van uw Azure SQL Database, een elastische Azure SQL-groep of een Azure SQL Managed instance naar een andere regio.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821434"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Azure SQL-resources verplaatsen naar een andere regio

In dit artikel leert u een algemene werk stroom voor het verplaatsen van uw Azure SQL Database één data base, een elastische pool en een beheerd exemplaar naar een nieuwe regio. 

## <a name="overview"></a>Overzicht

Er zijn verschillende scenario's waarin u uw bestaande Azure SQL-resources wilt verplaatsen van de ene regio naar een andere. U breidt uw bedrijf bijvoorbeeld uit naar een nieuwe regio en wilt deze optimaliseren voor de nieuwe klanten basis. U moet de bewerkingen ook verplaatsen naar een andere regio om redenen van naleving. Of Azure heeft een gloed nieuwe regio uitgebracht die een betere nabijheid biedt en de klant ervaring verbetert.  

Dit artikel bevat een algemene werk stroom voor het verplaatsen van resources naar een andere regio. De werk stroom bestaat uit de volgende stappen: 

- Controleer de vereisten voor het verplaatsen 
- Voorbereiden om de resources in het bereik te verplaatsen
- Het voorbereidings proces bewaken
- Het verplaatsings proces testen
- De daad werkelijke verplaatsing initiëren 
- De resources uit de bron regio verwijderen 


> [!NOTE]
> Dit artikel is van toepassing op migraties in de open bare Azure-Cloud of binnen dezelfde soevereine Cloud. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Eén data base verplaatsen

### <a name="verify-prerequisites"></a>Vereisten verifiëren 

1. Maak een logische doel server voor elke bron server. 
1. Configureer de firewall met de juiste uitzonde ringen met behulp van [Power shell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configureer de logische servers met de juiste aanmeldingen. Als u niet de abonnements beheerder of SQL Server-beheerder bent, kunt u met de beheerder samen werken om de benodigde machtigingen toe te wijzen. Zie [Azure SQL database-beveiliging beheren na nood herstel](sql-database-geo-replication-security-config.md)voor meer informatie. 
1. Als uw data bases zijn versleuteld met TDE en uw eigen versleutelings sleutel gebruiken in azure Key kluis, moet u ervoor zorgen dat het juiste versleutelings materiaal is ingericht in de doel regio's. Zie [Azure SQL transparent Data Encryption met door de klant beheerde sleutels in azure Key Vault](transparent-data-encryption-byok-azure-sql.md) voor meer informatie.
1. Als controle op database niveau is ingeschakeld, schakelt u deze optie uit en schakelt u in plaats daarvan controle op server niveau in. Na de failover moet het verkeer op database niveau worden gecontroleerd. Dit is niet gewenst of mogelijk na de verplaatsing. 
1. Voor audits op server niveau, moet u het volgende doen:
   - De opslag container, Log Analytics of Event Hub met de bestaande audit Logboeken wordt verplaatst naar de doel regio. 
   - Controle is geconfigureerd op de doel server. Zie [aan de slag met SQL database auditing](sql-database-auditing.md)voor meer informatie. 
1. Als uw exemplaar een op lange termijn Bewaar beleid (LTR) heeft, blijven de bestaande LTR-back-ups gekoppeld aan de huidige server. Omdat de doel server afwijkt, kunt u toegang krijgen tot de oudere LTR-back-ups in de bron regio met behulp van de bron server, zelfs als de server wordt verwijderd. 

  > [!NOTE]
  > Dit is onvoldoende om tussen de soevereine Cloud en een open bare regio te verplaatsen. Bij een dergelijke migratie moet u de LTR-back-ups verplaatsen naar de doel server, die momenteel niet wordt ondersteund. 

### <a name="prepare-resources"></a>Resources voorbereiden

1. Maak een [failovergroep](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) tussen de logische server van de bron naar de logische server van het doel.  
1. Voeg de data bases toe die u wilt verplaatsen naar de failovergroep. 
    - Replicatie van alle toegevoegde data bases wordt automatisch gestart. Zie [Aanbevolen procedures voor het gebruik van failover-groepen met afzonderlijke data bases](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)voor meer informatie. 
 
### <a name="monitor-the-preparation-process"></a>Het voorbereidings proces bewaken

U kunt periodiek [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) aanroepen om de replicatie van uw data bases van de bron naar het doel te bewaken. Het uitvoer object van `Get-AzSqlDatabaseFailoverGroup` bevat een eigenschap voor de **Replication State**: 
   - **Replication State = 2** (CATCH_UP) geeft aan dat de data base is gesynchroniseerd en veilig kan worden uitgevoerd. 
   - **Replication State = 0** (seeding) geeft aan dat de data base nog niet is geseedd en een poging tot failover mislukt. 

### <a name="test-synchronization"></a>Synchronisatie testen

Zodra **Replication State** is `2`, maakt u verbinding met elke Data Base of subset van data bases met behulp van het secundaire eind punt `<fog-name>.secondary.database.windows.net` en voert u een query uit op de data bases om de connectiviteit, de juiste beveiligings configuratie en gegevens replicatie te garanderen. 

### <a name="initiate-the-move"></a>De verplaatsing initiëren

1. Maak verbinding met de doel server met behulp van het secundaire eind punt `<fog-name>.secondary.database.windows.net`.
1. Gebruik [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) om te scha kelen naar het secundaire beheerde exemplaar als primair met volledige synchronisatie. Deze bewerking kan slagen of worden teruggezet. 
1. Controleer of de opdracht is voltooid met behulp van `nslook up <fog-name>.secondary.database.windows.net` om te controleren of de DNS CNAME-vermelding verwijst naar het IP-adres van de doel regio. Als de switch-opdracht mislukt, wordt de CNAME niet bijgewerkt. 

### <a name="remove-the-source-databases"></a>De bron databases verwijderen

Zodra de verplaatsing is voltooid, verwijdert u de resources in de bron regio om onnodige kosten te voor komen. 

1. Verwijder de failover-groep met [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Verwijder elke bron database met behulp van [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) voor elk van de data bases op de bron server. Hierdoor worden koppelingen met geo-replicatie automatisch beëindigd. 
1. Verwijder de bron server met behulp van [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Verwijder de sleutel kluis, Controleer opslag containers, Event Hub, AAD-exemplaar en andere afhankelijke resources, zodat deze niet meer in rekening worden gebracht. 

## <a name="move-elastic-pools"></a>Elastische Pools verplaatsen

### <a name="verify-prerequisites"></a>Vereisten verifiëren 

1. Maak een logische doel server voor elke bron server. 
1. Configureer de firewall met de juiste uitzonde ringen met behulp van [Power shell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configureer de logische servers met de juiste aanmeldingen. Als u niet de abonnements beheerder of SQL Server-beheerder bent, kunt u met de beheerder samen werken om de benodigde machtigingen toe te wijzen. Zie [Azure SQL database-beveiliging beheren na nood herstel](sql-database-geo-replication-security-config.md)voor meer informatie. 
1. Als uw data bases zijn versleuteld met TDE en uw eigen versleutelings sleutel gebruiken in azure Key kluis, moet u ervoor zorgen dat het juiste versleutelings materiaal is ingericht in de doel regio.
1. Maak een elastische doel groep voor elke elastische bron groep en zorg ervoor dat de groep wordt gemaakt in dezelfde servicelaag, met dezelfde naam en dezelfde grootte. 
1. Als een controle op database niveau is ingeschakeld, schakelt u deze uit en schakelt u in plaats daarvan controle op server niveau in. Na een failover is voor controle op database niveau een cross-Region verkeer vereist, dat niet gewenst is of mogelijk na de verplaatsing. 
1. Voor audits op server niveau, moet u het volgende doen:
    - De opslag container, Log Analytics of Event Hub met de bestaande audit Logboeken wordt verplaatst naar de doel regio.
    - De controle configuratie is geconfigureerd op de doel server. Zie [SQL database auditing](sql-database-auditing.md)voor meer informatie.
1. Als uw exemplaar een op lange termijn Bewaar beleid (LTR) heeft, blijven de bestaande LTR-back-ups gekoppeld aan de huidige server. Omdat de doel server afwijkt, kunt u toegang krijgen tot de oudere LTR-back-ups in de bron regio met behulp van de bron server, zelfs als de server wordt verwijderd. 

  > [!NOTE]
  > Dit is onvoldoende om tussen de soevereine Cloud en een open bare regio te verplaatsen. Bij een dergelijke migratie moet u de LTR-back-ups verplaatsen naar de doel server, die momenteel niet wordt ondersteund. 

### <a name="prepare-to-move"></a>Voorbereiden om te verplaatsen
 
1.  Maak een afzonderlijke [failover-groep](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) tussen elke elastische pool op de logische bron server en de elastische groep op de doel server. 
1.  Voeg alle data bases in de groep toe aan de failovergroep. 
    - De replicatie van de toegevoegde data bases wordt automatisch gestart. Zie [Aanbevolen procedures voor failover-groepen met elastische Pools](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)voor meer informatie. 

  > [!NOTE]
  > Hoewel het mogelijk is om een failovergroep te maken die meerdere elastische Pools bevat, wordt u ten zeerste aangeraden een afzonderlijke failovergroep voor elke groep te maken. Als u een groot aantal data bases hebt voor meerdere elastische Pools die u moet verplaatsen, kunt u de voorbereidende stappen parallel uitvoeren en vervolgens de stap parallel verplaatsen starten. Dit proces wordt beter geschaald en neemt minder tijd in beslag ten opzichte van meerdere elastische Pools in dezelfde failovergroep. 

### <a name="monitor-the-preparation-process"></a>Het voorbereidings proces bewaken

U kunt periodiek [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) aanroepen om de replicatie van uw data bases van de bron naar het doel te bewaken. Het uitvoer object van `Get-AzSqlDatabaseFailoverGroup` bevat een eigenschap voor de **Replication State**: 
   - **Replication State = 2** (CATCH_UP) geeft aan dat de data base is gesynchroniseerd en veilig kan worden uitgevoerd. 
   - **Replication State = 0** (seeding) geeft aan dat de data base nog niet is geseedd en een poging tot failover mislukt. 

### <a name="test-synchronization"></a>Synchronisatie testen
 
Zodra **Replication State** is `2`, maakt u verbinding met elke Data Base of subset van data bases met behulp van het secundaire eind punt `<fog-name>.secondary.database.windows.net` en voert u een query uit op de data bases om de connectiviteit, de juiste beveiligings configuratie en gegevens replicatie te garanderen. 

### <a name="initiate-the-move"></a>De verplaatsing initiëren
 
1. Maak verbinding met de doel server met behulp van het secundaire eind punt `<fog-name>.secondary.database.windows.net`.
1. Gebruik [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) om te scha kelen naar het secundaire beheerde exemplaar als primair met volledige synchronisatie. Deze bewerking kan slagen of worden teruggezet. 
1. Controleer of de opdracht is voltooid met behulp van `nslook up <fog-name>.secondary.database.windows.net` om te controleren of de DNS CNAME-vermelding verwijst naar het IP-adres van de doel regio. Als de switch-opdracht mislukt, wordt de CNAME niet bijgewerkt. 

### <a name="remove-the-source-elastic-pools"></a>De elastische bron Pools verwijderen
 
Zodra de verplaatsing is voltooid, verwijdert u de resources in de bron regio om onnodige kosten te voor komen. 

1. Verwijder de failover-groep met [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Verwijder elke elastische bron groep op de bron server met behulp van [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Verwijder de bron server met behulp van [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Verwijder de sleutel kluis, Controleer opslag containers, Event Hub, AAD-exemplaar en andere afhankelijke resources, zodat deze niet meer in rekening worden gebracht. 

## <a name="move-managed-instance"></a>Beheerd exemplaar verplaatsen

### <a name="verify-prerequisites"></a>Vereisten verifiëren
 
1. Maak voor elk beheerde bron exemplaar een beheerd exemplaar van dezelfde grootte in de doel regio.  
1. Configureer het netwerk voor een beheerd exemplaar. Zie [netwerk configuratie](sql-database-howto-managed-instance.md#network-configuration)voor meer informatie.
1. Configureer de doel-Master database met de juiste aanmeldingen. Als u niet de abonnements beheerder of SQL Server-beheerder bent, kunt u met de beheerder samen werken om de benodigde machtigingen toe te wijzen. 
1. Als uw data bases zijn versleuteld met TDE en uw eigen versleutelings sleutel gebruiken in azure Key kluis, moet u ervoor zorgen dat de Azure met dezelfde versleutelings sleutels bestaat in zowel de bron-als doel regio's. Zie [TDe met door de klant beheerde sleutels in azure Key Vault](transparent-data-encryption-byok-azure-sql.md)voor meer informatie.
1. Als controle is ingeschakeld voor het exemplaar, moet u ervoor zorgen dat:
    - De opslag container of het Event Hub met de bestaande logboeken wordt verplaatst naar de doel regio. 
    - Controle is geconfigureerd op het doel exemplaar. Zie [auditing with Managed instance](sql-database-managed-instance-auditing.md)(Engelstalig) voor meer informatie.
1. Als uw exemplaar een op lange termijn Bewaar beleid (LTR) heeft, blijven de bestaande LTR-back-ups gekoppeld aan de huidige server. Omdat de doel server afwijkt, kunt u toegang krijgen tot de oudere LTR-back-ups in de bron regio met behulp van de bron server, zelfs als de server wordt verwijderd. 

  > [!NOTE]
  > Dit is onvoldoende om tussen de soevereine Cloud en een open bare regio te verplaatsen. Bij een dergelijke migratie moet u de LTR-back-ups verplaatsen naar de doel server, die momenteel niet wordt ondersteund. 

### <a name="prepare-resources"></a>Resources voorbereiden

Maak een failovergroep tussen elk bron exemplaar en het bijbehorende doel exemplaar.
    - Replicatie van alle data bases op elk exemplaar wordt automatisch gestart. Zie [groepen met automatische failover](sql-database-auto-failover-group.md) voor meer informatie.

 
### <a name="monitor-the-preparation-process"></a>Het voorbereidings proces bewaken

U kunt periodiek [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) aanroepen om de replicatie van uw data bases van de bron naar het doel te bewaken. Het uitvoer object van `Get-AzSqlDatabaseFailoverGroup` bevat een eigenschap voor de **Replication State**: 
   - **Replication State = 2** (CATCH_UP) geeft aan dat de data base is gesynchroniseerd en veilig kan worden uitgevoerd. 
   - **Replication State = 0** (seeding) geeft aan dat de data base nog niet is geseedd en een poging tot failover mislukt. 

### <a name="test-synchronization"></a>Synchronisatie testen

Zodra **Replication State** is `2`, maakt u verbinding met elke Data Base of subset van data bases met behulp van het secundaire eind punt `<fog-name>.secondary.database.windows.net` en voert u een query uit op de data bases om de connectiviteit, de juiste beveiligings configuratie en gegevens replicatie te garanderen. 

### <a name="initiate-the-move"></a>De verplaatsing initiëren 

1. Maak verbinding met de doel server met behulp van het secundaire eind punt `<fog-name>.secondary.database.windows.net`.
1. Gebruik [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) om te scha kelen naar het secundaire beheerde exemplaar als primair met volledige synchronisatie. Deze bewerking kan slagen of worden teruggezet. 
1. Controleer of de opdracht is voltooid met behulp van `nslook up <fog-name>.secondary.database.windows.net` om te controleren of de DNS CNAME-vermelding verwijst naar het IP-adres van de doel regio. Als de switch-opdracht mislukt, wordt de CNAME niet bijgewerkt. 

### <a name="remove-the-source-managed-instances"></a>De door de bron beheerde instanties verwijderen
Zodra de verplaatsing is voltooid, verwijdert u de resources in de bron regio om onnodige kosten te voor komen. 

1. Verwijder de failover-groep met [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Hiermee wordt de configuratie van de failovergroep verwijderd en worden geo-replicatie koppelingen tussen de twee instanties verbroken. 
1. Verwijder het beheerde exemplaar van de bron met [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Verwijder alle extra resources in de resource groep, zoals het virtuele cluster, het virtuele netwerk en de beveiligings groep. 

## <a name="next-steps"></a>Volgende stappen 

Uw Azure SQL Database [beheren](sql-database-manage-after-migration.md) nadat deze is gemigreerd. 

