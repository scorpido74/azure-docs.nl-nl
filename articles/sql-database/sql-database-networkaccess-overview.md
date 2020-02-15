---
title: Netwerk toegangs beheer
description: Overzicht van netwerk toegangs beheer voor Azure SQL Database en Data Warehouse voor het beheren van toegang en het configureren van een enkele of gegroepeerde Data Base.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16ba90aab52c00f77af590f854217cd989df53b3
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251903"
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

In veel gevallen is de instelling **on** moeilijker dan de meeste klanten. Ze willen deze instelling mogelijk instellen op **uit** en vervangen door meer beperkende IP-firewall regels of Virtual Network Firewall regels. Dit heeft gevolgen voor de volgende functies die worden uitgevoerd op Vm's in azure die geen deel uitmaken van uw VNet en daarom verbinding maken met SQL data base via een Azure IP-adres.

### <a name="import-export-service"></a>Import export service
Bij import export service werkt **Azure-Services niet om toegang te krijgen** tot de server. U kunt het probleem echter omzeilen [door sqlpackage. exe hand matig uit te voeren vanuit een Azure VM of door de export](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) rechtstreeks in uw code uit te voeren met behulp van de DACFX-API.

### <a name="data-sync"></a>Gegevens synchroniseren
Als u de functie gegevens synchronisatie wilt gebruiken om **Azure-Services toegang te geven** tot de server, moet u afzonderlijke firewall regel vermeldingen maken om [IP-adressen toe te voegen](sql-database-server-level-firewall-rule.md) van de **SQL-service-tag** voor de regio die als host fungeert voor de **hub** -data base.
Voeg deze firewall regels op server niveau toe aan de logische servers die fungeren als host voor de data bases van de **hub** en het **lid** (deze kunnen zich in verschillende regio's bevinden)

Gebruik het volgende Power shell-script om de IP-adressen te genereren die overeenkomen met de SQL service-tag voor de regio vs West
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag retourneert het globale bereik voor SQL service-tag ondanks het opgeven van de locatie parameter. Zorg ervoor dat u deze filtert op de regio die de hub-data base host die door de synchronisatie groep wordt gebruikt

Houd er rekening mee dat de uitvoer van het Power shell-script in CIDR-notatie (Classless Inter-Domain Routing) is en dat deze moet worden geconverteerd naar een indeling van het begin-en eind-IP-adres met behulp van [Get-IPrangeStartEnd. ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) als volgt
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Voer de volgende aanvullende stappen uit om alle IP-adressen van CIDR te converteren naar de notatie voor begin-en eind adres van IP.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
U kunt deze nu als afzonderlijke firewall regels toevoegen en vervolgens instellen **dat Azure-Services toegang heeft** tot de server.


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

**Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. In dit artikel bent u geïnteresseerd in de type naam van **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database.

**Regel voor virtueel netwerk:** Een regel voor het virtuele netwerk voor uw SQL Database-Server is een subnet dat wordt vermeld in de toegangs beheer lijst (ACL) van uw SQL Database-Server. Het subnet moet de naam van het **micro soft. SQL** -type bevatten als u de toegangs beheer lijst voor uw SQL database wilt. Met een regel voor het virtuele netwerk krijgt uw SQL Database-Server de communicatie van elk knoop punt dat zich in het subnet bevindt, te accepteren.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP versus Virtual Network Firewall regels

Met de firewall van Azure SQL Server kunt u IP-adresbereiken opgeven waarvan de communicatie in SQL Database wordt geaccepteerd. Deze aanpak is nauw keurig voor stabiele IP-adressen die zich buiten het particuliere Azure-netwerk bevinden. Virtuele machines (Vm's) in het particuliere Azure-netwerk zijn echter geconfigureerd met *dynamische* IP-adressen. Dynamische IP-adressen kunnen veranderen wanneer de virtuele machine opnieuw wordt opgestart en de op IP gebaseerde firewall regel ongeldig maken. Het is Folly om een dynamisch IP-adres op te geven in een firewall regel, in een productie omgeving.

U kunt deze beperking omzeilen door een *statisch* IP-adres voor uw virtuele machine te verkrijgen. Zie voor meer informatie [privé IP-adressen configureren voor een virtuele machine met behulp van de Azure Portal](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). De aanpak van een statisch IP-adres kan echter moeilijk te beheren zijn, en het kost goed wanneer deze op schaal wordt uitgevoerd. 

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
