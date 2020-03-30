---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176495"
---
1. [Maak verbinding met de PowerShell-interface](#connect-to-the-powershell-interface).
2. Gebruik `Get-HcsApplianceInfo` de om de informatie voor uw apparaat te krijgen.

    In het volgende voorbeeld ziet u het gebruik van deze cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Hier is een tabel een samenvatting van een aantal van de belangrijke apparaat informatie:
    
    | Parameter                             | Beschrijving                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | De vriendelijke naam van het apparaat zoals geconfigureerd via de lokale web-gebruikersinterface tijdens de implementatie van het apparaat. De standaardvriendelijke naam is het serienummer van het apparaat.  |   |
    | SerialNumber                   | Het serienummer van het apparaat is een uniek nummer dat in de fabriek is toegewezen.                                                                             |   |
    | Model                          | Het model voor uw Data Box Edge- of Data Box Gateway-apparaat. Het model is virtueel voor Data Box Gateway en fysiek voor Data Box Edge.                   |   |
    | FriendlySoftwareVersie        | De vriendelijke string die overeenkomt met de apparaatsoftwareversie. Voor een systeem met preview zou de vriendelijke softwareversie Data Box Edge 1902 zijn. |   |
    | HcsVersie                     | De HCS-softwareversie die op uw apparaat wordt uitgevoerd. Zo is de HCS-softwareversie die overeenkomt met Data Box Edge 1902 1.4.771.324.            |   |
    | LocalCapacityInMb              | De totale lokale capaciteit van het apparaat in Megabits.                                                                                                        |   |
    | IsGeregistreerd                   | Deze waarde geeft aan of uw apparaat is geactiveerd met de service.                                                                                         |   |


