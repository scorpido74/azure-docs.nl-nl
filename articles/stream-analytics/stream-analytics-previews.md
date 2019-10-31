---
title: Preview-functies Azure Stream Analytics
description: In dit artikel vindt u een overzicht van de Azure Stream Analytics-functies die momenteel als preview-versie beschikbaar zijn.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: df3e8c1cd91c676c64d15c46c5acdc3d5bcfaa8e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161375"
---
# <a name="azure-stream-analytics-preview-features"></a>Preview-functies Azure Stream Analytics

Dit artikel bevat een overzicht van alle functies die momenteel als preview-versie beschikbaar zijn voor Azure Stream Analytics. Het gebruik van preview-functies in een productie omgeving wordt niet aanbevolen.

## <a name="public-previews"></a>Open bare voor beelden

De volgende functies zijn beschikbaar in de open bare preview-versie. U kunt deze functies vandaag nog gebruiken, maar niet in uw productie omgeving.

### <a name="online-scaling"></a>Online schalen

Wanneer u online schalen hebt, hoeft u uw taak niet te stoppen als u de SU-toewijzing wilt wijzigen. U kunt de SU-capaciteit van een actieve taak verg Roten of verkleinen zonder dat u deze hoeft te stoppen. Dit bouwt voort op de klant belofte van langlopende bedrijfskritische pijp lijnen die vandaag Stream Analytics worden aangeboden. Zie [Azure stream Analytics streaming-eenheden configureren](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)voor meer informatie.

### <a name="c-custom-de-serializers"></a>C#aangepaste deserializers
Ontwikkel aars kunnen gebruikmaken van de kracht van Azure Stream Analytics om gegevens in protobuf, XML of een aangepaste indeling te verwerken. U kunt [aangepaste deserializers](custom-deserializer-examples.md) implementeren in C#, die vervolgens kunnen worden gebruikt om de gebeurtenissen te deserialiseren die door Azure stream Analytics worden ontvangen.

### <a name="extensibility-with-c-custom-code"></a>Uitbreid baarheid C# met aangepaste code

Ontwikkel aars die Stream Analytics-modules in de Cloud of op IoT Edge maken, kunnen C# aangepaste functies schrijven of opnieuw gebruiken en ze rechtstreeks in de query aanroepen via door de [gebruiker gedefinieerde functies](stream-analytics-edge-csharp-udf-methods.md).

### <a name="managed-identity-authentication-with-power-bi"></a>Beheerde identiteits verificatie met Power BI

Azure Stream Analytics biedt volledige ondersteuning voor verificatie op basis van beheerde identiteiten met Power BI voor een dynamische dashboard ervaring.

### <a name="anomaly-detection"></a>Anomaliedetectie

Azure Stream Analytics ' machine learning-modellen bieden ondersteuning voor de detectie van *Prikker* en *spannings dips* naast bidirectionele, trage positieve en trage negatieve trends. Ga voor meer informatie naar [anomalie detectie in azure stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Debug-query stappen in Visual Studio

U kunt eenvoudig een voor beeld van de tussenliggende Rijset in een gegevens diagram bekijken bij het uitvoeren van lokale tests in Azure Stream Analytics-hulpprogram ma's voor Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokale tests met Live-gegevens in Visual Studio code

U kunt uw query's testen op Live-gegevens op uw lokale machine voordat u de taak naar Azure verzendt. Elke test herhaling neemt gemiddeld minder dan twee tot drie seconden in beslag, wat resulteert in een zeer efficiënt ontwikkelings proces.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio code voor Azure Stream Analytics

Azure Stream Analytics-taken kunnen worden gemaakt in Visual Studio code. Bekijk onze [zelf studie over de VS code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="anomaly-detection"></a>Anomaliedetectie

Azure Stream Analytics introduceert nieuwe machine learning modellen met ondersteuning voor de detectie van *Prikker* en *spannings dips* naast bidirectionele, trage positieve en trage negatieve trends. Ga voor meer informatie naar [anomalie detectie in azure stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).


### <a name="integration-with-azure-machine-learning"></a>Integratie met Azure Machine Learning

U kunt Stream Analytics taken schalen met behulp van Machine Learning (ML)-functies. Ga voor meer informatie over hoe u de functies van ML in uw Stream Analytics-taak kunt gebruiken [uw stream Analytics-taak schalen met Azure machine learning-functies](stream-analytics-scale-with-machine-learning-functions.md). Bekijk een praktijk scenario met het uitvoeren van [sentiment-analyse met behulp van Azure stream Analytics en Azure machine learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Live data tests in Visual Studio

Visual Studio Tools for Azure Stream Analytics verbeteren de lokale test functie waarmee u query's kunt testen op live gebeurtenis stromen vanuit Cloud bronnen zoals Event hub of IoT hub. Meer informatie over hoe u [Live-gegevens lokaal kunt testen met behulp van Azure stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Door de gebruiker gedefinieerde .NET-functies op IoT Edge

Met .NET Standard-door de gebruiker gedefinieerde functies kunt u .NET Standard-code uitvoeren als onderdeel van uw streaming-pijp lijn. U kunt eenvoudige C# klassen maken of het volledige project en de bibliotheken importeren. De volledige functionaliteit voor het ontwerpen en opsporen van fouten wordt ondersteund in Visual Studio. Ga voor meer informatie naar [.NET Standard-door de gebruiker gedefinieerde functies ontwikkelen voor Azure stream Analytics Edge-taken](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andere voor beelden

De volgende functies zijn ook beschikbaar als preview-aanvraag.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Beoordeling in realtime hoge prestaties met aangepaste ML-modellen die worden beheerd door Azure Machine Learning

Azure Stream Analytics biedt ondersteuning voor hoogwaardige, realtime scores door gebruik te maken van aangepaste vooraf getrainde Machine Learning modellen die worden beheerd door Azure Machine Learning en die worden gehost in azure Kubernetes service (AKS) of Azure Container Instances (ACI), met behulp van een werk stroom u hoeft geen code te schrijven. [Aanmelden](https://aka.ms/asapreview1) voor preview

### <a name="support-for-azure-stack"></a>Ondersteuning voor Azure Stack
Deze functie is ingeschakeld in de Azure IoT Edge runtime, maakt gebruik van aangepaste Azure Stack functies, zoals systeem eigen ondersteuning voor lokale invoer en uitvoer die op Azure Stack worden uitgevoerd (bijvoorbeeld Event Hubs, IoT Hub, Blob Storage). Met deze nieuwe integratie kunt u hybride architecturen bouwen waarmee u uw gegevens dichtbij kunt analyseren waar deze worden gegenereerd, de latentie wordt verkort en de inzichten worden gemaximaliseerd.
Met deze functie kunt u hybride architecturen bouwen waarmee u uw gegevens dichtbij kunt analyseren waar deze worden gegenereerd, de latentie wordt verkort en de inzichten worden gemaximaliseerd. U moet [zich registreren](https://aka.ms/asapreview1) voor deze preview.
