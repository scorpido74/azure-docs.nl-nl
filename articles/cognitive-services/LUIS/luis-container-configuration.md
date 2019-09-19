---
title: Docker-container instellingen-LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS container runtime-omgeving is geconfigureerd met behulp van de `docker run` opdracht argumenten. LUIS heeft enkele vereiste instellingen, samen met een aantal optionele instellingen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 9760475886ecb0f20d9f0f3981eab8246643da21
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101987"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding docker-containers configureren 

De container runtime-omgeving **Language Understanding** (Luis) wordt geconfigureerd met `docker run` de opdracht argumenten. LUIS heeft enkele vereiste instellingen, samen met een aantal optionele instellingen. Verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar zijn. De container-specifieke instellingen voor de invoer zijn [instellingen voor koppelen](#mount-settings) en de instellingen voor facturering. 

## <a name="configuration-settings"></a>Configuratie-instellingen

Deze container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Gebruikt voor het bijhouden van informatie over facturering.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u toevoegen [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ondersteuning voor telemetrie naar de container.|
|Ja|[Billing](#billing-setting)|Hiermee wordt de eindpunt-URI van de serviceresource in Azure opgegeven.|
|Ja|[Eula](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Logboek schrijven en, optioneel, metrische gegevens naar een Fluentd-server.|
|Nee|[Http-proxy](#http-proxy-credentials-settings)|Een HTTP-proxy configureren voor het maken van uitgaande aanvragen.|
|Nee|[Logging](#logging-settings)|Biedt ondersteuning voor ASP.NET Core logboekregistratie voor de container. |
|Ja|[Mounts](#mount-settings)|Gegevens lezen en schrijven van de hostcomputer naar de container en van de container naar de hostcomputer.|

> [!IMPORTANT]
> De [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), en [ `Eula` ](#eula-setting) instellingen samen worden gebruikt en u moet geldige waarden opgeven voor alle drie deze, anders uw container start niet. Zie voor meer informatie over het gebruik van deze configuratie-instellingen voor het starten van een container [facturering](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey instelling

De `ApiKey` instelling geeft u aan de Azure-resource-sleutel die wordt gebruikt voor het bijhouden van informatie over facturering voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Cognitive Services_ bron die is opgegeven [`Billing`](#billing-setting) voor de configuratie-instelling.

Deze instelling bevindt zich op de volgende locaties:

* Azure Portal: **Cognitive Services** Resource beheer, onder **sleutels**
* LUIS-portal: Pagina **sleutels en eindpunt instellingen** . 

Gebruik niet de starter-sleutel of de ontwerphandleiding. 

## <a name="applicationinsights-setting"></a>Application Insights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Facturering instellen

Met `Billing` deze instelling geeft u de eindpunt-URI op van de _Cognitive Services_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _Cognitive Services_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locaties:

* Azure Portal: **Cognitive Services** Overzicht, label`Endpoint`
* LUIS-portal: **Sleutels en eindpunt instellingen** pagina als onderdeel van de EINDPUNT-URI.

Vergeet niet om de `luis/v2.0` route ring in de URL op te geven, zoals wordt weer gegeven in de volgende tabel:


|Vereist| Name | Gegevenstype | Description |
|--|------|-----------|-------------|
|Ja| `Billing` | Reeks | URI van de facturering-eindpunt<br><br>Voorbeeld:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

De container LUIS geen gebruik maakt van invoer of uitvoer koppelt training of service-gegevens op te slaan. 

De exacte syntaxis van de locatie van de host koppelen, is afhankelijk van het hostbesturingssysteem. Bovendien de [hostcomputer](luis-container-howto.md#the-host-computer)van koppelpunten locatie is mogelijk niet toegankelijk is vanwege een conflict tussen de machtigingen die wordt gebruikt door de docker-service-account en de host koppelen locatie machtigingen. 

De volgende tabel beschrijft de instellingen die worden ondersteund.

|Vereist| Name | Gegevenstype | Description |
|-------|------|-----------|-------------|
|Ja| `Input` | Reeks | Het doel van de invoer koppelen. De standaardwaarde is `/input`. Dit is de locatie van de LUIS-pakketbestanden. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nee| `Output` | Reeks | Het doel van de uitvoer-koppelpunt. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat logboeken van LUIS-query's en logboeken voor containers. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van de docker-opdrachten uitvoeren

De volgende voorbeelden gebruiken de configuratie-instellingen om te laten zien hoe u om te schrijven en gebruik `docker run` opdrachten.  Zodra actief is, de container blijft actief totdat u [stoppen](luis-container-howto.md#stop-the-container) deze.

* In deze voor beelden wordt de Directory `C:` uit het station gebruikt om eventuele toegangs conflicten in Windows te voor komen. Als u gebruiken van een specifieke map als de invoermap wilt, moet u mogelijk de docker verlenen machtiging-service. 
* Wijzig de volgorde van de argumenten niet, tenzij u bekend bent met docker-containers.
* Als u een ander besturings systeem gebruikt, gebruikt u de juiste console/terminal, syntaxis voor koppelingen en een regel vervolg teken voor uw systeem. In deze voor beelden wordt ervan uitgegaan dat een Windows `^`-console met een regel voortzettings teken. Omdat de container een Linux-besturings systeem is, gebruikt de doel koppeling een syntaxis voor de Linux-stijl.

Vergeet niet om de `luis/v2.0` route ring in de URL op te geven, zoals wordt weer gegeven in de volgende tabel.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Indeling of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `LUIS` resource op de pagina `LUIS` Azure-sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar `LUIS` op de pagina overzicht van Azure.| Zie [vereiste para meters](luis-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start. Zie voor meer informatie, [facturering](luis-container-howto.md#billing).
> De waarde ApiKey is de **sleutel** van de pagina sleutels en eind punten in de Luis-Portal en is ook beschikbaar op de `Cognitive Services` pagina Azure-resource sleutels. 

### <a name="basic-example"></a>Eenvoudige voorbeeld

Het volgende voorbeeld is de minst argumenten mogelijk uit te voeren van de container:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Voorbeeld van de Application Insights

Het volgende voorbeeld wordt de Application Insights-argument voor het verzenden van telemetrie naar Application Insights, terwijl de container wordt uitgevoerd:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

De volgende opdracht stelt het niveau van logboekregistratie, `Logging:Console:LogLevel`, het configureren van het niveau van logboekregistratie voor [ `Information` ](https://msdn.microsoft.com). 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [over het installeren en uitvoeren van containers](luis-container-howto.md)
* Raadpleeg [problemen oplossen](troubleshooting.md) voor het oplossen van problemen met betrekking tot de functionaliteit van Luis.
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
