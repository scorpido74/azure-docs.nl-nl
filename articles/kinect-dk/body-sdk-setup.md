---
title: Quickstart - Volgen van lichamen met Azure Kinect DK instellen
description: In deze quickstart stelt u de SDK in voor het volgen van lichamen voor Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensor, toegang, diepte, SDK, lichaam, bijhouden, joint, instellen, cuda, nvidia
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277799"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Quickstart: Volgen van lichamen instellen voor Azure Kinect

Deze quickstart leidt u door het proces van het ophalen van lichamen die worden bijgehouden op uw Azure Kinect DK.

## <a name="system-requirements"></a>Systeemvereisten

Voor de SDK voor het volgen van lichamen moet een NVIDIA-GPU op de host-pc zijn geïnstalleerd. De aanbevolen vereisten voor de host-pc voor het volgen van lichamen worden beschreven op de pagina [systeemvereisten](system-requirements.md).

## <a name="install-software"></a>Software installeren

### <a name="install-the-latest-nvidia-driver"></a>[Installeer het nieuwste NVIDIA-stuurprogramma](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Download en installeer het nieuwste NVIDIA-stuurprogramma voor uw grafische kaart. Oudere stuurprogramma's zijn mogelijk niet compatibel met de binaire CUDA-bestanden die opnieuw worden gedistribueerd met de SDK voor het volgen van lichamen.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual C++ Redistributable voor Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Download en installeer Visual C++ Redistributable voor Visual Studio 2015. 

## <a name="set-up-hardware"></a>Hardware instellen

### <a name="set-up-azure-kinect-dk"></a>[Azure Kinect DK installeren](set-up-azure-kinect-dk.md)

Open de [Azure Kinect Viewer](azure-kinect-viewer.md) om te controleren of uw Azure Kinect DK juist is ingesteld.

## <a name="download-the-body-tracking-sdk"></a>De SDK voor het volgen van lichamen downloaden
 
1. Selecteer de koppeling om [de SDK voor het volgen van lichamen te downloaden](body-sdk-download.md)
2. Installeer de SDK voor het volgen van lichamen op uw pc.

## <a name="verify-body-tracking"></a>Volgen van lichamen verifiëren

Start de **Azure Kinect Viewer voor het bijhouden van de hoofdtekst** om te controleren of de SDK voor het volgen van lichamen correct is ingesteld. De viewer wordt geïnstalleerd met het MSI-installatieprogramma voor de SDK. U kunt deze vinden in het menu Start of op `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe`.

Als uw GPU niet krachtig genoeg is en u het resultaat toch wilt testen, kunt u de **Azure Kinect Body Tracking Viewer** starten met de volgende opdracht in de opdrachtregel: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Als alles goed is ingesteld, wordt er een venster met een 3D-puntenwolk en bijgehouden instanties weergegeven.


![3D-viewer voor het volgen van lichamen](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Voorbeelden

U vindt voorbeelden over het gebruik van de SDK voor het volgen van lichamen [hier](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Uw eerste toepassing bouwen om een lichaam te volgen](build-first-body-app.md)

