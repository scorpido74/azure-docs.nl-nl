---
title: App-groepen voor Windows Virtual Desktop Power shell-Azure beheren
description: Virtuele bureau blad-app-groepen voor Windows beheren met Power shell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 531c7a819bf83edff2756fe1e62859bcb8fef459
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615264"
---
# <a name="manage-app-groups-using-powershell"></a>App-groepen beheren met Power shell

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/manage-app-groups-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

De standaard app-groep die is gemaakt voor een nieuwe Windows Virtual Desktop-hostgroep publiceert ook het volledige bureau blad. Daarnaast kunt u een of meer RemoteApp-toepassings groepen maken voor de hostgroep. Volg deze zelf studie voor het maken van een RemoteApp-app-groep en het publiceren van afzonderlijke apps in het **Start** menu.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een RemoteApp-groep maken.
> * Toegang verlenen aan RemoteApp-program ma's.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de instructies in [de Power shell-module instellen](powershell-module.md) hebt gevolgd om uw Power shell-module in te stellen en u aan te melden bij uw Azure-account.

## <a name="create-a-remoteapp-group"></a>Een RemoteApp-groep maken

Een RemoteApp-groep maken met Power shell:

1. Voer de volgende Power shell-cmdlet uit om een nieuwe lege RemoteApp-toepassings groep te maken.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. Beschrijving Als u wilt controleren of de app-groep is gemaakt, kunt u de volgende cmdlet uitvoeren om een lijst met alle app-groepen voor de hostgroep weer te geven.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Voer de volgende cmdlet uit om een lijst met apps in het menu **Start** op te halen voor de installatie kopie van de virtuele machine van de hostgroep. Noteer de waarden voor **filepath**, **IconPath**, **Icon index**en andere belang rijke informatie voor de toepassing die u wilt publiceren.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more 
   ```

   De uitvoer moet alle items in het menu Start weer geven in een indeling als volgt:

   ```powershell
   AppAlias            : access
   CommandLineArgument : 
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE 
   FriendlyName        : 
   IconIndex           : 0 
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe 
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access 
   Name                : 0301RAG/Access 
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   
   AppAlias            : charactermap 
   CommandLineArgument : 
   FilePath            : C:\windows\system32\charmap.exe 
   FriendlyName        : 
   IconIndex           : 0 
   IconPath            : C:\windows\system32\charmap.exe 
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map 
   Name                : 0301RAG/Character Map 
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems 
   ```
   
4. Voer de volgende cmdlet uit om de toepassing te installeren `AppAlias`op basis van. `AppAlias`wordt weer gegeven wanneer u de uitvoer uit stap 3 uitvoert.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

5. Beschrijving Voer de volgende cmdlet uit om een nieuw RemoteApp-programma te publiceren naar de toepassings groep die u in stap 1 hebt gemaakt.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

6. Voer de volgende cmdlet uit om te controleren of de app is gepubliceerd.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Herhaal de stappen 1 tot en met 5 voor elke toepassing die u voor deze app-groep wilt publiceren.
8. Voer de volgende cmdlet uit om gebruikers toegang te verlenen tot de RemoteApp-program ma's in de app-groep.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Volgende stappen

Als u deze hand leiding hebt, raadpleegt u [een hostgroep maken om service-updates te valideren](create-validation-host-pool.md). U kunt een groep validatie-host gebruiken om service-updates te controleren voordat u deze naar uw productie omgeving doorvoert.