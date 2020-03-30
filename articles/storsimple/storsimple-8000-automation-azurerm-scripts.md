---
title: AzureRM PowerShell-scripts gebruiken om StorSimple-apparaten te beheren
description: Meer informatie over het gebruik van Azure Resource Manager-scripts om StorSimple-taken te automatiseren
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 03a5ef49b2d58d351d882b30b5d11e4a5ba90264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471955"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Azure Resource Manager SDK-scripts gebruiken om StorSimple-apparaten te beheren

In dit artikel wordt beschreven hoe Azure Resource Manager SDK-scripts kunnen worden gebruikt om uw StorSimple 8000-serieapparaat te beheren. Een voorbeeldscript is ook opgenomen om u door de stappen van het configureren van uw omgeving te leiden om deze scripts uit te voeren.

Dit artikel is alleen van toepassing op apparaten uit de StorSimple 8000-serie die alleen in Azure-portal worden uitgevoerd.

## <a name="sample-scripts"></a>Voorbeeldscripts

De volgende voorbeeldscripts zijn beschikbaar om verschillende StorSimple-taken te automatiseren.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabel met voorbeeldscripts op basis van Azure Resource Manager SDK

| Azure Resource Manager Script                    | Beschrijving                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Met dit script u uw StorSimple-apparaat autoriseren om de versleutelingssleutel voor servicegegevens te wijzigen.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Dit script maakt een 8010 of een 8020 StorSimple Cloud Appliance. Het cloudtoestel kan vervolgens worden geconfigureerd en geregistreerd met uw StorSimple Data Manager-service.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Dit script maakt of wijzigt StorSimple-volumes.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Dit script bevat alle back-ups voor een apparaat dat is geregistreerd bij uw StorSimple Device Manager-service.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Dit script alle back-up beleid voor uw StorSimple apparaat.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Dit script krijgt alle StorSimple-taken die worden uitgevoerd op uw StorSimple Device Manager-service.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Dit script scant de updateserver en laat u weten of er updates beschikbaar zijn om te installeren op uw StorSimple-apparaat.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Dit script installeert de beschikbare updates op uw StorSimple-apparaat.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Met dit script wordt een handmatige cloudmomentopname gestart en worden cloudmomentopnamen verwijderd die ouder zijn dan opgegeven bewaardagen.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Dit Azure Automation Runbook PowerShell-script rapporteert de status van alle back-uptaken.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Met dit script wordt één back-upobject verwijderd.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Dit script start een handmatige back-up op uw StorSimple-apparaat.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Dit script werkt de versleutelingssleutel voor servicegegevens bij voor alle 8010/8020 StorSimple Cloud Appliances die zijn geregistreerd bij uw StorSimple Device Manager-service.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Dit script markeert de ontbrekende back-ups na het analyseren van alle schema's die zijn gekoppeld aan back-upbeleid. Het controleert ook de back-up catalogus met de lijst met beschikbare back-ups.             |




## <a name="configure-and-run-a-sample-script"></a>Een voorbeeldscript configureren en uitvoeren

In deze sectie wordt een voorbeeldscript uitgevoerd en worden de verschillende stappen beschreven die nodig zijn om het script uit te voeren.

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u:

*   Azure PowerShell geïnstalleerd. Ga als een te werk om Azure PowerShell-modules te installeren:
    * Volg in een Windows-omgeving de stappen in [Azure PowerShell installeren en configureren.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) U Azure PowerShell installeren op uw Windows Server-host voor uw StorSimple als u er een gebruikt.
    * Volg in een Linux- of MacOS-omgeving de stappen in [Azure PowerShell installeren en configureren op MacOS of Linux.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux)

Ga voor meer informatie over het gebruik van Azure PowerShell aan [de slag met Azure PowerShell.](https://docs.microsoft.com/powershell/azure/get-started-azureps)

### <a name="run-azure-powershell-script"></a>Azure PowerShell-script uitvoeren

Het script dat in dit voorbeeld wordt gebruikt, bevat alle taken op een StorSimple-apparaat. Dit geldt ook voor de taken die zijn geslaagd, mislukt of aan de gang zijn. Voer de volgende stappen uit om het script te downloaden en uit te voeren.

1. Start Azure PowerShell. Maak een nieuwe map en wijzig de map in de nieuwe map.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Download NuGet CLI](https://www.nuget.org/downloads) onder de map die in de vorige stap is gemaakt. Er zijn verschillende versies van _nuget.exe_. Kies de versie die overeenkomt met uw SDK. Elke downloadlink verwijst rechtstreeks naar een _.exe-bestand._ Zorg ervoor dat u met de rechtermuisknop klikt en het bestand op uw computer opslaat in plaats van het vanuit de browser uit te voeren.

    U ook de volgende opdracht uitvoeren om het script te downloaden en op te slaan in dezelfde map die u eerder hebt gemaakt.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Download de afhankelijke SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Download het script van het voorbeeld Van GitHub-project.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Voer het script uit. Geef uw Azure-referenties op wanneer u wordt gevraagd om te verifiëren. Dit script moet een gefilterde lijst van alle taken op uw StorSimple-apparaat uitvoeren.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Voorbeelduitvoer

De volgende uitvoer wordt weergegeven wanneer het voorbeeldscript wordt uitgevoerd. De uitvoer bevat alle taken die zijn uitgevoerd op een geregistreerd apparaat dat is gestart op 25 september 2017 en die op 2 oktober 2017 is voltooid.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Volgende stappen

[Gebruik de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)
