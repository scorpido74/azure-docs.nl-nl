---
title: TPM-apparaat inschrijven bij Azure Device Provisioning Service met behulp van python
description: 'Quick Start: TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service (DPS) met behulp van de python inrichtings service-SDK. In deze quickstart wordt gebruikgemaakt van afzonderlijke registraties.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 60ce27ddc533b6c4066cea771f7a24570ff3c04c
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604889"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Quick Start: TPM-apparaat inschrijven voor IoT Hub Device Provisioning Service met behulp van de python inrichtings service-SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In deze Quick Start maakt u programmatisch een afzonderlijke inschrijving voor een TPM-apparaat in de Azure IoT Hub Device Provisioning Service, met behulp van de python inrichtings service-SDK met behulp van een python-voorbeeld toepassing.

## <a name="prerequisites"></a>Vereisten

- Volt ooien van [het instellen van de IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md).
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 2. x of 3. x](https://www.python.org/downloads/). In deze Quick Start wordt de [python inrichtings service-SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) hieronder geïnstalleerd.
- [PIP](https://pip.pypa.io/en/stable/installing/), indien niet opgenomen in uw distributie van python.
- Goedkeuringssleutel. Gebruik de stappen in [een gesimuleerd apparaat maken en inrichten](quick-create-simulated-device.md) of de goedkeurings sleutel gebruiken die is opgegeven bij de SDK, zoals hieronder wordt beschreven.

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de afgeschafte v1 python SDK. Apparaat-en service-clients voor de IOT hub Device Provisioning Service zijn nog niet beschikbaar in v2. Het team is momenteel hard werk om v2 te voorzien van functie pariteit.

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. [Download en installeer Python 2.x of 3.x](https://www.python.org/downloads/). Zorg ervoor dat u de 32-bits of 64-bits installatie gebruikt, zoals vereist door uw configuratie. Zorg ervoor dat u Python toevoegt aan uw platformspecifieke omgevingsvariabelen als u hierom wordt gevraagd tijdens de installatie. 

1. Kies een van de volgende opties voor de [python inrichtings service-SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client):

    - Bouw en compileer de **Azure IoT Python SDK**. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) voor het bouwen van de Python-pakketten. Als u een Windows-besturingssysteem hebt, gebruikt dan ook het [herdistribueerbare pakket van Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) om het gebruik van systeemeigen DLL's van Python mogelijk te maken.

    - [Installeer of upgrade *pip*, het Python pakketbeheersysteem](https://pip.pypa.io/en/stable/installing/) en installeer het pakket met behulp van de volgende opdracht:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. U hebt de goedkeuringssleutel nodig voor uw apparaat. Als u de stappen in de quickstart [Create and provision a simulated device](quick-create-simulated-device.md) (Een gesimuleerd apparaat maken en inrichten) voor het maken van een gesimuleerd TPM-apparaat hebt gevolgd, gebruikt u de sleutel voor dit apparaat. U kunt anders de volgende goedkeuringssleutel gebruiken die is opgegeven met de SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>De Python-voorbeeldcode wijzigen

In deze sectie ziet u hoe u de inrichtingsgegevens van het TPM-apparaat toevoegt aan de voorbeeldcode. 

1. Start een teksteditor en maak een nieuw bestand **TpmEnrollment.py**.

1. Voeg de volgende `import` -instructies en -variabelen aan het begin van het bestand **TpmEnrollment.py** toe. Vervang vervolgens `dpsConnectionString` door uw verbindingsreeks die te vinden is onder **Gedeeld toegangsbeleid** in **Device Provisioning Service** in **Azure Portal**. Vervang `endorsementKey` door de waarde die u eerder hebt genoteerd toen u [de omgeving voorbereidde](quick-enroll-device-tpm-python.md#prepareenvironment). Maak ten slotte een unieke `registrationid` en zorg dat deze alleen bestaat uit kleine letters en afbreekstreepjes.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Voeg de volgende functie en functieaanroep toe om het aanmaken van de groepsinschrijving te implementeren:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Sla het bestand **TpmEnrollment.py** op en sluit het.
 

## <a name="run-the-sample-tpm-enrollment"></a>De voorbeeld-TPM-inschrijving uitvoeren

1. Open een opdrachtprompt en voer het script uit.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Houd de uitvoer in de gaten voor een geslaagde inschrijving.

1. Navigeer naar de inrichtingsservice in Azure Portal. Selecteer **Inschrijvingen beheren**. U ziet dat het TPM-apparaat wordt weergegeven op het tabblad **Afzonderlijke registraties** met de naam `registrationid` die u eerder hebt gemaakt. 

    ![De TPM-inschrijving controleren in de portal](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent het Java service-voor beeld te verkennen, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het Python-voorbeeld op de computer.
1. Als u een gesimuleerd TPM-apparaat hebt gemaakt, sluit u het venster van de TPM-simulator.
1. Navigeer naar uw Device Provisioning Service in de Azure Portal, selecteer **inschrijvingen beheren**en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* voor de inschrijvings vermelding die u hebt gemaakt met behulp van deze Quick Start. Klik vervolgens op de knop **verwijderen** boven aan het deel venster.


## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u programmatisch een afzonderlijke inschrijvings vermelding gemaakt voor een TPM-apparaat en kunt u desgewenst een door de TPM gesimuleerd apparaat op uw computer maken en dit inrichten voor uw IoT-hub met behulp van de Azure-IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
