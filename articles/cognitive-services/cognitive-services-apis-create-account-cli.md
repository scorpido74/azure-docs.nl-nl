---
title: Een resource voor Cognitive Services maken met de Azure CLI
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door een resource te maken en u te abonneren met de Azure-opdrachtregelinterface.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219608"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Een resource voor Cognitive Services maken met behulp van de Azure Command-Line Interface (CLI)

Gebruik deze snelstart om aan de slag te gaan met Azure Cognitive Services met behulp van de [Azure Command Line Interface (CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Cognitieve services worden vertegenwoordigd door [Azure-resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) die u in uw Azure-abonnement maakt. Nadat u de resource hebt gemaakt, gebruikt u de sleutels en het eindpunt dat u hebt gegenereerd om uw toepassingen te verifiëren. 


In deze quickstart leert u hoe u zich aanmeldt voor Azure Cognitive Services en een account maakt met een abonnement met één service of multiservice, met behulp van de [Azure Command Line Interface (CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Deze services worden vertegenwoordigd door [Azure-resources,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)waarmee u verbinding maken met een of meer van de Azure Cognitive Services API's.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement - Maak er gratis [een.](https://azure.microsoft.com/free/)
* De [Azure Command Line Interface (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>De Azure CLI installeren en aanmelden 

Installeer de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Als u zich wilt aanmelden bij uw lokale installatie van de CLI, voert u de opdracht [az-aanmelding uit:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

U ook de groene **knop Uitproberen** gebruiken om deze opdrachten in uw browser uit te voeren.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Een nieuwe Azure Cognitive Services-brongroep maken

Voordat u een resource voor Cognitive Services maakt, moet u een Azure-brongroep hebben om de bron te bevatten. Wanneer u een nieuwe resource maakt, u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel ziet u hoe u een nieuwe resourcegroep maakt.

### <a name="choose-your-resource-group-location"></a>Uw locatie van de resourcegroep kiezen

Als u een bron wilt maken, hebt u een van de Azure-locaties nodig die beschikbaar zijn voor uw abonnement. Met de opdracht lijstlocaties van [het AZ-account](/cli/azure/account#az-account-list-locations) u een lijst met beschikbare locaties ophalen. De meeste cognitieve services zijn toegankelijk vanaf verschillende locaties. Kies de locatie die het dichtst bij u staat of bekijk welke locaties beschikbaar zijn voor de service.

> [!IMPORTANT]
> * Onthoud uw Azure-locatie, omdat u deze nodig hebt wanneer u de Azure Cognitive Services aanroept.
> * De beschikbaarheid van sommige cognitieve services kan per regio verschillen. Zie [Azure-producten per regio voor](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)meer informatie.  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Nadat u uw azure-locatie hebt, maakt u een nieuwe brongroep in de Azure CLI met de opdracht [az-groep maken.](/cli/azure/group#az-group-create)

Vervang in het onderstaande `westus2` voorbeeld de azure-locatie door een van de Azure-locaties die beschikbaar zijn voor uw abonnement.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Kies een cognitieve service- en prijscategorie

Wanneer u een nieuwe resource maakt, moet u de 'soort' service kennen die u wilt gebruiken, samen met de [gewenste prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of sku). U gebruikt deze en andere informatie als parameters bij het maken van de bron.

### <a name="multi-service"></a>Multiservice

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Meerdere diensten. Zie de [prijspagina](https://azure.microsoft.com/pricing/details/cognitive-services/) voor meer informatie.            | `CognitiveServices`     |


> [!NOTE]
> Veel van de cognitieve services hieronder hebben een gratis laag die u gebruiken om de service uit te proberen. Als u de gratis `F0` laag wilt gebruiken, gebruikt u als de sku voor uw resource.

### <a name="vision"></a>Vision

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Aangepaste visie - Voorspelling | `CustomVision.Prediction` |
| Aangepaste visie - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Search

| Service            | Soort                  |
|--------------------|-----------------------|
| Bing Automatische suggesties   | `Bing.Autosuggest.v7` |
| Aangepast zoeken met Bing | `Bing.CustomSearch`   |
| Bing Entiteiten zoeken | `Bing.EntitySearch`   |
| Bing Zoeken        | `Bing.Search.v7`      |
| Bing Spellingcontrole   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Speech

| Service            | Soort                 |
|--------------------|----------------------|
| Spraakservices    | `SpeechServices`     |
| Spraakherkenning | `SpeakerRecognition` |

### <a name="language"></a>Taal

| Service            | Soort                |
|--------------------|---------------------|
| Formulierbegrip | `FormUnderstanding` |
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

U vindt een lijst van beschikbare Cognitive Service "soorten" met de [az cognitiveservices account lijst-soorten](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) opdracht:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Een nieuwe resource toevoegen aan uw resourcegroep

Als u een nieuwe Cognitive Services-bron wilt maken en u wilt abonneren, gebruikt u de opdracht [voor het az cognitiveservices-account.](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) Met deze opdracht wordt een nieuwe opsteinbare bron toegevoegd aan de resourcegroep die eerder is gemaakt. Wanneer u uw nieuwe resource maakt, moet u de 'soort' service kennen die u wilt gebruiken, samen met de prijscategorie (of sku) en een Azure-locatie:

U een F0 (gratis) resource `anomaly-detector-resource` maken voor Anomaly Detector, met de onderstaande opdracht.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Haal de sleutels voor uw bron

Als u wilt inloggen op uw lokale installatie van de Command-Line Interface (CLI), gebruikt u de [inlogopdracht az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Gebruik de [lijst met gegevenslijst van AZ Cognitiveservices-accountsleutels](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) om de sleutels voor uw Cognitive Service-bron te krijgen.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Prijsniveaus en facturering

Prijsniveaus (en het bedrag dat u in rekening wordt gebracht) zijn gebaseerd op het aantal transacties dat u verzendt met behulp van uw verificatiegegevens. Elke prijscategorie geeft de:
* het maximumaantal toegestane transacties per seconde (TPS).
* servicefuncties die zijn ingeschakeld binnen de prijscategorie.
* De kosten voor een vooraf bepaald aantal transacties. Als u boven dit bedrag komt, worden er extra kosten in rekening gebracht, zoals aangegeven in de [prijsgegevens](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

## <a name="get-current-quota-usage-for-your-resource"></a>Huidige quotumgebruik voor uw resource weergeven

Gebruik de opdracht voor het gebruik van de lijst met az [cognitiveservices-account](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) om het gebruik van uw Cognitive Service-bron op te halen.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een bron voor Cognitive Services wilt opschonen en verwijderen, u deze of de brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen in de groep verwijderd.

Als u de brongroep en de bijbehorende resources wilt verwijderen, gebruikt u de opdracht Verwijderen van de AZ-groep.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor Dockercontainer](cognitive-services-container-support.md)
