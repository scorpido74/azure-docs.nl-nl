---
title: App-groepen beheren voor virtueel Windows-bureau blad-Azure
description: Hierin wordt beschreven hoe u Windows Virtual Desktop-tenants instelt in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e158c0a6090493bec0169c144f030300de921516
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679459"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Zelfstudie: App-groepen voor virtuele Windows-Bureau bladen beheren

De standaard app-groep die is gemaakt voor een nieuwe Windows Virtual Desktop-hostgroep publiceert ook het volledige bureau blad. Daarnaast kunt u een of meer RemoteApp-toepassings groepen maken voor de hostgroep. Volg deze zelf studie voor het maken van een RemoteApp-app-groep en het publiceren van afzonderlijke apps in het **Start** menu.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een RemoteApp-groep maken.
> * Toegang verlenen aan RemoteApp-program ma's.

Voordat u begint, moet u [de Power shell-module voor virtueel bureau blad van Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) voor gebruik in uw Power shell-sessie downloaden en importeren als u dat nog niet hebt gedaan. Daarna voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Een RemoteApp-groep maken

1. Voer de volgende Power shell-cmdlet uit om een nieuwe lege RemoteApp-toepassings groep te maken.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Beschrijving Als u wilt controleren of de app-groep is gemaakt, kunt u de volgende cmdlet uitvoeren om een lijst met alle app-groepen voor de hostgroep weer te geven.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Voer de volgende cmdlet uit om een lijst met apps in het menu **Start** op te halen voor de installatie kopie van de virtuele machine van de hostgroep. Noteer de waarden voor **filepath**, **IconPath**, **Icon index**en andere belang rijke informatie voor de toepassing die u wilt publiceren.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Voer de volgende cmdlet uit om de toepassing te installeren op basis van `AppAlias`. `AppAlias` wordt weer gegeven wanneer u de uitvoer uit stap 3 uitvoert.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Beschrijving Voer de volgende cmdlet uit om een nieuw RemoteApp-programma te publiceren naar de toepassings groep die u in stap 1 hebt gemaakt.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Voer de volgende cmdlet uit om te controleren of de app is gepubliceerd.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Herhaal de stappen 1 tot en met 5 voor elke toepassing die u voor deze app-groep wilt publiceren.
8. Voer de volgende cmdlet uit om gebruikers toegang te verlenen tot de RemoteApp-program ma's in de app-groep.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een app-groep kunt maken, deze kunt vullen met RemoteApp-program ma's en gebruikers kunt toewijzen aan de app-groep. Zie de volgende zelf studie voor meer informatie over het maken van een groep met validatie-hosts. U kunt een groep validatie-host gebruiken om service-updates te controleren voordat u deze naar uw productie omgeving doorvoert.

> [!div class="nextstepaction"]
> [Een hostgroep maken om service-updates te valideren](./create-validation-host-pool.md)
