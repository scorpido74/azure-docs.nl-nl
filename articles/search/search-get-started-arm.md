---
title: Een Azure Resource Manager-sjabloon gebruiken om uw service te implementeren
titleSuffix: Azure Cognitive Search
description: U kunt snel een instantie van de Azure Cognitive Search-service implementeren met behulp van de Azure Resource Manager-sjabloon.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 670cebe1c0f1c9002e33b729d0db9ee9f9a01283
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682525"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Quickstart: Cognitive Search implementeren met behulp van een Resource Manager-sjabloon

In dit artikel wordt stapsgewijs uitgelegd hoe u met behulp van een Resource Manager-sjabloon een Azure Cognitive Search-resource implementeert in Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-cognitive-search-service"></a>Een Cognitive Search-service maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50,70-85":::

De Azure-resource die in deze sjabloon is gedefinieerd:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): een Azure Cognitive Search-service maken

### <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon maakt u een Azure Cognitive Search-resource.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

In de portal wordt een formulier weergegeven waarmee u eenvoudig parameterwaarden kunt opgeven. Sommige parameters worden vooraf ingevuld met de standaardwaarden uit de sjabloon. U moet uw abonnement, de resourcegroep, locatie en servicenaam opgeven. Als u Cognitive Services wilt gebruiken in een pijplijn voor [AI-verrijking](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), bijvoorbeeld om binaire afbeeldingsbestanden voor tekst te analyseren, kiest u een locatie die zowel Cognitive Search als Cognitive Services biedt. Beide services moeten zich in dezelfde regio bevinden voor AI-verrijkingsworkloads. Wanneer u het formulier hebt ingevuld, moet u akkoord gaan met de voorwaarden en vervolgens de knop Kopen selecteren om uw implementatie te voltooien.

> [!div class="mx-imgBorder"]
> ![Weergave van sjabloon in Azure Portal](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Wanneer de implementatie is voltooid, hebt u toegang tot uw nieuwe resourcegroep en nieuwe zoekservice in de portal.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Cognitive Search-snelstarts en zelfstudies zijn gebaseerd op deze quickstart. Als u van plan bent om volgende quickstarts en zelfstudies te gaan gebruiken, wilt u deze resource misschien bewaren. Als u de resourcegroep niet meer nodig hebt, kunt u deze verwijderen. Hierdoor worden ook de Cognitive Search-service en de gerelateerde resources verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Cognitive Search-service gemaakt met behulp van een Azure Resource Manager-sjabloon en de implementatie gevalideerd. Als u meer wilt weten over Cognitive Search en Azure Resource Manager vindt u meer informatie in de onderstaande artikelen.

 - Lees een [overzicht van Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Een index maken](https://docs.microsoft.com/azure/search/search-get-started-portal) voor uw zoekservice
 - [Een zoek-app maken](https://docs.microsoft.com/azure/search/search-create-app-portal) met behulp van de portalwizard
 - [Een vaardighedenset maken](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) om informatie op te halen uit uw gegevens


