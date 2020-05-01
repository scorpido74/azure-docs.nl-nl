---
title: Service-updates voor de hostgroep voor Windows Virtual Desktop-Azure
description: Een groep met validatie-hosts maken om service-updates te bewaken voordat updates voor productie worden uitgevoerd.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b74b7f0b79ad4064d7133a19316d6aec6bd5ba3a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611565"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Zelf studie: een hostgroep maken om service-updates te valideren

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows-Tenant omgevingen voor virtueel bureau blad. Voordat u hostgroepen in uw productie omgeving implementeert, raden we u ten zeerste aan een groep met validatie-hosts te maken. Updates worden eerst toegepast op groepen met validatie-hosts, zodat u service-updates kunt bewaken voordat deze naar uw productie omgeving worden uitgevouwen. Zonder een validatie groep detecteert u mogelijk geen wijzigingen die fouten introduceren. Dit kan leiden tot uitval tijd voor gebruikers in uw productie omgeving.

Om ervoor te zorgen dat uw apps samen werken met de nieuwste updates, moet de groep met validatie-hosts net zo vergelijkbaar zijn als de hostgroepen in uw productie omgeving. Gebruikers moeten net zo vaak verbinding maken met de groep met validatie-hosts als voor de hostgroep voor productie. Als u automatische tests voor uw hostgroep hebt, moet u geautomatiseerde tests voor de groep met validatie-hosts toevoegen.

U kunt problemen met de validatie hostgroep oplossen met behulp van [de diagnostische functie](diagnostics-role-service.md) of de Windows-procedure voor het [oplossen van problemen met virtueel bureau blad](troubleshoot-set-up-overview.md).

>[!NOTE]
> U wordt aangeraden de groep validatie-host te verlaten om alle toekomstige updates te testen.

>[!IMPORTANT]
>De Windows Virtual Desktop lente 2020-release heeft momenteel geen problemen met het inschakelen en uitschakelen van validatie omgeving. Dit artikel wordt bijgewerkt wanneer het probleem is opgelost.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, volgt u de instructies in [de Power shell-module van Windows Virtual Desktop instellen](powershell-module.md) om uw Power shell-module in te stellen en u aan te melden bij Azure.

## <a name="create-your-host-pool"></a>Uw hostgroep maken

U kunt een hostgroep maken door de instructies in een van deze artikelen te volgen:
- [Zelf studie: een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Uw hostgroep definiëren als een groep met validatie-hosts

Voer de volgende Power shell-cmdlets uit om de nieuwe hostgroep te definiëren als een groep met validatie-hosts. Vervang de waarden tussen vier Kante haken door de waarden die relevant zijn voor uw sessie:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Voer de volgende Power shell-cmdlet uit om te controleren of de validatie-eigenschap is ingesteld. Vervang de waarden tussen vier Kante haken door de waarden die relevant zijn voor uw sessie.

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

## <a name="update-schedule"></a>Schema bijwerken

Service-updates gebeuren maandelijks. Als er sprake is van grote problemen, worden er op een meer frequente snelheid essentiële updates verstrekt.

Als er service-updates zijn, moet u ervoor zorgen dat er ten minste een kleine groep gebruikers zich op elke dag aanmeldt om de omgeving te valideren. We raden u aan om regel matig onze [TechCommunity-site](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) te bezoeken en alle berichten te volgen met WVDUPdate om op de hoogte te blijven van service-updates.

## <a name="next-steps"></a>Volgende stappen

Nu u een groep met validatie-hosts hebt gemaakt, kunt u leren hoe u Azure Service Health gebruikt voor het bewaken van uw Windows-implementatie voor virtueel bureau blad. 

> [!div class="nextstepaction"]
> [Servicewaarschuwingen instellen](./set-up-service-alerts.md)
