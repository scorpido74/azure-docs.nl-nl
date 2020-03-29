---
title: Een resource voor Cognitive Services maken in de Azure-portal
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door een bron in de Azure-portal te maken en u te abonneren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219476"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Een resource voor Cognitive Services maken met de Azure-portal

Gebruik deze snelstart om Azure Cognitive Services te gebruiken. Nadat u een Cognitive Service-bron hebt hebt aanmaken in de Azure-portal, krijgt u een eindpunt en een sleutel voor het verifiëren van uw toepassingen.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement - [Maak er gratis een.](https://azure.microsoft.com/free/)

## <a name="create-a-new-azure-cognitive-services-resource"></a>Een nieuwe Azure Cognitive Services-bron maken

1. Een resource maken.

    #### <a name="multi-service-resource"></a>[Multiservicebron](#tab/multiservice)
    
    De multi-service resource heet **Cognitive Services** in de portal. [Een resource voor Cognitive Services maken.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    
    Op dit moment biedt de multiservicebron toegang tot de volgende Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision  | Content Moderator                                    | Face               | Language Understanding (LUIS) | Tekstanalyse   |
    | Translator Text  | Bing Zoeken v7 <br>(Web, Image, News, Video, Visual) | Aangepast zoeken met Bing | Bing Entiteiten zoeken            | Bing Automatische suggesties |
    | Bing Spellingcontrole |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Single-serviceresource](#tab/singleservice)

    Gebruik de onderstaande koppelingen om een bron te maken voor de beschikbare cognitieve services:

    | Vision                      | Speech                  | Taal                          | Besluit             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Computervisie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Spraakservices](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Meeslepende lezer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Aangepaste vision-service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Sprekerherkenning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Aangepast zoeken met Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entiteiten zoeken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Tekstanalyse](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing Spellingcontrole](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Automatische suggesties](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Geef op de pagina **Maken** de volgende informatie op:

    #### <a name="multi-service-resource"></a>[Multiservicebron](#tab/multiservice)

    |    |    |
    |--|--|
    | **Naam** | Een beschrijvende naam voor uw bron voor cognitieve services. *MyCognitiveServicesResource*bijvoorbeeld . |
    | **Abonnement** | Selecteer een van uw beschikbare Azure-abonnementen. |
    | **Locatie** | De locatie van uw cognitieve service-instantie. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de beschikbaarheid van runtime van uw resource. |
    | **Prijslaag** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie de [API-prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/)voor meer informatie .
    | **Resourcegroep** | De Azure-brongroep die uw bron voor Cognitive Services bevat. U een nieuwe groep maken of toevoegen aan een reeds bestaande groep. |

    ![Scherm resourcemaken](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klik **op Maken**.

    #### <a name="single-service-resource"></a>[Single-serviceresource](#tab/singleservice)

    |    |    |
    |--|--|
    | **Naam** | Een beschrijvende naam voor uw bron voor cognitieve services. *Bijvoorbeeld TextAnalyticsResource*. |
    | **Abonnement** | Selecteer een van uw beschikbare Azure-abonnementen. |
    | **Locatie** | De locatie van uw cognitieve service-instantie. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de beschikbaarheid van runtime van uw resource. |
    | **Prijslaag** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie de [API-prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/)voor meer informatie .
    | **Resourcegroep** | De Azure-brongroep die uw bron voor Cognitive Services bevat. U een nieuwe groep maken of toevoegen aan een reeds bestaande groep. |

    ![Scherm resourcemaken](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klik **op Maken**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Haal de sleutels voor uw bron

1. Nadat uw resource is geïmplementeerd, klikt u op **Ga naar resource** onder Volgende **stappen**.

    ![Zoeken naar cognitieve services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. In het snelstartvenster dat wordt geopend, hebt u toegang tot uw sleutel en eindpunt.

    ![Sleutel- en eindpunt oppakken](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen in de groep verwijderd.

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. De resourcegroep zoeken die de te verwijderen resource bevat
3. Klik met de rechtermuisknop op de vermelding van de resourcegroep. Selecteer **Resourcegroep verwijderen** en bevestig dit.

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor Dockercontainer](cognitive-services-container-support.md)
