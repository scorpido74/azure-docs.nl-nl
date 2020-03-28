---
title: App-groepen voor Windows Virtual Desktop beheren - Azure
description: Beschrijft hoe u Windows Virtual Desktop-tenants in Azure Active Directory instelt.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127810"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Zelfstudie: App-groepen voor Windows Virtueel bureaublad beheren

De standaardappgroep die is gemaakt voor een nieuwe Windows Virtual Desktop-hostgroep, publiceert ook het volledige bureaublad. Daarnaast u een of meer RemoteApp-toepassingsgroepen maken voor de hostgroep. Volg deze zelfstudie om een RemoteApp-appgroep te maken en afzonderlijke **apps voor het menu Start** te publiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een RemoteApp-groep.
> * Geef toegang tot RemoteApp-programma's.

Download en [importeer de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voordat u begint met het downloaden en importeren van de Windows Virtual Desktop PowerShell-module om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Een RemoteApp-groep maken

1. Voer de volgende PowerShell-cmdlet uit om een nieuwe lege RemoteApp-appgroep te maken.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Optioneel) Als u wilt controleren of de appgroep is gemaakt, u de volgende cmdlet uitvoeren om een lijst met alle appgroepen voor de hostgroep weer te geven.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Voer de volgende cmdlet uit om een lijst met apps voor **het menu Start** op de virtuele machineafbeelding van de hostpool te krijgen. Noteer de waarden voor **FilePath,** **IconPath,** **IconIndex**en andere belangrijke informatie voor de toepassing die u wilt publiceren.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Voer de volgende cmdlet uit `AppAlias`om de toepassing te installeren op basis van . `AppAlias`wordt zichtbaar wanneer u de uitvoer uitvoert vanaf stap 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Optioneel) Voer de volgende cmdlet uit om een nieuw RemoteApp-programma te publiceren in de toepassingsgroep die in stap 1 is gemaakt.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Voer de volgende cmdlet uit om te controleren of de app is gepubliceerd.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Herhaal stap 1–5 voor elke toepassing die u voor deze appgroep wilt publiceren.
8. Voer de volgende cmdlet uit om gebruikers toegang te geven tot de RemoteApp-programma's in de app-groep.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een app-groep maakt, deze vult met RemoteApp-programma's en gebruikers aan de app-groep toewijst. Zie de volgende zelfstudie voor meer informatie over het maken van een groep validatiehost. U een groep validatiehostgebruiken om service-updates te controleren voordat u deze uitrolt naar uw productieomgeving.

> [!div class="nextstepaction"]
> [Een hostpool voor het valideren van service-updates maken](./create-validation-host-pool.md)
