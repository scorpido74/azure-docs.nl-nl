---
title: Server opnieuw starten-Azure PowerShell-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt opstarten met behulp van Power shell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: ff7a7b7d83089f575df1ac169556c2762dd674a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84050847"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Azure Database for MariaDB server opnieuw opstarten met Power shell

In dit onderwerp wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor tijdens de bewerking een korte onderbreking optreedt.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De hoeveelheid tijd die nodig is voor het volt ooien van een herstart is afhankelijk van het MariaDB-herstel proces. Om de herstarttijd te beperken, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](https://docs.microsoft.com/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.MariaDb in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Zodra de PowerShell-module Az.MariaDb algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for MariaDB-server maken met behulp van Power shell](quickstart-create-mariadb-server-database-using-azure-powershell.md)
