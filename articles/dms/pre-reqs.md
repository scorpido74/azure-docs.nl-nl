---
title: Vereisten voor Azure Database Migration Service
description: Meer informatie over een overzicht van de vereisten voor het gebruik van de Azure Database Migration Service om databasemigraties uit te voeren.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651488"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Overzicht van vereisten voor het gebruik van de Azure Database Migration Service

Er zijn verschillende vereisten vereist om ervoor te zorgen dat Azure Database Migration Service probleemloos wordt uitgevoerd bij het uitvoeren van databasemigraties. Sommige van de vereisten zijn van toepassing op alle scenario's (brondoelparen) die door de service worden ondersteund, terwijl andere vereisten uniek zijn voor een specifiek scenario.

Vereisten die zijn gekoppeld aan het gebruik van de Azure Database Migration Service worden weergegeven in de volgende secties.

## <a name="prerequisites-common-across-migration-scenarios"></a>Algemene vereisten voor migratiescenario's

Vereisten voor Azure Database Migration Service die veel voorkomen in alle ondersteunde migratiescenario's, zijn de noodzaak om:

* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Zorg ervoor dat de regels van uw nsg-regels (Virtual Network Network Security Group) de volgende communicatiepoorten 443, 53, 9354, 445, 12000 niet blokkeren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
* Wanneer u een firewalltoestel gebruikt voor uw brondatabase(s), moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Schakel het TCP/IP-protocol in, dat standaard is uitgeschakeld tijdens de installatie van SQL Server Express, door de instructies in het artikel [In- of uitschakelen van een Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) te volgen.

    > [!IMPORTANT]
    > Voor het maken van een exemplaar van Azure Database Migration Service is toegang nodig tot virtuele netwerkinstellingen die normaal gesproken niet binnen dezelfde brongroep vallen. Als gevolg hiervan heeft de gebruiker die een exemplaar van DMS maakt toestemming nodig op abonnementsniveau. Voer het volgende script uit om de vereiste rollen te maken, die u naar behoefte toewijzen:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Vereisten voor het migreren van SQL Server naar Azure SQL Database

Naast vereisten voor Azure Database Migration Service die gemeenschappelijk zijn voor alle migratiescenario's, zijn er ook vereisten die specifiek van toepassing zijn op het ene of het andere scenario.

Wanneer u de Azure Database Migration Service gebruikt om SQL Server-migraties naar Azure SQL Database uit te voeren, moet u, naast de vereisten die gemeenschappelijk zijn voor alle migratiescenario's, de volgende aanvullende vereisten aanpakken:

* Maak een Azure SQL Database-exemplaar, dit doet u door de details in het artikel [Een Azure SQL-database maken in de Azure-portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) te volgen.
* Download en installeer de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 of hoger.
* Stel uw Windows-firewall open voor toegang van de Azure Database Migration Service tot de brondatabase van SQL Server. Standaard verloopt dit via TCP-poort 1433.
* Als u meerdere benoemde SQL Server-exemplaren met behulp van dynamische poorten uitvoert, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat de Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
* Maak een [firewallregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op serverniveau voor de Azure SQL Database-server om voor de Azure Database Migration Service toegang tot de doeldatabase toe te staan. Geef het subnetbereik op van het virtuele netwerk dat wordt gebruikt voor de Azure Database Migration Service.
* Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het SQL Server-bronexemplaar [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-machtigingen hebben.
* Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het Azure SQL Database-doelexemplaar CONTROL DATABASE-machtiging hebben op de Azure SQL-doeldatabases.

   > [!NOTE]
   > Zie de zelfstudie [Migreren SQL Server naar Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)voor een volledige lijst met de vereisten die nodig zijn om de Azure Database Migration Service te gebruiken om migraties van SQL Server naar Azure SQL Database uit te voeren.
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Vereisten voor het migreren van SQL Server naar een beheerde Azure SQL Database-instantie

* Maak een Azure SQL Database-beheerde instantie door de details in het artikel [Een Azure SQL Database Managed Instance te volgen in de Azure-portal.](https://aka.ms/sqldbmi)
* Open uw firewalls om SMB-verkeer toe te staan op poort 445 voor het IP-adres of subnetbereik van de Azure Database Migration Service.
* Stel uw Windows-firewall open voor toegang van de Azure Database Migration Service tot de brondatabase van SQL Server. Standaard verloopt dit via TCP-poort 1433.
* Als u meerdere benoemde SQL Server-exemplaren met behulp van dynamische poorten uitvoert, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat de Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
* Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt voor verbinding met het bronexemplaar van SQL Server en het doelexemplaar van Managed Instance lid zijn van de serverrol sysadmin.
* Maak een netwerkshare die de Azure Database Migration Service kan gebruiken om een back-up te maken van de brondatabase.
* Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
* Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. De Azure Database Migration Service doet zich voor als de gebruikersreferenties om de back-upbestanden te uploaden naar azure storage-container voor herstelbewerking.
* Maak een blobcontainer en haal de SAS URI op met behulp van de stappen in het artikel [Azure Blob Storage resources beheren met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Selecteer alle machtigingen (Lezen, Schrijven, Verwijderen, Lijst) in het beleidsvenster tijdens het maken van de SAS URI.

   > [!NOTE]
   > Zie de zelfstudie [Migreren SQL Server naar Azure SQL Database Managed Instance](https://aka.ms/migratetomiusingdms)voor een volledige lijst met de vereisten die nodig zijn om de Azure Database Migration Service te gebruiken om migraties uit te voeren van SQL Server naar Azure SQL Database Managed Instance.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [Wat is de Azure Database Migration Service](dms-overview.md)voor een overzicht van de Azure Database Migration Service en regionale beschikbaarheid.
