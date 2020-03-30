---
title: Een failovergroep configureren
description: Meer informatie over het configureren van een groep automatische failovervoor voor een enkele azure-database, elastische pool en beheerde instantie met behulp van de Azure-portal, de Az CLI en PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461800"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Een failovergroep configureren voor Azure SQL Database

In dit onderwerp leert u hoe u een [groep voor automatische failoverswijze configureert](sql-database-auto-failover-group.md) voor een enkele azure-database, elastische groep en beheerde instantie met behulp van de Azure-portal of PowerShell. 

## <a name="single-database"></a>Individuele database
Maak de failovergroep en voeg er één database aan toe met behulp van de Azure-portal of PowerShell.

### <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende voorwaarden:

- De instellingen voor serverlogin en firewall voor de secundaire server moeten overeenkomen met die van uw primaire server. 

### <a name="create-failover-group"></a>Failovergroep maken

# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak uw failovergroep en voeg er uw enkele database aan toe met behulp van de Azure-portal.


1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de enkele database die u wilt toevoegen aan de failovergroep. 
1. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

   ![Open server voor enkele db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en selecteer **Groep toevoegen** om een nieuwe failovergroep te maken. 

    ![Nieuwe failovergroep toevoegen](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Voer op de pagina **Failovergroep** de vereiste waarden in of selecteer deze en selecteer **Vervolgens Maken**.

   - **Databases binnen de groep:** kies de database die u wilt toevoegen aan uw failovergroep. Als u de database toevoegt aan de failovergroep, wordt het georeplicatieproces automatisch gestart. 
        
    ![SQL DB toevoegen aan failovergroep](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Maak uw failovergroep en voeg er uw enkele database aan toe met PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Testfailover 

Test failover van uw failovergroep met behulp van de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Test failover van uw failovergroep met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de enkele database die u wilt toevoegen aan de failovergroep. 

   ![Open server voor enkele db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en kies vervolgens de failovergroep die u zojuist hebt gemaakt. 
  
   ![De failovergroep selecteren in de portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is. 
1. Selecteer **Failover** in het taakvenster om te mislukken over uw failovergroep die uw enkele database bevat. 
1. Selecteer **Ja** op de waarschuwing die u op de hoogte stelt dat de verbinding met TDS-sessies wordt verbroken. 

   ![Fail over uw failovergroep met uw SQL-database](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Controleer welke server nu primair is en welke server secundair is. Als failover is geslaagd, moeten de twee servers rollen hebben verwisseld. 
1. Selecteer **Failover** opnieuw om de servers terug te laten vallen naar hun oorspronkelijke rollen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Test failover van uw failovergroep met PowerShell.  

Controleer de rol van de secundaire replica: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Niet overgaan naar de secundaire server: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Failovergroep terugzetten naar de primaire server:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Als u de secundaire database moet verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire database voordat deze uit de failovergroep wordt verwijderd, kan onvoorspelbaar gedrag veroorzaken. 

## <a name="elastic-pool"></a>Elastische pool
Maak de failovergroep en voeg er een elastische pool aan toe met behulp van de Azure-portal of PowerShell.  

### <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende voorwaarden:

- De instellingen voor serverlogin en firewall voor de secundaire server moeten overeenkomen met die van uw primaire server. 

### <a name="create-the-failover-group"></a>De failovergroep maken 

Maak de failovergroep voor uw elastische pool met de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak uw failovergroep en voeg er uw elastische pool aan toe met behulp van de Azure-portal.

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de elastische groep die u wilt toevoegen aan de failovergroep. 
1. Selecteer in het deelvenster **Overzicht** de naam van de server onder **Servernaam** om de instellingen voor de server te openen.
  
    ![Open server voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en selecteer **Groep toevoegen** om een nieuwe failovergroep te maken. 

    ![Nieuwe failovergroep toevoegen](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Voer op de pagina **Failovergroep** de vereiste waarden in of selecteer deze en selecteer **Vervolgens Maken**. Maak een nieuwe secundaire server of selecteer een bestaande secundaire server. 

1. Selecteer **Databases binnen de groep** en kies vervolgens de elastische groep die u wilt toevoegen aan de failovergroep. Als er nog geen elastische pool bestaat op de secundaire server, verschijnt er een waarschuwing waarin u wordt gevraagd een elastische pool op de secundaire server te maken. Selecteer de waarschuwing en selecteer **OK** om de elastische groep op de secundaire server te maken. 
        
    ![Elastische pool toevoegen aan failovergroep](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecteer **Selecteren** om de instellingen van de elastische groep voor een groep te toepassen op de failovergroep en selecteer **Vervolgens Maken** om de failovergroep te maken. Als u de elastische groep toevoegt aan de failovergroep, wordt het georeplicatieproces automatisch gestart. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak uw failovergroep en voeg er uw elastische pool aan toe met PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Testfailover

Test failover van uw elastische pool met behulp van de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Fail your failover group over to the secondary server, then fail back using the Azure portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de elastische groep die u wilt toevoegen aan de failovergroep. 
1. Selecteer in het deelvenster **Overzicht** de naam van de server onder **Servernaam** om de instellingen voor de server te openen.
  
    ![Open server voor elastische pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en kies vervolgens de failovergroep die u in sectie 2 hebt gemaakt. 
  
   ![De failovergroep selecteren in de portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Controleer welke server primair is en welke server secundair is. 
1. Selecteer **Failover** in het taakvenster om te mislukken over uw failovergroep met uw elastische groep. 
1. Selecteer **Ja** op de waarschuwing die u op de hoogte stelt dat de verbinding met TDS-sessies wordt verbroken. 

   ![Fail over uw failovergroep met uw SQL-database](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Controleer welke server primair is, welke server secundair is. Als failover is geslaagd, moeten de twee servers rollen hebben verwisseld. 
1. Selecteer **Opnieuw Failover** om de failovergroep terug te laten gaan naar de oorspronkelijke instellingen. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Test failover van uw failovergroep met PowerShell.

Controleer de rol van de secundaire replica: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Niet overgaan naar de secundaire server: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Als u de secundaire database moet verwijderen, verwijdert u deze uit de failovergroep voordat u deze verwijdert. Het verwijderen van een secundaire database voordat deze uit de failovergroep wordt verwijderd, kan onvoorspelbaar gedrag veroorzaken. 

## <a name="managed-instance"></a>Beheerd exemplaar

Maak een failovergroep tussen twee beheerde exemplaren met de Azure-portal of PowerShell. 

U moet [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) configureren of een gateway maken voor het virtuele netwerk van elk beheerde instantie, de twee gateways verbinden en vervolgens de failovergroep maken. 

### <a name="prerequisites"></a>Vereisten
Houd rekening met de volgende voorwaarden:

- De secundaire beheerde instantie moet leeg zijn.
- Het subnetbereik voor het secundaire virtuele netwerk mag het subnetbereik van het primaire virtuele netwerk niet overlappen. 
- De collatie en tijdzone van de secundaire instantie moeten overeenkomen met die van de primaire instantie. 
- Bij het aansluiten van de twee gateways moet de **gedeelde sleutel** voor beide verbindingen hetzelfde zijn. 

### <a name="create-primary-virtual-network-gateway"></a>Primaire virtuele netwerkgateway maken 

Als u [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)niet hebt geconfigureerd, u de primaire virtuele netwerkgateway maken met de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak de primaire virtuele netwerkgateway met behulp van de Azure-portal. 

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw resourcegroep en selecteer de **virtuele netwerkbron** voor uw primaire beheerde instantie. 
1. Selecteer **Subnetten** onder **Instellingen** en selecteer vervolgens om een nieuw **gateway-subnet**toe te voegen. Laat de standaardwaarden achter. 

   ![Gateway toevoegen voor primaire beheerde instantie](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Zodra de subnetgateway is gemaakt, selecteert u Een resource `Virtual network gateway` **maken** in het linkernavigatiedeelvenster en typt u vervolgens het zoekvak in. Selecteer de **virtuele netwerkgatewaybron** die door **Microsoft is**gepubliceerd. 

   ![Een nieuwe virtuele netwerkgateway maken](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Vul de vereiste velden in om de gateway te configureren van uw primaire beheerde instantie. 

   In de volgende tabel worden de waarden weergegeven die nodig zijn voor de gateway voor de primaire beheerde instantie:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Naam** | De naam voor uw virtuele netwerkgateway. | 
    | **Regio** | Het gebied waar uw secundaire beheerde instantie zich bevindt. |
    | **Gatewaytype** | Selecteer **VPN**. |
    | **VPN-type** | Selecteer **Route-gebaseerd** |
    | **Sku**| Standaard laten `VpnGw1`staan van . |
    | **Locatie**| De locatie waar uw secundaire beheerde instantie en secundair virtueel netwerk zich bevinden.   |
    | **Virtueel netwerk**| Selecteer het virtuele netwerk voor uw secundaire beheerde instantie. |
    | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
    | **Naam van openbaar IP-adres**| Voer een naam in voor uw IP-adres. |
    | &nbsp; | &nbsp; |

1. Laat de andere waarden als standaard staan en selecteer **Controleren + maken** om de instellingen voor uw virtuele netwerkgateway te controleren.

   ![Primaire gateway-instellingen](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecteer **Maken** om uw nieuwe virtuele netwerkgateway te maken. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak de primaire virtuele netwerkgateway met PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Secundaire virtuele netwerkgateway maken

Maak de secundaire virtuele netwerkgateway met de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Herhaal de stappen in de vorige sectie om het virtuele netwerksubnet en de gateway voor de secundaire beheerde instantie te maken. Vul de vereiste velden in om de gateway voor uw secundaire beheerde instantie te configureren. 

   In de volgende tabel worden de waarden weergegeven die nodig zijn voor de gateway voor de secundaire beheerde instantie:

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waarbij uw secundaire beheerde instantie zich bevindt. |
   | **Naam** | De naam voor uw virtuele `secondary-mi-gateway`netwerkgateway, zoals . | 
   | **Regio** | Het gebied waar uw secundaire beheerde instantie zich bevindt. |
   | **Gatewaytype** | Selecteer **VPN**. |
   | **VPN-type** | Selecteer **Route-gebaseerd** |
   | **Sku**| Standaard laten `VpnGw1`staan van . |
   | **Locatie**| De locatie waar uw secundaire beheerde instantie en secundair virtueel netwerk zich bevinden.   |
   | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt `vnet-sql-mi-secondary`in sectie 2, zoals . |
   | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
   | **Naam van openbaar IP-adres**| Voer een naam in voor `secondary-gateway-IP`uw IP-adres, zoals . |
   | &nbsp; | &nbsp; |

   ![Secundaire gateway-instellingen](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak de secundaire virtuele netwerkgateway met PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>De gateways verbinden 
Maak verbindingen tussen de twee gateways via de Azure-portal of PowerShell. 

Er moeten twee verbindingen worden gemaakt: de verbinding van de primaire gateway naar de secundaire gateway en vervolgens de verbinding van de secundaire gateway naar de primaire gateway. 

De gedeelde sleutel die voor beide verbindingen wordt gebruikt, moet voor elke verbinding hetzelfde zijn. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Maak verbindingen tussen de twee gateways met behulp van de Azure-portal. 

1. Selecteer **Een resource maken** vanuit de [Azure-portal](https://portal.azure.com).
1. Typ `connection` in het zoekvak en druk op Enter om te zoeken, waardoor u naar de **verbindingsbron** gaat, gepubliceerd door Microsoft.
1. Selecteer **Maken** om uw verbinding te maken. 
1. Selecteer op het tabblad **Basisbeginselen** de volgende waarden en selecteer **OK**. 
    1. Selecteer `VNet-to-VNet` voor het **type Verbinding**. 
    1. Selecteer uw abonnement in de vervolgkeuzelijst. 
    1. Selecteer de resourcegroep voor uw beheerde instantie in de vervolgkeuzelijst. 
    1. De locatie van uw primaire beheerde instantie selecteren in de vervolgkeuzelijst 
1. Selecteer of voer op het tabblad **Instellingen** de volgende waarden in en selecteer **OK:**
    1. Kies de primaire netwerkgateway voor de eerste `Primary-Gateway`virtuele **netwerkgateway,** zoals .  
    1. Kies de secundaire netwerkgateway voor de `Secondary-Gateway`tweede virtuele **netwerkgateway,** zoals . 
    1. Schakel het selectievakje naast **Bidirectionele connectiviteit instellen**in. 
    1. Laat de standaardnaam van de primaire verbinding over of wijzig deze tot een waarde naar keuze. 
    1. Geef een **gedeelde sleutel (PSK)** op `mi1m2psk`voor de verbinding, zoals . 

   ![Gatewayverbinding maken](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Controleer op het tabblad **Overzicht** de instellingen voor uw bidirectionele verbinding en selecteer **OK** om uw verbinding te maken. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak verbindingen tussen de twee gateways met PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>De failovergroep maken 
Maak de failovergroep voor uw beheerde exemplaren met de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak de failovergroep voor uw beheerde exemplaren met Azure-portal. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure-portal.](https://portal.azure.com) Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer de primaire beheerde instantie die u wilt toevoegen aan de failovergroep.  
1. Navigeer **onder Instellingen**naar **Instantiefailovergroepen** en kies vervolgens de **groep Toevoegen** om de pagina **Failovergroep instantie** te openen. 

   ![Een failovergroep toevoegen](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Typ op de pagina **Failovergroep instance** de naam van uw failovergroep en kies vervolgens de secundaire beheerde instantie in de vervolgkeuzelijst. Selecteer **Maken** om de failovergroep te maken. 

   ![Failovergroep maken](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Zodra de implementatie van failovergroepen is voltooid, wordt u teruggezet naar de **groeppagina Failover.** 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak de failovergroep voor uw beheerde instanties met PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Testfailover

Test failover van uw failovergroep met behulp van de Azure-portal of PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Test failover van uw failovergroep met behulp van de Azure-portal. 

1. Navigeer naar uw _secundaire_ beheerde instantie in de [Azure-portal](https://portal.azure.com) en selecteer **Instantiefailovergroepen** onder instellingen. 
1. Controleer welke beheerde instantie de primaire instantie is en welke beheerde instantie de secundaire instantie is. 
1. Selecteer **Failover** en selecteer **Vervolgens Ja** op de waarschuwing dat de verbinding met TDS-sessies wordt verbroken. 

   ![Fail over de failovergroep](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Controleer welke manged instantie de primaire is en welke instantie de secundaire is. Als failover geslaagd is, moeten de twee instanties van rol zijn gewisseld. 

   ![Beheerde exemplaren zijn van rol gewisseld na failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Ga naar de nieuwe _secundaire_ beheerde instantie en selecteer **Failover** nogmaals om de primaire instantie terug naar de primaire rol te laten mislukken. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Test failover van uw failovergroep met PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Eindpunt voor listener zoeken

Zodra uw failovergroep is geconfigureerd, werkt u de verbindingstekenreeks voor uw toepassing bij naar het listenereindpunt. Hierdoor blijft uw toepassing verbonden met de failovergroeplistener, in plaats van met de primaire database, elastische pool of beheerde instantie. Op die manier hoeft u de verbindingstekenreeks niet elke keer handmatig bij te werken wanneer uw Azure SQL-databaseentiteit mislukt en wordt het verkeer doorgestuurd naar de entiteit die momenteel primair is. 

Het listenereindpunt is in `fog-name.database.windows.net`de vorm van , en is zichtbaar in de Azure-portal, bij het weergeven van de failovergroep:

![Tekenreeks failovergroepverbinding](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Opmerkingen

- Als u een failovergroep voor een enkele of samengevoegde database verwijdert, wordt de replicatie niet gestopt en wordt de gerepliceerde database niet verwijderd. U moet georeplicatie handmatig stoppen en de database van de secundaire server verwijderen als u een enkele of samengevoegde database wilt toevoegen aan een failovergroep nadat deze is verwijderd. Als u geen van beide dingen `The operation cannot be performed due to multiple errors` doet, kan dit leiden tot een fout die vergelijkbaar is met wanneer u de database probeert toe te voegen aan de failovergroep. 


## <a name="next-steps"></a>Volgende stappen

Zie de volgende zelfstudies voor gedetailleerde stappen die een failovergroep configureren:
- [Eén database toevoegen aan een failovergroep](sql-database-single-database-failover-group-tutorial.md)
- [Een elastische pool aan een failovergroep toevoegen](sql-database-elastic-pool-failover-group-tutorial.md)
- [Beheerde exemplaren toevoegen aan een failovergroep](sql-database-managed-instance-failover-group-tutorial.md)
 
Zie [georeplicatie-](sql-database-active-geo-replication.md) en [autofailovergroepen](sql-database-auto-failover-group.md)voor een overzicht van azure SQL Database-opties voor hoge beschikbaarheid. 
