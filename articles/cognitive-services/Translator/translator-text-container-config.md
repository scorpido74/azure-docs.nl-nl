---
title: Containers configureren-Translator Text
titleSuffix: Azure Cognitive Services
description: Translator Text biedt containers met een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507669"
---
# <a name="configure-translator-text-docker-containers"></a>Translator Text docker-containers configureren

Translator Text voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.

## <a name="configuration-settings"></a>Configuratie-instellingen

De container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](https://docs.microsoft.com/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Ja|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Schrijft logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|Nee|HTTP-proxy|Hiermee configureert u een HTTP-proxy voor het maken van uitgaande aanvragen.|
|Nee|[Userenv](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|Nee|[Koppelt](#mount-settings)|Leest en schrijft gegevens van de hostcomputer naar de container en van de container terug naar de hostcomputer.|

> [!IMPORTANT]
> De [`Eula`](#eula-setting) -instelling moet worden gegeven met de waarde `accept`. anders kan de container niet worden gestart.

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboek registratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de optie `--mount` op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

De Translator Text-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Naam | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Translator Text containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaard waarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren 

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u `docker run`-opdrachten schrijft en gebruikt.  Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](how-to-install-containers.md#stop-the-container) .

* **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\`, als een regel voortzettings teken. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

## <a name="translator-text-container-docker-examples"></a>Voor beelden van Translator Text container docker

De volgende docker-voor beelden zijn voor de Translator Text-container.

### <a name="basic-example"></a>Basis voorbeeld

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Voor beeld van logboek registratie

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over het installeren en uitvoeren van containers](how-to-install-containers.md)
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
