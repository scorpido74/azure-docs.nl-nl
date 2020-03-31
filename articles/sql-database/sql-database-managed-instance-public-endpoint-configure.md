---
title: Openbaar eindpunt configureren - beheerde instantie
description: Meer informatie over het configureren van een openbaar eindpunt voor beheerde instantie
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256157"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Openbaar eindpunt configureren in beheerd exemplaar van Azure SQL Database

Openbaar eindpunt voor een [beheerde instantie](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) biedt gegevenstoegang tot uw beheerde instantie van buiten het [virtuele netwerk.](../virtual-network/virtual-networks-overview.md) U hebt toegang tot uw beheerde instantie via Azure-services met meerdere tenants, zoals Power BI, Azure App Service of een on-premises netwerk. Door het openbare eindpunt op een beheerde instantie te gebruiken, hoeft u geen VPN te gebruiken, wat kan helpen bij het voorkomen van problemen met vpn-doorvoer.

In dit artikel leer je hoe je:

> [!div class="checklist"]
> - Openbaar eindpunt inschakelen voor uw beheerde instantie in de Azure-portal
> - Openbaar eindpunt inschakelen voor uw beheerde instantie met PowerShell
> - Uw beheerde groep voor instantienetwerkbeveiliging configureren om verkeer toe te staan naar het beheerde eindpunt van de instantie
> - De tekenreeks voor de verbinding met beheerde instantie seinvoordeopenbare eindpuntverbinding verkrijgen

## <a name="permissions"></a>Machtigingen

Vanwege de gevoeligheid van gegevens die zich in een beheerde instantie bevinden, vereist de configuratie om beheerd openbaar eindpunt voor instanties in te schakelen een proces in twee stappen. Deze veiligheidsmaatregel houdt zich aan de scheiding van taken (SoD):

- Het inschakelen van openbaar eindpunt op een beheerde instantie moet worden gedaan door de beheerde instantiebeheerder. De beheerde instantiebeheerder is te vinden op **de overzichtspagina** van uw SQL-beheerde instantiebron.
- Verkeer toestaan met behulp van een netwerkbeveiligingsgroep die moet worden uitgevoerd door een netwerkbeheerder. Zie [machtigingen voor netwerkbeveiligingsgroepen](../virtual-network/manage-network-security-group.md#permissions)voor meer informatie.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Openbaar eindpunt inschakelen voor een beheerde instantie in de Azure-portal

1. De Azure-portal starten op<https://portal.azure.com/.>
1. Open de brongroep met de beheerde instantie en selecteer de **SQL-beheerde instantie** waarop u het openbare eindpunt wilt configureren.
1. Selecteer **in de beveiligingsinstellingen** het tabblad **Virtueel netwerk.**
1. Selecteer op de pagina Virtuele netwerkconfiguratie de optie **Inschakelen** en vervolgens het pictogram **Opslaan** om de configuratie bij te werken.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Openbaar eindpunt inschakelen voor een beheerde instantie met PowerShell

### <a name="enable-public-endpoint"></a>Openbaar eindpunt inschakelen

Voer de volgende PowerShell-opdrachten uit. Vervang **abonnement-id** door uw abonnements-ID. Vervang **ook de rg-naam** door de resourcegroep voor uw beheerde instantie en vervang **de mi-naam** door de naam van uw beheerde instantie.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Openbaar eindpunt uitschakelen

Als u het openbare eindpunt wilt uitschakelen met PowerShell, voert u de volgende opdracht uit (en vergeet u ook niet om de NSG voor de binnenkomende poort 3342 te sluiten als u deze hebt geconfigureerd):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Openbaar eindpuntverkeer toestaan in de netwerkbeveiligingsgroep

1. Als u de configuratiepagina van de beheerde instantie nog steeds hebt geopend, navigeert u naar het tabblad **Overzicht.** Anders gaat u terug naar de **SQL-beheerde instantiebron.** Selecteer de koppeling **Virtueel netwerk/subnet,** waarmee u naar de pagina Virtuele netwerkconfiguratie gaat.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selecteer het tabblad **Subnetten** in het linkerconfiguratiedeelvenster van uw virtuele netwerk en noteer de **beveiligingsgroep** voor uw beheerde instantie.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Ga terug naar uw resourcegroep die uw beheerde instantie bevat. U ziet de naam van de **netwerkbeveiligingsgroep** hierboven vermeld. Selecteer de naam die u wilt ingaan op de configuratiepagina van de netwerkbeveiligingsgroep.

1. Selecteer het tabblad **Binnenkomende beveiligingsregels** en Voeg een regel **toe** die een hogere prioriteit heeft dan de **deny_all_inbound** regel met de volgende instellingen: </br> </br>

    |Instelling  |Voorgestelde waarde  |Beschrijving  |
    |---------|---------|---------|
    |**Bron**     |Elk IP-adres of servicetag         |<ul><li>Selecteer voor Azure-services zoals Power BI de Azure Cloud Service Tag</li> <li>Gebruik NAT IP-adres voor uw computer of Azure VM</li></ul> |
    |**Poortbereiken van bron**     |*         |Laat dit over aan * (alle) als bronpoorten zijn meestal dynamisch toegewezen en als zodanig, onvoorspelbaar |
    |**Bestemming**     |Alle         |Bestemming als doel verlaten om verkeer toe te staan in het beheerde instantiesubnet |
    |**Bestemmingspoortbereiken**     |3342         |Doelpoort van scope naar 3342, het beheerde openbare TDS-eindpunt |
    |**Protocol**     |TCP         |Beheerde instantie maakt gebruik van TCP-protocol voor TDS |
    |**Actie**     |Toestaan         |Binnenkomend verkeer toestaan om beheerde instantie via het openbare eindpunt te beheren |
    |**Prioriteit**     |1300         |Controleer of deze regel een hogere prioriteit heeft dan de **deny_all_inbound** regel |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Poort 3342 wordt gebruikt voor openbare eindpuntverbindingen met beheerde instantie en kan op dit moment niet worden gewijzigd.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>De tekenreeks voor beheerde instantieverbinding voor openbare eindpunten verkrijgen

1. Navigeer naar de sql-beheerde instantieconfiguratiepagina die is ingeschakeld voor openbaar eindpunt. Selecteer het tabblad **Verbindingstekenreeksen** onder de configuratie **Instellingen.**
1. Houd er rekening mee dat de naam van de openbare eindpunthost wordt geleverd in de indeling <mi_name>. **openbare**.<dns_zone>.database.windows.net en dat de poort die wordt gebruikt voor de verbinding is 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het veilig beheren van Azure SQL Database-instantie met openbaar eindpunt](sql-database-managed-instance-public-endpoint-securely.md).