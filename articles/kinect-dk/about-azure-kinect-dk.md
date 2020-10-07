---
title: Over Azure Kinect DK
description: Overzicht van de hulpprogramma's voor de Azure Kinect Developer Kit (DK) en de geïntegreerde services.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azure, kinect, overview, dev kit, DK, device, depth, body tracking, speech, cognitive services, SDKs, SDK, firmware
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277810"
---
# <a name="about-azure-kinect-dk"></a>Over Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK is een ontwikkelaarskit met geavanceerde AI-sensoren die geavanceerde computer vision- en spraakmodellen bieden.  Kinect bevat een dieptesensor, een ruimtelijke microfoonmatrix met videocamera en een oriëntatiesensor als een klein alles-in-een-apparaat met meerdere modi, opties en SDK's (Software Development Kit). Aankopen kunnen worden gedaan in de [Microsoft Online Store](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

De Azure Kinect DK-ontwikkelomgeving bestaat uit de volgende SDK's:

- Sensor-SDK voor sensor- en apparaattoegang op laag niveau.
- Body Tracking-SDK voor het in 3D volgen van lichamen.
- Speech Cognitive Services-SDK voor het inschakelen van microfoontoegang en Azure-spraakservices in de cloud.

Bovendien kunnen er Cognitive Vision-services worden gebruikt met de RGB-camera van het apparaat.

   ![Diagram voor Azure Kinect-SDK's](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Sensor-SDK van Azure Kinect

De Azure Kinect Sensor-SDK biedt op laag niveau toegang tot de sensor voor Azure Kinect DK-hardwaresensor- en -apparaatconfiguratie.

Voor meer informatie over de Azure Kinect Sensor-SDK raadpleegt u [De Sensor-SDK gebruiken](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Functies van de Sensor-SDK van Azure Kinect

De Sensor-SDK beschikt over de volgende functies die na de installatie kunnen worden uitgevoerd in de Azure Kinect DK:

- Toegang tot dieptecamera en modusbeheer (een passieve IR-modus inclusief modi voor brede en smalle weergavediepte) 
- Toegang tot de RGB-camera en beheer hiervan (bijvoorbeeld belichting en witbalans) 
- Toegang tot de bewegingssensor (gyroscoop en versnellingsmeter) 
- Gesynchroniseerde dieptecamera (RGB) die kan worden gestreamd met een configureerbare vertraging tussen camera's 
- Beheer van externe-apparaatsynchronisatie met een configureerbare vertragings-offset tussen apparaten 
- Toegang tot de metagegevens van het camerakader (met bijvoorbeeld de beeldresolutie, timestamp, etc.) 
- Toegang tot kalibratiegegevens van het apparaat 

### <a name="azure-kinect-sensor-sdk-tools"></a>Hulpprogramma's voor de Sensor-SDK van Azure Kinect

De volgende hulpprogramma's zijn beschikbaar in de Sensor-SDK:

- Een viewer-hulpprogramma voor het bewaken van gegevensstromen van apparaten en het configureren van verschillende modi.
- Een hulpprogramma voor het vastleggen van sensorgegevens en een API voor afspelen waarbij gebruik wordt gemaakt van de Matroska-containerindeling.
- Een hulpprogramma voor het bijwerken van de firmware van de Azure Kinect DK.

## <a name="azure-kinect-body-tracking-sdk"></a>De Body Tracking-SDK van Azure Kinect

De Body Tracking-SDK bevat een Windows-bibliotheek en runtime voor het in 3D volgen van lichamen als deze wordt gebruikt in combinatie met de Azure Kinect DK-hardware.

### <a name="azure-kinect-body-tracking-features"></a>Functies van Body Tracking van Azure Kinect

De volgende functies voor het volgen van lichamen zijn beschikbaar in de bijbehorende SDK:

- Biedt lichaamsegmentatie.
- Bevat een anatomisch correct skelet voor elk gedeeltelijke of volledige lichaam in FOV.
- Biedt een unieke identiteit voor elk lichaam.
- Volgt lichamen in de loop van de tijd.

### <a name="azure-kinect-body-tracking-tools"></a>Hulpprogramma's voor het volgen van lichamen voor Azure Kinect

- Body Tracker biedt een viewer-hulpprogramma voor het in 3D volgen van lichamen.

## <a name="speech-cognitive-services-sdk"></a>Speech-SDK voor Cognitive Services

De Speech-SDK maakt het mogelijk om met Azure verbonden spraakservices te gebruiken.

### <a name="speech-services"></a>Spraakservices

- Spraak naar tekst
- Spraakomzetting
- Tekst naar spraak

>[!NOTE]
>De Azure Kinect DK heeft geen luidsprekers.

Ga voor meer informatie naar [de documentatie van Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="vision-services"></a>Vision-services

De volgende [Azure Cognitive Vision Services](https://azure.microsoft.com/services/cognitive-services/directory/vision/) bieden Azure-services waarmee inhoud in afbeeldingen en video's kan worden geïdentificeerd en geanalyseerd.

- [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Face](https://azure.microsoft.com/services/cognitive-services/face/)
- [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Content moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

Services worden continu verder ontwikkeld en verbeterd. Denk er dus aan om regelmatig te controleren op nieuwe of extra [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) om uw toepassing te verbeteren. Bekijk de [Cognitive Services-labs](https://labs.cognitive.microsoft.com/) om al in een vroeg stadium te ontdekken welke nieuwe services er komen.

## <a name="azure-kinect-hardware-requirements"></a>Hardwarevereisten voor Azure Kinect

De Azure Kinect DK bevat de nieuwste sensortechnologieën van Microsoft in één via USB verbonden apparaat. Zie [Azure Kinect DK-hardwarespecificatie](hardware-specification.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een overzicht van Azure Kinect DK. De volgende stap is om zelf aan de slag te gaan en alles in te stellen.

> [!div class="nextstepaction"]
>[Snelstart: Azure Kinect DK installeren](set-up-azure-kinect-dk.md)
