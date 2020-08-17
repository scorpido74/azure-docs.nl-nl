---
title: Een verbindingsreeks genereren met PowerShell - Azure Database for MariaDB
description: Dit artikel biedt een Azure PowerShell-voorbeeld voor het genereren van een verbindingsreeks om verbinding met Azure Database for MySQL te maken.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/5/2020
ms.openlocfilehash: b4b0853cb71ccc85826d1230fb7828ed9df24b52
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839946"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Azure Database for MariaDB-verbindingsreeks genereren met PowerShell

In dit artikel wordt beschreven hoe u een verbindingsreeks genereert voor een Azure Database for MariaDB-server. U kunt een verbindingsreeks gebruiken om verbinding te maken met een Azure Database for MariaDB vanuit verschillende toepassingen.

## <a name="requirements"></a>Vereisten

In dit artikel worden de resources die in de volgende handleiding zijn gemaakt, als uitgangspunt gebruikt:

* [Quickstart: Een Azure Database for MariaDB-server maken met behulp van PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De cmdlet `Get-AzMariaDbConnectionString` wordt gebruikt om een verbindingsreeks te genereren voor het verbinden van toepassingen met Azure Database for MariaDB. In het volgende voorbeeld wordt de verbindingsreeks voor een PHP-client uit **mydemoserver** geretourneerd.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Azure Database for MariaDB-serverparameters aanpassen met behulp van PowerShell](howto-configure-server-parameters-using-powershell.md)
