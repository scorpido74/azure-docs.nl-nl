---
title: Dockercontainer-instellingen - LUIS
titleSuffix: Azure Cognitive Services
description: De runtime-omgeving van luis-container is geconfigureerd met de `docker run` opdrachtargumenten. LUIS heeft verschillende vereiste instellingen, samen met een paar optionele instellingen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795012"
---
# <a name="configure-language-understanding-docker-containers"></a>Containers voor taalbegrijpendocker configureren 

De runtime-omgeving voor **taalbegrip** (LUIS) `docker run` wordt geconfigureerd met de opdrachtargumenten. LUIS heeft verschillende vereiste instellingen, samen met een paar optionele instellingen. Er zijn verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar. De containerspecifieke instellingen zijn de [invoerbevestigingsinstellingen](#mount-settings) en de factureringsinstellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

Deze container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey (ApiKey)](#apikey-setting)|Wordt gebruikt om factureringsgegevens bij te houden.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee u telemetrieondersteuning azure [application insights](https://docs.microsoft.com/azure/application-insights) toevoegen aan uw container.|
|Ja|[Facturering](#billing-setting)|Hiermee geeft u het eindpunt URI van de servicebron op Azure op.|
|Ja|[Overeenkomst](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Vloeiend](#fluentd-settings)|Schrijf logboek- en, optioneel, metrische gegevens naar een Fluentd-server.|
|Nee|[Http-proxy](#http-proxy-credentials-settings)|Een HTTP-proxy configureren voor het maken van uitgaande aanvragen.|
|Nee|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core-registratieondersteuning voor uw container. |
|Ja|[Mounts](#mount-settings)|Lees en schrijf gegevens van hostcomputer tot container en van container terug naar hostcomputer.|

> [!IMPORTANT]
> De [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting), [`Eula`](#eula-setting) , en instellingen worden samen gebruikt, en u moet geldige waarden voor alle drie van hen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](luis-container-howto.md#billing)

## <a name="apikey-setting"></a>ApiKey-instelling

De `ApiKey` instelling geeft de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet [`Billing`](#billing-setting) een geldige sleutel zijn voor de resource Cognitive _Services_ die is opgegeven voor de configuratie-instelling.

Deze instelling is te vinden op de volgende plaatsen:

* Azure-portal: **Resourcebeheer van cognitive services,** onder **toetsen**
* LUIS-portal: pagina **Sleutels en Eindpuntinstellingen.** 

Gebruik de startsleutel of de ontwerpsleutel niet. 

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Factureringsinstelling

De `Billing` instelling geeft het eindpunt URI op van de _resource Cognitive Services_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldig eindpunt URI zijn voor een _resource cognitive services_ op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling is te vinden op de volgende plaatsen:

* Azure-portal: overzicht **van cognitieve services,** gelabeld`Endpoint`
* LUIS-portal: **pagina Sleutels en eindpuntinstellingen** als onderdeel van het eindpunt URI.

| Vereist | Name | Gegevenstype | Beschrijving |
|----------|------|-----------|-------------|
| Ja      | `Billing` | tekenreeks | Factureringseindpunt URI. Zie het verzamelen van [vereiste parameters](luis-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de factureringuri. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](../cognitive-services-custom-subdomains.md)meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Eula-instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Vloeiende instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyreferenties instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montage-instellingen

Gebruik bindingsbevestigingen om gegevens van en naar de container te lezen en te schrijven. U een invoermount of uitvoerbevestiging `--mount` opgeven door de optie op te geven in de opdracht [Docker run.](https://docs.docker.com/engine/reference/commandline/run/) 

De LUIS-container gebruikt geen invoer- of uitvoerbevestigingen om trainings- of servicegegevens op te slaan. 

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de locatie van de [hostcomputer](luis-container-howto.md#the-host-computer)mogelijk niet toegankelijk vanwege een conflict tussen machtigingen die worden gebruikt door het dockerserviceaccount en de locatiemachtigingen voor de hostmount. 

In de volgende tabel worden de ondersteunde instellingen beschreven.

|Vereist| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Ja| `Input` | Tekenreeks | Het doel van de invoerberg. De standaardwaarde is `/input`. Dit is de locatie van de LUIS-pakketbestanden. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nee| `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`. Dit is de locatie van de logs. Dit omvat LUIS-querylogboeken en containerlogboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken.  Eenmaal uitgevoerd, de container blijft draaien totdat u [het stopt.](luis-container-howto.md#stop-the-container)

* Deze voorbeelden gebruiken de `C:` directory buiten het station om eventuele machtigingsconflicten op Windows te voorkomen. Als u een specifieke map als invoermap moet gebruiken, moet u mogelijk de dockerservicetoestemming verlenen. 
* Wijzig de volgorde van de argumenten niet, tenzij u zeer vertrouwd bent met dockercontainers.
* Als u een ander besturingssysteem gebruikt, gebruikt u de juiste console/terminal, mapsyntaxis voor bevestigingen en regelvervolgteken voor uw systeem. In deze voorbeelden wordt uitgegaan van `^`een Windows-console met een regelvervolgteken . Omdat de container een Linux-besturingssysteem is, maakt de doelhouder gebruik van een syntaxis van de Linux-stijl.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Opmaak of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpuntsleutel van `LUIS` de bron `LUIS` op de pagina Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het factureringseindpunt is beschikbaar op de pagina Azure-overzicht. `LUIS`| Zie [het verzamelen van vereiste parameters](luis-container-howto.md#gathering-required-parameters) voor expliciete voorbeelden. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet. Zie [Facturering voor](luis-container-howto.md#billing)meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina Sleutels en Eindpunten `Cognitive Services` in de LUIS-portal en is ook beschikbaar op de pagina Azure-bronsleutels. 

### <a name="basic-example"></a>Basisvoorbeeld

In het volgende voorbeeld vindt u de minste argumenten om de container uit te voeren:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Voorbeeld ApplicationInsights

In het volgende voorbeeld wordt het argument ApplicationInsights ingesteld om telemetrie naar Application Insights te verzenden terwijl de container wordt uitgevoerd:

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

### <a name="logging-example"></a>Voorbeeld van logboekregistratie 

Met de volgende opdracht `Logging:Console:LogLevel`wordt het logboekregistratieniveau [`Information`](https://msdn.microsoft.com)ingesteld om het logboekregistratieniveau te configureren op . 

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

* Controleren [hoe u containers installeert en uitvoert](luis-container-howto.md)
* Raadpleeg [Probleemoplossing](troubleshooting.md) om problemen met luis-functionaliteit op te lossen.
* Meer [Cognitive Services-containers gebruiken](../cognitive-services-container-support.md)
