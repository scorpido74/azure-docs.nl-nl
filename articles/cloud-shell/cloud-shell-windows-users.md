---
title: Azure Cloud Shell voor Windows-gebruikers | Microsoft Documenten
description: Gids voor gebruikers die niet bekend zijn met Linux-systemen
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204140"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell in Azure Cloud Shell voor Windows-gebruikers

In mei 2018 werden wijzigingen [aangekondigd](https://azure.microsoft.com/blog/pscloudshellrefresh/) in PowerShell in Azure Cloud Shell.
De PowerShell-ervaring in Azure Cloud Shell draait [powershell core 6](https://github.com/powershell/powershell) nu in een Linux-omgeving.
Met deze wijziging kunnen er enkele verschillen zijn in de PowerShell-ervaring in Cloud Shell in vergelijking met wat wordt verwacht in een Windows PowerShell-ervaring.

## <a name="file-system-case-sensitivity"></a>Gevoeligheid van bestandssysteemcase

Het bestandssysteem is case-ongevoelig in Windows, terwijl op Linux het bestandssysteem casegevoelig is.
Voorheen `file.txt` `FILE.txt` en werden beschouwd als hetzelfde bestand, maar nu worden ze beschouwd als verschillende bestanden.
De juiste behuizing `tab-completing` moet worden gebruikt terwijl in het bestandssysteem.
PowerShell-specifieke ervaringen, `tab-completing` zoals cmdletnamen, parameters en waarden, zijn niet hoofdlettergevoelig.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell-aliassen vs Linux-hulpprogramma's

Sommige bestaande PowerShell-aliassen hebben dezelfde namen als ingebouwde Linux-opdrachten, `cat`zoals , ,`ls`, `sort`enz. `sleep` In PowerShell Core 6 zijn aliassen die botsen met ingebouwde Linux-commando's verwijderd.
Hieronder staan de gemeenschappelijke aliassen die zijn verwijderd, evenals hun gelijkwaardige opdrachten:  

|Alias verwijderd   |Equivalent, opdracht   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Aanhoudende $HOME

Eerdere gebruikers konden alleen scripts en andere bestanden in hun Cloud Drive blijven gebruiken.
Nu is de $HOME directory van de gebruiker ook voor alle sessies voortduurde.

## <a name="powershell-profile"></a>PowerShell-profiel

Standaard wordt het PowerShell-profiel van een gebruiker niet gemaakt.
Als u uw profiel `PowerShell` wilt `$HOME/.config`maken, maakt u een map onder .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Onder `$HOME/.config/PowerShell`, u uw `profile.ps1` profielbestanden `Microsoft.PowerShell_profile.ps1`maken - en/of.

## <a name="whats-new-in-powershell-core-6"></a>Nieuw in PowerShell Core 6

Raadpleeg de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) en de [powershell-blogpost voor](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) meer informatie over wat er nieuw is in PowerShell Core 6.
