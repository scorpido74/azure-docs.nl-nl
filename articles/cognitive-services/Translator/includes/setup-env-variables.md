---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393823"
---
## <a name="set-up"></a>Instellen

### <a name="create-a-translator-text-resource"></a>Een bron voor vertalertekst maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor vertalertekst met de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proefsleutel](https://azure.microsoft.com/try/cognitive-services) die 7 dagen gratis geldig is. Na het aanmelden is het beschikbaar op de Azure-website.
* Een bestaande bron weergeven in de [Azure-portal](https://portal.azure.com/).

Nadat u een sleutel van uw proefabonnement of resource hebt opgehaald, maakt u twee [omgevingsvariabelen:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- De abonnementssleutel voor uw translator text-bron.
* `TRANSLATOR_TEXT_ENDPOINT`- Het wereldwijde eindpunt voor vertalertekst. Gebruik `https://api.cognitive.microsofttranslator.com/`.
