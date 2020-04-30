---
title: Private Link
description: Overzicht van de functie privé-eind punt
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 4338c179fb8c0eebbb64ac5b33dc5dd8878d0794
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176716"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>Persoonlijke koppeling voor Azure SQL Database en het Data Warehouse

Met persoonlijke koppeling kunt u via een **persoonlijk eind punt**verbinding maken met verschillende PaaS-Services in Azure. Ga naar de documentatie pagina van de [persoonlijke koppeling](../private-link/index.yml) voor een lijst met PaaS-services die functionaliteit voor persoonlijke koppelingen ondersteunen. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek [VNet](../virtual-network/virtual-networks-overview.md) en subnet. 

> [!IMPORTANT]
> Dit artikel is van toepassing op Azure SQL Server en op zowel SQL Database-als SQL Data Warehouse-data bases die zijn gemaakt op de Azure SQL-Server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse. Dit artikel is *niet* van toepassing op een implementatie van een **beheerd exemplaar** in Azure SQL database.

## <a name="data-exfiltration-prevention"></a>Preventie van gegevens exfiltration

Gegevens exfiltration in Azure SQL Database zijn wanneer een geautoriseerde gebruiker, zoals een database beheerder, gegevens uit het ene systeem kan extra heren en de andere locatie of het systeem buiten de organisatie kan verplaatsen. De gebruiker verplaatst bijvoorbeeld de gegevens naar een opslag account dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker die SQL Server Management Studio (SSMS) uitvoert binnen een Azure-VM die verbinding maakt met een SQL Database. Deze SQL Database bevindt zich in het Data Center vs-West. In het onderstaande voor beeld ziet u hoe u de toegang tot de open bare eind punten op SQL Database met behulp van besturings elementen voor netwerk toegang kunt beperken.

