---
title: Resources verplaatsen naar een andere regio
description: Meer informatie over het verplaatsen van uw Azure SQL Database, Azure SQL elastic pool of Azure SQL managed instance naar een andere regio.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821434"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Azure SQL-resources verplaatsen naar een andere regio

In dit artikel vindt u een algemene werkstroom voor het verplaatsen van uw enkele database van Azure SQL Database, elastische pool en beheerde instantie naar een nieuwe regio. 

## <a name="overview"></a>Overzicht

Er zijn verschillende scenario's waarin u uw bestaande Azure SQL-resources van de ene regio naar de andere wilt verplaatsen. U breidt uw bedrijf bijvoorbeeld uit naar een nieuwe regio en wilt het optimaliseren voor het nieuwe klantenbestand. Of u moet de bewerkingen om nalevingsredenen naar een andere regio verplaatsen. Of Azure heeft een gloednieuwe regio uitgebracht die een betere nabijheid biedt en de klantervaring verbetert.  

Dit artikel biedt een algemene werkstroom voor het verplaatsen van resources naar een andere regio. De werkstroom bestaat uit de volgende stappen: 

- Controleer de vereisten voor de verhuizing 
- Voorbereiden om de resources in het bereik te verplaatsen
- Het voorbereidingsproces bewaken
- Het verhuisproces testen
- De werkelijke zet starten 
- De resources uit het brongebied verwijderen 


> [!NOTE]
> Dit artikel is van toepassing op migraties binnen de Azure public cloud of binnen dezelfde soevereine cloud. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Eén database verplaatsen

### <a name="verify-prerequisites"></a>Vereisten verifiëren 

