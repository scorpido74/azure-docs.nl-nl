---
title: Een Azure Resource Manager-sjabloon gebruiken om uw service te implementeren
titleSuffix: Azure Cognitive Search
description: U snel een Azure Cognitive Search-serviceinstantie implementeren met behulp van de sjabloon Azure Resource Manager.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607622"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Snelstart: Cognitief zoeken implementeren met een sjabloon ResourceManager

In dit artikel wordt u door het proces voor het gebruik van een resourcemanagersjabloon om een Azure Cognitive Search-bron in de Azure-portal te implementeren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-cognitive-search-service"></a>Een service voor cognitief zoeken maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure-sjablonen.](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices)

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

De Azure-bron die in deze sjabloon is gedefinieerd:

- [Microsoft.Search/searchServices:](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices)een Azure Cognitive Search-service maken

### <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon maakt een Azure Cognitive Search-bron.

[![Implementeren in Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

De portal geeft een formulier weer waarmee u eenvoudig parameterwaarden opgeven. Sommige parameters zijn vooraf gevuld met de standaardwaarden van de sjabloon. U moet uw abonnements-, resourcegroep-, locatie- en servicenaam opgeven. Als u Cognitive Services wilt gebruiken in een [AI-verrijkingspijplijn,](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) bijvoorbeeld om binaire afbeeldingsbestanden op tekst te analyseren, kiest u een locatie die zowel cognitive search als cognitive services biedt. Beide services moeten zich in dezelfde regio bevinden voor AI-verrijkingsworkloads. Zodra u het formulier hebt ingevuld, moet u akkoord gaan met de algemene voorwaarden en vervolgens de aankoopknop selecteren om uw implementatie te voltooien.

> [!div class="mx-imgBorder"]
> ![Azure-portalweergave van sjabloon](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Wanneer uw implementatie is voltooid, hebt u toegang tot uw nieuwe brongroep en nieuwe zoekservice in de portal.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Cognitieve Zoeken quickstarts en tutorials bouwen voort op deze quickstart. Als u van plan bent om door te gaan met de daaropvolgende quickstarts en tutorials, u deze bron op zijn plaats laten. Wanneer dit niet meer nodig is, u de brongroep verwijderen, die de service Cognitive Search en gerelateerde bronnen verwijdert.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een cognitive search-service gemaakt met behulp van een Azure Resource Manager-sjabloon en de implementatie gevalideerd. Ga verder naar de onderstaande artikelen voor meer informatie over Cognitief zoeken en Azure Resource Manager.

 - Lees een [overzicht van Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Een index](https://docs.microsoft.com/azure/search/search-get-started-portal) voor uw zoekservice maken
 - [Een zoek-app maken](https://docs.microsoft.com/azure/search/search-create-app-portal) met de wizard Portal
 - [Maak een skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) om informatie uit uw gegevens te extraheren


