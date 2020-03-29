---
title: Azure Kubernetes-service uitvoeren - Text Analytics
titleSuffix: Azure Cognitive Services
description: Implementeer de containerafbeelding Text Analytics in Azure Kubernetes Service en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 3264ec5a83277e6bb4befad46cd1337175e911c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383410"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Een Text Analytics-container implementeren in Azure Kubernetes-service

Meer informatie over het implementeren van de containerafbeelding Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) naar Azure Kubernetes Service (AKS). In deze procedure ziet u hoe u een Text Analytics-bron maakt, hoe u een bijbehorende sentimentanalyseafbeelding maakt en hoe u deze orkestratie van de twee vanuit een browser uitvoeren. Het gebruik van containers kan uw aandacht verleggen van het beheren van infrastructuur naar in plaats daarvan gericht op applicatieontwikkeling.

## <a name="prerequisites"></a>Vereisten

Deze procedure vereist verschillende tools die lokaal moeten worden geïnstalleerd en uitgevoerd. Gebruik Azure Cloud Shell niet. Je hebt het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een teksteditor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/download).
* De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) is geïnstalleerd.
* De [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) geïnstalleerd.
* Een Azure-bron met de juiste prijscategorie. Niet alle prijsniveaus werken met deze container:
    * **Azure Text Analytics-bron** met alleen F0- of standaardprijsniveaus.
    * **Azure Cognitive Services-bron** met de prijslaag S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Volgende stappen

* Meer [cognitive services-containers gebruiken](../../cognitive-services-container-support.md)
* De [met tekstanalyse verbonden service gebruiken](../vs-text-connected-service.md)
