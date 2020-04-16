---
title: Besturingselementen voor netwerktoegang
description: Overzicht van netwerktoegangsbesturingselementen voor Azure SQL Database en Data Warehouse om toegang te beheren en een enkele of samengevoegde database te configureren.
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
ms.date: 03/09/2020
ms.openlocfilehash: 8b4ee679b21d904f997f727f5f26275c86acc9c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414417"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database- en Data Warehouse-netwerktoegangsbesturingselementen

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database- als SQL Data Warehouse-databases die zijn gemaakt op de Azure SQL-server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL Database Managed Instance**. Zie [Verbinding maken met een beheerde instantie voor](sql-database-managed-instance-connect-app.md) meer informatie over de netwerkconfiguratie.

Wanneer u een nieuwe Azure SQL Server maakt vanuit de [Azure-portal,](sql-database-single-database-get-started.md)is het resultaat een openbaar eindpunt in de indeling, *yourservername.database.windows.net*.

U de volgende besturingselementen voor netwerktoegang gebruiken om selectief toegang tot de SQL-database toe te staan via het openbare eindpunt:
- Azure Services toestaan: wanneer u bent ingesteld op AAN, kunnen andere bronnen binnen de Azure-grens, bijvoorbeeld een Azure Virtual Machine, toegang krijgen tot SQL-database

- IP-firewallregels: gebruik deze functie om verbindingen vanaf een specifiek IP-adres expliciet toe te staan, bijvoorbeeld van on-premises machines

U ook privétoegang tot de SQL-database toestaan vanuit [virtuele netwerken](../virtual-network/virtual-networks-overview.md) via:
- Firewallregels voor virtuele netwerken: gebruik deze functie om verkeer van een specifiek virtueel netwerk binnen de Azure-grens toe te staan

- Private Link: gebruik deze functie om een privéeindpunt voor Azure SQL Server te maken binnen een specifiek virtueel netwerk



Zie de onderstaande video voor een uitleg op hoog niveau van deze toegangscontroles en wat ze doen:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Azure-services toestaan 
Tijdens het maken van een nieuwe Azure SQL Server [vanuit Azure-portal](sql-database-single-database-get-started.md)blijft deze instelling onaangevinkt.



U deze instelling ook wijzigen via het firewalldeelvenster nadat de Azure SQL Server als volgt is gemaakt.
  
 ![Schermafbeelding van serverfirewall beheren][2]

Wanneer u bent ingesteld **op ON** Azure SQL Server, staat u communicatie toe van alle bronnen binnen de Azure-grens, die al dan niet deel uitmaken van uw abonnement.

In veel gevallen is de **ON-instelling** toleranter dan wat de meeste klanten willen. Ze willen deze instelling mogelijk instellen op **UIT** en vervangen door meer beperkende IP-firewallregels of firewallregels voor virtuele netwerken. Dit heeft gevolgen voor de volgende functies die worden uitgevoerd op VM's in Azure die geen deel uitmaken van uw VNet en dus verbinding maken met Sql Database via een Azure IP-adres.

### <a name="import-export-service"></a>Exportservice importeren
Exportservice importeren werkt niet wanneer **Toegang tot Azure-services toestaan** is ingesteld op **UIT.** U het probleem echter oplossen [door sqlpackage.exe handmatig uit te voeren vanaf een Azure VM of de export](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) rechtstreeks in uw code uit te voeren met behulp van de DACFx API.

