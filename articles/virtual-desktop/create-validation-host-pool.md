---
title: Updates van windows Virtual Desktop-hostpoolservice - Azure
description: Een groep validatiehostmaken om service-updates te controleren voordat updates naar de productie worden uitgerold.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365386"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Zelfstudie: Een hostgroep maken om service-updates te valideren

Hostpools zijn een verzameling van een of meer identieke virtuele machines binnen Windows Virtual Desktop-tenantomgevingen. Voordat u hostpools implementeert in uw productieomgeving, raden we u ten zeerste aan een hostpool voor validatie te maken. Updates worden eerst toegepast op validatiehostgroepen, zodat u service-updates controleren voordat ze worden uitgerold naar uw productieomgeving. Zonder een validatiehostgroep ontdekt u mogelijk geen wijzigingen die fouten introduceren, wat kan leiden tot downtime voor gebruikers in uw productieomgeving.

Om ervoor te zorgen dat uw apps werken met de nieuwste updates, moet de hostgroep voor validatie zo vergelijkbaar zijn met hostpools in uw productieomgeving. Gebruikers moeten net zo vaak verbinding maken met de groep validatiehost als met de productiehostpool. Als u geautomatiseerdtesten op uw hostpool hebt, moet u geautomatiseerde tests opnemen in de hostgroep voor validatie.

U problemen in de groep validatiehost opsporen met [de diagnostische functie](diagnostics-role-service.md) of de artikelen voor het oplossen van problemen met het oplossen van problemen met Windows Virtual [Desktop.](troubleshoot-set-up-overview.md)

>[!NOTE]
> We raden u aan de groep validatiehostop zijn plaats te laten om alle toekomstige updates te testen.

Download en importeer de Windows Virtual Desktop PowerShell-module voordat u begint met het downloaden en importeren van [de Windows Virtual Desktop PowerShell-module,](/powershell/windows-virtual-desktop/overview/)als u dat nog niet hebt gedaan. Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Uw hostpool maken

U een hostpool maken door de instructies in een van deze artikelen te volgen:
- [Zelfstudie: Een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostpool maken met een Azure Resource Manager-sjabloon](create-host-pools-arm-template.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Uw hostpool definiëren als een hostgroep voor validatie

Voer de volgende PowerShell-cmdlets uit om de nieuwe hostgroep te definiëren als een hostpool voor validatie. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Voer de volgende PowerShell-cmdlet uit om te controleren of de eigenschap validatie is ingesteld. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

De resultaten van de cmdlet moeten lijken op deze output:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Updateschema

Service-updates gebeuren maandelijks. Als er grote problemen zijn, worden er vaker kritieke updates geleverd.

## <a name="next-steps"></a>Volgende stappen

Nu u een groep validatiehost hebt gemaakt, u meer informatie krijgen over het gebruik van Azure Service Health om uw Windows Virtual Desktop-implementatie te controleren. 

> [!div class="nextstepaction"]
> [Servicewaarschuwingen instellen](./set-up-service-alerts.md)
