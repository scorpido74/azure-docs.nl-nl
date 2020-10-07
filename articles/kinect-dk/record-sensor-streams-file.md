---
title: Quickstart - Azure Kinect-sensorstromen vastleggen in een bestand
description: In deze quickstart leert u hoe u gegevensstromen vanuit de Sensor-SDK vastlegt in een bestand.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, record, play back, reader, matroska, mkv, streams, depth, rgb, camera, color, imu, audio, sensor
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277772"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Quickstart: Azure Kinect-sensorstromen vastleggen in een bestand

In deze quickstart vindt u informatie over hoe u de [Azure Kinect-recorder](azure-kinect-recorder.md) kunt gebruiken om gegevensstromen van de sensor-SDK vast te leggen in een bestand.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

In deze quickstart wordt uitgegaan van het volgende:

- U hebt de Azure Kinect DK verbonden met uw hostcomputer en u hebt deze op de juiste wijze ingeschakeld.
- U hebt de hardware [ingesteld](set-up-azure-kinect-dk.md).

## <a name="create-recording"></a>Opname maken

1. Open een opdrachtprompt en geef het pad op naar de [Azure Kinect-recorder](azure-kinect-recorder.md), die zich in de map met geïnstalleerde hulpprogramma's bevindt (als `k4arecorder.exe`). Bijvoorbeeld: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Maak een opname van 5 seconden.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. De recorder maakt standaard gebruik van de dieptemodus NFOV (zonder binning) en heeft een uitvoer van 1080p RGB met 30 fps, inclusief IMU-gegevens. Raadpleeg [Azure Kinect-recorder](azure-kinect-recorder.md) voor een volledig overzicht van de opnameopties en voor tips.

## <a name="play-back-recording"></a>Opnamen afspelen

U kunt de [Azure Kinect-viewer](azure-kinect-viewer.md) gebruiken voor het afspelen van een opname.

1. [`k4aviewer.exe`](azure-kinect-viewer.md) starten
2. Vouw het tabblad **Opname openen** uit en open de opname.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u sensorstromen kunt vastleggen in een bestand, is het tijd voor het volgende

> [!div class="nextstepaction"]
> [Uw eerste Azure Kinect-toepassing bouwen](build-first-app.md)
