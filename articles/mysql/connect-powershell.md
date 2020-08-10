---
title: Een verbindingsreeks genereren met PowerShell - Azure Database for MySQL
description: Deze quickstart biedt een Azure PowerShell-voorbeeld voor het genereren van een verbindingsreeks te maken met Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544915"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Azure Database for MySQL: Een verbindingsreeks genereren met PowerShell

In dit artikel wordt beschreven hoe u een verbindingsreeks genereert voor een Azure Database for MySQL-server. U kunt een verbindingsreeks gebruiken om verbinding te maken met een Azure Database for MySQL vanuit verschillende toepassingen.

## <a name="requirements"></a>Vereisten

In dit artikel worden de resources die in de volgende handleiding zijn gemaakt, als uitgangspunt gebruikt:

* [Snelstart: Een Azure Database for MySQL-server maken met behulp van PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De cmdlet `Get-AzMySqlConnectionString` wordt gebruikt om een verbindingsreeks te genereren voor het verbinden van toepassingen met Azure Database for MySQL. In het volgende voorbeeld wordt de verbindingsreeks voor een PHP-client uit **mydemoserver** geretourneerd.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Geldige waarden voor de parameter `Client` zijn:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for MySQL ontwerpen met PowerShell](tutorial-design-database-using-powershell.md)
