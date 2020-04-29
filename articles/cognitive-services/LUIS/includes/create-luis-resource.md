---
title: LUIS-resource maken
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879195"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>LUIS-resources maken in Azure Portal

1. Met [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) kunt u beginnen met het maken van Luis-resources in de Azure Portal.

1. Voer alle vereiste instellingen in:

    |Naam|Doel|
    |--|--|
    |Abonnementsnaam| het abonnement dat wordt gefactureerd voor de resource.|
    |Resourcegroep| Een aangepaste naam voor de resource groep die u kiest of maakt. Met resource groepen kunt u Azure-resources groeperen voor toegang en beheer.|
    |Naam| Een aangepaste naam die u kiest, die wordt gebruikt als uw aangepaste subdomein voor uw ontwerp-en Voorspellings eindpunt query's.|
    |Locatie van ontwerpen|De regio die aan uw model is gekoppeld.|
    |Prijs categorie ontwerpen|De prijs categorie bepaalt de maximale trans actie per seconde en maand.|
    |Runtime-locatie|De regio die is gekoppeld aan de gepubliceerde Voorspellings eindpunt runtime.|
    |Prijs categorie voor runtime|De prijs categorie bepaalt de maximale trans actie per seconde en maand.|

    > [!div class="mx-imgBorder"]
    > [![De Language Standing resource maken](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klik op **controleren + maken** en wacht tot de resource is gemaakt.
1. Nadat beide resources zijn gemaakt, selecteert u in de Azure Portal nog de nieuwe ontwerp resource en vervolgens **Quick** starts voor het maken van een **eind punt-URL** en **sleutel** voor het schrijven van een programma.

> [!TIP]
> Als u de resources wilt gebruiken, wijst u in de LUIS-Portal [de resources toe](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).