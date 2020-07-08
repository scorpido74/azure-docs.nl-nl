---
title: Opslag Azure PowerShell automatisch verg Roten-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u automatische groei opslag kunt inschakelen met behulp van Power shell in Azure Database for PostgreSQL.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 00fee745c8a12f02d7066d504ffce02e0ec1469f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84739971"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Opslag automatisch uitbreiden in Azure Database for PostgreSQL server met behulp van Power shell

In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

Met opslag automatisch verg Roten wordt voor komen dat uw server [de opslag limiet bereikt](/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit) en alleen-lezen wordt. Voor servers met 100 GB of minder ingerichte opslag, wordt de grootte verhoogd met 5 GB wanneer de vrije ruimte lager is dan 10%. Voor servers met meer dan 100 GB ingerichte opslag wordt de grootte verhoogd met 5% wanneer de beschik bare ruimte lager is dan 10 GB. De maximale opslag limieten gelden zoals opgegeven in de sectie opslag van de [prijs categorieën Azure database for PostgreSQL](/azure/postgresql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Houd er rekening mee dat opslag alleen omhoog kan worden geschaald.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](https://docs.microsoft.com/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for postgresql server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de Az.PostgreSql PowerShell-module in preview is, moet u deze afzonderlijk van de Az PowerShell-module installeren met behulp van de volgende opdracht: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Zodra de Az.PostgreSql PowerShell-module algemeen beschikbaar is, wordt deze onderdeel van toekomstige releases van Az PowerShell-modules en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-postgresql-server-storage-auto-grow"></a>Automatisch verg Roten van PostgreSQL-Server opslag inschakelen

Schakel het automatisch uitbreiden van de server in op een bestaande server met de volgende opdracht:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Automatisch verg Roten van de server inschakelen bij het maken van een nieuwe server met de volgende opdracht:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees replica's maken en beheren in azure database for PostgreSQL met behulp van Power shell](howto-read-replicas-powershell.md).
