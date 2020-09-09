---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303981"
---
>[!NOTE]
> Voor eindpunten voor resources die zijn gemaakt na 1 juli 2019 wordt de aangepaste indeling voor subdomeinen gebruikt die hieronder wordt weergegeven. Zie [Aangepaste subdomeinnamen voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) voor meer informatie en een volledige lijst met regionale eindpunten. 

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Ink Recognizer met behulp van [Azure Portal](../../cognitive-services-apis-create-account.md).

Nadat u een resource hebt gemaakt, haalt u uw eindpunt en sleutel op door uw resource te openen in [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) en op **Quickstart** te klikken.

Maak twee [omgevingsvariabelen](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`: de abonnementssleutel voor het verifiëren van uw aanvragen. 

* `INK_RECOGNITION_ENDPOINT`: het eindpunt voor uw resource. Dit zal er als volgt uitzien: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
