---
title: Azure Stream Analytics-taken lokaal ontwikkelen en debuggen
description: Meer informatie over het ontwikkelen en testen van Azure Stream Analytics-taken op uw lokale computer voordat u ze uitvoert in Azure-portal.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879841"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Azure Stream Analytics-taken lokaal ontwikkelen en debuggen

Hoewel u Azure Stream Analytics-taken maken en testen in de Azure-portal, geven veel ontwikkelaars de voorkeur aan een lokale ontwikkelingservaring. Stream Analytics maakt het eenvoudig om uw favoriete codeeditor- en ontwikkelingstools te gebruiken om taken te maken en te testen met live gebeurtenisstreams vanuit Azure Event Hub, IoT Hub en Blob Storage met behulp van een volledig functionerende lokale runtime met één knooppunt. U ook taken rechtstreeks vanuit uw lokale ontwikkelomgeving indienen bij Azure.

## <a name="local-development-environments"></a>Lokale ontwikkelingsomgevingen

De manier waarop u Stream Analytics-taken op uw lokale computer ontwikkelt, is afhankelijk van uw voorkeuren voor het implementeren en de beschikbaarheid van functies. Zie [Azure Stream Analytics-functievergelijking](feature-comparison.md) om te zien welke functies worden ondersteund voor elke ontwikkelomgeving.

De omgevingen in de volgende tabel ondersteunen lokale ontwikkeling:

|Omgeving                              |Beschrijving    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Met de [Azure Stream Analytics Tools-extensie](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) voor Visual Studio Code u uw Stream analytics-taak zowel lokaal als in de cloud ontwerpen, beheren en testen met een rijk IntelliSense- en native source-besturingselement. Ondersteunt ontwikkeling op Linux, MacOS en Windows. Zie [Een Azure Stream Analytics-taak maken in Visual Studio Code](quick-create-vs-code.md)voor meer informatie.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics Tools maakt deel uit van de azure-ontwikkel- en gegevensopslag- en verwerkingsworkloads in Visual Studio. U Visual Studio gebruiken om aangepaste C# door de gebruiker gedefinieerde functies en deserializers te schrijven. Zie [Een Azure Stream Analytics-taak maken met Visual Studio](stream-analytics-quick-create-vs.md)voor meer informatie.|
|[Opdrachtprompt of terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Het Azure Stream Analytics CI/CD NuGet-pakket biedt hulpprogramma's voor visual studio-projectbuild, lokale tests op een willekeurige machine. Het Ci/CD-npm-pakket azure stream analytics biedt hulpprogramma's voor projectbuilds van Visual Studio Code (die een Azure Resource Manager-sjabloon genereert) op een willekeurige machine.|

## <a name="next-steps"></a>Volgende stappen

* [Query's van TestStream Analytics lokaal met voorbeeldgegevens met Behulp van Visual Studio Code](visual-studio-code-local-run.md)
* [Test Stream Analytics-query's lokaal tegen live stream-invoer met behulp van Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Query's van Stream Analytics lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Live gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-live-data-local-testing.md)
