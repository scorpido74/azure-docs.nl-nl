---
title: Lees replica's beheren-Azure PowerShell-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL met behulp van Power shell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 9ac85299311c1fd233988c6472d6325934dd42dd
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614536"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Lees replica's maken en beheren in Azure Database for MySQL met behulp van Power shell

In dit artikel leert u hoe u in de Azure Database for MySQL-service Lees replica's maakt en beheert met behulp van Power shell. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

## <a name="azure-powershell"></a>Azure PowerShell

U kunt met behulp van Power shell Lees replica's maken en beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de AZ. MySql Power shell-module in preview is, moet u deze afzonderlijk van de AZ Power shell-module installeren met `Install-Module -Name Az.MySql -AllowPrerelease`behulp van de volgende opdracht:.
> Zodra de AZ. MySql Power shell-module algemeen beschikbaar is, wordt het onderdeel van toekomstige AZ Power shell-module releases en is deze systeem eigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de hoofd server zich in een van deze prijs categorieën bevindt.

### <a name="create-a-read-replica"></a>Een lees replica maken

Een lees replica-server kan worden gemaakt met behulp van de volgende opdracht:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Voor `New-AzMySqlServerReplica` de opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de replica-server is gemaakt.  |
| Naam | mydemoreplicaserver | De naam van de nieuwe replica server die wordt gemaakt. |

Gebruik de **locatie** parameter om een lees replica te maken. In het volgende voor beeld wordt een replica gemaakt in de regio **VS-West** .

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica.

Lees replica's worden standaard gemaakt met dezelfde server configuratie als de Master, tenzij de **SKU** -para meter is opgegeven.

> [!NOTE]
> Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de Master om ervoor te zorgen dat de replica kan blijven werken met de Master.

### <a name="list-replicas-for-a-master-server"></a>Replica's voor een hoofd server weer geven

Als u alle replica's voor een bepaalde hoofd server wilt weer geven, voert u de volgende opdracht uit:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Voor `Get-AzMySqlReplica` de opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| ServerName | mydemoserver | De naam of ID van de hoofd server. |

### <a name="delete-a-replica-server"></a>Een replica server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd `Remove-AzMySqlServer` door de cmdlet uit te voeren.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Een hoofd server verwijderen

> [!IMPORTANT]
> Als u een hoofdserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de hoofdserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een master server wilt verwijderen, kunt u `Remove-AzMySqlServer` de cmdlet uitvoeren.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Database for MySQL server opnieuw opstarten met Power shell](howto-restart-server-powershell.md)
