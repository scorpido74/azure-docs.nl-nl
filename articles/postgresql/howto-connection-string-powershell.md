---
title: Een connection string genereren met Power shell-Azure Database for PostgreSQL
description: In dit artikel vindt u een Azure PowerShell voor beeld van het genereren van een connection string om verbinding te maken met Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908888"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Een Azure Database for PostgreSQL connection string genereren met Power shell

In dit artikel wordt beschreven hoe u een connection string genereert voor een Azure Database for PostgreSQL-server. U kunt een connection string gebruiken om verbinding te maken met een Azure Database for PostgreSQL van verschillende toepassingen.

## <a name="requirements"></a>Vereisten

In dit artikel worden de resources gebruikt die in de volgende hand leiding zijn gemaakt als uitgangs punt:

* [Snelstartgids: een Azure Database for PostgreSQL-server maken met behulp van Power shell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De `Get-AzPostgreSqlConnectionString` cmdlet wordt gebruikt voor het genereren van een Connection String voor het verbinden van toepassingen met Azure database for PostgreSQL. In het volgende voor beeld wordt de connection string voor een PHP-client uit **mydemoserver**geretourneerd.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Geldige waarden voor de `Client` para meter zijn:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL server parameters aanpassen met behulp van Power shell](howto-configure-server-parameters-using-powershell.md)
