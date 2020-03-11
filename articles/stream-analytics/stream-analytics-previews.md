---
title: Azure Stream Analytics preview-functies
description: In dit artikel geeft een lijst van de Azure Stream Analytics-functies die momenteel in preview.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969622"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics preview-functies

In dit artikel bevat een overzicht van alle functies die momenteel in Preview-versie van Azure Stream Analytics. Preview-functies gebruiken in een productieomgeving wordt niet aanbevolen.

## <a name="public-previews"></a>Openbare preview-versies

De volgende functies zijn in openbare preview. U kunt profiteren van deze functies vandaag, maar niet gebruiken in uw productieomgeving.

### <a name="online-scaling"></a>Online schalen

Wanneer u online schalen hebt, hoeft u uw taak niet te stoppen als u de SU-toewijzing wilt wijzigen. U kunt de SU-capaciteit van een actieve taak verg Roten of verkleinen zonder dat u deze hoeft te stoppen. Dit bouwt voort op de klant belofte van langlopende bedrijfskritische pijp lijnen die vandaag Stream Analytics worden aangeboden. Zie [Azure stream Analytics streaming-eenheden configureren](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)voor meer informatie.

### <a name="c-custom-de-serializers"></a>C#aangepaste deserializers
Ontwikkel aars kunnen gebruikmaken van de kracht van Azure Stream Analytics om gegevens in protobuf, XML of een aangepaste indeling te verwerken. U kunt [aangepaste deserializers](custom-deserializer-examples.md) implementeren in C#, die vervolgens kunnen worden gebruikt om de gebeurtenissen te deserialiseren die door Azure stream Analytics worden ontvangen.

### <a name="extensibility-with-c-custom-code"></a>Uitbreid baarheid C# met aangepaste code

Ontwikkel aars die Stream Analytics-modules in de Cloud of op IoT Edge maken, kunnen C# aangepaste functies schrijven of opnieuw gebruiken en ze rechtstreeks in de query aanroepen via door de [gebruiker gedefinieerde functies](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Debug-query stappen in Visual Studio

U kunt eenvoudig een voor beeld van de tussenliggende Rijset in een gegevens diagram bekijken bij het uitvoeren van lokale tests in Azure Stream Analytics-hulpprogram ma's voor Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokale tests met Live-gegevens in Visual Studio code

U kunt uw query's testen op Live-gegevens op uw lokale machine voordat u de taak naar Azure verzendt. Elke test herhaling neemt gemiddeld minder dan twee tot drie seconden in beslag, wat resulteert in een zeer efficiënt ontwikkelings proces.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio code voor Azure Stream Analytics

Azure Stream Analytics-taken kunnen worden gemaakt in Visual Studio code. Bekijk onze [zelf studie over de VS code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integratie met Azure Machine Learning

Stream Analytics-taken met Machine Learning (ML)-functies, kunt u schalen. Ga voor meer informatie over hoe u de functies van ML in uw Stream Analytics-taak kunt gebruiken [uw stream Analytics-taak schalen met Azure machine learning-functies](stream-analytics-scale-with-machine-learning-functions.md). Bekijk een praktijk scenario met het uitvoeren van [sentiment-analyse met behulp van Azure stream Analytics en Azure machine learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Live gegevens testen in Visual Studio

Visual Studio-hulpprogramma's voor Azure Stream Analytics, verbeter de lokale testen functie waarmee u voor het testen van query's voor live-gebeurtenis-stromen van cloudbronnen, zoals Event Hub of IoT-hub. Meer informatie over hoe u [Live-gegevens lokaal kunt testen met behulp van Azure stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-gebruiker gedefinieerde functies op IoT Edge

Met .NET standard-door gebruiker gedefinieerde functies, kunt u de standaard .NET-code uitvoeren als onderdeel van uw streamingpijplijn automatiseert. U kunt eenvoudig C#-klassen maken of importeren van volledige-project en -bibliotheken. Volledige Maak- en debugfase van ervaring wordt ondersteund in Visual Studio. Ga voor meer informatie naar [.NET Standard-door de gebruiker gedefinieerde functies ontwikkelen voor Azure stream Analytics Edge-taken](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andere voor beelden

De volgende functies zijn ook beschikbaar als preview-aanvraag.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Beoordeling in realtime hoge prestaties met aangepaste ML-modellen die worden beheerd door Azure Machine Learning

Azure Stream Analytics biedt ondersteuning voor hoogwaardige, realtime scores door gebruik te maken van aangepaste vooraf getrainde Machine Learning modellen die worden beheerd door Azure Machine Learning en die worden gehost in azure Kubernetes service (AKS) of Azure Container Instances (ACI), met behulp van een werk stroom u hoeft geen code te schrijven. [Aanmelden](https://aka.ms/asapreview1) voor preview

### <a name="support-for-azure-stack"></a>Ondersteuning voor Azure Stack
Deze functie is ingeschakeld in de Azure IoT Edge runtime, maakt gebruik van aangepaste Azure Stack functies, zoals systeem eigen ondersteuning voor lokale invoer en uitvoer die op Azure Stack worden uitgevoerd (bijvoorbeeld Event Hubs, IoT Hub, Blob Storage). Met deze nieuwe integratie kunt u hybride architecturen bouwen waarmee u uw gegevens dichtbij kunt analyseren waar deze worden gegenereerd, de latentie wordt verkort en de inzichten worden gemaximaliseerd.
Met deze functie kunt u hybride architecturen bouwen waarmee u uw gegevens dichtbij kunt analyseren waar deze worden gegenereerd, de latentie wordt verkort en de inzichten worden gemaximaliseerd. U moet [zich registreren](https://aka.ms/asapreview1) voor deze preview.
