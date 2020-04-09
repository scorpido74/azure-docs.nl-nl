---
title: Azure Container Instances uitvoeren - Text Analytics
titleSuffix: Azure Cognitive Services
description: Implementeer de tekstanalysecontainers naar de Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e4b61c6fe2f62745d0f5268221cbb5c84803eb10
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876407"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Een Text Analytics-container implementeren in Azure Container Instances

Meer informatie over het implementeren van de container Cognitive Services [Text Analytics][install-and-run-containers] naar Azure [Container Instances][container-instances]. Deze procedure is een voorbeeld van het maken van een Text Analytics-bron, het maken van een bijbehorende sentimentanalyseafbeelding en de mogelijkheid om deze orkestratie van de twee vanuit een browser uit te oefenen. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

## <a name="prerequisites"></a>Vereisten

* Gebruik een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Volgende stappen 

* Meer [Cognitive Services-containers gebruiken](../../cognitive-services-container-support.md)
* De [met tekstanalyse verbonden service gebruiken](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances