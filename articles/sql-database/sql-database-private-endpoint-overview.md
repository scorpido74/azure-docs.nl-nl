---
title: Private Link
description: Overzicht van de functie Privéeindpunt
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: ab9c5c5c1134d2e09a790a788a3b7e55f807dd9b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78945375"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>Private Link voor Azure SQL Database en Data Warehouse

Met Private Link u verbinding maken met verschillende PaaS-services in Azure via een **privéeindpunt.** Ga naar de pagina [Privékoppelingsdocumentatie](../private-link/index.yml) voor een lijst met PaaS-services die de functionaliteit van Private Link ondersteunen. Een privéeindpunt is een privé-IP-adres binnen een specifiek [VNet](../virtual-network/virtual-networks-overview.md) en Subnet. 

> [!IMPORTANT]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database- als SQL Data Warehouse-databases die zijn gemaakt op de Azure SQL-server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse. Dit artikel is *niet* van toepassing op een **beheerde instantieimplementatie** in Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Preventie van gegevensexfiltratie

Gegevensexfiltratie in Azure SQL Database is wanneer een geautoriseerde gebruiker, zoals een databasebeheerder, gegevens uit het ene systeem kan extraheren en een andere locatie of systeem buiten de organisatie kan verplaatsen. De gebruiker verplaatst de gegevens bijvoorbeeld naar een opslagaccount dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker met SQL Server Management Studio (SSMS) in een Azure VM die verbinding maakt met een SQL-database. Deze SQL-database bevindt zich in het Datacenter van West-VS. In het onderstaande voorbeeld ziet u hoe u de toegang met openbare eindpunten in SQL Database beperken met behulp van netwerktoegangsbesturingselementen.

1. Schakel al het Azure-serviceverkeer naar SQL Database uit via het openbare eindpunt door Azure Services toestaan in te stellen om uit te **schakelen.** Zorg ervoor dat er geen IP-adressen zijn toegestaan in de firewallregels op server- en databaseniveau. Zie Azure [SQL Database- en Data Warehouse-netwerktoegangsbesturingselementen voor](sql-database-networkaccess-overview.md)meer informatie.
1. Geef alleen verkeer naar de SQL-database toe met het privé-IP-adres van de VM. Zie voor meer informatie de artikelen over [serviceeindpunt-](sql-database-vnet-service-endpoint-rule-overview.md) en [VNet-firewallregels](sql-database-firewall-configure.md).
1. Verklein op de Azure VM het bereik van uitgaande verbinding met behulp van [Network Security Groups (NSGs)](../virtual-network/manage-network-security-group.md) en Service Tags als volgt
    - Geef een NSG-regel op om verkeer toe te staan voor Service Tag = SQL. WestUs - alleen toestaan verbinding met SQL Database in West US
    - Geef een NSG-regel op (met een **hogere prioriteit)** om verkeer voor Service Tag = SQL te weigeren - het weigeren van verbindingen met SQL Database in alle regio's

Aan het einde van deze installatie kan de Azure VM alleen verbinding maken met SQL-databases in de regio West-VS. De connectiviteit is echter niet beperkt tot één SQL-database. De VM kan nog steeds verbinding maken met SQL-databases in de regio West-VS, inclusief de databases die geen deel uitmaken van het abonnement. Hoewel we het bereik van gegevensexfiltratie in het bovenstaande scenario hebben teruggebracht tot een specifieke regio, hebben we het niet helemaal geëlimineerd.

Met Private Link kunnen klanten nu netwerktoegangsbesturingselementen instellen, zoals NSG's om de toegang tot het privéeindpunt te beperken. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privéeindpunten. Een kwaadwillende insider heeft alleen toegang tot de toegewezen PaaS-bron (bijvoorbeeld een SQL-database) en geen andere bron. 

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via private peering

Wanneer klanten verbinding maken met het openbare eindpunt van on-premises machines, moet hun IP-adres worden toegevoegd aan de IP-gebaseerde firewall met behulp van een [firewallregel op serverniveau](sql-database-server-level-firewall-rule.md). Hoewel dit model goed werkt voor het toestaan van toegang tot individuele machines voor dev of testworkloads, is het moeilijk te beheren in een productieomgeving.

Met Private Link kunnen klanten toegang tot het privéeindpunt inschakelen via [ExpressRoute,](../expressroute/expressroute-introduction.md)private peering of VPN-tunneling. Klanten kunnen dan alle toegang via het openbare eindpunt uitschakelen en de IP-gebaseerde firewall niet gebruiken om IP-adressen toe te staan.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Private Link instellen voor Azure SQL Database 

