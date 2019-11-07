---
title: Netwerk toegangs beheer van Azure SQL Database en Data Warehouse
description: Overzicht van netwerk toegangs beheer voor Azure SQL Database en Data Warehouse voor het beheren van toegang en het configureren van een enkele of gegroepeerde Data Base.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: deee668c3597047a5d5f0fd153626916c3fd8517
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687712"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Netwerk toegangs beheer van Azure SQL Database en Data Warehouse

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL Server en op zowel SQL Database-als SQL Data Warehouse-data bases die zijn gemaakt op de Azure SQL-Server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL database Managed instance**. Zie [verbinding maken met een beheerd exemplaar](sql-database-managed-instance-connect-app.md) voor meer informatie over de netwerk configuratie.

Wanneer u een nieuw Azure-SQL Server maakt [vanuit Azure Portal](sql-database-single-database-get-started.md), is het resultaat een openbaar eind punt in de notatie *yourservername.database.Windows.net*. Standaard is alle toegang tot het open bare eind punt geweigerd. U kunt vervolgens de volgende besturings elementen voor netwerk toegang gebruiken om de toegang tot de SQl-data base selectief via het open bare eind punt toe te staan
- Azure-Services toestaan:-wanneer deze is ingesteld op aan, worden andere resources binnen de Azure-grens, bijvoorbeeld een virtuele machine van Azure, toegang tot SQL Database

- IP-firewall regels:-gebruik deze functie om expliciet verbindingen van een specifiek IP-adres toe te staan, bijvoorbeeld van on-premises machines.

- Virtual Network Firewall regels:-gebruik deze functie om verkeer toe te staan van een specifieke Virtual Network binnen de Azure-grens

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure-Services toestaan 
Tijdens het maken van een nieuwe Azure-SQL Server [van Azure Portal](sql-database-single-database-get-started.md), wordt deze instelling uitgeschakeld.

 ![Scherm opname van nieuwe server maken][1]

U kunt deze instelling ook wijzigen via het deel venster Firewall nadat de Azure-SQL Server is gemaakt als volgt.
  
 ![Scherm opname van Server Firewall beheren][2]

Als deze functie is ingesteld op **op** Azure SQL Server worden communicaties van alle resources binnen de Azure-grens toegestaan die al dan niet deel uitmaken van uw abonnement.

In veel gevallen is de instelling **on** moeilijker dan de meeste klanten. Ze willen deze instelling mogelijk instellen op **uit** en vervangen door meer beperkende IP-firewall regels of Virtual Network Firewall regels. Dit heeft gevolgen voor de volgende functies:

### <a name="import-export-service"></a>Import export service

Azure SQL Database import-export service wordt uitgevoerd op virtuele machines in Azure. Deze Vm's bevinden zich niet in uw VNet en krijgen daarom een Azure IP-adres bij het maken van verbinding met uw data base. Bij het verwijderen van **Azure-Services toegang verlenen** tot de server kunnen deze vm's geen toegang krijgen tot uw data bases.
U kunt het probleem omzeilen door de BACPAC-import bewerking uit te voeren of rechtstreeks in uw code te exporteren met behulp van de DACFx-API.

### <a name="sql-database-query-editor"></a>Query-Editor SQL Database

De Azure SQL Database query-editor wordt geïmplementeerd op virtuele machines in Azure. Deze Vm's bevinden zich niet in uw VNet. Daarom krijgen de Vm's een Azure-IP-adres bij het maken van verbinding met uw data base. Bij het verwijderen van **Azure-Services toegang tot de server toestaan**, hebben deze vm's geen toegang tot uw data bases.

### <a name="table-auditing"></a>Tabel controle

Op dit moment zijn er twee manieren om controle in te scha kelen op uw SQL Database. Het controleren van de tabel mislukt wanneer u service-eind punten hebt ingeschakeld in uw Azure SQL Server. U kunt dit doen door over te stappen op de controle van de blob.

### <a name="impact-on-data-sync"></a>Impact op de gegevens synchronisatie

Azure SQL Database heeft de functie gegevens synchronisatie die verbinding maakt met uw data bases met behulp van Azure Ip's. Wanneer u service-eind punten gebruikt, schakelt u **Azure-Services toegang verlenen** tot de server toegang tot uw SQL database-server en wordt de functie voor gegevens synchronisatie verbroken.

## <a name="ip-firewall-rules"></a>IP-firewall regels
Op IP gebaseerde firewall is een functie van Azure SQL Server die ervoor zorgt dat toegang tot uw database server pas wordt voor komen wanneer u de [IP-adressen](sql-database-server-level-firewall-rule.md) van de client computers expliciet toevoegt.


