---
title: Containers installeren en uitvoeren-Translator Text
titleSuffix: Azure Cognitive Services
description: Instructies voor het downloaden, installeren en uitvoeren van containers voor Translator Text Analytics in deze walkthrough zelf studie.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507878"
---
# <a name="install-and-run-translator-text-containers"></a>Translator Text containers installeren en uitvoeren

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Met containers kunt u de Translator Text-Api's in uw eigen omgeving uitvoeren en zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer.

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u Translator Text containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie het [docker-overzicht](https://docs.docker.com/engine/docker-overview/)voor een primer op basis van docker en container.<br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker`-opdrachten.|

## <a name="request-access-to-the-container-registry"></a>Toegang aanvragen tot het container register

U moet eerst het [aanvraag formulier voor de cognitieve Translator text containers](https://aka.ms/translatorcontainerform) volt ooien en verzenden om toegang tot de container aan te vragen.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>De hostcomputer

De host is een op x64 gebaseerde computer met een Linux-besturings systeem waarop de docker-container wordt uitgevoerd. Dit kan een computer in uw locatie zijn of een docker-hosting service in azure, zoals:

* Azure Kubernetes-service.
* Azure Container Instances.
* Een [Kubernetes](https://kubernetes.io/) -cluster is geïmplementeerd op Azure stack.

### <a name="container-requirements-and-recommendations"></a>Container vereisten en aanbevelingen

De volgende tabel beschrijft de minimale en aanbevolen CPU-kernen, ten minste 2,6 gigahertz (GHz) of sneller, en geheugen, in gigabytes (GB), om voor elke Translator Text container toe te wijzen.

| Container | Minimum | Taal paar |
|-----------|---------|---------------|
| Translator Text | 4-core, 4 GB geheugen | 4 |

Voor elk taal paar kunt u het beste 1 GB geheugen hebben. De Translator Text-container heeft standaard drie of vier taal paren, afhankelijk van de `<image-tag>` die u uitvoert. Zie [ondersteunde talen en omzetting](#supported-languages-and-translation) voor meer informatie. De kern en het geheugen komen overeen met de instellingen `--cpus` en `--memory` die worden gebruikt als onderdeel van de `docker run` opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie met `docker pull` ophalen

Container installatie kopieën voor Translator Text zijn beschikbaar in de volgende container opslagplaats. De tabel wijst ook de tags van de container-installatie kopie en de bijbehorende ondersteunde talen toe.

| Container | Afbeeldings code | Ondersteunde talen |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE`en `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`en `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Docker-pull voor de Translator Text container

Als u de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht wilt uitvoeren, moet u eerst toegang hebben tot het container register. Vanuit de Azure CLI kunt u zich aanmelden bij de Azure Container Registry met behulp van de [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) opdracht.

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Met deze opdracht wordt uw huidige gebruiker geverifieerd met de bijbehorende Azure Container Registry. U kunt de `docker pull`-opdracht nu uitvoeren.

> [!NOTE]
> Afhankelijk van de taal ondersteuning die u nodig hebt, geeft u de bijbehorende `<image-tag>`op.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run). Er zijn meer [voor beelden](translator-text-container-config.md#example-docker-run-commands) van de `docker run`-opdracht beschikbaar.
1. [Zoek het Vertaal eindpunt van de container](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>De container met `docker run` uitvoeren

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om een van de drie containers uit te voeren. [Voor beelden](translator-text-container-config.md#example-docker-run-commands) van de opdracht `docker run` zijn beschikbaar.

### <a name="run-container-example-of-docker-run-command"></a>Container voorbeeld van de opdracht docker run uitvoeren

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Deze opdracht:

* Voert een Translator Text container uit vanuit de container installatie kopie
* Wijst 4 CPU-kernen en 4 GB aan geheugen toe
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY toe voor de container
* Hiermee wordt de gebruiksrecht overeenkomst geaccepteerd
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer

### <a name="how-to-collect-docker-logs"></a>Docker-logboeken verzamelen

Voor het oplossen van problemen kan het handig zijn om de docker-logboeken van de uitvoer van de container weer te geven. Voer eerst de opdracht [docker PS](https://docs.docker.com/engine/reference/commandline/ps/) uit met de opmaak vlag om de details te beperken die voor alle containers worden weer gegeven.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Voer vervolgens de opdracht [docker-logboeken](https://docs.docker.com/engine/reference/commandline/logs/) uit met de `<Container ID>` voor de desbetreffende container om de logboeken weer te geven.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

Met de opdracht logboeken voor docker-logbestanden worden de fout logboeken voor de afgelopen vier uur verzameld.

## <a name="supported-languages-and-translation"></a>Ondersteunde talen en omzetting

De methode `POST /translate` ondersteunt de volgende taal conversies, van het *Engels* naar een doel taal en andersom. Houd er rekening mee dat u naar en vanuit het Engels kunt gaan met een van de talen die worden weer gegeven, maar *niet* van de ene *Engelse* taal naar een andere *niet-Engelse* taal.

> [!NOTE]
> Voor optimale kwaliteit moeten consumenten slechts één zin per aanvraag verzenden.

| Taal conversie | ISO-conversie van taal | Afbeeldings Tags |
|--|--|:--|
| Engels: left_right_arrow: Chinees | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Engels: left_right_arrow: Russisch | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Engels: left_right_arrow: Arabisch | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Engels: left_right_arrow: Duits | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` en `de_en_es_fr_1.0.0` |
| Engels: left_right_arrow: Spaans | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Engels: left_right_arrow: Frans | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> De `Eula` moet worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.

## <a name="query-the-containers-translate-endpoint"></a>Het Vertaal eindpunt van de container opvragen

De container biedt een op REST gebaseerde Vertaal-eindpunt-API. Enkele voor beelden van het gebruik van dit eind punt:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Voer de volgende krul opdracht uit vanaf de gewenste CLI.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Als u dit krul bericht probeert te plaatsen voordat de container gereed is, wordt de reactie ' service is tijdelijk niet beschikbaar ' beëindigd. Wacht totdat de container gereed is en probeer het opnieuw.

De volgende uitvoer wordt afgedrukt op de-console.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Ga naar de pagina Swagger, http://localhost:5000/swagger/index.html

1. **Post/Translate** selecteren
1. Een **try-out** selecteren
1. Voer de para meter **from in** als `en-US`
1. Voer de **als** -para meter in als `de-DE`
1. Voer de para meter **API-Version** in als `3.0`
1. Vervang `string` onder **teksten**door de volgende JSON
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Selecteer **uitvoeren**, de resulterende vertalingen worden uitgevoerd in de **hoofd tekst**van het antwoord. U zou er ongeveer als volgt uit moeten zien:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Start Visual Studio en maak een nieuwe console toepassing.
1. Bewerk het `*.csproj` bestand om het `<LangVersion>7.1</LangVersion>`-knoop punt toe te C# voegen. Hiermee geeft u 7,1 op.
1. Voeg het pakket [Newton soft. json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet, versie 11.0.2, toe.
1. Vervang in `Program.cs`alle bestaande code door het volgende:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Druk op **F5** om het programma uit te voeren.
1. De volgende uitvoer wordt afgedrukt op de-console.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](translator-text-container-config.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd om Translator Text containers te downloaden, te installeren en uit te voeren. Samenvatting:

* Translator Text biedt meerdere Linux-containers voor docker, waarbij verschillende taal paren worden ingekapseld.
* Container installatie kopieën worden gedownload uit het REGI ster ' container preview '.
* Container installatie kopieën worden uitgevoerd in docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in Translator Text containers door de URI van de host op te geven van de container.

## <a name="next-steps"></a>Volgende stappen

* Containers voor configuratie-instellingen [configureren](translator-text-container-config.md) controleren
* Raadpleeg de veelgestelde [vragen over de container](../containers/container-faq.md) om problemen met betrekking tot de functionaliteit op te lossen.
