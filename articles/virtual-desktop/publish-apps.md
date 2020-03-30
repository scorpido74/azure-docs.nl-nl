---
title: Ingebouwde apps publiceren in Windows Virtual Desktop - Azure
description: Ingebouwde apps publiceren in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127740"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Ingebouwde apps publiceren in Windows Virtual Desktop

In dit artikel vindt u hoe u apps publiceert in uw Windows Virtual Desktop-omgeving.

## <a name="publish-built-in-apps"></a>Ingebouwde apps publiceren

Een ingebouwde app publiceren:

1. Maak verbinding met een van de virtuele machines in uw hostpool.
2. Download de **PackageFamilyName** van de app die u wilt publiceren door de instructies in dit artikel te [volgen.](/powershell/module/appx/get-appxpackage?view=win10-ps/)
3. Voer ten slotte de volgende `<PackageFamilyName>` cmdlet uit met vervangen door de **PackageFamilyName** die u in de vorige stap hebt gevonden:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop ondersteunt alleen publicerende apps `C:\Program Files\Windows Apps`met installatielocaties die beginnen met .

## <a name="update-app-icons"></a>App-pictogrammen bijwerken

Nadat u een app hebt gepubliceerd, heeft deze het standaard pictogram van de Windows-app in plaats van de normale pictogramafbeelding. Als u het pictogram wilt wijzigen in het normale pictogram, plaatst u de afbeelding van het gewenste pictogram op een netwerkshare. Ondersteunde afbeeldingsindelingen zijn PNG, BMP, GIF, JPG, JPEG en ICO.

## <a name="publish-microsoft-edge"></a>Microsoft Edge publiceren

Het proces dat u gebruikt om Microsoft Edge te publiceren, is iets anders dan het publicatieproces voor andere apps. Voer deze cmdlet uit als u Microsoft Edge wilt publiceren met de standaardstartpagina:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van feeds om te organiseren hoe apps worden weergegeven voor gebruikers bij [Feed aanpassen voor Windows Virtual Desktop-gebruikers](customize-feed-for-virtual-desktop-users.md).
- Meer informatie over de functie MSIX-app koppelen bij [Het instellen van de MSIX-app.Learn](app-attach.md)about the MSIX app attach feature at Set up MSIX app attach .

