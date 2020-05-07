---
title: Server opnieuw starten-Azure PowerShell-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een Azure Database for MySQL server opnieuw kunt opstarten met behulp van Power shell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 935459a398c07d3b4f61c76dec75b083a2354720
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609014"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Azure Database for MySQL server opnieuw opstarten met Power shell

In dit onderwerp wordt beschreven hoe u een Azure Database for MySQL server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor tijdens de bewerking een korte onderbreking optreedt.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De hoeveelheid tijd die nodig is voor het volt ooien van een herstart is afhankelijk van het MySQL-herstel proces. Om de herstarttijd te beperken, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de AZ. MySql Power shell-module in preview is, moet u deze afzonderlijk van de AZ Power shell-module installeren met `Install-Module -Name Az.MySql -AllowPrerelease`behulp van de volgende opdracht:.
> Zodra de AZ. MySql Power shell-module algemeen beschikbaar is, wordt het onderdeel van toekomstige AZ Power shell-module releases en is deze systeem eigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for MySQL-server maken met behulp van Power shell](quickstart-create-mysql-server-database-using-azure-powershell.md)
