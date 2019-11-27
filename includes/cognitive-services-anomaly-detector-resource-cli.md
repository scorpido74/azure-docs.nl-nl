---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483022"
---
Begin met het gebruik van de afwijkende detector-service door een van de onderstaande Azure-resources te maken.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Een proef resource maken (wordt geopend op een nieuw tabblad)</a>
    * Er is geen Azure-abonnement nodig: 
    * Gratis geldig voor zeven dagen. Nadat u zich hebt aangemeld, is een proef sleutel en een eind punt beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Dit is een uitstekende optie als u anomalie detectie wilt proberen, maar geen Azure-abonnement hebt.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Een afwijkende detector bron maken (wordt geopend op een nieuw tabblad)</a>:
    * Beschikbaar via de Azure Portal totdat u de resource verwijdert.
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
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

***