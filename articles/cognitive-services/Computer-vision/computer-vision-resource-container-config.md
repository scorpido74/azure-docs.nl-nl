---
title: Containers configureren - Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u zowel de vereiste als de optionele instellingen voor Tekstcontainers herkennen in Computer Vision configureert.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718985"
---
# <a name="configure-computer-vision-docker-containers"></a>Computer Vision Docker-containers configureren

U configureert de runtime-omgeving van `docker run` de Computer Vision-container met behulp van de opdrachtargumenten. Deze container heeft verschillende vereiste instellingen, samen met een paar optionele instellingen. Er zijn verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar. De containerspecifieke instellingen zijn de factureringsinstellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) , en instellingen worden samen gebruikt, en u moet geldige waarden voor alle drie van hen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](computer-vision-how-to-install-containers.md)

## <a name="apikey-configuration-setting"></a>ApiKey-configuratie-instelling

De `ApiKey` instelling geeft `Cognitive Services` de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet [`Billing`](#billing-configuration-setting) een geldige sleutel zijn voor de resource Cognitive _Services_ die is opgegeven voor de configuratie-instelling.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: **Resourcebeheer van cognitive services,** onder **toetsen**

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling voor factureringsconfiguratie

De `Billing` instelling geeft het eindpunt URI op van de _resource Cognitive Services_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldig eindpunt URI zijn voor een _resource cognitive services_ op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: overzicht **van cognitieve services,** gelabeld`Endpoint`

Vergeet niet `vision/v1.0` om de routeroutering toe te voegen aan het eindpunt URI zoals weergegeven in de volgende tabel. 

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | Factureringseindpunt URI<br><br>Voorbeeld:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula-instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Vloeiende instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyreferenties instellingen

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montage-instellingen

Gebruik bindingsbevestigingen om gegevens van en naar de container te lezen en te schrijven. U een invoermount of uitvoerbevestiging `--mount` opgeven door de optie op te geven in de opdracht [Docker run.](https://docs.docker.com/engine/reference/commandline/run/)

De Computer Vision-containers gebruiken geen invoer- of uitvoerbevestigingen om trainings- of servicegegevens op te slaan. 

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de locatie van de [hostcomputer](computer-vision-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk vanwege een conflict tussen machtigingen die worden gebruikt door het Docker-serviceaccount en de locatiemachtigingen voor de hostmount. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Computer Vision containers maken hier geen gebruik van.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`. Dit is de locatie van de logs. Dit geldt ook voor containerlogboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken.  Eenmaal uitgevoerd, de container blijft draaien totdat u [het stopt.](computer-vision-how-to-install-containers.md#stop-the-container)

* **Line-continuation teken**: De Docker commando's in `\`de volgende secties gebruiken de back slash, als een lijn voortzetting teken. Vervang of verwijder dit op basis van de vereisten van uw hostbesturingssysteem. 
* **Argumentvolgorde:** Wijzig de volgorde van de argumenten niet, tenzij u zeer vertrouwd bent met Docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Opmaak of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpuntsleutel van `Computer Vision` de bron `Computer Vision` op de pagina Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het factureringseindpunt is beschikbaar op de pagina Azure-overzicht. `Computer Vision`| Zie [het verzamelen van vereiste parameters](computer-vision-how-to-install-containers.md#gathering-required-parameters) voor expliciete voorbeelden. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](computer-vision-how-to-install-containers.md#billing)meer informatie.
> De ApiKey-waarde **Key** is de `Cognitive Services` sleutel van de pagina Azure Resource-sleutels.

## <a name="container-docker-examples"></a>Voorbeelden van Container Docker

De volgende Docker-voorbeelden zijn voor de leescontainer.

### <a name="basic-example"></a>Basisvoorbeeld

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Voorbeeld van logboekregistratie 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* Bekijk [hoe u containers installeert en uitvoert.](computer-vision-how-to-install-containers.md)
