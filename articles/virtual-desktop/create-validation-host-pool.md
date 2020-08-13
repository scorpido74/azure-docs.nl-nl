---
title: Windows Virtual Desktop-hostgroep voor service-updates - Azure
description: Een validatiehostgroep maken om service-updates te bewaken voordat updates worden geïmplementeerd voor productie.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8eac40ad958a10b8c853304ee2be8b2dc27af1a2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008709"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Zelfstudie: Een hostpool voor het valideren van service-updates maken

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows Virtual Desktop-tenantomgevingen. Voordat u hostgroepen in uw productieomgeving implementeert, raden we u ten zeerste aan een validatiehostgroep te maken. Updates worden eerst toegepast op validatiehostgroepen, zodat u service-updates kunt bewaken voordat deze in de productieomgeving worden geïmplementeerd. Zonder een validatiehostgroep kan het zijn dat u wijzigingen over het hoofd ziet die fouten veroorzaken. Dit kan leiden tot uitvaltijd voor gebruikers in uw productieomgeving.

Om ervoor te zorgen dat uw apps goed werken met de nieuwste updates, moet de validatiehostgroep zo veel mogelijk lijken op de hostgroepen in uw productieomgeving. Gebruikers moeten net zo vaak verbinding maken met de validatiehostgroep als met de productiehostgroep. Als u automatische tests gebruikt voor uw hostgroep, moet u deze ook uitvoeren op de validatiehostgroep.

U kunt problemen in de validatiehostgroep opsporen met [de diagnostische functie](diagnostics-role-service.md) of de [artikelen voor het oplossen van problemen met Windows Virtual Desktop](troubleshoot-set-up-overview.md).

>[!NOTE]
> U wordt aangeraden de validatiehostgroep te laten staan om alle toekomstige updates te testen.

>[!IMPORTANT]
>Momenteel doen zich in Windows Virtual Desktop met Azure Resource Management-integratie enige problemen voor met het in- en uitschakelen van validatieomgevingen. Dit artikel zal worden bijgewerkt zodra het probleem is opgelost.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, volgt u de instructies in [De PowerShell-module van Windows Virtual Desktop instellen](powershell-module.md) om uw PowerShell-module in te stellen en zich bij Azure aan te melden.

## <a name="create-your-host-pool"></a>Uw hostgroep maken

U kunt een hostgroep maken door de instructies in een van deze artikelen te volgen:
- [Zelfstudie: Een hostpool maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Uw hostgroep definiëren als een validatiehostgroep

Voer de volgende PowerShell-cmdlets uit om de nieuwe hostgroep te definiëren als een validatiehostgroep. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Voer de volgende PowerShell-cmdlet uit om te controleren of de validatie-eigenschap is ingesteld. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

De resultaten van de cmdlet moeten er ongeveer als volgt uitzien:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Updateplanning

Service-updates worden maandelijks uitgevoerd. Als er grote problemen zijn, worden er sneller essentiële updates uitgegeven.

Als er service-updates beschikbaar zijn, moet u ervoor zorgen dat dagelijks een kleine groep gebruikers zich aanmeldt om de omgeving te valideren. U wordt aangeraden onze [TechCommunity-site](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) regelmatig te bezoeken en berichten met WVDUPdate te volgen om op de hoogte te blijven van service-updates.

## <a name="next-steps"></a>Volgende stappen

Nu u een validatiehostgroep hebt gemaakt, kunt u leren hoe u Azure Service Health gebruikt om uw Windows Virtual Desktop-implementatie te bewaken.

> [!div class="nextstepaction"]
> [Servicewaarschuwingen instellen](./set-up-service-alerts.md)
