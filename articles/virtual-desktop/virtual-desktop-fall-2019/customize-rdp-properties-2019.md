---
title: RDP-eigenschappen aanpassen met Power shell najaar 2019-Azure
description: RDP-eigenschappen voor virtuele Windows-Bureau bladen aanpassen met Power shell-cmdlets.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 844c59c6e5ea6d730c64fea5fb2dee51f602c382
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527622"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Remote Desktop Protocol eigenschappen voor een hostgroep aanpassen

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het najaar van 2019 die geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../customize-rdp-properties.md) als u Azure Resource Manager Windows Virtual Desktop-objecten wilt beheren die zijn geïntroduceerd in de update Lente 2020.

Als u de eigenschappen van de Remote Desktop Protocol (RDP) van een hostgroep wilt aanpassen, zoals de ervaring voor meerdere monitors en audio-omleiding, kunt u een optimale ervaring bieden aan uw gebruikers op basis van hun behoeften. U kunt RDP-eigenschappen in virtueel bureau blad van Windows aanpassen met behulp van de para meter **-CustomRdpProperty** in de cmdlet **set-RdsHostPool** .

Zie [ondersteunde RDP-Bestands instellingen](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) voor een volledige lijst met ondersteunde eigenschappen en hun standaard waarden.

Eerst [downloadt en importeert u de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Standaard RDP-eigenschappen

Gepubliceerde RDP-bestanden bevatten standaard de volgende eigenschappen:

|RDP-eigenschappen | Desktopcomputers | RemoteApps |
|---|---| --- |
| Modus voor meerdere monitors | Ingeschakeld | N.v.t. |
| Omleidingen van stations ingeschakeld | Stations, klem bord, printers, COM-poorten, USB-apparaten en-Smart Cards| Stations, klem bord en printers |
| Modus voor externe audio | Lokaal afspelen | Lokaal afspelen |

Aangepaste eigenschappen die u voor de hostgroep definieert, overschrijven deze standaard waarden.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Eén aangepaste RDP-eigenschap toevoegen of bewerken

Als u één aangepaste RDP-eigenschap wilt toevoegen of bewerken, voert u de volgende Power shell-cmdlet uit:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName is gemarkeerd.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Meerdere aangepaste RDP-eigenschappen toevoegen of bewerken

Als u meerdere aangepaste RDP-eigenschappen wilt toevoegen of bewerken, voert u de volgende Power shell-cmdlets uit door de aangepaste RDP-eigenschappen op te geven als een door punt komma's gescheiden teken reeks:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName is gemarkeerd.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Alle aangepaste RDP-eigenschappen opnieuw instellen

U kunt afzonderlijke aangepaste RDP-eigenschappen opnieuw instellen op de standaard waarden door de instructies in [een enkele aangepaste RDP-eigenschap toevoegen of bewerken](#add-or-edit-a-single-custom-rdp-property)te volgen, of u kunt alle aangepaste RDP-eigenschappen voor een hostgroep opnieuw instellen door de volgende Power shell-cmdlet uit te voeren:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName is gemarkeerd.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de RDP-eigenschappen voor een bepaalde hostgroep hebt aangepast, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om ze te testen als onderdeel van een gebruikers sessie. In de volgende twee procedures wordt uitgelegd hoe u verbinding maakt met een sessie met de client van uw keuze:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-10-2019.md)
- [Verbinding maken met de webclient](connect-web-2019.md)
