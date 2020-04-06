---
title: Windows-apparaten automatisch inrichten met DPS - Azure IoT Edge | Microsoft Documenten
description: Gebruik een gesimuleerd apparaat op uw Windows-machine om automatische apparaatinrichting voor Azure IoT Edge te testen met de service voor apparaatinrichting
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fc051e2a0ebbdae7c62ff8a249747d118d3c2ce4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668681"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Een gesimuleerd IoT Edge-apparaat maken en inrichten met een virtuele TPM op Windows

Azure IoT Edge-apparaten kunnen automatisch worden ingericht met de [Service Voor het inrichten van apparaten,](../iot-dps/index.yml) net als apparaten die niet randfunctionaliteit hebben. Als u niet bekend bent met het proces van automatisch inrichten, bekijkt u de [concepten voor automatisch inrichten](../iot-dps/concepts-auto-provisioning.md) voordat u verdergaat.

DPS ondersteunt symmetrische sleutelattest voor IoT Edge-apparaten in zowel individuele inschrijving als groepsinschrijving. Als u voor groepsinschrijving de optie 'is IoT Edge-apparaat' inschakelt om waar te zijn in symmetrische sleutelattest, worden alle apparaten die onder die inschrijvingsgroep zijn geregistreerd, gemarkeerd als IoT Edge-apparaten.

In dit artikel ziet u hoe u automatische inrichting op een gesimuleerd IoT Edge-apparaat testen met de volgende stappen:

* Een exemplaar van de IoT Hub Device Provisioning Service (DPS) maken.
* Maak een gesimuleerd apparaat op uw Windows-machine met een gesimuleerde Trusted Platform Module (TPM) voor hardwarebeveiliging.
* Maak een individuele inschrijving voor het apparaat.
* Installeer de 3T Edge-runtime en sluit het apparaat aan op IoT Hub.

> [!TIP]
> In dit artikel wordt beschreven het testen van automatische inrichting met behulp van TPM-attest op virtuele apparaten, maar veel van het geldt ook bij het gebruik van fysieke TPM-hardware.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkelingsmachine. In dit artikel wordt Windows 10 gebruikt.
* Een actieve IoT Hub.

> [!NOTE]
> TPM 2.0 is vereist bij het gebruik van TPM-attest met DPS en kan alleen worden gebruikt om individuele, niet groepsinschrijvingen te maken.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De Service voor het inrichten van IoT-hub-apparaten instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U de instructies volgen in [Het instellen van de IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

Nadat u de service voor apparaatinrichting hebt uitgevoerd, kopieert u de waarde van **id-bereik** vanaf de overzichtspagina. U gebruikt deze waarde wanneer u de Runtime van IoT Edge configureert.

> [!TIP]
> Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeuringssleutel**bepalen, die uniek is voor elke TPM-chip en wordt verkregen van de TPM-chipfabrikant die eraan is gekoppeld. U een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeuringssleutel te maken.
>
> Volg de instructies in het artikel [Hoe u apparaatinschrijvingen met Azure Portal beheert](../iot-dps/how-to-manage-enrollments.md) om uw inschrijving in DPS te maken en vervolgens verder te gaan met de sectie Runtime van [IoT Edge](#install-the-iot-edge-runtime) installeren in dit artikel om door te gaan.

## <a name="simulate-a-tpm-device"></a>TPM-apparaat simuleren

Maak een gesimuleerd TPM-apparaat op uw Windows-ontwikkelingsmachine. Haal de **registratie-id** en **goedkeuringssleutel** voor uw apparaat op en gebruik deze om een afzonderlijke inschrijvingsvermelding in DPS te maken.

Wanneer u een inschrijving maakt in DPS, u een **eerste apparaattweelingstatus**declareren. In de apparaattweeling u tags instellen op het groeperen van apparaten op basis van elke statistiek die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om [automatische implementaties](how-to-deploy-monitor.md)te maken.

Kies de SDK-taal die u wilt gebruiken om het gesimuleerde apparaat te maken en volg de stappen totdat u de afzonderlijke inschrijving maakt.

Wanneer u de afzonderlijke inschrijving maakt, selecteert u **True** om te verklaren dat het gesimuleerde TPM-apparaat op uw Windows-ontwikkelingsmachine een **IoT Edge-apparaat**is.

> [!TIP]
> In azure cli u een [inschrijving](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) of een [inschrijvingsgroep](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) maken en de vlag met randfunctionaliteit gebruiken om op te geven dat een apparaat of groep apparaten een IoT **Edge-apparaat** is.

Gesimuleerdapparaat en individuele inschrijvingsgidsen:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Nadat u de afzonderlijke inschrijving hebt hebt aanmaken, slaat u de waarde van de **registratie-id op.** U gebruikt deze waarde wanneer u de Runtime van IoT Edge configureert.

## <a name="install-the-iot-edge-runtime"></a>De runtime van IoT Edge installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en u extra containers op het apparaat implementeren, zodat u code aan de rand uitvoeren.

U hebt de volgende informatie nodig bij het inrichten van uw apparaat:

* De waarde van het **DPS-id-bereik**
* De **apparaatregistratie-id** die u hebt gemaakt

Installeer de Runtime van IoT Edge op het apparaat waarop de gesimuleerde TPM wordt uitgevoerd. U configureert de 3T Edge-runtime voor automatische, niet handmatige inprovisioning.

> [!TIP]
> Houd het venster dat de TPM-simulator draait open tijdens uw installatie en testen.

Zie De runtime van [Azure IoT Edge installeren op Windows](how-to-install-iot-edge-windows.md)voor meer gedetailleerde informatie over het installeren van IoT Edge op Windows, inclusief vereisten en instructies voor taken zoals het beheren van containers en het bijwerken van IoT Edge.

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van PowerShell gebruikt bij het installeren van IoT Edge, niet PowerShell (x86).

1. De opdracht **Deploy-IoTEdge** controleert of uw Windows-machine een ondersteunde versie heeft, schakelt de functie containers in en downloadt vervolgens de moby-runtime en de IoT Edge-runtime. De opdracht is standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT Core-apparaten automatisch opnieuw worden opgestart. Andere Windows 10- of Windows Server-apparaten kunnen u vragen om opnieuw op te starten. Start het apparaat nu opnieuw op. Zodra uw apparaat klaar is, voert u PowerShell opnieuw uit als beheerder.

1. Met de opdracht **Initialize-IoTEdge** configureert u de runtime van IoT Edge op uw machine. De opdracht is standaard ingesteld op handmatiginrichten met Windows-containers. Gebruik `-Dps` de vlag om de apparaatinrichtingsservice te gebruiken in plaats van handmatig inrichten.

   Vervang de tijdelijke `{scope_id}` aanduidingswaarden voor en `{registration_id}` door de gegevens die u eerder hebt verzameld.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Als de runtime succesvol is gestart, u naar uw IoT-hub gaan en IoT Edge-modules op uw apparaat implementeren. Gebruik de volgende opdrachten op uw apparaat om te controleren of de runtime is geïnstalleerd en gestart.  

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bestudeer servicelogs van de laatste 5 minuten.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lijst met lopende modules.

```powershell
iotedge list
```

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvingsproces voor apparaatinrichtingsservice u de apparaat-id en de dubbele tags van het apparaat instellen op hetzelfde moment als het nieuwe apparaat. U deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te targeten met automatisch apparaatbeheer. Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal met behulp van de Azure-portal](how-to-deploy-monitor.md) of het gebruik van Azure [CLI](how-to-deploy-monitor-cli.md)