### <a name="creation-process"></a>Creatieproces
Privéeindpunten kunnen worden gemaakt met de portal, PowerShell of Azure CLI:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [Powershell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Goedkeuringsproces
Zodra de netwerkbeheerder het Private Endpoint (PE) heeft gemaakt, kan de SQL-beheerder de Private Endpoint Connection (PEC) naar SQL Database beheren.

1. Navigeren naar de SQL-serverbron in de Azure-portal zoals weergegeven in de onderstaande schermafbeelding

    - (1) Selecteer de privéeindpuntverbindingen in het linkerdeelvenster
    - (2) Toont een lijst met alle Private Endpoint Connections (PECs)
    - (3) Overeenkomstige Private Endpoint ![(PE) gemaakt Screenshot van alle PECs][3]

1. Selecteer een individuele PEC in de lijst door deze te selecteren.
![Schermafbeelding van geselecteerde PEC][6]

1. De SQL-beheerder kan ervoor kiezen een PEC goed te keuren of af te wijzen en optioneel een korte tekstreactie toe te voegen.
![Schermafbeelding van pec-goedkeuring][4]

1. Na goedkeuring of afwijzing geeft de lijst de juiste status weer, samen met de antwoordtekst.
![Schermafbeelding van alle PECs na goedkeuring][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Voorbeelden van Private Link voor Azure SQL Database gebruiken 

Clients kunnen verbinding maken met het privéeindpunt vanuit hetzelfde VNet, VNet in dezelfde regio peered of via VNet-naar-VNet-verbinding tussen regio's. Daarnaast kunnen clients verbinding maken vanuit on-premises via ExpressRoute, private peering of VPN-tunneling. Hieronder vindt u een vereenvoudigd diagram met de algemene use cases.

 ![Diagram met connectiviteitsopties][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Connectiviteit met SQL-database testen vanaf een Azure VM in hetzelfde virtuele netwerk (VNet)

Stel dat u in dit scenario een VM (Azure Virtual Machine) hebt gemaakt met Windows Server 2016. 

1. [Start een RdP-sessie (Remote Desktop) en maak verbinding met de virtuele machine.](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) 
1. U vervolgens een aantal basisconnectiviteitscontroles uitvoeren om ervoor te zorgen dat de VM verbinding maakt met SQL Database via het privéeindpunt met behulp van de volgende hulpprogramma's:
    1. Telnet
    1. Psping Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Connectiviteit controleren met Telnet

[Telnet Client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) is een Windows-functie die kan worden gebruikt om connectiviteit te testen. Afhankelijk van de versie van het Windows-besturingssysteem moet u deze functie mogelijk expliciet inschakelen. 

Open een opdrachtpromptvenster nadat u Telnet hebt geïnstalleerd. Voer de opdracht Telnet uit en geef het IP-adres en het privéeindpunt van de SQL-database op.

```
>telnet 10.1.1.5 1433
```

Wanneer Telnet verbinding maakt, ziet u een leeg scherm in het opdrachtvenster, zoals de onderstaande afbeelding:

 ![Diagram van telnet][2]

### <a name="check-connectivity-using-psping"></a>Connectiviteit controleren met Psping

[Psping](/sysinternals/downloads/psping) kan als volgt worden gebruikt om te controleren of de Private endpoint connection (PEC) luistert naar verbindingen op poort 1433.

Voer psping als volgt uit door de FQDN voor uw SQL Database-server en poort 1433 te leveren:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Uit de uitvoer blijkt dat Psping het privé-IP-adres dat aan de PEC is gekoppeld, kan pingen.

### <a name="check-connectivity-using-nmap"></a>Connectiviteit controleren met Nmap

Nmap (Network Mapper) is een gratis en open-source tool die wordt gebruikt voor netwerkdetectie en beveiligingscontrole. Voor meer informatie en de https://nmap.orgdownload link, bezoek . U deze tool gebruiken om ervoor te zorgen dat het privéeindpunt luistert naar verbindingen op poort 1433.

Voer Nmap als volgt uit door het adresbereik van het subnet op te geven dat het privéeindpunt host.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Het resultaat laat zien dat één IP-adres omhoog is; die overeenkomt met het IP-adres voor het privéeindpunt.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Connectiviteit controleren met SQL Server Management Studio (SSMS)
> [!NOTE]
> Gebruik de **FQDN (Fully Qualified Domain Name)** van de server in verbindingstekenreeksen voor uw clients. Alle inlogpogingen die rechtstreeks op het IP-adres worden gedaan, mislukken. Dit gedrag is door het ontwerp, omdat privé eindpunt verkeer naar de SQL Gateway in de regio leidt en de FQDN moet worden opgegeven om aanmeldingen te laten slagen.

Volg de stappen hier om SSMS te gebruiken [om verbinding te maken met de SQL Database.](sql-database-connect-query-ssms.md) Nadat u verbinding hebt gemaakt met de SQL-database met SSMS, controleert u of u verbinding maakt vanaf het privé-IP-adres van de Azure VM door de volgende query uit te voeren:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Beperkingen 
Verbindingen met privéeindpunt **ondersteunen** proxy alleen als [verbindingsbeleid](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Verbinding maken vanaf een Azure VM in Peered Virtual Network (VNet) 

[VNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) configureren om connectiviteit met de SQL-database vast te stellen vanuit een Azure VM in een peered VNet.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Verbinding maken vanuit een Azure VM in vnet-naar-VNet-omgeving

Configureer [VNet-to-VNet VPN-gatewayverbinding](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om verbinding te maken met een SQL-database vanuit een Azure VM in een andere regio of abonnement.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN

Als u verbinding wilt maken met een on-premises omgeving met de SQL-database, kiest en implementeert u een van de opties:
- [Point-to-Site-verbinding](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Site-naar-site-VPN-verbinding](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Verbinding maken vanuit een Azure SQL Data Warehouse met Azure Storage met Polybase

PolyBase wordt vaak gebruikt om gegevens te laden in Azure SQL Data Warehouse vanuit Azure Storage-accounts. Als het Azure Storage-account waarvan u gegevens laadt, alleen toegang beperkt tot een set VNet-subnetten via Privéeindpunten, Serviceeindpunten of IP-gebaseerde firewalls, wordt de verbinding van PolyBase met het account verbroken. Volg de stappen die [u hier](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)biedt voor het inschakelen van scenario's voor het importeren en exporteren van PolyBase met Azure SQL Data Warehouse dat verbinding maakt met Azure Storage die is beveiligd met een VNet. 



## <a name="next-steps"></a>Volgende stappen

- Zie [Uw database beveiligen](sql-database-security-overview.md) voor een overzicht van azure SQL-databasebeveiliging
- Zie [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md) voor een overzicht van Azure SQL Database-connectiviteit

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
