---
title: Containers configureren-Computer Vision
titleSuffix: Azure Cognitive Services
description: Configureer verschillende instellingen voor Tekst herkennen containers in Computer Vision.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 186f2f60aad15b336265114d7c85c757e0dd333f
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102290"
---
# <a name="configure-recognize-text-docker-containers"></a>Tekst herkennen docker-containers configureren

De runtime-omgeving van **tekst herkennen** container wordt geconfigureerd `docker run` met de opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar zijn. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), en [ `Eula` ](#eula-setting) instellingen samen worden gebruikt en u moet geldige waarden opgeven voor alle drie deze, anders uw container start niet. Zie voor meer informatie over het gebruik van deze configuratie-instellingen voor het starten van een container [facturering](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>ApiKey configuratie-instelling

Met `ApiKey` deze instelling geeft u `Cognitive Services` de Azure-resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Cognitive Services_ bron die is opgegeven [`Billing`](#billing-configuration-setting) voor de configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** Resource beheer, onder **sleutels**

## <a name="applicationinsights-setting"></a>Application Insights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Facturering van configuratie-instelling

Met `Billing` deze instelling geeft u de eindpunt-URI op van de _Cognitive Services_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _Cognitive Services_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** Overzicht, label`Endpoint`

Vergeet niet om de `vision/v1.0` route ring toe te voegen aan de URI van het eind punt, zoals wordt weer gegeven in de volgende tabel. 

|Vereist| Name | Gegevenstype | Description |
|--|------|-----------|-------------|
|Ja| `Billing` | Reeks | URI van de facturering-eindpunt<br><br>Voorbeeld:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Overeenkomst instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor http-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Instellingen voor koppelen

Gebruik bind koppelt om te lezen en schrijven van gegevens naar en van de container. U kunt opgeven van een koppelpunt invoer of uitvoer koppelen door op te geven de `--mount` optie in de [docker uitvoeren](https://docs.docker.com/engine/reference/commandline/run/) opdracht.

De Computer Vision-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie van de host koppelen, is afhankelijk van het hostbesturingssysteem. Daarnaast is de koppel locatie van de [hostcomputer](computer-vision-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Name | Gegevenstype | Description |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Computer Vision containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer-koppelpunt. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van de docker-opdrachten uitvoeren 

De volgende voorbeelden gebruiken de configuratie-instellingen om te laten zien hoe u om te schrijven en gebruik `docker run` opdrachten.  Zodra actief is, de container blijft actief totdat u [stoppen](computer-vision-how-to-install-containers.md#stop-the-container) deze.

* **Regel voortzettings teken**: De docker-opdrachten in de volgende secties gebruiken de back slash `\`,, als een regel voortzetting teken. Vervang of verwijder deze op basis van het hostbesturingssysteem vereisten. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vergeet niet om de `vision/v1.0` route ring toe te voegen aan de URI van het eind punt, zoals wordt weer gegeven in de volgende tabel. 

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Indeling of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Computer Vision` resource op de pagina `Computer Vision` Azure-sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar `Computer Vision` op de pagina overzicht van Azure.| Zie [vereiste para meters](computer-vision-how-to-install-containers.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](computer-vision-how-to-install-containers.md#billing).
> De ApiKey-waarde is de **sleutel** van de `Cognitive Services` pagina Azure-resource sleutels. 

## <a name="recognize-text-container-docker-examples"></a>Voor beelden van de docker tekst container herkennen

De volgende docker-voor beelden zijn voor de tekst container recognize. 

### <a name="basic-example"></a>Eenvoudige voorbeeld 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [over het installeren en uitvoeren van containers](computer-vision-how-to-install-containers.md)
