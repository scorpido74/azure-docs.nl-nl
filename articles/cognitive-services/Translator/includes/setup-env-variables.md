---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906572"
---
## <a name="set-up"></a>Instellen

### <a name="create-a-translator-text-resource"></a>Een Translator Text resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Translator Text met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef versie](https://azure.microsoft.com/try/cognitive-services) die 7 dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de Azure-website.
* Bekijk een bestaande resource in de [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, maakt u twee [omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`-De abonnements sleutel voor uw Translator Text-resource.
* `TRANSLATOR_TEXT_ENDPOINT`-Het regionale eind punt of de aangepaste subdomeinnaam voor uw resource.
