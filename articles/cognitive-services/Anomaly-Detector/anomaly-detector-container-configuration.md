---
title: Een container configureren voor Anomaly Detector API
titleSuffix: Azure Cognitive Services
description: De runtime-runtime-omgeving van de `docker run` Anomalie Detector-api-container is geconfigureerd met behulp van de opdrachtargumenten. Deze container heeft verschillende vereiste instellingen, samen met een paar optionele instellingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875177"
---
# <a name="configure-anomaly-detector-containers"></a>Anomaly Detector-containers configureren

De runtime-omgeving **van de anomaliedetectorcontainer** is geconfigureerd met de `docker run` opdrachtargumenten. Deze container heeft verschillende vereiste instellingen, samen met een paar optionele instellingen. Er zijn verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar. De containerspecifieke instellingen zijn de factureringsinstellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

Deze container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey (ApiKey)](#apikey-configuration-setting)|Wordt gebruikt om factureringsgegevens bij te houden.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee u telemetrieondersteuning azure [application insights](https://docs.microsoft.com/azure/application-insights) toevoegen aan uw container.|
|Ja|[Facturering](#billing-configuration-setting)|Hiermee geeft u het eindpunt URI van de servicebron op Azure op.|
|Ja|[Overeenkomst](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Vloeiend](#fluentd-settings)|Schrijf logboek- en, optioneel, metrische gegevens naar een Fluentd-server.|
|Nee|[Http-proxy](#http-proxy-credentials-settings)|Een HTTP-proxy configureren voor het maken van uitgaande aanvragen.|
|Nee|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core-registratieondersteuning voor uw container. |
|Nee|[Mounts](#mount-settings)|Lees en schrijf gegevens van hostcomputer tot container en van container terug naar hostcomputer.|

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) , en instellingen worden samen gebruikt, en u moet geldige waarden voor alle drie van hen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](anomaly-detector-container-howto.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey-configuratie-instelling

De `ApiKey` instelling geeft de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet [`Billing`](#billing-configuration-setting) een geldige sleutel zijn voor de _Anomaliedetectorbron_ die is opgegeven voor de configuratie-instelling.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: **Resourcebeheer van anomaliedetector,** onder **sleutels**

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling voor factureringsconfiguratie

De `Billing` instelling geeft het eindpunt URI op van de _anomaliedetectorbron_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldig eindpunt URI zijn voor een _Anomaliedetectorbron_ in Azure.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: overzicht **van anomaliedetector,** gelabeld`Endpoint`

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | Factureringseindpunt URI. Zie het verzamelen van [vereiste parameters](anomaly-detector-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de factureringuri. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](../cognitive-services-custom-subdomains.md)meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Eula-instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Vloeiende instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor Http-proxyreferenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Montage-instellingen

Gebruik bindingsbevestigingen om gegevens van en naar de container te lezen en te schrijven. U een invoermount of uitvoerbevestiging `--mount` opgeven door de optie op te geven in de opdracht [Docker run.](https://docs.docker.com/engine/reference/commandline/run/)

De Anomaly Detector-containers gebruiken geen invoer- of uitvoerbevestigingen om trainings- of servicegegevens op te slaan. 

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de locatie van de [hostcomputer](anomaly-detector-container-howto.md#the-host-computer)mogelijk niet toegankelijk vanwege een conflict tussen machtigingen die worden gebruikt door het Docker-serviceaccount en de locatiemachtigingen voor de hostmount. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Anomalie Detector containers maken hier geen gebruik van.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`. Dit is de locatie van de logs. Dit geldt ook voor containerlogboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd 

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken.  Eenmaal uitgevoerd, de container blijft draaien totdat u [het stopt.](anomaly-detector-container-howto.md#stop-the-container)

* **Line-continuation teken**: De Docker commando's in `\`de volgende secties gebruiken de back slash, als een lijn voortzetting teken voor een bash shell. Vervang of verwijder dit op basis van de vereisten van uw hostbesturingssysteem. Bijvoorbeeld, de lijn voortzetting teken voor windows `^`is een caret, . Vervang de rug slash met de caret. 
* **Argumentvolgorde:** Wijzig de volgorde van de argumenten niet, tenzij u zeer vertrouwd bent met Docker-containers.

Vervang waarde tussen `{}`haakjes, , met uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Opmaak of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpuntsleutel van `Anomaly Detector` de bron `Anomaly Detector` op de pagina Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het factureringseindpunt is beschikbaar op de pagina Azure-overzicht. `Anomaly Detector`| Zie [het verzamelen van vereiste parameters](anomaly-detector-container-howto.md#gathering-required-parameters) voor expliciete voorbeelden. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](anomaly-detector-container-howto.md#billing)meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina Azure Anomaly Detector Resource-sleutels. 

## <a name="anomaly-detector-container-docker-examples"></a>Voorbeelden van Anomalie Detector container Docker

De volgende Docker voorbeelden zijn voor de Anomalie Detector container. 

### <a name="basic-example"></a>Basisvoorbeeld 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Voorbeeld van logboekregistratie met opdrachtregelargumenten

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* [Een anomaliedetectorcontainer implementeren in Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Meer informatie over de API-service voor Anomalie Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
