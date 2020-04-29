---
title: Een Cognitive Services resource maken in de Azure Portal
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door een resource te maken en te abonneren in de Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219476"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Een Cognitive Services resource maken met behulp van de Azure Portal

Gebruik deze Quick Start om Azure Cognitive Services te gebruiken. Nadat u een cognitieve service resource hebt gemaakt in de Azure Portal, krijgt u een eind punt en een sleutel voor het verifiëren van uw toepassingen.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Een nieuwe Azure Cognitive Services-resource maken

1. Een resource maken.

    #### <a name="multi-service-resource"></a>[Resource met meerdere services](#tab/multiservice)
    
    De resource met meerdere services heeft de naam **Cognitive Services** in de portal. [Maak een Cognitive Services resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    Op dit moment biedt de resource met meerdere services toegang tot de volgende Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision  | Content Moderator                                    | Face               | Language Understanding (LUIS) | Tekstanalyse   |
    | Translator Text  | Bing Search V7 <br>(Web, afbeelding, nieuws, video, visueel element) | Bing Aangepaste zoekopdrachten | Bing Entiteiten zoeken            | Bing Automatische suggesties |
    | Bing Spellingcontrole |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Resource met één service](#tab/singleservice)

    Gebruik de onderstaande koppelingen om een resource te maken voor de beschik bare Cognitive Services:

    | Vision                      | Speech                  | Taal                          | Besluit             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Computer vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Spraak Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Insluitende lezer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Aangepaste Vision-service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing Aangepaste zoekopdrachten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entiteiten zoeken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Tekstanalyse](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing Spellingcontrole](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Automatische suggesties](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Geef op de pagina **maken** de volgende informatie op:

    #### <a name="multi-service-resource"></a>[Resource met meerdere services](#tab/multiservice)

    |    |    |
    |--|--|
    | **Naam** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *MyCognitiveServicesResource*. |
    | **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
    | **Locatie** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de runtime-Beschik baarheid van uw resource. |
    | **Prijs categorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/)voor de API voor meer informatie.
    | **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of toevoegen aan een bestaande groep. |

    ![Scherm voor het maken van resources](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klik op **maken**.

    #### <a name="single-service-resource"></a>[Resource met één service](#tab/singleservice)

    |    |    |
    |--|--|
    | **Naam** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *TextAnalyticsResource*. |
    | **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
    | **Locatie** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de runtime-Beschik baarheid van uw resource. |
    | **Prijs categorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/)voor de API voor meer informatie.
    | **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of toevoegen aan een bestaande groep. |

    ![Scherm voor het maken van resources](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klik op **maken**.

    ***


## <a name="get-the-keys-for-your-resource"></a>De sleutels voor uw resource ophalen

1. Nadat de implementatie van de resource is voltooid, klikt u in de **volgende stappen**op **Ga naar resource** .

    ![Zoeken naar Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. In het deel venster Quick start dat wordt geopend, kunt u toegang krijgen tot uw sleutel en eind punt.

    ![Sleutel en eind punt ophalen](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle resources in de groep verwijderd.

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resource groep met de resource die u wilt verwijderen
3. Klik met de rechter muisknop op de vermelding van de resource groep. Selecteer **Resourcegroep verwijderen** en bevestig dit.

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor docker-container](cognitive-services-container-support.md)
