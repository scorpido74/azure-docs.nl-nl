---
title: LUIS-resources maken
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879195"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>LUIS-resources maken in Azure Portal

1. Gebruik [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) om te beginnen met het maken van LUIS-resources in Azure Portal.

1. Geef alle vereiste instellingen op:

    |Name|Doel|
    |--|--|
    |Abonnementsnaam| Het abonnement dat wordt gefactureerd voor de resource.|
    |Resourcegroep| Een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.|
    |Name| Een door u gekozen aangepaste naam die wordt gebruikt als het aangepaste subdomein voor uw ontwerp- en voorspellingseindpuntquery's.|
    |Locatie van creatie|De regio die aan uw model is gekoppeld.|
    |Prijscategorie creatie|De prijscategorie bepaalt het maximale aantal transacties per seconde en maand.|
    |Runtime-locatie|De regio die is gekoppeld aan de gepubliceerde voorspellingseindpuntruntime.|
    |Prijscategorie runtime|De prijscategorie bepaalt het maximale aantal transacties per seconde en maand.|

    > [!div class="mx-imgBorder"]
    > [![De Language Understanding-resource maken](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klik op **Controleren + maken** en wacht totdat de resource is gemaakt.
1. Nadat beide resources zijn gemaakt, selecteert u, terwijl u zich nog steeds in Azure Portal bevindt, de nieuwe ontwerpresource en vervolgens **Quickstarts** om de **eindpunt-URL** en **sleutel** voor creatie te verkrijgen, zodat u ontwerpen met behulp van een programma kunt uitvoeren.

> [!TIP]
> Als u de resources wilt gebruiken in de LUIS-portal [moet u de resources](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps) toewijzen.