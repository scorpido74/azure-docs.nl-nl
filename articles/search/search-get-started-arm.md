---
title: Een Azure Resource Manager sjabloon gebruiken om uw service te implementeren
titleSuffix: Azure Cognitive Search
description: U kunt snel een exemplaar van Azure Cognitive Search service implementeren met behulp van de Azure Resource Manager-sjabloon.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80607622"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Snelstartgids: Cognitive Search implementeren met behulp van een resource manager-sjabloon

In dit artikel wordt stapsgewijs uitgelegd hoe u een resource manager-sjabloon gebruikt om een Azure Cognitive Search-bron te implementeren in de Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-cognitive-search-service"></a>Een Cognitive Search-service maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure-sjablonen](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

De Azure-resource die in deze sjabloon is gedefinieerd:

- [Micro soft. Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): een Azure Cognitive Search-service maken

### <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon maakt u een Azure Cognitive Search-resource.

[![Implementeren in Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

In de portal wordt een formulier weer gegeven waarmee u eenvoudig parameter waarden kunt opgeven. Sommige para meters worden vooraf ingevuld met de standaard waarden van de sjabloon. U moet uw abonnement, de resource groep, de locatie en de service naam opgeven. Als u Cognitive Services wilt gebruiken in een [AI-verrijkings](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) pijplijn, bijvoorbeeld om binaire afbeeldings bestanden voor tekst te analyseren, kiest u een locatie die zowel Cognitive Search als Cognitive Services biedt. Beide services moeten zich in dezelfde regio bevinden voor AI-verrijkings workloads. Zodra u het formulier hebt ingevuld, moet u akkoord gaan met de voor waarden en vervolgens de knop aanschaffen selecteren om uw implementatie te volt ooien.

> [!div class="mx-imgBorder"]
> ![Azure Portal weer gave van sjabloon](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Wanneer de implementatie is voltooid, hebt u toegang tot uw nieuwe resource groep en nieuwe zoek service in de portal.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Cognitive Search Snelstartgids en zelf studies zijn gebaseerd op deze Snelstartgids. Als u van plan bent om verder te gaan met de volgende Quick starts en zelf studies, wilt u deze resource mogelijk blijven gebruiken. Wanneer u deze niet meer nodig hebt, kunt u de resource groep verwijderen, waarmee de Cognitive Search-service en gerelateerde resources worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Cognitive Search-service gemaakt met behulp van een Azure Resource Manager sjabloon en de implementatie gevalideerd. Ga verder met de volgende artikelen voor meer informatie over Cognitive Search en Azure Resource Manager.

 - Een [overzicht van Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) lezen
 - [Een index maken](https://docs.microsoft.com/azure/search/search-get-started-portal) voor uw zoek service
 - [Een zoek-app maken](https://docs.microsoft.com/azure/search/search-create-app-portal) met de wizard Portal
 - [Een vakkennisset maken](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) om gegevens uit uw gegevens te extra heren


