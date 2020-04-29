---
title: Ingebouwde apps publiceren in virtueel bureau blad van Windows-Azure
description: Het publiceren van ingebouwde apps in Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127740"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Ingebouwde apps publiceren in virtueel bureau blad van Windows

In dit artikel wordt uitgelegd hoe u apps publiceert in uw virtuele Windows-desktop omgeving.

## <a name="publish-built-in-apps"></a>Ingebouwde apps publiceren

Een ingebouwde app publiceren:

1. Maak verbinding met een van de virtuele machines in uw hostgroep.
2. Volg de instructies in [dit artikel](/powershell/module/appx/get-appxpackage?view=win10-ps/)om de **PackageFamilyName** op te halen van de app die u wilt publiceren.
3. Voer ten slotte de volgende cmdlet uit `<PackageFamilyName>` met vervangen door de **PackageFamilyName** die u in de vorige stap hebt gevonden:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows virtueel bureau blad ondersteunt alleen het publiceren van apps met installatie locaties `C:\Program Files\Windows Apps`die beginnen met.

## <a name="update-app-icons"></a>App-pictogrammen bijwerken

Nadat u een app hebt gepubliceerd, heeft deze het standaard pictogram van de Windows-app in plaats van de normale pictogram afbeelding. Als u het pictogram wilt wijzigen in het gewone pictogram, plaatst u de afbeelding van het pictogram dat u wilt op een netwerk share. Ondersteunde afbeeldings indelingen zijn PNG, BMP, GIF, JPG, JPEG en ICO.

## <a name="publish-microsoft-edge"></a>Micro soft Edge publiceren

Het proces dat u gebruikt om micro soft Edge te publiceren, is iets anders dan het publicatie proces voor andere apps. Als u micro soft Edge wilt publiceren met de standaard startpagina, voert u deze cmdlet uit:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van feeds om te organiseren hoe apps worden weer gegeven voor gebruikers bij het [aanpassen van de feed voor Windows-gebruikers met een virtueel bureau blad](customize-feed-for-virtual-desktop-users.md).
- Meer informatie over de functie voor het toevoegen van MSIX-apps bij het [instellen van MSIX app attach](app-attach.md).

