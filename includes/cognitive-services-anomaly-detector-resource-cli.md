---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554725"
---
Begin met het gebruik van de afwijkende detector-service door een van de onderstaande Azure-resources te maken.

* Een [proef resource](https://azure.microsoft.com/try/cognitive-services/#decision) (geen Azure-abonnement vereist): 
    * Gratis geldig voor zeven dagen. Nadat u zich hebt aangemeld, is een proef sleutel en een eind punt beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Dit is een uitstekende optie als u anomalie detectie wilt proberen, maar geen Azure-abonnement hebt.

* Een [afwijkende detector-bron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * Beschikbaar via de [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) totdat u de resource verwijdert.
    * Gebruik de gratis prijs categorie om de service te proberen en pas later bij te werken naar een betaalde laag voor productie.

### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

>[!NOTE]
> De eind punten voor niet-proef resources die zijn gemaakt na 1 juli 2019, gebruiken de aangepaste indeling voor subdomeinen die hieronder wordt weer gegeven. Zie [aangepaste subdomein namen voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)voor meer informatie en een volledige lijst met regionale eind punten. 

Met uw sleutel en eind punt van de resource die u hebt gemaakt, maakt u twee omgevings variabelen voor verificatie:

* `ANOMALY_DETECTOR_KEY`: de resource sleutel voor het verifiëren van uw aanvragen.
* `ANOMALY_DETECTOR_ENDPOINT`: het resource-eind punt voor het verzenden van API-aanvragen. Dit ziet er als volgt uit: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Volg de instructies voor uw besturings systeem.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile` en voeg de omgevings variabele toe:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

***