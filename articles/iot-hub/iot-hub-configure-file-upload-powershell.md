---
title: De Azure PowerShell gebruiken om het uploaden van bestanden te configureren | Microsoft Documenten
description: De Azure PowerShell-cmdlets gebruiken om uw IoT-hub te configureren om bestandsuploads vanaf verbonden apparaten in te schakelen. Bevat informatie over het configureren van het Azure-opslagaccount voor bestemming.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60318438"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Uploaden van IoT-hubbestanden configureren met PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Als u de functionaliteit voor het uploaden van bestanden wilt gebruiken [in IoT Hub,](iot-hub-devguide-file-upload.md)moet u eerst een Azure-opslagaccount koppelen aan uw IoT-hub. U een bestaand opslagaccount gebruiken of een nieuw account maken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Een Azure IoT-hub. Als u geen IoT-hub hebt, u de [cmdlet Nieuw-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) gebruiken om er een te maken of de portal gebruiken om [een IoT-hub](iot-hub-create-through-portal.md)te maken.

* Een Azure Storage-account. Als u geen Azure-opslagaccount hebt, u de [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.storage/) van Azure Storage gebruiken om er een te maken of de portal te gebruiken om [een opslagaccount te maken](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Aanmelden en uw Azure-account instellen

Meld u aan bij uw Azure-account en selecteer uw abonnement.

1. Voer bij de PowerShell-prompt de cmdlet **Connect-AzAccount** uit:

    ```powershell
    Connect-AzAccount
    ```

2. Als u meerdere Azure-abonnementen hebt, geeft aanmelden bij Azure u toegang tot alle Azure-abonnementen die zijn gekoppeld aan uw referenties. Gebruik de volgende opdracht om de Azure-abonnementen weer te geven die beschikbaar zijn voor gebruik:

    ```powershell
    Get-AzSubscription
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken om de opdrachten uit te voeren om uw IoT-hub te beheren. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Uw opslagaccountgegevens ophalen

In de volgende stappen wordt ervan uitgegaan dat u uw opslagaccount hebt gemaakt met behulp van het **implementatiemodel Resource Manager** en niet het **klassieke** implementatiemodel.

Als u bestandsuploads vanaf uw apparaten wilt configureren, hebt u de verbindingstekenreeks voor een Azure-opslagaccount nodig. Het opslagaccount moet in hetzelfde abonnement zitten als uw IoT-hub. U hebt ook de naam van een blobcontainer in het opslagaccount nodig. Gebruik de volgende opdracht om uw opslagaccountsleutels op te halen:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Noteer de **sleutelwaarde** van de opslagaccount waarde. Je hebt het nodig in de volgende stappen.

U een bestaande blobcontainer gebruiken voor het uploaden van bestanden of een nieuwe uploadn:

* Als u de bestaande blobcontainers in uw opslagaccount wilt weergeven, gebruikt u de volgende opdrachten:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Als u een blobcontainer in uw opslagaccount wilt maken, gebruikt u de volgende opdrachten:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Uw IoT-hub configureren

U nu uw IoT-hub configureren om [bestanden naar de IoT-hub te uploaden](iot-hub-devguide-file-upload.md) met uw opslagaccountgegevens.

De configuratie vereist de volgende waarden:

* **Opslagcontainer:** een blobcontainer in een Azure-opslagaccount in uw huidige Azure-abonnement om te koppelen aan uw IoT-hub. U hebt de benodigde opslagaccountgegevens in de vorige sectie opgehaald. IoT Hub genereert automatisch SAS-URI's met schrijfmachtigingen voor deze blobcontainer voor apparaten die kunnen worden gebruikt wanneer ze bestanden uploaden.

* **Meldingen ontvangen voor geüploade bestanden:** Meldingen voor het uploaden van bestanden inschakelen of uitschakelen.

* **SAS TTL**: Deze instelling is de time-to-live van de SAS URI's die door IoT Hub naar het apparaat worden teruggestuurd. Standaard ingesteld op een uur.

* **Standaardttl-instellingen voor bestandsmeldingen:** de time-to-live van een melding voor het uploaden van bestanden voordat deze is verlopen. Standaard ingesteld op één dag.

* **Maximale leveringshoeveelheid bestandsmeldingen:** het aantal keren dat de IoT Hub een melding voor het uploaden van bestanden probeert te leveren. Standaard ingesteld op 10.

Gebruik de volgende PowerShell-cmdlet om de instellingen voor het uploaden van bestanden op uw IoT-hub te configureren:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Volgende stappen

Zie [Bestanden uploaden vanaf een apparaat voor](iot-hub-devguide-file-upload.md)meer informatie over de mogelijkheden voor het uploaden van bestanden van IoT Hub.

Volg deze koppelingen voor meer informatie over het beheer van Azure IoT Hub:

* [IoT-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-statistieken](iot-hub-metrics.md)
* [Controle van bewerkingen](iot-hub-operations-monitoring.md)

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Beveilig uw IoT-oplossing van de grond af aan](../iot-fundamentals/iot-security-ground-up.md)
