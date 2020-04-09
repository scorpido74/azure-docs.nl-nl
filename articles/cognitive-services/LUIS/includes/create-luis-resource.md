---
title: LUIS-bron maken
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879195"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>LUIS-resources maken in Azure-portal

1. Gebruik [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) om LUIS-resources te maken in de Azure-portal.

1. Voer alle vereiste instellingen in:

    |Name|Doel|
    |--|--|
    |Abonnementsnaam| het abonnement dat wordt gefactureerd voor de resource.|
    |Resourcegroep| Een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen u Azure-bronnen groeperen voor toegang en beheer.|
    |Name| Een aangepaste naam die u kiest en die wordt gebruikt als uw aangepaste subdomein voor uw ontwerp- en voorspellingseindpuntquery's.|
    |Ontwerplocatie|De regio die aan uw model is gekoppeld.|
    |Prijscategorie voor het ontwerpen|De prijscategorie bepaalt de maximale transactie per seconde en maand.|
    |Runtime-locatie|De regio die is gekoppeld aan de runtime van het gepubliceerde voorspellingseindpunt.|
    |Prijscategorie runtime|De prijscategorie bepaalt de maximale transactie per seconde en maand.|

    > [!div class="mx-imgBorder"]
    > [![De bron voor taalbegrip maken](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klik **op Controleren + maken** en wachten tot de resource is gemaakt.
1. Nadat beide resources zijn gemaakt, nog steeds in de Azure-portal, selecteert u de nieuwe ontwerpbron en **start Quickstarts** om de **URL** en **de sleutel** voor het ontwerpen van het ontwerp programmatisch te krijgen.

> [!TIP]
> Als u de resources wilt gebruiken, [wijst u de resources toe](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)in de LUIS-portal .