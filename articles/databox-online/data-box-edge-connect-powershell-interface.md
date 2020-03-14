---
title: Verbinding maken met Microsoft Azure Data Box Edge apparaat en beheren via de Windows Power shell-interface | Microsoft Docs
description: Hierin wordt beschreven hoe u verbinding maakt met Data Box Edge en vervolgens beheert via de Windows Power shell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265478"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Een Azure Data Box Edge-apparaat beheren via Windows Power shell

Met Azure Data Box Edge oplossing kunt u gegevens verwerken en naar Azure verzenden via het netwerk. In dit artikel worden enkele van de configuratie-en beheer taken voor uw Data Box Edge-apparaat beschreven. U kunt de Azure Portal, de lokale webgebruikersinterface of de Windows Power shell-interface gebruiken om uw apparaat te beheren.

Dit artikel is gericht op de taken die u gebruikt om de Power shell-interface te gebruiken.

Dit artikel bevat de volgende procedures:

- Verbinding maken met de Power shell-interface
- Een ondersteunings pakket maken
- Certificaat uploaden
- Het apparaat opnieuw instellen
- Apparaatgegevens weer geven
- Reken logboeken ophalen
- Reken modules controleren en problemen oplossen

## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de Power shell-interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Een ondersteunings pakket maken

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Certificaat uploaden

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

U kunt ook IoT Edge certificaten uploaden om een beveiligde verbinding in te scha kelen tussen uw IoT Edge apparaat en de downstream-apparaten waarmee verbinding kan worden gemaakt. Er zijn drie IoT Edge certificaten (*PEM* -indeling) die u moet installeren:

- Basis-CA-certificaat of de eigenaar-CA
- Device CA-certificaat
- Certificaat van de apparaats sleutel

In het volgende voor beeld ziet u het gebruik van deze cmdlet om IoT Edge certificaten te installeren:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Wanneer u deze cmdlet uitvoert, wordt u gevraagd het wacht woord voor de netwerk share op te geven.

Ga voor meer informatie over certificaten naar [Azure IOT Edge certificaten](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) of [Installeer certificaten op een gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Apparaatgegevens weer geven
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Uw apparaat opnieuw instellen

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Reken logboeken ophalen

Als de compute-functie op uw apparaat is geconfigureerd, kunt u de reken logboeken ook ophalen via de Power shell-interface.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Gebruik de `Get-AzureDataBoxEdgeComputeRoleLogs` om de reken logboeken voor uw apparaat op te halen.

    In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Hier volgt een beschrijving van de para meters die worden gebruikt voor de cmdlet:
    - `Path`: Geef een netwerkpad op naar de share waar u het Compute-pakket wilt maken.
    - `Credential`: Geef de gebruikers naam op voor de netwerk share. Wanneer u deze cmdlet uitvoert, moet u het wacht woord voor delen opgeven.
    - `FullLogCollection`: deze para meter zorgt ervoor dat in het logboek pakket alle reken logboeken worden opgenomen. Het logboek pakket bevat standaard slechts een subset van de logboeken.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Reken modules controleren en problemen oplossen

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>De externe sessie afsluiten

Als u de externe Power shell-sessie wilt afsluiten, sluit u het Power shell-venster.

## <a name="next-steps"></a>Volgende stappen

- [Azure Data Box Edge](data-box-edge-deploy-prep.md) in de Azure-portal implementeren.
