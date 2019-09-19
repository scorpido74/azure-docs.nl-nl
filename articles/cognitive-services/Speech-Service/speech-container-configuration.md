---
title: Spraak containers configureren
titleSuffix: Azure Cognitive Services
description: De spraak container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: f7e2e95b553039b88267f730787fbbac82099948
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105171"
---
# <a name="configure-speech-service-containers"></a>Spraak service containers configureren

Met spraak containers kunnen klanten één spraak toepassings architectuur maken die is geoptimaliseerd om te profiteren van zowel robuuste Cloud mogelijkheden als Edge-locatie. De twee spraak containers die worden ondersteund, zijn nu **spraak-naar-tekst** en **tekst-naar-spraak**. 

De runtime-omgeving voor de **spraak** container wordt `docker run` geconfigureerd met de opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar zijn. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), en [ `Eula` ](#eula-setting) instellingen samen worden gebruikt en u moet geldige waarden opgeven voor alle drie deze, anders uw container start niet. Zie voor meer informatie over het gebruik van deze configuratie-instellingen voor het starten van een container [facturering](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey configuratie-instelling

De `ApiKey` instelling geeft u aan de Azure-resource-sleutel die wordt gebruikt voor het bijhouden van informatie over facturering voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _spraak_ bron die is opgegeven [`Billing`](#billing-configuration-setting) voor de configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Spraak** Resource beheer, onder **sleutels**

## <a name="applicationinsights-setting"></a>Application Insights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Facturering van configuratie-instelling

Met `Billing` deze instelling geeft u de eindpunt-URI op van de _spraak_ bron op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _spraak_ bron op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Spraak** Overzicht, label`Endpoint`

|Vereist| Name | Gegevenstype | Description |
|--|------|-----------|-------------|
|Ja| `Billing` | Reeks | URI van de facturering-eindpunt<br><br>Voorbeeld:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

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

De spraak containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie van de host koppelen, is afhankelijk van het hostbesturingssysteem. Bovendien de [hostcomputer](speech-container-howto.md#the-host-computer)van koppelpunten locatie is mogelijk niet toegankelijk is vanwege een conflict tussen de machtigingen die wordt gebruikt door de docker-service-account en de host koppelen locatie machtigingen. 

|Optioneel| Name | Gegevenstype | Description |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | In spraak containers wordt dit niet gebruikt.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer-koppelpunt. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van de docker-opdrachten uitvoeren 

De volgende voorbeelden gebruiken de configuratie-instellingen om te laten zien hoe u om te schrijven en gebruik `docker run` opdrachten.  Zodra actief is, de container blijft actief totdat u [stoppen](speech-container-howto.md#stop-the-container) deze.

* **Regel voortzettings teken**: De docker-opdrachten in de volgende secties gebruiken de back slash `\`,, als een regel voortzetting teken. Vervang of verwijder deze op basis van het hostbesturingssysteem vereisten. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Indeling of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Speech` resource op de pagina `Speech` Azure-sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar `Speech` op de pagina overzicht van Azure.| Zie [vereiste para meters](speech-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](#billing-configuration-setting).
> De ApiKey-waarde is de **sleutel** van de pagina met Azure-spraak bron sleutels. 

## <a name="speech-container-docker-examples"></a>Voor beelden van spraak container docker

De volgende docker-voor beelden zijn voor de spraak container. 

### <a name="basic-example-for-speech-to-text"></a>Eenvoudig voor beeld voor spraak naar tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="basic-example-for-text-to-speech"></a>Basis voorbeeld voor tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Logboek registratie van voor spraak naar tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Voor beeld van logboek registratie voor tekst naar spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [over het installeren en uitvoeren van containers](speech-container-howto.md)
