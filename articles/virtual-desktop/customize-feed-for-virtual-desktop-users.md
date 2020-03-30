---
title: Feed aanpassen voor Windows Virtual Desktop-gebruikers - Azure
description: Feed aanpassen voor Windows Virtual Desktop-gebruikers met PowerShell-cmdlets.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128079"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Feed aanpassen voor Windows Virtual Desktop-gebruikers

U de feed aanpassen zodat de remoteapp- en externe bureaubladbronnen op een herkenbare manier worden weergegeven voor uw gebruikers.

Download en importeer eerst [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>De weergavenaam voor een RemoteApp aanpassen

U de weergavenaam voor een gepubliceerde RemoteApp wijzigen door de vriendelijke naam in te stellen. Standaard is de vriendelijke naam dezelfde als de naam van het RemoteApp-programma.

Voer de volgende PowerShell-cmdlet uit om een lijst met gepubliceerde RemoteApps voor een app-groep op te halen:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Een screenshot van PowerShell cmdlet Get-RDSRemoteApp met naam en FriendlyName gemarkeerd.](media/get-rdsremoteapp.png)

Als u een vriendelijke naam wilt toewijzen aan een RemoteApp, voert u de volgende PowerShell-cmdlet uit:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Een screenshot van PowerShell cmdlet Set-RDSRemoteApp met naam en nieuwe FriendlyName gemarkeerd.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>De weergavenaam voor een extern bureaublad aanpassen

U de weergavenaam voor een gepubliceerd extern bureaublad wijzigen door een vriendelijke naam in te stellen. Als u handmatig een hostpool en bureaublad-appgroep hebt gemaakt via PowerShell, is de standaardvriendelijke naam 'Sessiebureaublad'. Als u een hostgroep en bureaublad-appgroep hebt gemaakt via de gitHub Azure Resource Manager-sjabloon of de Azure Marketplace-aanbieding, is de standaardvriendelijke naam dezelfde als de naam van de hostgroep.

Voer de volgende PowerShell-cmdlet uit om de externe desktopbron op te halen:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Een screenshot van PowerShell cmdlet Get-RDSRemoteApp met naam en FriendlyName gemarkeerd.](media/get-rdsremotedesktop.png)

Als u een vriendelijke naam wilt toewijzen aan de externe desktopbron, voert u de volgende PowerShell-cmdlet uit:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Een screenshot van PowerShell cmdlet Set-RDSRemoteApp met naam en nieuwe FriendlyName gemarkeerd.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de feed voor gebruikers hebt aangepast, u zich aanmelden bij een Windows Virtual Desktop-client om deze uit te testen. Ga hiervoor verder met de how-to-tos verbinding met Windows Virtual Desktop:
    
 * [Verbinding maken vanaf Windows 10 of Windows 7](connect-windows-7-and-10.md)
 * [Verbinding maken via een webbrowser](connect-web.md) 
