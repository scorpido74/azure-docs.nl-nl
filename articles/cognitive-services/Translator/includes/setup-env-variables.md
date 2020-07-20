---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144234"
---
## <a name="set-up"></a>Instellen

### <a name="create-a-translator-resource"></a>Een Translator-resource maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Translator met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Een bestaande resource bekijken in het [Azure-portal](https://portal.azure.com/).

Nadat u een sleutel van uw proefversie of resource hebt opgehaald, maakt u twee [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - De abonnementssleutel voor uw Translator-resource.
* `TRANSLATOR_TEXT_ENDPOINT` - Het globale eindpunt voor Translator. Gebruik `https://api.cognitive.microsofttranslator.com/`.
