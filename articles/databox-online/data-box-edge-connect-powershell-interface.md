---
title: Verbinding maken met en beheren van Microsoft Azure Data Box Edge-apparaat via de Windows PowerShell-interface | Microsoft Documenten
description: Beschrijft hoe u verbinding maken met en vervolgens Data Box Edge beheren via de Windows PowerShell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265478"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Een Azure Data Box Edge-apparaat beheren via Windows PowerShell

Met de Azure Data Box Edge-oplossing u gegevens verwerken en via het netwerk naar Azure verzenden. In dit artikel worden enkele configuratie- en beheertaken voor uw Data Box Edge-apparaat beschreven. U de Azure-portal, de lokale web-gebruikersinterface of de Windows PowerShell-interface gebruiken om uw apparaat te beheren.

Dit artikel richt zich op de taken die u uitvoert met de PowerShell-interface.

Dit artikel bevat de volgende procedures:

- Verbinding maken met de PowerShell-interface
- Een ondersteuningspakket maken
- Certificaat uploaden
- Het apparaat opnieuw instellen
- Apparaatgegevens weergeven
- Compute-logboeken opmaken
- Compute-modules bewaken en oplossen

## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de PowerShell-interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Een ondersteuningspakket maken

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Certificaat uploaden

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

U ook IoT Edge-certificaten uploaden om een veilige verbinding tussen uw IoT Edge-apparaat en de downstream-apparaten die er verbinding mee kunnen maken, mogelijk in te schakelen. Er zijn drie IoT Edge-certificaten (*.pem-indeling)* die u moet installeren:

- Root CA-certificaat of de eigenaar CA
- Apparaat CA-certificaat
- Apparaatsleutelcertificaat

In het volgende voorbeeld wordt het gebruik van deze cmdlet weergegeven om IoT Edge-certificaten te installeren:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Wanneer u deze cmdlet uitvoert, wordt u gevraagd het wachtwoord voor het netwerkaandeel op te geven.

Ga voor meer informatie over certificaten naar [Azure IoT Edge-certificaten](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) of [Installeer certificaten op een gateway.](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)

## <a name="view-device-information"></a>Apparaatgegevens weergeven
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Het apparaat opnieuw instellen

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Compute-logboeken opmaken

Als de rekenrol is geconfigureerd op uw apparaat, u de compute logs ook via de PowerShell-interface krijgen.

1. [Maak verbinding met de PowerShell-interface](#connect-to-the-powershell-interface).
2. Gebruik `Get-AzureDataBoxEdgeComputeRoleLogs` de om de compute logs voor uw apparaat te krijgen.

    In het volgende voorbeeld ziet u het gebruik van deze cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Hier is een beschrijving van de parameters die worden gebruikt voor de cmdlet:
    - `Path`: Geef een netwerkpad op naar het aandeel waar u het compute log-pakket wilt maken.
    - `Credential`: Geef de gebruikersnaam op voor het netwerkaandeel. Wanneer u deze cmdlet uitvoert, moet u het share-wachtwoord opgeven.
    - `FullLogCollection`: Deze parameter zorgt ervoor dat het logboekpakket alle compute logs bevat. Standaard bevat het logboekpakket slechts een subset van logboeken.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Compute-modules bewaken en oplossen

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>De externe sessie afsluiten

Als u de externe PowerShell-sessie wilt afsluiten, sluit u het PowerShell-venster.

## <a name="next-steps"></a>Volgende stappen

- [Azure Data Box Edge](data-box-edge-deploy-prep.md) in de Azure-portal implementeren.
