---
title: Automatisch Windows-apparaten inrichten met DPS-Azure IoT Edge | Microsoft Docs
description: Een gesimuleerd apparaat op uw Windows-computer gebruiken voor het testen van automatische apparaatinrichting voor Azure IoT Edge met Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457139"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Een gesimuleerd IoT Edge-apparaat maken en inrichten met een virtuele TPM in Windows

Azure IoT Edge apparaten kunnen automatisch worden ingericht met behulp van de [Device Provisioning Service](../iot-dps/index.yml) , net zoals apparaten die niet Edge-ingeschakeld zijn. Als u niet bekend bent met het proces van automatische inrichting, raadpleegt u de [concepten voor automatische inrichting](../iot-dps/concepts-auto-provisioning.md) voordat u doorgaat.

DPS ondersteunt symmetrische sleutel attest voor IoT Edge-apparaten in zowel individuele inschrijving als groeps inschrijving. Als u bij het inschrijven van de groep de optie ' is IoT Edge apparaat ' moet worden ingesteld op True voor symmetrische-sleutel attest, worden alle apparaten die zijn geregistreerd onder die registratie groep als IoT Edge-apparaten gemarkeerd. 

In dit artikel leest u hoe u automatisch inrichten op een gesimuleerd IoT Edge apparaat kunt testen met de volgende stappen:

* Maak een exemplaar van de IoT Hub Device Provisioning Service (DPS).
* Een gesimuleerd apparaat maakt op uw Windows-machine met een gesimuleerde Trusted Platform Module (TPM) voor hardwarebeveiliging.
* Maak een afzonderlijke inschrijving voor het apparaat.
* Installeer de IoT Edge-runtime en verbind het apparaat met IoT Hub.

> [!NOTE]
> TPM 2,0 is vereist voor het gebruik van TPM-Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet-groeps registraties.

> [!TIP]
> In dit artikel wordt beschreven hoe u het automatisch inrichten kunt testen met behulp van TPM-Attestation op virtuele apparaten, maar dit is ook van toepassing bij gebruik van fysieke TPM-hardware.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkelcomputer. In dit artikel wordt gebruikgemaakt van Windows 10.
* Een actieve IoT-Hub.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en een koppeling naar uw IoT hub. U kunt de instructies voor [het instellen van de IOT hub DPS](../iot-dps/quick-setup-auto-provision.md)volgen.

Nadat u de Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **id-bereik** van de pagina overzicht. U gebruikt deze waarde bij het configureren van de IoT Edge-runtime.

> [!TIP]
> Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeurings sleutel**bepalen, die uniek is voor elke TPM-chip en die is verkregen van de fabrikant van de TPM-chip die eraan is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeurings sleutel te maken.
>
> Volg de instructies in het artikel de registratie van [apparaten beheren met Azure Portal](../iot-dps/how-to-manage-enrollments.md) om uw inschrijving in DPS te maken en ga vervolgens verder met de sectie [de IOT Edge runtime installeren](#install-the-iot-edge-runtime) in dit artikel om door te gaan.

## <a name="simulate-a-tpm-device"></a>TPM-apparaat simuleren

Maak een gesimuleerd TPM-apparaat op uw Windows-ontwikkelcomputer. Haal de **registratie-id** en **goedkeurings sleutel** voor uw apparaat op en gebruik deze om een afzonderlijke inschrijvings vermelding in DPS te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand**van het apparaat te declareren. U kunt in de apparaatdubbel labels instellen om apparaten te groeperen door elke meetwaarde die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie, of het apparaat. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-monitor.md).

De SDK-taal die u gebruiken wilt voor het maken van het gesimuleerde apparaat kiezen en volg de stappen totdat u de afzonderlijke registratie maken.

Wanneer u de afzonderlijke inschrijving maakt, selecteert u **waar** om te declareren dat het gesimuleerde TPM-apparaat op uw Windows-ontwikkel computer een **IOT edge apparaat**is.

Gesimuleerd apparaat en handleidingen voor afzonderlijke inschrijving:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Nadat u de afzonderlijke inschrijving hebt gemaakt, slaat u de waarde van de **registratie-id**op. U gebruikt deze waarde bij het configureren van de IoT Edge-runtime.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen in containers worden uitgevoerd, en kunnen u extra containers implementeren op het apparaat, zodat u kunt de code aan de rand uitvoeren.

U hebt de volgende informatie nodig bij het inrichten van uw apparaat:

* De waarde voor het bereik van de DPS **-id**
* De **registratie-id** van het apparaat dat u hebt gemaakt

Installeer de IoT Edge runtime op het apparaat waarop de gesimuleerde TPM wordt uitgevoerd. U configureert de IoT Edge runtime voor automatisch, niet hand matig, provisioning.

> [!TIP]
> Houd het venster met de TPM-simulator openen tijdens het installeren en testen.

Zie [install the Azure IOT Edge runtime on Windows](how-to-install-iot-edge-windows.md)(Engelstalig) voor meer informatie over het installeren van IOT Edge op Windows, inclusief vereisten en instructies voor taken zoals het beheren van containers en het bijwerken van IOT Edge.

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van Power shell gebruikt bij het installeren van IoT Edge, niet in Power shell (x86).

1. Met de opdracht **Deploy-IoTEdge** wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft, wordt de functie containers ingeschakeld, waarna de Moby-runtime en de IOT Edge-runtime worden gedownload. De opdracht wordt standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT-kern apparaten automatisch opnieuw worden opgestart. Op andere Windows 10-of Windows Server-apparaten wordt u mogelijk gevraagd om opnieuw op te starten. Als dit het geval is, start u het apparaat nu opnieuw op. Zodra het apparaat klaar is, voert u Power shell als beheerder opnieuw uit.

1. De **initialisatie-IoTEdge-** opdracht configureert de IOT Edge runtime op de computer. De opdracht wordt standaard ingesteld op hand matig inrichten met Windows-containers. Gebruik de vlag `-Dps` om de Device Provisioning Service te gebruiken in plaats van hand matig inrichten.

   Vervang de waarden van de tijdelijke aanduidingen voor `{scope_id}` en `{registration_id}` door de gegevens die u eerder hebt verzameld.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Controleer of geslaagde installatie

Als de runtime is gestart, kunt u met ingang van uw IoT-Hub en begin met het implementeren van IoT Edge-modules naar uw apparaat. Gebruik de volgende opdrachten op uw apparaat om te controleren dat de runtime geïnstalleerd en gestart.  

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bekijk Logboeken van de laatste 5 minuten.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lijst met modules.

```powershell
iotedge list
```

## <a name="next-steps"></a>Volgende stappen

Het inschrijvingsproces Device Provisioning Service kunt u instellen de apparaat-ID en het apparaat dubbele tags op hetzelfde moment als u het nieuwe apparaat inrichten. Deze waarden kunt u afzonderlijke apparaten of groepen van apparaten met behulp van automatische Apparaatbeheer. Meer informatie over [het implementeren en bewaken van IOT Edge modules op schaal met behulp van de Azure Portal](how-to-deploy-monitor.md) of [met Azure cli](how-to-deploy-monitor-cli.md)
