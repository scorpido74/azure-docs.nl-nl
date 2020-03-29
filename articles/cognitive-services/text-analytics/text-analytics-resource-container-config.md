---
title: Containers configureren - Tekstanalyse
titleSuffix: Azure Cognitive Services
description: Text Analytics biedt elke container een gemeenschappelijk configuratiekader, zodat u eenvoudig opslag-, logboekregistratie- en telemetrie- en beveiligingsinstellingen voor uw containers configureren en beheren.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 8a39327275dca43ddb6ce0e46a3e3bb51ec4555b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795298"
---
# <a name="configure-text-analytics-docker-containers"></a>Containers voor text Analytics-docker configureren

Text Analytics biedt elke container een gemeenschappelijk configuratiekader, zodat u eenvoudig opslag-, logboekregistratie- en telemetrie- en beveiligingsinstellingen voor uw containers configureren en beheren.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) , en instellingen worden samen gebruikt, en u moet geldige waarden voor alle drie van hen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](how-tos/text-analytics-how-to-install-containers.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey-configuratie-instelling

De `ApiKey` instelling geeft de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet [`Billing`](#billing-configuration-setting) een geldige sleutel zijn voor de _Text Analytics-bron_ die is opgegeven voor de configuratie-instelling.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: **text Analytics-bronbeheer** onder **Toetsen**

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling voor factureringsconfiguratie

De `Billing` instelling geeft het eindpunt URI op van de _Text Analytics-bron_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldig eindpunt URI zijn voor een __Text Analytics-bron_ op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: **overzicht van tekstanalyse,** gelabeld`Endpoint`

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | Factureringseindpunt URI. Zie het verzamelen van [vereiste parameters](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de factureringuri. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](../cognitive-services-custom-subdomains.md)meer informatie en een volledige lijst met regionale eindpunten. |

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

De text analytics-containers gebruiken geen invoer- of uitvoerbevestigingen om trainings- of servicegegevens op te slaan. 

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de locatie van de [hostcomputer](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk vanwege een conflict tussen machtigingen die worden gebruikt door het dockerserviceaccount en de locatiemachtigingen voor de hostmount. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Text Analytics-containers gebruiken dit niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`. Dit is de locatie van de logs. Dit geldt ook voor containerlogboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd 

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken.  Eenmaal uitgevoerd, de container blijft draaien totdat u [het stopt.](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)

* **Line-continuation teken**: De docker opdrachten in de `\`volgende secties gebruiken de back slash, als een lijn voortzetting teken. Vervang of verwijder dit op basis van de vereisten van uw hostbesturingssysteem. 
* **Argumentvolgorde:** Wijzig de volgorde van de argumenten niet, tenzij u zeer vertrouwd bent met dockercontainers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Opmaak of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpuntsleutel van `Text Analytics` de resource `Text Analytics` die beschikbaar is op de pagina Azure Keys. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | De waarde van het factureringseindpunt is beschikbaar op de pagina Azure-overzicht. `Text Analytics`| Zie [het verzamelen van vereiste parameters](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) voor expliciete voorbeelden. |

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](how-tos/text-analytics-how-to-install-containers.md#billing)meer informatie.
> De ApiKey-waarde **Key** is de `Text Analytics` sleutel van de pagina Azure Resource-sleutels. 

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Volgende stappen

* Controleren [hoe u containers installeert en uitvoert](how-tos/text-analytics-how-to-install-containers.md)
* Meer [Cognitive Services-containers gebruiken](../cognitive-services-container-support.md)
