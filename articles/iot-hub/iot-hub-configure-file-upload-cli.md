---
title: Bestandsupload naar IoT Hub configureren met Azure CLI | Microsoft Documenten
description: Het configureren van bestandsuploads naar Azure IoT Hub met behulp van het Azure CLI met meerdere platforms.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302522"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Uploaden van IoT Hub-bestanden configureren met Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Als [u bestanden wilt uploaden vanaf een apparaat,](iot-hub-devguide-file-upload.md)moet u eerst een Azure Storage-account koppelen aan uw IoT-hub. U een bestaand opslagaccount gebruiken of een nieuw account maken.

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Een Azure IoT-hub. Als u geen IoT-hub hebt, kunt [ `az iot hub create` ](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) u de opdracht gebruiken om er een te maken of [een IoT-hub maken met behulp van de portal.](iot-hub-create-through-portal.md)

* Een Azure Storage-account. Als u geen Azure Storage-account hebt, u de Azure CLI gebruiken om er een te maken. Zie [Een opslagaccount maken](../storage/common/storage-create-storage-account.md) voor meer informatie.

## <a name="sign-in-and-set-your-azure-account"></a>Aanmelden en uw Azure-account instellen

Meld u aan bij uw Azure-account en selecteer uw abonnement.

1. Voer bij de opdrachtprompt deze [aanmeldingsopdracht](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) uit:

    ```azurecli
    az login
    ```

    Volg de instructies om te verifiëren met de code en meld u aan bij uw Azure-account via een webbrowser.

2. Als u meerdere Azure-abonnementen hebt en u zich aanmeldt bij Azure, hebt u toegang tot alle Azure accounts die zijn gekoppeld aan uw referenties. Gebruik de volgende [opdracht om de Azure-accounts weer te geven](https://docs.microsoft.com/cli/azure/account) die u kunt gebruiken:

    ```azurecli
    az account list
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken om de opdrachten uit te voeren om uw IoT-hub te maken. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Uw opslagaccountgegevens ophalen

In de volgende stappen wordt ervan uitgegaan dat u uw opslagaccount hebt gemaakt met behulp van het **implementatiemodel Resource Manager** en niet het **klassieke** implementatiemodel.

Als u bestandsuploads vanaf uw apparaten wilt configureren, hebt u de verbindingstekenreeks voor een Azure-opslagaccount nodig. Het opslagaccount moet in hetzelfde abonnement zitten als uw IoT-hub. U hebt ook de naam van een blobcontainer in het opslagaccount nodig. Gebruik de volgende opdracht om uw opslagaccountsleutels op te halen:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Noteer de waarde van de **verbindingstekenreeks.** Je hebt het nodig in de volgende stappen.

U een bestaande blobcontainer gebruiken voor het uploaden van bestanden of een nieuwe uploaden:

* Als u de bestaande blobcontainers in uw opslagaccount wilt weergeven, gebruikt u de volgende opdracht:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Als u een blobcontainer in uw opslagaccount wilt maken, gebruikt u de volgende opdracht:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Bestand uploaden

U nu uw IoT-hub configureren om [bestanden naar de IoT-hub te uploaden](iot-hub-devguide-file-upload.md) met uw opslagaccountgegevens.

De configuratie vereist de volgende waarden:

* **Opslagcontainer:** een blobcontainer in een Azure-opslagaccount in uw huidige Azure-abonnement om te koppelen aan uw IoT-hub. U hebt de benodigde opslagaccountgegevens in de vorige sectie opgehaald. IoT Hub genereert automatisch SAS-URI's met schrijfmachtigingen voor deze blobcontainer voor apparaten die kunnen worden gebruikt wanneer ze bestanden uploaden.

* **Meldingen ontvangen voor geüploade bestanden:** Meldingen voor het uploaden van bestanden inschakelen of uitschakelen.

* **SAS TTL**: Deze instelling is de time-to-live van de SAS URI's die door IoT Hub naar het apparaat worden teruggestuurd. Standaard ingesteld op een uur.

* **Standaardttl-instellingen voor bestandsmeldingen:** de time-to-live van een melding voor het uploaden van bestanden voordat deze is verlopen. Standaard ingesteld op één dag.

* **Maximale leveringshoeveelheid bestandsmeldingen:** het aantal keren dat de IoT Hub een melding voor het uploaden van bestanden probeert te leveren. Standaard ingesteld op 10.

Gebruik de volgende Azure CLI-opdrachten om de instellingen voor het uploaden van bestanden op uw IoT-hub te configureren:

<!--Robinsh this is out of date, add cloud powershell -->

Gebruik in een bash shell:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

U de configuratie van het uploaden van bestanden op uw IoT-hub controleren met de volgende opdracht:

```azurecli
az iot hub show --name {your iot hub name}
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