## <a name="virtual-network-firewall-rules"></a>Firewall regels voor Virtual Network

Naast de IP-regels, kunt u met de firewall van Azure SQL Server *virtuele netwerk regels*definiëren.  
Zie voor meer informatie [Virtual Network Service-eind punten en-regels voor Azure SQL database](sql-database-vnet-service-endpoint-rule-overview.md) of Bekijk deze video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologie van Azure Networking  
Houd rekening met de volgende Azure-netwerk termen wanneer u Virtual Network Firewall-regels bekijkt

**Virtueel netwerk:** U kunt virtuele netwerken koppelen aan uw Azure-abonnement 

**Subnet:** Een virtueel netwerk bevat **subnetten**. Alle Azure virtual machines (Vm's) die u hebt toegewezen aan subnetten. Eén subnet kan meerdere Vm's of andere reken knooppunten bevatten. Reken knooppunten die zich buiten uw virtuele netwerk bevinden, hebben geen toegang tot het virtuele netwerk tenzij u de beveiliging zo configureert dat toegang wordt toegestaan.

**Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt] [VM-virtueel-netwerk-service-eind punten-overzicht-649d] is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. In dit artikel bent u geïnteresseerd in de type naam van **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database.

**Regel voor virtueel netwerk:** Een regel voor het virtuele netwerk voor uw SQL Database-Server is een subnet dat wordt vermeld in de toegangs beheer lijst (ACL) van uw SQL Database-Server. Het subnet moet de naam van het **micro soft. SQL** -type bevatten als u de toegangs beheer lijst voor uw SQL database wilt. Met een regel voor het virtuele netwerk krijgt uw SQL Database-Server de communicatie van elk knoop punt dat zich in het subnet bevindt, te accepteren.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP versus Virtual Network Firewall regels

Met de firewall van Azure SQL Server kunt u IP-adresbereiken opgeven waarvan de communicatie in SQL Database wordt geaccepteerd. Deze aanpak is nauw keurig voor stabiele IP-adressen die zich buiten het particuliere Azure-netwerk bevinden. Virtuele machines (Vm's) in het particuliere Azure-netwerk zijn echter geconfigureerd met *dynamische* IP-adressen. Dynamische IP-adressen kunnen veranderen wanneer de virtuele machine opnieuw wordt opgestart en de op IP gebaseerde firewall regel ongeldig maken. Het is Folly om een dynamisch IP-adres op te geven in een firewall regel, in een productie omgeving.

U kunt deze beperking omzeilen door een *statisch* IP-adres voor uw virtuele machine te verkrijgen. Zie [privé IP-adressen configureren voor een virtuele machine met behulp van de Azure Portal] [VM-configure-Private-IP-Addresss-for-a-virtual-machine-using-the-Azure-Portal-321w] voor meer informatie. De aanpak van een statisch IP-adres kan echter moeilijk te beheren zijn, en het kost goed wanneer deze op schaal wordt uitgevoerd. 

Regels voor virtuele netwerken zijn eenvoudiger om de toegang tot stand te brengen en te beheren vanaf een specifiek subnet dat uw virtuele machines bevat.

> [!NOTE]
> U kunt nog geen SQL Database in een subnet hebben. Als uw Azure SQL Database Server een knoop punt in een subnet in het virtuele netwerk is, kunnen alle knoop punten in het virtuele netwerk communiceren met uw SQL Database. In dit geval kunnen uw Vm's communiceren met SQL Database zonder dat er regels voor het virtuele netwerk of IP-regels nodig zijn.

## <a name="next-steps"></a>Volgende stappen

- Zie een [Azure-SQL database maken](sql-database-single-database-get-started.md)voor een Snelstartgids voor het maken van een IP-firewall regel op server niveau.

- Zie [Virtual Network Service-eind punten en-regels voor Azure SQL database](sql-database-vnet-service-endpoint-rule-overview.md)voor een Snelstartgids voor het maken van een Vnet-firewall regel op server niveau.

- Voor hulp bij het maken van verbinding met een Azure-SQL database van open-source of toepassingen van derden raadpleegt [u Quick Start code samples to SQL database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Voor informatie over aanvullende poorten die u mogelijk moet openen, raadpleegt u de sectie **SQL database: buiten** bevindend in poorten van meer [dan 1433 voor ADO.NET 4,5 en SQL database](sql-database-develop-direct-route-ports-adonet-v12.md)

- Zie [Azure SQL-connectiviteits architectuur](sql-database-connectivity-architecture.md) voor een overzicht van Azure SQL database connectiviteit

- Zie [uw data base beveiligen](sql-database-security-overview.md) voor een overzicht van Azure SQL database beveiliging

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
