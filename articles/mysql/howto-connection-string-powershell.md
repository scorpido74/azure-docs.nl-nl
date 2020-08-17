---
title: Een verbindingsreeks genereren met PowerShell - Azure Database for MySQL
description: Dit artikel biedt een Azure PowerShell-voorbeeld voor het genereren van een verbindingsreeks om verbinding met Azure Database for MariaDB te maken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/5/2020
ms.openlocfilehash: 37a257e2ca7f354659c032833480dd207cc1bed9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839928"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Een Azure Database for MariaDB-verbindingsreeks genereren met PowerShell

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
> [Azure Database for MySQL-serverparameters aanpassen met behulp van PowerShell](howto-configure-server-parameters-using-powershell.md)
