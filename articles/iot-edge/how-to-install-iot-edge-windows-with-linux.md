---
title: Azure IoT Edge voor Linux installeren op Windows | Microsoft Docs
description: Azure IoT Edge installatie-instructies voor Linux-containers op Windows 10, Windows Server en Windows IoT core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80133166"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Gebruik IoT Edge in Windows om Linux-containers uit te voeren

Test IoT Edge-modules voor Linux-apparaten met behulp van een Windows-machine.

In een productie scenario moeten Windows-apparaten alleen Windows-containers uitvoeren. Een veelvoorkomend ontwikkel scenario is het gebruik van een Windows-computer om IoT Edge-modules voor Linux-apparaten te bouwen. Met de IoT Edge runtime voor Windows kunt u Linux-containers uitvoeren voor **test-en ontwikkelings** doeleinden.

Dit artikel bevat de stappen voor het installeren van de Azure IoT Edge runtime met Linux-containers op uw Windows x64-systeem (AMD/Intel). Zie [de Azure IOT Edge runtime installeren op Windows](how-to-install-iot-edge-windows.md)voor meer informatie over het installatie programma van IOT Edge runtime, inclusief details over alle installatie parameters.

Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)voor informatie over wat er in de nieuwste versie van IOT Edge is opgenomen.

## <a name="prerequisites"></a>Vereisten

Gebruik deze sectie om te controleren of uw Windows-apparaat IoT Edge kan ondersteunen en om dit voor te bereiden voor een container engine vóór de installatie.

### <a name="supported-windows-versions"></a>Ondersteunde Windows-versies

Azure IoT Edge met Linux-containers kan worden uitgevoerd op elke versie van Windows die voldoet aan de [vereisten voor docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Als u IoT Edge wilt installeren op een virtuele machine, schakelt u geneste virtualisatie in en wijst u ten minste 2 GB geheugen toe. Het inschakelen van geneste virtualisatie verschilt afhankelijk van de Hyper Visor die u gebruikt. Voor virtuele machines van de 2e generatie is geneste virtualisatie standaard ingeschakeld. Voor VMWare is er een wissel knop om de functie op uw virtuele machine in te scha kelen.

### <a name="prepare-the-container-engine"></a>De container engine voorbereiden

Azure IoT Edge is afhankelijk van een met [OCI compatibele](https://www.opencontainers.org/) container engine. Het grootste verschil in de configuratie tussen het uitvoeren van Windows-en Linux-containers op een Windows-computer is dat de IoT Edge-installatie een Windows-container runtime bevat, maar u moet uw eigen runtime voor Linux-containers opgeven voordat u IoT Edge installeert.

Als u een Windows-computer wilt instellen voor het ontwikkelen en testen van containers voor Linux-apparaten, kunt u [docker Desktop](https://www.docker.com/docker-windows) gebruiken als uw container engine. U moet docker installeren en configureren voor het [gebruik van Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) voordat u IOT Edge installeert.  

Als uw IoT Edge-apparaat een Windows-computer is, controleert u of deze voldoet aan de [systeem vereisten](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) voor Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge installeren op een nieuw apparaat

>[!NOTE]
>Azure IoT Edge software pakketten zijn onderworpen aan de licentie voorwaarden in de pakketten (in de licentie lijst). Lees de licentie voorwaarden voordat u het pakket gebruikt. Uw installatie en het gebruik van het pakket zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, mag u het pakket niet gebruiken.

Met een Power shell-script wordt de Azure IoT Edge Security daemon gedownload en geïnstalleerd. De Security daemon start vervolgens de eerste van twee runtime modules, de IoT Edge-agent, waarmee externe implementaties van andere modules worden ingeschakeld.

Wanneer u de IoT Edge runtime voor de eerste keer op een apparaat installeert, moet u het apparaat inrichten met een identiteit van een IoT-hub. Eén IoT Edge apparaat kan hand matig worden ingericht met behulp van een apparaat verbindings reeks die is geleverd door uw IoT-hub. U kunt ook de Device Provisioning Service gebruiken om apparaten automatisch in te richten. Dit is handig wanneer u veel apparaten hebt om in te stellen.

U vindt meer informatie over de verschillende installatie opties en-para meters in het artikel [installatie van de Azure IOT Edge runtime op Windows](how-to-install-iot-edge-windows.md). Zodra u docker-bureau blad hebt geïnstalleerd en geconfigureerd voor Linux-containers, wordt door het belangrijkste installatie verschil Linux met de para meter **-ContainerOs** gedeclareerd. Bijvoorbeeld:

1. Als u dit nog niet hebt gedaan, registreert u een nieuw IoT Edge apparaat en haalt u het apparaat connection string op. Kopieer de connection string voor gebruik verderop in deze sectie. U kunt deze stap volt ooien met behulp van de volgende hulpprogram ma's:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure-CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Voer PowerShell uit als beheerder.

   >[!NOTE]
   >Gebruik een AMD64-sessie van Power shell om IoT Edge, niet Power shell (x86), te installeren. Als u niet zeker weet welk sessie type u gebruikt, voert u de volgende opdracht uit:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Met de opdracht **Deploy-IoTEdge** wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft, wordt de functie containers ingeschakeld en wordt de Moby-runtime (die niet wordt gebruikt voor Linux-containers) en de IOT Edge runtime gedownload. De opdracht wordt standaard ingesteld op Windows-containers, dus Linux declareren als het desired container-besturings systeem.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Op dit moment kunnen IoT-kern apparaten automatisch opnieuw worden opgestart. Op andere Windows 10-of Windows Server-apparaten wordt u mogelijk gevraagd om opnieuw op te starten. Als dit het geval is, start u het apparaat nu opnieuw op. Zodra het apparaat klaar is, voert u Power shell als beheerder opnieuw uit.

5. De **initialisatie-IoTEdge-** opdracht configureert de IOT Edge runtime op de computer. De opdracht wordt standaard ingesteld op hand matig inrichten met een apparaat connection string. Declareer Linux als het gewenste container besturingssysteem.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Wanneer u hierom wordt gevraagd, geeft u de connection string op van het apparaat dat u in stap 1 hebt opgehaald. Het apparaat connection string het fysieke apparaat koppelt aan een apparaat-ID in IoT Hub.

   De connection string van het apparaat heeft de volgende indeling en mag geen aanhalings tekens bevatten:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Geslaagde installatie controleren

Controleer de status van de IoT Edge-service:

```powershell
Get-Service iotedge
```

Bekijk service Logboeken in de afgelopen vijf minuten:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Voer een automatische controle uit voor de meest voorkomende configuratie-en netwerk fouten:

```powershell
iotedge check
```

Een lijst met actieve modules weer geven. Na een nieuwe installatie is de enige module die u moet zien, **edgeAgent**. Nadat u [IOT Edge-modules](how-to-deploy-modules-portal.md) voor de eerste keer hebt geïmplementeerd, wordt de andere systeem module, **edgeHub**, ook op het apparaat gestart.

```powershell
iotedge list
```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat hebt ingericht terwijl de runtime is geïnstalleerd, kunt u [IOT Edge modules implementeren](how-to-deploy-modules-portal.md).

Als u problemen ondervindt met de installatie van IoT Edge op de juiste wijze, raadpleegt u de pagina [probleem oplossing](troubleshoot.md) .

Zie [de IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IOT Edge.
