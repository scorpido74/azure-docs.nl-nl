---
title: Azure IoT Edge voor Linux installeren op Windows | Microsoft Documenten
description: Installatie-instructies voor Azure IoT Edge voor Linux-containers op Windows 10, Windows Server en Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133166"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>IoT Edge op Windows gebruiken om Linux-containers uit te voeren

Test IoT Edge-modules voor Linux-apparaten met een Windows-machine.

In een productiescenario mogen Windows-apparaten alleen Windows-containers uitvoeren. Een veelvoorkomend ontwikkelingsscenario is echter het gebruik van een Windows-computer om IoT Edge-modules voor Linux-apparaten te bouwen. Met de IoT Edge-runtime voor Windows u Linux-containers uitvoeren voor **test- en ontwikkelingsdoeleinden.**

In dit artikel worden de stappen weergegeven om de Azure IoT Edge-runtime te installeren met Linux-containers op uw Windows x64-systeem (AMD/Intel). Zie De runtime van [Azure IoT Edge installeren op Windows](how-to-install-iot-edge-windows.md)voor meer informatie over het installatieprogramma Voor Runtime IoT Edge, inclusief details over alle installatieparameters.

Zie [Azure IoT Edge-releases](https://github.com/Azure/azure-iotedge/releases)voor informatie over wat er is opgenomen in de nieuwste versie van IoT Edge.

## <a name="prerequisites"></a>Vereisten

Gebruik deze sectie om te controleren of uw Windows-apparaat IoT Edge kan ondersteunen en om het voor te bereiden op een containerengine voordat het wordt geïnstalleerd.

### <a name="supported-windows-versions"></a>Ondersteunde Windows-versies

Azure IoT Edge met Linux-containers kan worden uitgevoerd op elke versie van Windows die voldoet aan de [vereisten voor Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Als u IoT Edge op een virtuele machine wilt installeren, schakelt u geneste virtualisatie in en wijst u ten minste 2 GB geheugen toe. Hoe u geneste virtualisatie inschakelt, is afhankelijk van de hypervisor die u gebruikt. Voor Hyper-V hebben virtuele machines van generatie 2 standaard virtualisatie ingeschakeld. Voor VMWare is er een schakelaar om de functie op uw virtuele machine in te schakelen.

### <a name="prepare-the-container-engine"></a>Bereid de containermotor voor

Azure IoT Edge is gebaseerd op een [OCI-compatibele](https://www.opencontainers.org/) containerengine. Het grootste configuratieverschil tussen het uitvoeren van Windows- en Linux-containers op een Windows-machine is dat de IoT Edge-installatie een Windows-containerruntime bevat, maar u moet uw eigen runtime voor Linux-containers bieden voordat u IoT Edge installeert.

Als u een Windows-machine wilt instellen om containers voor Linux-apparaten te ontwikkelen en te testen, u [Docker Desktop](https://www.docker.com/docker-windows) gebruiken als uw containerengine. U moet Docker installeren en configureren om [Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) te gebruiken voordat u IoT Edge installeert.  

Als uw IoT Edge-apparaat een Windows-computer is, controleert u of het voldoet aan de [systeemvereisten](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) voor Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge installeren op een nieuw apparaat

>[!NOTE]
>Azure IoT Edge-softwarepakketten zijn onderworpen aan de licentievoorwaarden in de pakketten (in de licentiemap). Lees de licentievoorwaarden voordat u het pakket gebruikt. Uw installatie en gebruik van het pakket vormt uw aanvaarding van deze voorwaarden. Als u het niet eens bent met de licentievoorwaarden, gebruik dan het pakket niet.

Een PowerShell-script downloadt en installeert de Azure IoT Edge-beveiligingsdaemon. De security daemon start vervolgens de eerste van twee runtime modules, de IoT Edge-agent, die externe implementaties van andere modules mogelijk maakt.

Wanneer u de IoT Edge-runtime voor de eerste keer op een apparaat installeert, moet u het apparaat voorzien van een identiteit van een IoT-hub. Eén IoT Edge-apparaat kan handmatig worden ingericht met behulp van een tekenreeks voor apparaatverbindingen die door uw IoT-hub worden geleverd. U de service voor apparaatinrichting ook gebruiken om apparaten automatisch in te richten, wat handig is wanneer u veel apparaten moet instellen.

U meer lezen over de verschillende installatieopties en parameters in het artikel [Installeer de Azure IoT Edge-runtime op Windows.](how-to-install-iot-edge-windows.md) Zodra u Docker Desktop hebt geïnstalleerd en geconfigureerd voor Linux-containers, is het belangrijkste installatieverschil het declareren van Linux met de parameter **-ContainerOs.** Bijvoorbeeld:

1. Als u dit nog niet hebt gedaan, registreert u een nieuw IoT Edge-apparaat en haalt u de tekenreeks voor apparaatverbinding op. Kopieer de verbindingstekenreeks die u later in deze sectie wilt gebruiken. U deze stap voltooien met de volgende tools:

   * [Azure-portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure-CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio-code](how-to-register-device.md#register-with-visual-studio-code)

2. Voer PowerShell uit als beheerder.

   >[!NOTE]
   >Gebruik een AMD64-sessie van PowerShell om IoT Edge te installeren, niet PowerShell (x86). Als u niet zeker weet welk sessietype u gebruikt, voert u de volgende opdracht uit:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. De opdracht **Deploy-IoTEdge** controleert of uw Windows-machine een ondersteunde versie heeft, schakelt de functie containers in en downloadt vervolgens de moby-runtime (die niet wordt gebruikt voor Linux-containers) en de Runtime van IoT Edge. De opdracht standaard naar Windows containers, dus declareren Linux als de gewenste container besturingssysteem.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Op dit moment kunnen IoT Core-apparaten automatisch opnieuw worden opgestart. Andere Windows 10- of Windows Server-apparaten kunnen u vragen om opnieuw op te starten. Start het apparaat nu opnieuw op. Zodra uw apparaat klaar is, voert u PowerShell opnieuw uit als beheerder.

5. Met de opdracht **Initialize-IoTEdge** configureert u de runtime van IoT Edge op uw machine. De opdracht is standaard ingesteld op handmatiginrichten met een apparaatverbindingstekenreeks. Verklaar Linux opnieuw als het gewenste containerbesturingssysteem.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Geef de tekenreeks voor de verbinding van het apparaat op die u in stap 1 hebt opgehaald. De tekenreeks voor apparaatverbinding koppelt het fysieke apparaat aan een apparaat-id in IoT Hub.

   De tekenreeks voor apparaatverbinding neemt de volgende indeling en mag geen aanhalingstekens bevatten:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Controleer de status van de IoT Edge-service:

```powershell
Get-Service iotedge
```

Bestudeer servicelogboeken van de laatste 5 minuten:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Voer een geautomatiseerde controle uit op de meest voorkomende configuratie- en netwerkfouten:

```powershell
iotedge check
```

Lijst met lopende modules. Na een nieuwe installatie is **edgeAgent**de enige module die u moet zien uitvoeren. Nadat u [IoT Edge-modules](how-to-deploy-modules-portal.md) voor de eerste keer hebt geïmplementeerd, wordt de andere systeemmodule, **edgeHub,** ook op het apparaat gestart.

```powershell
iotedge list
```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat hebt ingericht met de runtime geïnstalleerd, u [IoT Edge-modules implementeren.](how-to-deploy-modules-portal.md)

Als u problemen ondervindt bij het correct installeren van IoT Edge, raadpleegt u de [pagina voor het oplossen van problemen.](troubleshoot.md)

Zie [De IoT Edge-beveiligingsdaemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IoT Edge.
