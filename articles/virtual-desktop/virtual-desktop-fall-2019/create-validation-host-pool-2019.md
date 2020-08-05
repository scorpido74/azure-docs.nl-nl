---
title: Windows Virtual Desktop (classic)-hostgroep voor service-updates - Azure
description: Een validatiehostgroep maken om service-updates te bewaken voordat updates worden geïmplementeerd voor productie.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 448f02990798869e3dd5a804d1b0e0038b810a24
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288667"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>Zelfstudie: Een hostgroep maken voor het valideren van service-updates in Windows Virtual Desktop (classic)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (classic), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../create-validation-host-pool.md) als u probeert Azure Resource Manager Windows Virtual Desktop-objecten te beheren.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows Virtual Desktop-tenantomgevingen. Voordat u hostgroepen in uw productieomgeving implementeert, raden we u ten zeerste aan een validatiehostgroep te maken. Updates worden eerst toegepast op validatiehostgroepen, zodat u service-updates kunt bewaken voordat deze in de productieomgeving worden geïmplementeerd. Zonder een validatiehostgroep kan het zijn dat u wijzigingen over het hoofd ziet die fouten veroorzaken. Dit kan leiden tot uitvaltijd voor gebruikers in uw productieomgeving.

Om ervoor te zorgen dat uw apps goed werken met de nieuwste updates, moet de validatiehostgroep zo veel mogelijk lijken op de hostgroepen in uw productieomgeving. Gebruikers moeten net zo vaak verbinding maken met de validatiehostgroep als met de productiehostgroep. Als u automatische tests gebruikt voor uw hostgroep, moet u deze ook uitvoeren op de validatiehostgroep.

U kunt problemen in de validatiehostgroep opsporen met [de diagnostische functie](diagnostics-role-service-2019.md) of de [artikelen voor het oplossen van problemen met Windows Virtual Desktop](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> U wordt aangeraden de validatiehostgroep te laten staan om alle toekomstige updates te testen.

[Download en importeer eerst de PowerShell-module van Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/) als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Uw hostgroep maken

U kunt een hostgroep maken door de instructies in een van deze artikelen te volgen:
- [Zelfstudie: Een hostpool maken met Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Een hostpool maken met een Azure Resource Manager-sjabloon](create-host-pools-arm-template.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Uw hostgroep definiëren als een validatiehostgroep

Voer de volgende PowerShell-cmdlets uit om de nieuwe hostgroep te definiëren als een validatiehostgroep. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Voer de volgende PowerShell-cmdlet uit om te controleren of de validatie-eigenschap is ingesteld. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

De resultaten van de cmdlet moeten er ongeveer als volgt uitzien:

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

## <a name="update-schedule"></a>Updateplanning

Service-updates worden maandelijks uitgevoerd. Als er grote problemen zijn, worden er sneller essentiële updates uitgegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u een validatiehostgroep hebt gemaakt, kunt u leren hoe u Azure Service Health gebruikt om uw Windows Virtual Desktop-implementatie te bewaken. 

> [!div class="nextstepaction"]
> [Servicewaarschuwingen instellen](set-up-service-alerts-2019.md)