### <a name="data-sync"></a>Gegevens synchroniseren
Als u de functie Gegevenssynchronisatie wilt gebruiken met **Toegang toestaan tot Azure-services** ingesteld op **UIT,** moet u afzonderlijke firewallregelvermeldingen maken om [IP-adressen toe](sql-database-server-level-firewall-rule.md) te voegen van de **Sql-servicetag** voor de regio die de **Hub-database** host.
Voeg deze firewallregels op serverniveau toe aan de logische servers die zowel **hub-** als **liddatabases** hosten (die zich in verschillende regio's kunnen bevinden)

Gebruik het volgende PowerShell-script om de IP-adressen te genereren die overeenkomen met sql-servicetag voor de regio West-VS
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
> Get-AzNetworkServiceTag retourneert het globale bereik voor Sql Service Tag, ondanks het opgeven van de parameter Locatie. Zorg ervoor dat u deze filtert naar de regio waar de Hub-database wordt gebruikt door uw synchronisatiegroep

Houd er rekening mee dat de uitvoer van het PowerShell-script zich bevindt in CIDR-notatie (Classless Inter-Domain Routing) en dit moet worden geconverteerd naar een indeling van het IP-adres start en einde met [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) als volgt
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Volg de volgende aanvullende stappen om alle IP-adressen van CIDR om te zetten naar ip-adresindeling starten en beëindigen.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
U deze nu toevoegen als afzonderlijke firewallregels en vervolgens **Azure-services toestaan om toegang te krijgen tot de server naar** UIT.


## <a name="ip-firewall-rules"></a>IP-firewallregels
Ip-gebaseerde firewall is een functie van Azure SQL Server die alle toegang tot uw databaseserver verhindert totdat u expliciet [IP-adressen](sql-database-server-level-firewall-rule.md) van de clientmachines toevoegt.


## <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtuele netwerken

Naast IP-regels u met de Azure SQL Server-firewall *virtuele netwerkregels*definiëren.  
Zie [Eindpunten en regels voor de Virtual Network-service voor Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) of bekijk deze video voor meer informatie:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure-netwerkterminologie  
Wees je bewust van de volgende Azure Networking-voorwaarden terwijl je firewallregels voor virtuele netwerken verkent

**Virtueel netwerk:** U virtuele netwerken aan uw Azure-abonnement laten gekoppeld 

**Subnet:** Een virtueel netwerk bevat **subnetten.** Alle Azure virtuele machines (VM's) die u hebt toegewezen aan subnetten. Eén subnet kan meerdere VM's of andere compute nodes bevatten. Compute-knooppunten die zich buiten uw virtuele netwerk bevinden, hebben geen toegang tot uw virtuele netwerk, tenzij u uw beveiliging configureert om toegang toe te staan.

**Eindpunt van de virtuele netwerkservice:** Een [eindpunt van de service voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschapswaarden een of meer formele namen van azure-servicetypen bevatten. In dit artikel zijn we geïnteresseerd in de typenaam van **Microsoft.Sql**, die verwijst naar de Azure-service met de naam SQL Database.

**Virtuele netwerkregel:** Een virtuele netwerkregel voor uw SQL Database-server is een subnet dat wordt vermeld in de lijst met toegangscontrole (ACL) van uw SQL Database-server. Als u zich in de ACL voor uw SQL-database wilt bevinden, moet het subnet de typenaam **Microsoft.Sql** bevatten. Een virtuele netwerkregel vertelt uw SQL Database-server om communicatie te accepteren vanaf elk knooppunt dat zich op het subnet bevindt.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP vs. Firewallregels voor virtuele netwerken

Met de Azure SQL Server-firewall u IP-adresbereiken opgeven waaruit communicatie wordt geaccepteerd in SQL Database. Deze aanpak is prima voor stabiele IP-adressen die zich buiten het Azure-privénetwerk bevinden. Virtuele machines (VM's) binnen het Azure-privénetwerk zijn echter geconfigureerd met *dynamische* IP-adressen. Dynamische IP-adressen kunnen veranderen wanneer uw VM opnieuw wordt opgestart en op hun beurt de IP-gebaseerde firewallregel ongeldig maken. Het zou dwaasheid zijn om een dynamisch IP-adres op te geven in een firewallregel, in een productieomgeving.

U deze beperking omzeilen door een *statisch* IP-adres voor uw VM te verkrijgen. Zie [Privé-IP-adressen configureren voor een virtuele machine voor](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)meer informatie met behulp van de Azure-portal. Echter, de statische IP-aanpak kan moeilijk te beheren, en het is duur wanneer gedaan op schaal. 

Virtuele netwerkregels zijn eenvoudiger om toegang op te stellen en te beheren vanaf een specifiek subnet dat uw VM's bevat.

> [!NOTE]
> U SQL Database nog niet op een subnet hebben. Als uw Azure SQL Database-server een knooppunt was op een subnet in uw virtuele netwerk, kunnen alle knooppunten binnen het virtuele netwerk communiceren met uw SQL-database. In dit geval kunnen uw VM's communiceren met SQL Database zonder dat er virtuele netwerkregels of IP-regels nodig zijn.

## <a name="private-link"></a>Private Link 
Met Private Link u verbinding maken met Azure SQL Server via een **privéeindpunt.** Een privéeindpunt is een privé-IP-adres binnen een specifiek [virtueel netwerk](../virtual-network/virtual-networks-overview.md) en subnet.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een Azure SQL-database maken](sql-database-single-database-get-started.md)voor een snelle start bij het maken van een IP-firewallregel op serverniveau.

- Zie [Eindpunten en regels voor De Service voor extern](sql-database-vnet-service-endpoint-rule-overview.md)netwerk voor Azure SQL Database voor een snelle start bij het maken van een Vnet-firewallregel op serverniveau.

- Zie [Client quickstart](https://msdn.microsoft.com/library/azure/ee336282.aspx)code samples naar SQL Database voor hulp bij het maken van verbinding met een Azure SQL-database vanuit open source of toepassingen van derden.

- Zie de **SQL-database: Buiten vs binnen- en binnenkant** van [ports na 1433 voor ADO.NET 4.5 en SQL Database voor](sql-database-develop-direct-route-ports-adonet-v12.md) informatie over aanvullende poorten die u mogelijk moet openen.

- Zie [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md) voor een overzicht van Azure SQL Database Connectivity

- Zie [Uw database beveiligen](sql-database-security-overview.md) voor een overzicht van azure SQL-databasebeveiliging

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