1. Schakel alle Azure-service **verkeer uit voor**SQL database via het open bare eind punt door de instelling Azure-Services toestaan in te scha kelen. Zorg ervoor dat er geen IP-adressen zijn toegestaan in de firewall regels op server-en database niveau. Zie [Azure SQL database en het Data Warehouse netwerk toegangs beheer](sql-database-networkaccess-overview.md)voor meer informatie.
1. Alleen verkeer naar het SQL Database toestaan met behulp van het privé-IP-adres van de virtuele machine. Zie de artikelen over [service-eind punten](sql-database-vnet-service-endpoint-rule-overview.md) en VNet- [firewall regels](sql-database-firewall-configure.md)voor meer informatie.
1. Verfijn op de virtuele Azure-machine het bereik van de uitgaande verbinding door [netwerk beveiligings groepen (nsg's)](../virtual-network/manage-network-security-group.md) en service tags als volgt te gebruiken
    - Geef een NSG-regel op om verkeer voor service label = SQL toe te staan. Westus: alleen verbinding toestaan met SQL Database in VS-West
    - Geef een NSG-regel (met een **hogere prioriteit**) op om verkeer te weigeren voor service Tags = SQL-verbindingen met SQL database in alle regio's weigeren

Aan het einde van deze installatie kan de Azure-VM alleen verbinding maken met SQL-data bases in de regio vs-West. De connectiviteit is echter niet beperkt tot één SQL Database. De virtuele machine kan nog steeds verbinding maken met SQL-data bases in de regio vs-West, met inbegrip van de data bases die geen onderdeel zijn van het abonnement. Hoewel we het bereik van de gegevens exfiltration in het bovenstaande scenario naar een bepaalde regio hebben gereduceerd, hebben we het niet geheel verwijderd.

Met een persoonlijke koppeling kunnen klanten nu netwerk toegangs beheer instellen, zoals Nsg's om de toegang tot het persoonlijke eind punt te beperken. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privé-eind punten. Een kwaadwillende Insider heeft alleen toegang tot de toegewezen PaaS-resource (bijvoorbeeld een SQL Database) en geen andere resource. 

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via persoonlijke peering

Wanneer klanten verbinding maken met het open bare eind punt vanaf on-premises computers, moet hun IP-adres worden toegevoegd aan de op IP gebaseerde firewall met behulp van een [firewall regel op server niveau](sql-database-server-level-firewall-rule.md). Hoewel dit model goed werkt voor het toestaan van toegang tot afzonderlijke machines voor ontwikkel-of test werkbelastingen, is het moeilijk te beheren in een productie omgeving.

Met een persoonlijke koppeling kunnen klanten cross-premises toegang tot het privé-eind punt bieden met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md), privé-peering of VPN-tunneling. Klanten kunnen vervolgens alle toegang uitschakelen via het open bare eind punt en de op IP gebaseerde firewall niet gebruiken om IP-adressen toe te staan.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Privé-koppeling instellen voor Azure SQL Database 

### <a name="creation-process"></a>Proces maken
Privé-eind punten kunnen worden gemaakt met behulp van de portal, Power shell of Azure CLI:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Goedkeurings proces
Zodra de netwerk beheerder het persoonlijke eind punt (PE) heeft gemaakt, kan de SQL-beheerder de verbinding met het privé-eind punt (PEC) met SQL Database beheren.

1. Ga naar de SQL Server-Resource in het Azure Portal volgens de stappen in de onderstaande scherm afbeelding

    - (1) Selecteer de verbindingen met het privé-eind punt in het linkerdeel venster
    - (2) toont een lijst met alle privé-eindpunt verbindingen (PECs)
    - (3) de ![scherm opname van het bijbehorende privé-eind punt (PE) van alle Pecs][3]

1. Selecteer een individuele PEC uit de lijst door deze te selecteren.
![Scherm opname van geselecteerde PEC][6]

1. De SQL-beheerder kan kiezen voor het goed keuren of afwijzen van een PEC en optioneel een antwoord op een korte tekst toevoegen.
![Scherm afbeelding van PEC-goed keuring][4]

1. Na goed keuring of weigering wordt in de lijst de juiste staat en de antwoord tekst weer gegeven.
![Scherm afbeelding van alle PECs na goed keuring][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Cases van een persoonlijke koppeling gebruiken voor Azure SQL Database 

Clients kunnen verbinding maken met het persoonlijke eind punt van hetzelfde VNet, gepeerd VNet in dezelfde regio, of via VNet-naar-VNet-verbindingen tussen regio's. Daarnaast kunnen clients via on-premises verbinding maken met behulp van ExpressRoute, persoonlijke peering of VPN-tunneling. Hieronder ziet u een vereenvoudigd diagram waarin de algemene gebruiks voorbeelden worden weer gegeven.

 ![Diagram van connectiviteits opties][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>De connectiviteit van een Azure-VM in hetzelfde Virtual Network (VNet) testen met SQL Database

Voor dit scenario wordt ervan uitgegaan dat u een virtuele machine van Azure (VM) hebt gemaakt met Windows Server 2016. 

1. [Start een extern bureaublad-sessie (RDP) en maak verbinding met de virtuele machine](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. U kunt vervolgens een aantal basis connectiviteits controles uitvoeren om ervoor te zorgen dat de virtuele machine verbinding maakt met SQL Database via het persoonlijke eind punt met behulp van de volgende hulpprogram ma's:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Connectiviteit controleren met Telnet

[Telnet-client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) is een Windows-functie die kan worden gebruikt om de verbinding te testen. Afhankelijk van de versie van het Windows-besturings systeem moet u deze functie mogelijk expliciet inschakelen. 

Open een opdracht prompt venster nadat u Telnet hebt geïnstalleerd. Voer de Telnet-opdracht uit en geef het IP-adres en het persoonlijke eind punt van de SQL Database op.

```
>telnet 10.1.1.5 1433
```

Wanneer Telnet verbinding maakt, wordt in het opdracht venster een leeg scherm weer gegeven, zoals in de onderstaande afbeelding:

 ![Diagram van Telnet][2]

### <a name="check-connectivity-using-psping"></a>Connectiviteit controleren met behulp van Psping

[Psping](/sysinternals/downloads/psping) kan als volgt worden gebruikt om te controleren of de verbinding met het privé-eind punt (PEC) luistert naar verbindingen op poort 1433.

Voer psping als volgt uit door de FQDN-naam voor uw SQL Database-Server en poort 1433 op te geven:

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

De uitvoer laat zien dat Psping het privé-IP-adres dat is gekoppeld aan de PEC kan pingen.

### <a name="check-connectivity-using-nmap"></a>Connectiviteit controleren met behulp van nmap

Nmap (Network Mapper) is een gratis en open source-hulp programma dat wordt gebruikt voor netwerk detectie en beveiligings controle. Ga voor meer informatie en de download koppeling naar https://nmap.org. U kunt dit hulp programma gebruiken om ervoor te zorgen dat het privé-eind punt luistert naar verbindingen op poort 1433.

Voer nmap als volgt uit door het adres bereik op te geven van het subnet dat als host fungeert voor het persoonlijke eind punt.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

In het resultaat ziet u dat één IP-adres actief is. die overeenkomt met het IP-adres voor het persoonlijke eind punt.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Connectiviteit controleren met behulp van SQL Server Management Studio (SSMS)
> [!NOTE]
> Gebruik de **FQDN-naam (Fully Qualified Domain Name)** van de-server in verbindings reeksen voor uw clients. Aanmeldings pogingen rechtstreeks naar het IP-adres worden uitgevoerd. Dit gedrag is inherent aan het ontwerp, omdat het persoonlijke eind punt verkeer naar de SQL-gateway in de regio routeert en de FQDN moet worden opgegeven voor geslaagde aanmeldingen.

Volg de onderstaande stappen om SSMS te gebruiken [om verbinding te maken met de SQL database](sql-database-connect-query-ssms.md). Nadat u verbinding hebt gemaakt met de SQL Database met behulp van SSMS, controleert u of u verbinding maakt vanaf het privé-IP-adres van de virtuele Azure-machine door de volgende query uit te voeren:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Beperkingen 
Verbindingen met een privé-eind punt ondersteunen alleen **proxy** als het [verbindings beleid](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Verbinding maken vanaf een Azure-VM in een peered Virtual Network (VNet) 

Configureer [VNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) om verbinding te maken met de SQL database van een Azure-vm in een gekoppeld VNet.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Verbinding maken vanaf een virtuele machine van Azure in VNet-naar-VNet-omgeving

Configureer de [vnet-naar-VNet VPN-gateway verbinding](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om verbinding te maken met een SQL database van een Azure-vm in een andere regio of een ander abonnement.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN

Als u verbinding wilt maken vanuit een on-premises omgeving met de SQL Database, kiest en implementeert u een van de volgende opties:
- [Punt-naar-site-verbinding](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Site-naar-site-VPN-verbinding](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Verbinding maken tussen een Azure SQL Data Warehouse en Azure Storage met poly base

Poly Base wordt vaak gebruikt voor het laden van gegevens in Azure SQL Data Warehouse van Azure Storage-accounts. Als het Azure Storage account waarvan u gegevens wilt laden, alleen toegang heeft tot een set VNet-subnetten via particuliere eind punten, service-eind punten of op IP gebaseerde firewalls, wordt de connectiviteit van poly Base naar het account verbroken. Voor het inschakelen van zowel poly base import-als export scenario's met Azure SQL Data Warehouse verbinding maken met Azure Storage die zijn beveiligd met een VNet, volgt u de stappen die [hier](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)worden beschreven. 



## <a name="next-steps"></a>Volgende stappen

- Zie [uw data base beveiligen](sql-database-security-overview.md) voor een overzicht van Azure SQL database beveiliging
- Zie [Azure SQL-connectiviteits architectuur](sql-database-connectivity-architecture.md) voor een overzicht van Azure SQL database connectiviteit

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
