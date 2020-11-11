---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369071"
---
>[!NOTE]
> Voor eindpunten voor resources die zijn gemaakt na 1 juli 2019 wordt de aangepaste indeling voor subdomeinen gebruikt die hieronder wordt weergegeven. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten. 

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Ink Recognizer met behulp van [Azure Portal](../../cognitive-services-apis-create-account.md).

Nadat u een resource hebt gemaakt, haalt u uw eindpunt en sleutel op door uw resource te openen in [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) en op **Quickstart** te klikken.

Maak twee [omgevingsvariabelen](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`: de abonnementssleutel voor het verifiëren van uw aanvragen. 

* `INK_RECOGNITION_ENDPOINT`: het eindpunt voor uw resource. Dit zal er als volgt uitzien: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`