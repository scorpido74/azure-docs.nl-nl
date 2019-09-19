---
title: Een container configureren voor anomalie detectie-API
titleSuffix: Azure Cognitive Services
description: De runtime-omgeving voor de anomalie detectie-API-container `docker run` wordt geconfigureerd met de opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 4a961080bc124e53a8c5fe4dcc5f3cd6f21e9e5c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102576"
---
# <a name="configure-anomaly-detector-containers"></a>Anomaly Detector-containers configureren

De **afwijkende detector** container runtime-omgeving wordt geconfigureerd `docker run` met de opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar zijn. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

Deze container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Gebruikt voor het bijhouden van informatie over facturering.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u toevoegen [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ondersteuning voor telemetrie naar de container.|
|Ja|[Billing](#billing-configuration-setting)|Hiermee wordt de eindpunt-URI van de serviceresource in Azure opgegeven.|
|Ja|[Eula](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Logboek schrijven en, optioneel, metrische gegevens naar een Fluentd-server.|
|Nee|[Http-proxy](#http-proxy-credentials-settings)|Een HTTP-proxy configureren voor het maken van uitgaande aanvragen.|
|Nee|[Logging](#logging-settings)|Biedt ondersteuning voor ASP.NET Core logboekregistratie voor de container. |
|Nee|[Mounts](#mount-settings)|Gegevens lezen en schrijven van de hostcomputer naar de container en van de container naar de hostcomputer.|

> [!IMPORTANT]
> De [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), en [ `Eula` ](#eula-setting) instellingen samen worden gebruikt en u moet geldige waarden opgeven voor alle drie deze, anders uw container start niet. Zie voor meer informatie over het gebruik van deze configuratie-instellingen voor het starten van een container [facturering](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey configuratie-instelling

De `ApiKey` instelling geeft u aan de Azure-resource-sleutel die wordt gebruikt voor het bijhouden van informatie over facturering voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _afwijkende detector_ -resource die [`Billing`](#billing-configuration-setting) is opgegeven voor de configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Anomalie detectie** Resource beheer, onder **sleutels**

## <a name="applicationinsights-setting"></a>Application Insights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Facturering van configuratie-instelling

Met `Billing` deze instelling geeft u de eindpunt-URI op van de bron voor de afwijkings _detector_ op Azure die wordt gebruikt om facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een afwijkende _detector_ -resource in Azure.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Anomalie detectie** Overzicht, label`Endpoint`

|Vereist| Name | Gegevenstype | Description |
|--|------|-----------|-------------|
|Ja| `Billing` | Reeks | URI van de facturering-eindpunt<br><br>Voorbeeld:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

De afwijkende detector containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie van de host koppelen, is afhankelijk van het hostbesturingssysteem. Daarnaast is de koppel locatie van de [hostcomputer](anomaly-detector-container-howto.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Name | Gegevenstype | Description |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Afwijkende detector containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer-koppelpunt. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van de docker-opdrachten uitvoeren 

De volgende voorbeelden gebruiken de configuratie-instellingen om te laten zien hoe u om te schrijven en gebruik `docker run` opdrachten.  Zodra actief is, de container blijft actief totdat u [stoppen](anomaly-detector-container-howto.md#stop-the-container) deze.

* **Regel voortzettings teken**: De docker-opdrachten in de volgende secties gebruiken de back slash `\`,, als een regel voortzettings teken voor een bash-shell. Vervang of verwijder deze op basis van het hostbesturingssysteem vereisten. Het regel vervolg teken voor Windows is bijvoorbeeld een caret, `^`. Vervang de back slash door het caret. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang de waarde tussen vier Kante haken, `{}`met uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Indeling of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Anomaly Detector` resource op de pagina `Anomaly Detector` Azure-sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar `Anomaly Detector` op de pagina overzicht van Azure.| Zie [vereiste para meters](anomaly-detector-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](anomaly-detector-container-howto.md#billing).
> De ApiKey-waarde is de **sleutel** van de pagina Azure anomalie detector-bron sleutels. 

## <a name="anomaly-detector-container-docker-examples"></a>Voor beelden van afwijkende detector container-docker

De volgende docker-voor beelden zijn voor de anomalie detectie container. 

### <a name="basic-example"></a>Eenvoudige voorbeeld 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Voorbeeld van de logboekregistratie met opdrachtregelargumenten

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* [Een anomalie detectie container implementeren naar Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
