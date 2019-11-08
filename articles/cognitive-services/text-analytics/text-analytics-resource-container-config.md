---
title: Containers configureren-Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.
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
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795298"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics docker-containers configureren

Text Analytics voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De instellingen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders kan de container niet worden gestart. Zie [facturering](how-tos/text-analytics-how-to-install-containers.md#billing)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

Met de instelling `ApiKey` geeft u de Azure-resource sleutel op die wordt gebruikt om de facturerings gegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Text Analytics_ bron die is opgegeven voor de configuratie-instelling [`Billing`](#billing-configuration-setting) .

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Text Analytics** Resource Management onder **sleutels**

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met de instelling `Billing` geeft u de eindpunt-URI op van de _Text Analytics_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een __Text Analytics_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: overzicht van **Text Analytics** , gelabelde `Endpoint`

|Vereist| Naam | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [aangepaste subdomein namen voor Cognitive Services](../cognitive-services-custom-subdomains.md)voor meer informatie en een volledige lijst met regionale eind punten. |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor http-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboek registratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de optie `--mount` op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

De Text Analytics-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Naam | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Text Analytics containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaard waarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren 

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u `docker run`-opdrachten schrijft en gebruikt.  Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) .

* **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\`, als een regel voortzettings teken. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Text Analytics` resource die beschikbaar is op de pagina Azure `Text Analytics` sleutels. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de pagina overzicht van Azure `Text Analytics`.| Zie [vereiste para meters](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

> [!IMPORTANT]
> De opties `Eula`, `Billing`en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](how-tos/text-analytics-how-to-install-containers.md#billing)voor meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina Azure `Text Analytics` resource sleutels. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Taaldetectie](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over het installeren en uitvoeren van containers](how-tos/text-analytics-how-to-install-containers.md)
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
