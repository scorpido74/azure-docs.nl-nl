---
title: Een Cognitive Services resource maken met behulp van de Azure CLI
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door met de Azure-opdracht regel interface een abonnement te maken op een resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: b77d69248059a494d823afcd149382dd52b0e49d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317124"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Een Cognitive Services resource maken met behulp van de Azure-opdracht regel interface (CLI)

Gebruik deze Quick Start om aan de slag te gaan met Azure Cognitive Services met behulp van de [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Cognitive Services worden vertegenwoordigd door Azure- [resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) die u in uw Azure-abonnement hebt gemaakt. Nadat u de resource hebt gemaakt, gebruikt u de sleutels en het eind punt dat u hebt gegenereerd voor het verifiëren van uw toepassingen. 


In deze Quick Start leert u hoe u zich kunt registreren voor Azure Cognitive Services en hoe u een account met een single-service of meerdere service-abonnement kunt maken met behulp van de [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Deze services worden vertegenwoordigd door Azure- [resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), waarmee u verbinding kunt maken met een of meer van de azure-Cognitive Services-API's.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/) .
* De [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>De Azure CLI installeren en aanmelden 

Installeer de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de opdracht [AZ login](https://docs.microsoft.com/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

U kunt ook de knop groene **try it** gebruiken om deze opdrachten uit te voeren in uw browser.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Een nieuwe Azure Cognitive Services-resource groep maken

Voordat u een Cognitive Services resource maakt, moet u een Azure-resource groep hebben om de resource te kunnen bevatten. Wanneer u een nieuwe resource maakt, hebt u de optie om een nieuwe resource groep te maken of een bestaande te gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resource groep maakt.

### <a name="choose-your-resource-group-location"></a>De locatie van de resource groep kiezen

Als u een resource wilt maken, hebt u een van de Azure-locaties die beschikbaar zijn voor uw abonnement. U kunt een lijst met beschik bare locaties ophalen met de opdracht [AZ account list-locations](/cli/azure/account#az-account-list-locations) . De meeste Cognitive Services kunnen vanaf verschillende locaties worden geopend. Kies het account dat het dichtst bij u ligt of Bekijk welke locaties beschikbaar zijn voor de service.

> [!IMPORTANT]
> * Onthoud uw Azure-locatie, omdat u deze nodig hebt wanneer u de Azure Cognitive Services aanroept.
> * De beschik baarheid van sommige Cognitive Services kan per regio verschillen. Zie [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)voor meer informatie.  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Nadat u uw Azure-locatie hebt, maakt u een nieuwe resource groep in de Azure CLI met behulp van de opdracht [AZ Group Create](/cli/azure/group#az-group-create) .

Vervang in het onderstaande voor beeld de Azure-locatie door `westus2` een van de Azure-locaties die beschikbaar zijn voor uw abonnement.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Een cognitieve service en prijs categorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten wat de soort service is die u wilt gebruiken, samen met de gewenste [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of SKU). U gebruikt deze en andere informatie als para meters bij het maken van de resource.

### <a name="multi-service"></a>Meerdere services

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Meerdere services. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/) voor meer informatie.            | `CognitiveServices`     |


> [!NOTE]
> Veel van de onderstaande Cognitive Services hebben een gratis laag die u kunt gebruiken om de service te proberen. Als u de gratis laag wilt gebruiken, gebruikt u `F0` als de SKU voor uw resource.

### <a name="vision"></a>Vision

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision-voor spelling | `CustomVision.Prediction` |
| Custom Vision-training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Search

| Service            | Soort                  |
|--------------------|-----------------------|
| Bing Automatische suggesties   | `Bing.Autosuggest.v7` |
| Bing Aangepaste zoekopdrachten | `Bing.CustomSearch`   |
| Bing Entiteiten zoeken | `Bing.EntitySearch`   |
| Bing Zoeken        | `Bing.Search.v7`      |
| Bing Spellingcontrole   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Speech

| Service            | Soort                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Spraakherkenning | `SpeakerRecognition` |

### <a name="language"></a>Taal

| Service            | Soort                |
|--------------------|---------------------|
| Formulier uitleg | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Tekstanalyse     | `TextAnalytics`     |
| Tekstomzetting   | `TextTranslation`   |

### <a name="decision"></a>Besluit

| Service           | Soort               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

U vindt een lijst met ' soorten ' beschik bare cognitieve service ' met de opdracht [AZ cognitiveservices account list-typen](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Een nieuwe resource toevoegen aan de resource groep

Als u een nieuwe Cognitive Services resource wilt maken en hierop wilt abonneren, gebruikt u de opdracht [AZ cognitiveservices account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) . Met deze opdracht wordt een nieuwe factureer bare resource toegevoegd aan de resource groep die u eerder hebt gemaakt. Wanneer u een nieuwe resource maakt, moet u weten wat de soort service is die u wilt gebruiken, samen met de prijs categorie (of SKU) en een Azure-locatie:

U kunt een F0 (gratis) resource maken voor anomalie detectie, `anomaly-detector-resource` met de naam met de onderstaande opdracht.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>De sleutels voor uw resource ophalen

Als u zich wilt aanmelden bij de lokale installatie van de opdracht regel interface (CLI), gebruikt u de opdracht [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli-interactive
az login
```

Gebruik de opdracht [AZ cognitiveservices account Keys List](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) om de sleutels voor uw cognitieve service resource op te halen.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Prijs categorieën en facturering

De prijs categorieën (en de hoeveelheid die u ontvangt) zijn gebaseerd op het aantal trans acties dat u verzendt met behulp van uw verificatie gegevens. Met elke prijs categorie geeft u het volgende op:
* het maximum aantal toegestane trans acties per seconde (TPS).
* Service functies zijn ingeschakeld in de prijs categorie.
* De kosten voor een vooraf gedefinieerd bedrag aan trans acties. Boven deze hoeveelheid worden er extra kosten in rekening gebracht, zoals is opgegeven in de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

## <a name="get-current-quota-usage-for-your-resource"></a>Huidig quotum gebruik voor uw resource ophalen

Gebruik de opdracht [AZ cognitiveservices account list-Usage](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) om het gebruik van uw cognitieve service resource op te halen.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services resource wilt opschonen en verwijderen, kunt u deze of de resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle resources in de groep verwijderd.

Als u de resource groep en de bijbehorende resources wilt verwijderen, gebruikt u de opdracht AZ Group Delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor docker-container](cognitive-services-container-support.md)
