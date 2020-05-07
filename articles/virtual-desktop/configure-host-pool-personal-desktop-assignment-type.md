---
title: Toewijzings type voor het persoonlijk bureau blad van Windows Virtual Desktop-Azure
description: Het toewijzings type configureren voor een Windows virtueel bureau blad-hostgroep voor persoonlijk bureau blad.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8451dc14a7ed42aa92f9adbd5ad050936949e302
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612415"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Het toewijzings type voor de hostgroep voor persoonlijk bureau blad configureren

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

U kunt het toewijzings type van uw persoonlijke bureau blad-hostgroep zodanig configureren dat uw Windows Virtual Desktop-omgeving beter aansluit bij uw behoeften. In dit onderwerp wordt uitgelegd hoe u een automatische of directe toewijzing voor uw gebruikers kunt configureren.

>[!NOTE]
> De instructies in dit artikel zijn alleen van toepassing op Personal Desktop-hostgroepen, geen gegroepeerde hostgroepen, omdat gebruikers in gegroepeerde hostgroepen niet zijn toegewezen aan specifieke sessie hosts.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de Windows Power shell-module voor virtueel bureau blad al hebt gedownload en geïnstalleerd. Als dat niet het geval is, volgt u de instructies in [de Power shell-module instellen](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Automatische toewijzing configureren

Automatische toewijzing is het standaard toewijzings type voor nieuwe Personal Desktop-hostgroepen die zijn gemaakt in uw Windows Virtual Desktop-omgeving. Voor het automatisch toewijzen van gebruikers is geen specifieke sessiehost vereist.

Als u gebruikers automatisch wilt toewijzen, moet u ze eerst toewijzen aan de groep persoonlijke bureau blad-hosts zodat ze het bureau blad in hun feed kunnen zien. Wanneer een toegewezen gebruiker het bureau blad start in hun feed, claimt deze een beschik bare sessiehost als ze nog niet zijn verbonden met de hostgroep, waardoor het toewijzings proces wordt voltooid.

Als u een hostgroep wilt configureren om gebruikers automatisch toe te wijzen aan Vm's, voert u de volgende Power shell-cmdlet uit:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Voer de volgende Power shell-cmdlet uit om een gebruiker toe te wijzen aan de groep persoonlijke bureau blad-hosts:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Directe toewijzing configureren

In tegens telling tot automatische toewijzing moet u, wanneer u directe toewijzing gebruikt, de gebruiker toewijzen aan de Personal Desktop-hostgroep en een specifieke sessiehost voordat ze verbinding kunnen maken met hun persoonlijke bureau blad. Als de gebruiker alleen is toegewezen aan een hostgroep zonder een sessietime-toewijzing, hebben ze geen toegang tot bronnen.

Voer de volgende Power shell-cmdlet uit om een hostgroep zo te configureren dat deze directe toewijzing van gebruikers aan sessiehost vereist:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Voer de volgende Power shell-cmdlet uit om een gebruiker toe te wijzen aan de groep persoonlijke bureau blad-hosts:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Als u een gebruiker aan een specifieke sessiehost wilt toewijzen, voert u de volgende Power shell-cmdlet uit:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Volgende stappen

Nu u het persoonlijke bureau blad-toewijzings type hebt geconfigureerd, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om het te testen als onderdeel van een gebruikers sessie. In de volgende twee procedures wordt uitgelegd hoe u verbinding maakt met een sessie met de client van uw keuze:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-and-10.md)
- [Verbinding maken met de webclient](connect-web.md)
- [Verbinding maken met de Android-client](connect-android.md)
- [Verbinding maken met de iOS-client](connect-ios.md)
- [Verbinding maken met de macOS-client](connect-macos.md)