1. Maak een doellogische server voor elke bronserver. 
1. Configureer de firewall met de juiste uitzonderingen met [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configureer de logische servers met de juiste aanmeldingen. Als u niet de abonnementsbeheerder of SQL-serverbeheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. Zie [Azure SQL-databasebeveiliging beheren na herstel na een ramp](sql-database-geo-replication-security-config.md)voor meer informatie. 
1. Als uw databases zijn versleuteld met TDE en uw eigen versleutelingssleutel in Azure-sleutelkluis gebruiken, moet u ervoor zorgen dat het juiste versleutelingsmateriaal is ingericht in de doelgebieden. Zie [Azure SQL Transparent Data Encryption with customer-managed keys in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) voor meer informatie
1. Als de controle op databaseniveau is ingeschakeld, schakelt u deze uit en schakelt u in plaats daarvan controle op serverniveau in. Na failover vereist controle op databaseniveau het verkeer tussen de regio's, wat na de verhuizing niet gewenst of mogelijk is. 
1. Voor audits op serverniveau moet u ervoor zorgen dat:
   - De opslagcontainer, Log Analytics of event hub met de bestaande audit logs wordt verplaatst naar het doelgebied. 
   - Auditing is geconfigureerd op de doelserver. Zie [Aan de slag met SQL-databasecontrole](sql-database-auditing.md)voor meer informatie. 
1. Als uw instantie een lange termijn bewaarbeleid (LTR) heeft, blijven de bestaande LTR-back-ups gekoppeld aan de huidige server. Omdat de doelserver anders is, hebt u toegang tot de oudere LTR-back-ups in het brongebied via de bronserver, zelfs als de server wordt verwijderd. 

  > [!NOTE]
  > Dit zal onvoldoende zijn om te bewegen tussen de soevereine wolk en een openbare regio. Voor een dergelijke migratie moeten de LTR-back-ups worden verplaatst naar de doelserver, die momenteel niet wordt ondersteund. 

### <a name="prepare-resources"></a>Resources voorbereiden

1. Maak een [failovergroep](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) tussen de logische server van de bron naar de logische server van het doel.  
1. Voeg de databases toe die u wilt verplaatsen naar de failovergroep. 
    - Replicatie van alle toegevoegde databases wordt automatisch gestart. Zie [Aanbevolen procedures voor het gebruik van failovergroepen met afzonderlijke databases](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)voor meer informatie. 
 
### <a name="monitor-the-preparation-process"></a>Het voorbereidingsproces bewaken

U [get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodiek bellen om replicatie van uw databases te controleren van de bron naar het doel. Het uitvoerobject `Get-AzSqlDatabaseFailoverGroup` van bevat een eigenschap voor de **replicatiestaat:** 
   - **ReplicationState = 2** (CATCH_UP) geeft aan dat de database is gesynchroniseerd en veilig kan worden mislukt. 
   - **ReplicatieStaat = 0** (SEEDING) geeft aan dat de database nog niet is ingezaaid en dat een poging tot failover mislukt. 

### <a name="test-synchronization"></a>Synchronisatie van tests

Zodra **Replicatiestaat** is, `2`maak verbinding met elke database of `<fog-name>.secondary.database.windows.net` subset van databases met behulp van het secundaire eindpunt en voer elke query uit tegen de databases om connectiviteit, een goede beveiligingsconfiguratie en gegevensreplicatie te garanderen. 

### <a name="initiate-the-move"></a>De verhuizing starten

1. Maak verbinding met de doelserver `<fog-name>.secondary.database.windows.net`met behulp van het secundaire eindpunt .
1. Gebruik [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) om de secundaire beheerde instantie te schakelen naar de primaire met volledige synchronisatie. Deze bewerking zal slagen, of het zal terugdraaien. 
1. Controleer of de opdracht is `nslook up <fog-name>.secondary.database.windows.net` voltooid met behulp van de DNS CNAME-vermelding naar het IP-adres van het doelgebied. Als de schakelopdracht mislukt, wordt de CNAME niet bijgewerkt. 

### <a name="remove-the-source-databases"></a>De brondatabases verwijderen

Zodra de verplaatsing is voltooid, verwijdert u de resources in het brongebied om onnodige kosten te voorkomen. 

1. Verwijder de failovergroep met [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Verwijder elke brondatabase met [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) voor elk van de databases op de bronserver. Hierdoor worden georeplicatiekoppelingen automatisch beëindigd. 
1. Verwijder de bronserver met [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Verwijder de sleutelkluis, controleopslagcontainers, gebeurtenishub, AAD-instantie en andere afhankelijke resources om niet meer voor deze resources te worden gefactureerd. 

## <a name="move-elastic-pools"></a>Elastische pools verplaatsen

### <a name="verify-prerequisites"></a>Vereisten verifiëren 

1. Maak een doellogische server voor elke bronserver. 
1. Configureer de firewall met de juiste uitzonderingen met [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configureer de logische servers met de juiste aanmeldingen. Als u niet de abonnementsbeheerder of SQL-serverbeheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. Zie [Azure SQL-databasebeveiliging beheren na herstel na een ramp](sql-database-geo-replication-security-config.md)voor meer informatie. 
1. Als uw databases zijn versleuteld met TDE en uw eigen versleutelingssleutel in Azure-sleutelkluis gebruiken, moet u ervoor zorgen dat het juiste versleutelingsmateriaal in de doelregio is ingericht.
1. Maak een doelelastische groep voor elke elastische brongroep, die ervoor zorgt dat de groep in dezelfde servicelaag wordt gemaakt, met dezelfde naam en dezelfde grootte. 
1. Als een controle op databaseniveau is ingeschakeld, schakelt u deze uit en schakelt u in plaats daarvan controle op serverniveau in. Na failover vereist controle op databaseniveau cross-region verkeer, wat niet gewenst is of mogelijk na de verhuizing. 
1. Voor audits op serverniveau moet u ervoor zorgen dat:
    - De opslagcontainer, Log Analytics of event hub met de bestaande audit logs wordt verplaatst naar het doelgebied.
    - De auditconfiguratie is geconfigureerd op de doelserver. Zie [SQL-databasecontrole](sql-database-auditing.md)voor meer informatie .
1. Als uw instantie een lange termijn bewaarbeleid (LTR) heeft, blijven de bestaande LTR-back-ups gekoppeld aan de huidige server. Omdat de doelserver anders is, hebt u toegang tot de oudere LTR-back-ups in het brongebied via de bronserver, zelfs als de server wordt verwijderd. 

  > [!NOTE]
  > Dit zal onvoldoende zijn om te bewegen tussen de soevereine wolk en een openbare regio. Voor een dergelijke migratie moeten de LTR-back-ups worden verplaatst naar de doelserver, die momenteel niet wordt ondersteund. 

### <a name="prepare-to-move"></a>Voorbereiden om te bewegen
 
1.  Maak een afzonderlijke [failovergroep](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) tussen elke elastische groep op de bronlogische server en de elastische pool op de doelserver. 
1.  Voeg alle databases in de groep toe aan de failovergroep. 
    - Replicatie van de toegevoegde databases wordt automatisch gestart. Zie [aanbevolen procedures voor failovergroepen met elastische pools](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)voor meer informatie. 

  > [!NOTE]
  > Hoewel het mogelijk is om een failovergroep te maken die meerdere elastische groepen bevat, raden we u ten zeerste aan om voor elke groep een aparte failovergroep te maken. Als u een groot aantal databases in meerdere elastische groepen hebt die u moet verplaatsen, u de voorbereidingsstappen parallel uitvoeren en de stap parallel starten. Dit proces zal beter schalen en zal minder tijd in beslag nemen in vergelijking met het hebben van meerdere elastische pools in dezelfde failover groep. 

### <a name="monitor-the-preparation-process"></a>Het voorbereidingsproces bewaken

U [get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodiek bellen om replicatie van uw databases te controleren van de bron naar het doel. Het uitvoerobject `Get-AzSqlDatabaseFailoverGroup` van bevat een eigenschap voor de **replicatiestaat:** 
   - **ReplicationState = 2** (CATCH_UP) geeft aan dat de database is gesynchroniseerd en veilig kan worden mislukt. 
   - **ReplicatieStaat = 0** (SEEDING) geeft aan dat de database nog niet is ingezaaid en dat een poging tot failover mislukt. 

### <a name="test-synchronization"></a>Synchronisatie van tests
 
Zodra **Replicatiestaat** is, `2`maak verbinding met elke database of `<fog-name>.secondary.database.windows.net` subset van databases met behulp van het secundaire eindpunt en voer elke query uit tegen de databases om connectiviteit, een goede beveiligingsconfiguratie en gegevensreplicatie te garanderen. 

### <a name="initiate-the-move"></a>De verhuizing starten
 
1. Maak verbinding met de doelserver `<fog-name>.secondary.database.windows.net`met behulp van het secundaire eindpunt .
1. Gebruik [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) om de secundaire beheerde instantie te schakelen naar de primaire met volledige synchronisatie. Deze bewerking zal slagen, of het zal terugdraaien. 
1. Controleer of de opdracht is `nslook up <fog-name>.secondary.database.windows.net` voltooid met behulp van de DNS CNAME-vermelding naar het IP-adres van het doelgebied. Als de schakelopdracht mislukt, wordt de CNAME niet bijgewerkt. 

### <a name="remove-the-source-elastic-pools"></a>Verwijder de bron elastische pools
 
Zodra de verplaatsing is voltooid, verwijdert u de resources in het brongebied om onnodige kosten te voorkomen. 

1. Verwijder de failovergroep met [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Verwijder elke bronelastische groep op de bronserver met [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Verwijder de bronserver met [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Verwijder de sleutelkluis, controleopslagcontainers, gebeurtenishub, AAD-instantie en andere afhankelijke resources om niet meer voor deze resources te worden gefactureerd. 

## <a name="move-managed-instance"></a>Beheerde instantie verplaatsen

### <a name="verify-prerequisites"></a>Vereisten verifiëren
 
1. Maak voor elke door de bron beheerde instantie een doelbeheerde instantie van dezelfde grootte in het doelgebied.  
1. Configureer het netwerk voor een beheerde instantie. Zie [netwerkconfiguratie](sql-database-howto-managed-instance.md#network-configuration)voor meer informatie.
1. Configureer de doelmasterdatabase met de juiste aanmeldingen. Als u niet de abonnementsbeheerder of SQL-serverbeheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. 
1. Als uw databases zijn versleuteld met TDE en uw eigen versleutelingssleutel in Azure-sleutelkluis gebruiken, moet u ervoor zorgen dat de AKV met identieke versleutelingssleutels bestaat in zowel bron- als doelregio's. Zie [TDE met door de klant beheerde sleutels in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)voor meer informatie.
1. Als de controle voor de instantie is ingeschakeld, moet u ervoor zorgen dat:
    - De opslagcontainer of gebeurtenishub met de bestaande logboeken wordt verplaatst naar het doelgebied. 
    - De controle is geconfigureerd op de doelinstantie. Zie [controle met beheerde instantie voor](sql-database-managed-instance-auditing.md)meer informatie .
1. Als uw instantie een lange termijn bewaarbeleid (LTR) heeft, blijven de bestaande LTR-back-ups gekoppeld aan de huidige server. Omdat de doelserver anders is, hebt u toegang tot de oudere LTR-back-ups in het brongebied via de bronserver, zelfs als de server wordt verwijderd. 

  > [!NOTE]
  > Dit zal onvoldoende zijn om te bewegen tussen de soevereine wolk en een openbare regio. Voor een dergelijke migratie moeten de LTR-back-ups worden verplaatst naar de doelserver, die momenteel niet wordt ondersteund. 

### <a name="prepare-resources"></a>Resources voorbereiden

Maak een failovergroep tussen elke broninstantie en de bijbehorende doelinstantie.
    - Replicatie van alle databases voor elke instantie wordt automatisch gestart. Zie [Groepen automatisch failoveren](sql-database-auto-failover-group.md) voor meer informatie.

 
### <a name="monitor-the-preparation-process"></a>Het voorbereidingsproces bewaken

U [get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) periodiek bellen om replicatie van uw databases te controleren van de bron naar het doel. Het uitvoerobject `Get-AzSqlDatabaseFailoverGroup` van bevat een eigenschap voor de **replicatiestaat:** 
   - **ReplicationState = 2** (CATCH_UP) geeft aan dat de database is gesynchroniseerd en veilig kan worden mislukt. 
   - **ReplicatieStaat = 0** (SEEDING) geeft aan dat de database nog niet is ingezaaid en dat een poging tot failover mislukt. 

### <a name="test-synchronization"></a>Synchronisatie van tests

Zodra **Replicatiestaat** is, `2`maak verbinding met elke database of `<fog-name>.secondary.database.windows.net` subset van databases met behulp van het secundaire eindpunt en voer elke query uit tegen de databases om connectiviteit, een goede beveiligingsconfiguratie en gegevensreplicatie te garanderen. 

### <a name="initiate-the-move"></a>De verhuizing starten 

1. Maak verbinding met de doelserver `<fog-name>.secondary.database.windows.net`met behulp van het secundaire eindpunt .
1. Gebruik [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) om de secundaire beheerde instantie te schakelen naar de primaire met volledige synchronisatie. Deze bewerking zal slagen, of het zal terugdraaien. 
1. Controleer of de opdracht is `nslook up <fog-name>.secondary.database.windows.net` voltooid met behulp van de DNS CNAME-vermelding naar het IP-adres van het doelgebied. Als de schakelopdracht mislukt, wordt de CNAME niet bijgewerkt. 

### <a name="remove-the-source-managed-instances"></a>De door de bron beheerde exemplaren verwijderen
Zodra de verplaatsing is voltooid, verwijdert u de resources in het brongebied om onnodige kosten te voorkomen. 

1. Verwijder de failovergroep met [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Hierdoor wordt de configuratie van de failovergroep en worden georeplicatiekoppelingen tussen de twee instanties beëindigd. 
1. Verwijder de door de bron beheerde instantie met [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Verwijder eventuele extra bronnen in de brongroep, zoals het virtuele cluster, het virtuele netwerk en de beveiligingsgroep. 

## <a name="next-steps"></a>Volgende stappen 

[Beheer](sql-database-manage-after-migration.md) uw Azure SQL Database zodra deze is gemigreerd. 

