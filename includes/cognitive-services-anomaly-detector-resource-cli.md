---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483022"
---
Begin met het gebruik van de Anomaliedetectorservice door een van de onderstaande Azure-bronnen te maken.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Een proefbron maken (wordt geopend in een nieuw tabblad)</a>
    * Geen Azure-abonnement nodig: 
    * Geldig voor zeven dagen, gratis. Na het aanmelden zijn een proefsleutel en eindpunt beschikbaar op de [Azure-website.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Dit is een geweldige optie als u Anomalie Detector wilt proberen, maar geen Azure-abonnement hebt.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Een anomaliedetectorbron maken (wordt op een nieuw tabblad geopend):</a>
    * Beschikbaar via de Azure-portal totdat u de bron verwijdert.
    * Gebruik de gratis prijscategorie om de service uit te proberen en upgrade later naar een betaalde laag voor productie.



### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

>[!NOTE]
> De eindpunten voor niet-proefresources die na 1 juli 2019 zijn gemaakt, gebruiken de aangepaste subdomeinindeling hieronder. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)meer informatie en een volledige lijst met regionale eindpunten. 

Maak met behulp van uw sleutel en eindpunt van de resource die u hebt gemaakt twee omgevingsvariabelen voor verificatie:

* `ANOMALY_DETECTOR_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `ANOMALY_DETECTOR_ENDPOINT`- Het resourceeindpunt voor het verzenden van API-aanvragen. Het zal er als volgt uitzien: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Gebruik de instructies voor uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

***