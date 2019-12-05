---
title: Spraak containers configureren
titleSuffix: Azure Cognitive Services
description: Speech Service voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 34b4664ec13f7ba1871433e37d86170b2207a17a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816570"
---
# <a name="configure-speech-service-containers"></a>Spraak service containers configureren

Met spraak containers kunnen klanten één spraak toepassings architectuur maken die is geoptimaliseerd om te profiteren van zowel robuuste Cloud mogelijkheden als Edge-locatie. De vier spraak containers die worden ondersteund, zijn nu, **spraak naar tekst**, **aangepast**naar tekst, **tekst-naar-spraak**en **aangepaste tekst-naar-spraak**.

De runtime-omgeving voor de **spraak** container wordt geconfigureerd met behulp van de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar zijn. De container-specifieke instellingen zijn de facturerings instellingen.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), en [ `Eula` ](#eula-setting) instellingen samen worden gebruikt en u moet geldige waarden opgeven voor alle drie deze, anders uw container start niet. Zie voor meer informatie over het gebruik van deze configuratie-instellingen voor het starten van een container [facturering](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey configuratie-instelling

De `ApiKey` instelling geeft u aan de Azure-resource-sleutel die wordt gebruikt voor het bijhouden van informatie over facturering voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _spraak_ bron die is opgegeven voor de configuratie-instelling [`Billing`](#billing-configuration-setting) .

Deze instelling bevindt zich op de volgende locatie:

- Azure Portal: resource beheer voor **spraak** , onder **sleutels**

## <a name="applicationinsights-setting"></a>Application Insights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Facturering van configuratie-instelling

Met de instelling `Billing` geeft u de eindpunt-URI op van de _spraak_ bron op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _spraak_ bron op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

- Azure Portal: overzicht **van spraak** , gelabelde `Endpoint`

| Verplicht | Naam | Gegevenstype | Beschrijving |
| -------- | ---- | --------- | ----------- |
| Ja | `Billing` | Tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](speech-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [aangepaste subdomein namen voor Cognitive Services](../cognitive-services-custom-subdomains.md)voor meer informatie en een volledige lijst met regionale eind punten. |

## <a name="eula-setting"></a>Overeenkomst instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Instellingen voor koppelen

Gebruik bind koppelt om te lezen en schrijven van gegevens naar en van de container. U kunt opgeven van een koppelpunt invoer of uitvoer koppelen door op te geven de `--mount` optie in de [docker uitvoeren](https://docs.docker.com/engine/reference/commandline/run/) opdracht.

De standaard spraak containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. Aangepaste spraak containers zijn echter afhankelijk van volume koppelt.

De exacte syntaxis van de locatie van de host koppelen, is afhankelijk van het hostbesturingssysteem. Bovendien de [hostcomputer](speech-container-howto.md#the-host-computer)van koppelpunten locatie is mogelijk niet toegankelijk is vanwege een conflict tussen de machtigingen die wordt gebruikt door de docker-service-account en de host koppelen locatie machtigingen.

| Optioneel | Naam | Gegevenstype | Beschrijving |
| -------- | ---- | --------- | ----------- |
| Niet toegestaan | `Input` | Tekenreeks | Standaard spraak containers gebruiken deze niet. Aangepaste spraak containers gebruiken [volume koppelingen](#volume-mount-settings).                                                                                    |
| Optioneel | `Output` | Tekenreeks | Het doel van de uitvoer-koppelpunt. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Instellingen voor volume koppeling

De aangepaste spraak containers gebruiken [volume koppelingen](https://docs.docker.com/storage/volumes/) om aangepaste modellen te behouden. U kunt een volume koppeling opgeven door de optie `-v` (of `--volume`) toe te voegen aan de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

Aangepaste modellen worden gedownload de eerste keer dat een nieuw model wordt opgenomen als onderdeel van de opdracht voor het uitvoeren van de aangepaste spraak container docker run. Opeenvolgende uitvoeringen van dezelfde `ModelId` voor een aangepaste spraak container gebruiken het eerder gedownloade model. Als het volume niet is gekoppeld, kunnen aangepaste modellen niet worden bewaard.

De instelling voor volume koppeling bestaat uit drie kleur `:` gescheiden velden:

1. Het eerste veld is de naam van het volume op de hostmachine, bijvoorbeeld _C:\Input_.
2. Het tweede veld is de map in de container, bijvoorbeeld _/usr/local/models_.
3. Het derde veld (optioneel) is een door komma's gescheiden lijst met opties. Zie [volumes gebruiken](https://docs.docker.com/storage/volumes/)voor meer informatie.

### <a name="volume-mount-example"></a>Voor beeld van volume koppeling

```bash
-v C:\input:/usr/local/models
```

Met deze opdracht koppelt u de _C:\Input_ -Directory van de host-computer aan de containers _/usr/local/models_ map.

> [!IMPORTANT]
> De instellingen voor volume koppeling zijn alleen van toepassing op **Custom speech-naar-tekst** -en **aangepaste tekst-naar-spraak** -containers. De standaard containers **voor spraak naar tekst** en **tekst naar spraak** maken geen gebruik van volume koppelt.

## <a name="example-docker-run-commands"></a>Voorbeeld van de docker-opdrachten uitvoeren

De volgende voorbeelden gebruiken de configuratie-instellingen om te laten zien hoe u om te schrijven en gebruik `docker run` opdrachten. Zodra actief is, de container blijft actief totdat u [stoppen](speech-container-howto.md#stop-the-container) deze.

- **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\`, als een regel voortzettings teken. Vervang of verwijder deze op basis van het hostbesturingssysteem vereisten.
- **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Indeling of voorbeeld |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | De eindpunt sleutel van de `Speech` resource op de pagina Azure `Speech`-sleutels.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de pagina overzicht van Azure `Speech`. | Zie [vereiste para meters](speech-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start. Zie voor meer informatie, [facturering](#billing-configuration-setting).
> De ApiKey-waarde is de **sleutel** van de pagina met Azure-spraak bron sleutels.

## <a name="speech-container-docker-examples"></a>Voor beelden van spraak container docker

De volgende docker-voor beelden zijn voor de spraak container.

## <a name="speech-to-texttabstt"></a>[Spraak naar tekst](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Basis voorbeeld voor spraak naar tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Voor beeld van logboek registratie voor spraak naar tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-naar-tekst](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Eenvoudig voor beeld voor Custom Speech-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Voor beeld van logboek registratie voor Custom Speech-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speechtabtss"></a>[Tekst naar spraak](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Eenvoudig voor beeld voor tekst naar spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Voor beeld van logboek registratie voor tekst naar spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speechtabctts"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Basis voorbeeld voor aangepaste tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Logboek registratie voor een aangepaste tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Volgende stappen

- Beoordeling [over het installeren en uitvoeren van containers](speech-container-howto.md)
