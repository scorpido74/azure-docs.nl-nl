---
title: Azure Stream Analytics preview-functies
description: In dit artikel worden de Azure Stream Analytics-functies weergegeven die momenteel in de preview staan.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878303"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics preview-functies

In dit artikel worden alle functies samengevat die momenteel in de preview voor Azure Stream Analytics staan. Het gebruik van voorbeeldfuncties in een productieomgeving wordt niet aanbevolen.

## <a name="public-previews"></a>Openbare previews

De volgende functies zijn in openbare preview. U vandaag nog profiteren van deze functies, maar gebruik ze niet in uw productieomgeving.

### <a name="online-scaling"></a>Online schalen

Met online schalen hoeft u uw taak niet te stoppen als u de SU-toewijzing moet wijzigen. U de SU-capaciteit van een lopende taak vergroten of verkleinen zonder deze te hoeven stoppen. Dit bouwt voort op de belofte van klanten van langlopende bedrijfskritische pijplijnen die Stream Analytics vandaag de dag biedt. Zie [Streaming-eenheden van Azure Stream Analytics configureren](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)voor meer informatie.

### <a name="c-custom-de-serializers"></a>C# custom de-serializers
Ontwikkelaars kunnen de kracht van Azure Stream Analytics gebruiken om gegevens te verwerken in Protobuf, XML of een aangepaste indeling. U [aangepaste deserialisators](custom-deserializer-examples.md) implementeren in C#, die vervolgens kunnen worden gebruikt om gebeurtenissen die door Azure Stream Analytics zijn ontvangen, te de-serialiseren.

### <a name="extensibility-with-c-custom-code"></a>Uitbreidbaarheid met aangepaste C#-code

Ontwikkelaars die Stream [Analytics-modules](stream-analytics-edge-csharp-udf-methods.md)maken in de cloud of op IoT Edge kunnen aangepaste C#-functies schrijven of hergebruiken en deze rechtstreeks in de query aanroepen via door de gebruiker gedefinieerde functies.


### <a name="debug-query-steps-in-visual-studio"></a>Stappen voor foutopsporing in Visual Studio

U eenvoudig een voorbeeld bekijken van de tussenliggende rijset in een gegevensdiagram wanneer u lokale tests doet in Azure Stream Analytics-hulpprogramma's voor Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokale tests met live gegevens in Visual Studio Code

U uw query's testen op basis van live gegevens op uw lokale machine voordat u de taak indient bij Azure. Elke testiteratie duurt gemiddeld minder dan twee tot drie seconden, wat resulteert in een zeer efficiënt ontwikkelingsproces.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio-code voor Azure Stream Analytics

Azure Stream Analytics-taken kunnen worden geschreven in Visual Studio Code. Zie onze [VS Code aan de slag tutorial](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Real-time high performance scoring met aangepaste ML-modellen beheerd door Azure Machine Learning

Azure Stream Analytics ondersteunt hoge prestaties, realtime scores door gebruik te maken van aangepaste vooraf getrainde Machine Learning-modellen die worden beheerd door Azure Machine Learning en worden gehost in Azure Kubernetes Service (AKS) of Azure Container Instances (ACI), met behulp van een werkstroom waarvoor u geen code hoeft te schrijven. [Aanmelden](https://aka.ms/asapreview1) voor voorbeeld


### <a name="live-data-testing-in-visual-studio"></a>Live data testen in Visual Studio

Visual Studio-hulpprogramma's voor Azure Stream Analytics verbeteren de lokale testfunctie waarmee u query's testen op live gebeurtenisstreams van cloudbronnen zoals Event Hub of IoT-hub. Meer informatie over het [lokaal testen van live-gegevens met Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET door de gebruiker gedefinieerde functies op IoT Edge

Met .NET-standaard door de gebruiker gedefinieerde functies u .NET Standard-code uitvoeren als onderdeel van uw streamingpijplijn. U eenvoudige C#-klassen maken of volledige project- en bibliotheken importeren. Volledige ontwerp- en foutopsporingservaring wordt ondersteund in Visual Studio. Ga voor meer informatie naar [Ontwikkel .NET Standard door de gebruiker gedefinieerde functies voor Azure Stream Analytics Edge-taken](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andere voorbeelden

De volgende functies zijn ook beschikbaar in preview op aanvraag.

### <a name="support-for-azure-stack"></a>Ondersteuning voor Azure Stack
Deze functie is ingeschakeld op de Azure IoT Edge-runtime en maakt gebruik van aangepaste Azure Stack-functies, zoals native ondersteuning voor lokale invoer en uitvoer die worden uitgevoerd op Azure Stack (bijvoorbeeld Gebeurtenishubs, IoT Hub, Blob-opslag). Met deze nieuwe integratie u hybride architecturen bouwen die uw gegevens dicht bij de plaats waar deze worden gegenereerd, de latentie verlagen en inzichten maximaliseren.
Met deze functie u hybride architecturen bouwen die uw gegevens kunnen analyseren in de buurt van waar ze worden gegenereerd, waardoor de latentie wordt verlaagd en inzichten kunnen worden gemaximaliseerd. U moet [zich aanmelden](https://aka.ms/asapreview1) voor deze preview.
