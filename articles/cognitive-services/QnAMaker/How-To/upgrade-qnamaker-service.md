---
title: Een upgrade uitvoeren van uw QnA Maker-service-QnA Maker
titleSuffix: Azure Cognitive Services
description: Deel of werk uw QnA Maker Services bij om de resources beter te kunnen beheren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640484"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Uw QnA Maker-service delen of bijwerken
Deel of werk uw QnA Maker Services bij om de resources beter te kunnen beheren. 

U kunt ervoor kiezen om de afzonderlijke onderdelen van de QnA Maker stack bij te werken nadat de eerste keer is gemaakt. Bekijk [hier](https://aka.ms/qnamaker-docs-capacity)de details van de afhankelijke onderdelen en de SKU-selectie.

## <a name="share-existing-services-with-qna-maker"></a>Bestaande Services delen met QnA Maker

QnA Maker maakt verschillende Azure-resources. Gebruik de volgende tabel om te begrijpen wat u wel en niet kunt delen om het beheer te beperken en te profiteren van het delen van kosten:

|Service|Delen|
|--|--|
|Cognitive Services|X|
|App Service-abonnement|✔|
|App Service|X|
|Application Insights|✔|
|Zoekservice|✔|

## <a name="upgrade-qna-maker-management-sku"></a>QnA Maker Management SKU upgraden

Als u meer vragen en antwoorden in uw Knowledge Base nodig hebt, moet u de prijs categorie van QnA Maker service upgraden naar uw huidige laag. 

Een upgrade uitvoeren van de QnA Maker Management SKU:

1. Ga in de Azure Portal naar uw QnA Maker-resource en selecteer **prijs categorie**.

    ![QnA Maker resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Kies de juiste SKU en druk op **selecteren**.

    ![QnA Maker prijzen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>App service bijwerken

 Als uw Knowledge Base meer aanvragen van uw client-app nodig heeft, moet u de prijs categorie van uw app service bijwerken.

U kunt de app service [omhoog](https://docs.microsoft.com/azure/app-service/manage-scale-up) of omlaag schalen.

1. Ga naar de app service-resource in het Azure Portal en selecteer de gewenste opties voor **omhoog** of **omlaag** schalen.

    ![App service-schaal QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Azure Search-service bijwerken

Als u van plan bent een groot aantal Knowledge bases te hebben, moet u uw Azure Search service prijs categorie bijwerken. 

Het is momenteel niet mogelijk om een in-place upgrade uit te voeren van de Azure Search-SKU. U kunt echter een nieuwe Azure Search-resource met de gewenste SKU maken, de gegevens herstellen naar de nieuwe resource en deze vervolgens koppelen aan de QnA Maker-stack.

1. Maak een nieuwe Azure-Zoek resource in het Azure Portal en kies de gewenste SKU.

    ![QnA Maker Azure Search-resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Herstel de indexen van de oorspronkelijke Azure Search-resource naar de nieuwe. Zie de voorbeeld code voor het [](https://github.com/pchoudhari/QnAMakerBackupRestore)terugzetten van back-ups.

3. Zodra de gegevens zijn hersteld, gaat u naar uw nieuwe Azure Search-resource, selecteert u **sleutels**en noteert u de **naam** en de **beheerders sleutel**.

    ![QnA Maker Azure-Zoek sleutels](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Als u de nieuwe Azure Search-resource wilt koppelen aan de QnA Maker stack, gaat u naar de QnA Maker app-service.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Selecteer **Toepassings instellingen** en vervang de velden **AzureSearchName** en **AzureSearchAdminKey** uit stap 3.

    ![Appservice-instelling van QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Start de App-service.

    ![QnA Maker appservice opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [QnA Maker-API gebruiken](../Quickstarts/csharp.md)
