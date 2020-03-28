---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996857"
---
>[!NOTE]
> Eindpunten voor resources die na 1 juli 2019 zijn gemaakt, gebruiken de aangepaste subdomeinindeling hieronder. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)meer informatie en een volledige lijst met regionale eindpunten. 

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor Ink Recognizer met behulp van de [Azure-portal](../../cognitive-services-apis-create-account.md). 

* U ook een [proefsleutel](https://azure.microsoft.com/try/cognitive-services/#decision) gratis krijgen die zeven dagen geldig is. Na het aanmelden zijn het en een eindpunt beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/)

Nadat u een resource hebt gemaakt, krijgt u uw eindpunt en sleutel door uw resource te openen op de [Azure-portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)en op Snel starten te **klikken.**

Maak twee [omgevingsvariabelen:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- Het eindpunt voor uw resource. Het zal er als volgt uitzien: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- De abonnementssleutel voor het verifiëren van uw aanvragen.   
