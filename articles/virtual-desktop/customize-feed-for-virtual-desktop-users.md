---
title: Feed aanpassen voor Windows-gebruikers met virtueel bureau blad-Azure
description: Het aanpassen van de feed voor Windows-virtuele bureau blad-gebruikers met Power shell-cmdlets.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 49b678bea820f3cbead7479bb414ca9f35a29fa4
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163620"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Feed aanpassen voor Windows Virtual Desktop-gebruikers

U kunt de feed aanpassen zodat de RemoteApp-en extern bureau blad-resources op een herken bare manier worden weer gegeven voor uw gebruikers.

[Down load en Importeer eerst de Windows Virtual Desktop Power shell-module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan. Daarna voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>De weergave naam voor een RemoteApp aanpassen

U kunt de weergave naam voor een gepubliceerde RemoteApp wijzigen door de beschrijvende naam in te stellen. De beschrijvende naam is standaard hetzelfde als de naam van het RemoteApp-programma.

Voer de volgende Power shell-cmdlet uit om een lijst met gepubliceerde RemoteApps voor een app-groep op te halen:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName is gemarkeerd.](media/get-rdsremoteapp.png)

Voer de volgende Power shell-cmdlet uit om een beschrijvende naam toe te wijzen aan een RemoteApp:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Een scherm opname van de Power shell-cmdlet Set-RDSRemoteApp met de naam en de nieuwe FriendlyName is gemarkeerd.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>De weergave naam voor een Extern bureaublad aanpassen

U kunt de weergave naam voor een gepubliceerd extern bureau blad wijzigen door een beschrijvende naam in te stellen. Als u hand matig een hostgroep en een groep met desktop-apps hebt gemaakt via Power shell, is de standaard beschrijvende naam "Session Desktop". Als u een groep voor een hostgroep en een bureau blad-app hebt gemaakt via de GitHub Azure Resource Manager sjabloon of de Azure Marketplace-aanbieding, is de standaard beschrijvende naam hetzelfde als de naam van de hostgroep.

Voer de volgende Power shell-cmdlet uit om de resource van het externe bureau blad op te halen:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName is gemarkeerd.](media/get-rdsremotedesktop.png)

Voer de volgende Power shell-cmdlet uit om een beschrijvende naam toe te wijzen aan de extern bureau blad-resource:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Een scherm opname van de Power shell-cmdlet Set-RDSRemoteApp met de naam en de nieuwe FriendlyName is gemarkeerd.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de feed voor gebruikers hebt aangepast, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om het te testen. Als u dit wilt doen, gaat u naar de uitleg verbinding maken met Windows virtueel bureau blad:
    
 * [Verbinding maken vanaf Windows 10 of Windows 7](connect-windows-7-and-10.md)
 * [Verbinding maken via een webbrowser](connect-web.md) 
