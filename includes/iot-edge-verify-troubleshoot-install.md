---
title: Na installatie en inrichting controleren op geslaagde en probleem oplossing
description: bestand opnemen
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979539"
---
## <a name="verify-successful-setup"></a>Geslaagde installatie controleren

Controleer de status van de IoT Edge-service. Deze moet worden weer gegeven als actief.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Bekijk service Logboeken.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Als u de IoT Edge runtime zojuist hebt geïnstalleerd, ziet u mogelijk een lijst met fouten uit de tijd tussen het uitvoeren van **Deploy-IoTEdge** en **Initialize-IoTEdge**. Deze fouten worden verwacht, omdat de service probeert te starten voordat deze wordt geconfigureerd.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Voer het [hulp programma voor probleem oplossing](../articles/iot-edge/troubleshoot.md#run-the-check-command) uit om te controleren op de meest voorkomende configuratie-en netwerk fouten.

```powershell
iotedge check
```

De **$edgeHub** systeem module wordt niet geïmplementeerd op het apparaat totdat u uw eerste module implementeert voor IOT Edge op het apparaat. Als gevolg hiervan geeft de geautomatiseerde controle een fout melding voor de `Edge Hub can bind to ports on host` connectiviteits controle. Deze fout kan worden genegeerd, tenzij deze optreedt nadat een module op het apparaat is geïmplementeerd.

Ten slotte lijst met actieve modules:

```powershell
iotedge list
```

Na een nieuwe installatie is de enige module die u moet zien, **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Tips en problemen oplossen

Op apparaten die zijn beperkt, wordt het ten zeerste aanbevolen om de *OptimizeForPerformance* -omgevings variabele in te stellen op *False* volgens instructies in de [hand leiding](../articles/iot-edge/troubleshoot.md)voor het oplossen van problemen.

Als uw apparaat geen verbinding kan maken met IoT Hub en uw netwerk een proxy server heeft, volgt u de stappen in [uw IOT edge-apparaat configureren om te communiceren via een proxy server](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

Op Linux-apparaten hebt u verhoogde bevoegdheden nodig om opdrachten uit te voeren `iotedge` . Nadat u de runtime hebt geïnstalleerd, meldt u zich af bij uw computer en meldt u zich weer aan om uw machtigingen automatisch bij te werken. Tot slot kunt `sudo` u gebruiken om opdrachten uit te voeren met verhoogde bevoegdheden.

# <a name="windows"></a>[Windows](#tab/windows)

Op Windows-apparaten met Windows-containers is de Moby-container engine geïnstalleerd als onderdeel van IoT Edge. De Moby-engine is ontworpen om parallel met docker Desktop te worden uitgevoerd. U kunt `docker` opdrachten gebruiken als u rechtstreeks wilt communiceren met de containers op het apparaat. U moet echter specifiek de Moby-engine richten in het geval docker Desktop ook op het apparaat is geïnstalleerd.

Als u bijvoorbeeld alle docker-installatie kopieën wilt weer geven, gebruikt u de volgende opdracht:

```powershell
docker images
```

Als u alle Moby-installatie kopieën wilt weer geven, wijzigt u dezelfde opdracht met een verwijzing naar de Moby-Engine:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

De engine-URI wordt vermeld in de uitvoer van het installatie script of u kunt deze vinden in de sectie container runtime-instellingen voor het bestand config. yaml.

![moby_runtime URI in config. yaml](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
