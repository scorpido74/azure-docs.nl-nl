---
title: RDP-eigenschappen aanpassen met PowerShell - Azure
description: Rdp-eigenschappen voor Windows Virtual Desktop aanpassen met PowerShell-cmdlets.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128061"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Eigenschappen van Extern bureaublad-protocol aanpassen voor een hostgroep

Door de RDP-eigenschappen (Remote Desktop Protocol) van een hostpool (Remote Desktop Protocol) aan te pas, zoals multi-monitor ervaring en audioomleiding, u een optimale ervaring voor uw gebruikers bieden op basis van hun behoeften. U RDP-eigenschappen in Windows Virtual Desktop aanpassen met de parameter **-CustomRdpProperty** in de cmdlet **Set-RdsHostPool.**

Zie [ondersteunde RDP-bestandsinstellingen](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) voor een volledige lijst met ondersteunde eigenschappen en de standaardwaarden ervan.

Download en importeer eerst [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Standaard-RDP-eigenschappen

Standaard bevatten gepubliceerde RDP-bestanden de volgende eigenschappen:

|RDP-eigenschappen | Desktopcomputers | RemoteApps |
|---|---| --- |
| Modus met meerdere beeldschermen | Ingeschakeld | N.v.t. |
| Omleidingen van het station ingeschakeld | Schijven, klembord, printers, COM-poorten, USB-apparaten en smartcards| Stations, klembord en printers |
| Externe audiomodus | Lokaal spelen | Lokaal spelen |

Alle aangepaste eigenschappen die u definieert voor de hostgroep, overschrijven deze standaardwaarden.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Eén aangepaste RDP-eigenschap toevoegen of bewerken

Als u één aangepaste RDP-eigenschap wilt toevoegen of bewerken, voert u de volgende PowerShell-cmdlet uit:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Een screenshot van PowerShell cmdlet Get-RDSRemoteApp met naam en FriendlyName gemarkeerd.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Meerdere aangepaste RDP-eigenschappen toevoegen of bewerken

Als u meerdere aangepaste RDP-eigenschappen wilt toevoegen of bewerken, voert u de volgende PowerShell-cmdlets uit door de aangepaste RDP-eigenschappen op te geven als een tekenreeks die is gescheiden door de puntkomma:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Een screenshot van PowerShell cmdlet Get-RDSRemoteApp met naam en FriendlyName gemarkeerd.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Alle aangepaste RDP-eigenschappen opnieuw instellen

U afzonderlijke aangepaste RDP-eigenschappen opnieuw instellen op hun standaardwaarden door de instructies in [Een aangepaste RDP-eigenschap toevoegen of bewerken,](#add-or-edit-a-single-custom-rdp-property)of u alle aangepaste RDP-eigenschappen voor een hostgroep opnieuw instellen door de volgende PowerShell-cmdlet uit te voeren:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Een screenshot van PowerShell cmdlet Get-RDSRemoteApp met naam en FriendlyName gemarkeerd.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de RDP-eigenschappen voor een bepaalde hostgroep hebt aangepast, u zich aanmelden bij een Windows Virtual Desktop-client om ze te testen als onderdeel van een gebruikerssessie. Deze volgende twee How-tos vertellen u hoe u verbinding maken met een sessie met behulp van de client van uw keuze:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-and-10.md)
- [Verbinding maken met de webclient](connect-web.md)
